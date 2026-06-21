---
title: Linux中USB设备的绑定
published: 2023-12-14
pinned: false
tags: [Linux]
category: 学习项目
draft: false
---

## 必要性
随着我们开发的机器人具备的功能越来越复杂，那么需要外接的USB设备会逐渐多起来。
* 每次插在不同的接口上，写着同样的/dev/ttyUSB0有时候就不能用了。
* 但看/dev/ACM0根本看不出来是什么设备
* 设备权限或状态需要每次插上都设置一次很麻烦
可以使用使用udev规则创建USB设备挂载点映射

udev的路径在`/etc/udev/rules.d/`,后续规则都需要写在这个文件，并且写完后拔插USB设备或者执行
```
sudo service udev reload
sudo service udev restart
```
## 绑定别名
先执行`lsusb`可以看到我插入的rplidar，其ID为`10c4 ea60`,我想将其命名为rplidar,只需要向rules.d写入
```
KERNEL=="ttyUSB*", ATTRS{idVendor}=="10c4", ATTRS{idProduct}=="ea60", MODE:="0777", SYMLINK+="rplidar"
```
重加载udev 服务并重新拔插后，执行
```
ls -l /dev | grep ttyUSB
ls /dev/rplidar
```
如果配置成功，你会看到 `rplidar` 已经指向了对应的 `ttyUSB`
后续代码中均可以用/dev/rplidar来指向雷达

## ID冲突时区分
插上一个IMU后，发现其ID也是`10c4 ea60`，与雷达冲突。此时我们就需要在rules.d中新增KERNELS匹配键，这样才能将IMU和雷达区分开。先拔下雷达执行如下命令查看KERNELS
```
udevadm info --attribute-walk --name=/dev/ttyUSB1 | grep KERNELS
```
预期输出
```
xxxxxxxxxxxxxxx:
KERNELS=="1-2"
xxx
xx
xxxx
ATTRS{idVendor}=="10c4" 
ATTRS{idProduct}=="ea60"
```
其中KERNELS`1-2` 代表主板上的第 1 个 USB 控制器的第 2 号端口，表示IMU插在1-2上。如果换了位置则需要重新绑定。
再插上雷达，也能同样获取一个KERNELS，假设为1-3
则可编辑udev规则为
```
# 匹配 RPLIDAR KERNEL=="ttyUSB*", ATTRS{idVendor}=="10c4", ATTRS{idProduct}=="ea60", KERNELS=="1-3", MODE:="0777", SYMLINK+="rplidar"
# 匹配 IMU KERNEL=="ttyUSB*", ATTRS{idVendor}=="10c4", ATTRS{idProduct}=="ea60", KERNELS=="1-2", MODE:="0777", SYMLINK+="imu"
```
执行如下命令应该能同时看到这两个独立的软链接。
```
ls -l /dev | grep -E "rplidar|imu"
```

## 关闭相机休眠
某年某月开发某个双相机设备时，出现其中一个相机莫名其妙无法打开。后续进一步观察发现此相机刚开机可以打开，静置一段时间就检测不到此设备。再三查证，此相机内置了休眠功能。可以使用udev规则来关闭
这里给出当时所有的udev来参考
```
# 第一个摄像头 - HRY USB Camera (命名为scan)
SUBSYSTEM=="video4linux", ATTRS{idVendor}=="05a3", ATTRS{idProduct}=="9230", ATTRS{serial}=="20241009", ENV{ID_V4L_CAPABILITIES}==":capture:", SYMLINK+="scan"
SUBSYSTEM=="video4linux", ATTRS{idVendor}=="05a3", ATTRS{idProduct}=="9230", ATTRS{serial}=="20241009", ENV{ID_V4L_CAPABILITIES}!=":capture:", SYMLINK+="scan-ctrl"

# 第二个摄像头 - RYS RGB Camera (命名为cam)
SUBSYSTEM=="video4linux", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="5161", ATTRS{serial}=="200901010001", ENV{ID_V4L_CAPABILITIES}==":capture:", SYMLINK+="cam"
SUBSYSTEM=="video4linux", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="5161", ATTRS{serial}=="200901010001", ENV{ID_V4L_CAPABILITIES}!=":capture:", SYMLINK+="cam-ctrl"

# === 禁用自动挂起规则 ===
# 为scan摄像头禁用USB自动挂起
SUBSYSTEM=="usb", ATTRS{idVendor}=="05a3", ATTRS{idProduct}=="9230", ATTRS{serial}=="20241009", ATTR{power/control}="on"
SUBSYSTEM=="usb", ATTRS{idVendor}=="05a3", ATTRS{idProduct}=="9230", ATTRS{serial}=="20241009", ATTR{power/autosuspend}="-1"

# 为cam摄像头禁用USB自动挂起
SUBSYSTEM=="usb", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="5161", ATTRS{serial}=="200901010001", ATTR{power/control}="on"
SUBSYSTEM=="usb", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="5161", ATTRS{serial}=="200901010001", ATTR{power/autosuspend}="-1"

# 为视频设备禁用电源管理
SUBSYSTEM=="video4linux", ATTRS{idVendor}=="05a3", ATTRS{idProduct}=="9230", ATTR{power/control}="on"
SUBSYSTEM=="video4linux", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="5161", ATTR{power/control}="on"

# 更通用的规则 - 禁用所有USB摄像头的自动挂起
SUBSYSTEM=="usb", ATTRS{bInterfaceClass}=="0e", ATTRS{bInterfaceSubClass}=="01", ATTR{power/control}="on"
SUBSYSTEM=="usb", ATTRS{bInterfaceClass}=="0e", ATTRS{bInterfaceSubClass}=="01", ATTR{power/autosuspend}="-1"
```