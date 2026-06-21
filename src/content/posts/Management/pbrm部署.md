---
title: 北极熊导航部署
published: 2026-02-16
pinned: false
description: 北极熊导航部署
tags: [战队,导航]
category: 管理
draft: false
---
感谢北极熊战队的开源，感谢派大星的指导，为我们战队在开发哨兵过程中提供了重要的参考和帮助。

---
# 部署仿真环境
## 安装ros
```bash
wget http://fishros.com/install -O fishros && . fishros
sudo apt install git -y
```
## 安装[Fortress](https://gazebosim.org/docs/fortress/install_ubuntu/)
```bash
sudo apt-get update
sudo apt-get install lsb-release gnupg
sudo curl https://packages.osrfoundation.org/gazebo.gpg --output /usr/share/keyrings/pkgs-osrf-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/pkgs-osrf-archive-keyring.gpg] https://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/gazebo-stable.list > /dev/null
sudo apt-get update
sudo apt-get install ignition-fortress
```
## 构建工作空间
```bash
sudo apt install python3-pip -y
sudo pip install vcstool2
pip install xmacro
#推荐科学上网
mkdir -p ~/ros_ws
cd ~/ros_ws
git clone https://github.com/SMBU-PolarBear-Robotics-Team/rmu_gazebo_simulator.git src/rmu_gazebo_simulator
vcs import src < src/rmu_gazebo_simulator/dependencies.repos
rosdep install -r --from-paths src --ignore-src --rosdistro $ROS_DISTRO -y
colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=release --parallel-workers 2
```
## 启动
```bash
export LIBGL_ALWAYS_SOFTWARE=1
export SVGA_VGPU10=0
ros2 launch rmu_gazebo_simulator bringup_sim.launch.py
```

# 部署导航
## 安装gicp
```bash
sudo apt install -y libeigen3-dev libomp-dev
git clone https://github.com/koide3/small_gicp.git
cd small_gicp
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release && make -j
sudo make install
```
## 构建工作空间
```bash
cd ~/ros2_ws
git clone --recursive https://github.com/SMBU-PolarBear-Robotics-Team/pb2025_sentry_nav.git src/pb2025_sentry_nav
#添加仿真pcd
rosdep install -r --from-paths src --ignore-src --rosdistro $ROS_DISTRO -y
colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release --parallel-workers 2
```
## 启动
```bash
ros2 launch pb2025_nav_bringup rm_navigation_simulation_launch.py \
world:=rmul_2025 \
slam:=False \
use_sim_time:=True
```

# 常用内容
### 
在rviz中显示机器人
```bash
ros2 launch pb2025_robot_description robot_description_launch.py
```
### 控制机器人移动
```sh
ros2 run rmoss_gz_base test_chassis_cmd.py --ros-args -r __ns:=/red_standard_robot1/robot_base -p v:=0.3 -p w:=0.3
#根据提示进行输入，支持平移与自旋
```
### 修改世界
1. 修改`rmu_gazebo_simulator/rmu_gazebo_simulator/config/gz_world.yaml`中的 `world`
2. 前往 [FlowUs](https://flowus.cn/lihanchen/share/87f81771-fc0c-4e09-a768-db01f4c136f4?code=4PP1RS) 下载对应pcd放到`pb2025_sentry_nav/pb2025_nav_bringup/pcd`
3. 启动导航的命令中的参数做对应修改

# 添加世界

```
rmuc_2026
|-- meshes
|      -- rmuc_2026.dae
|-- model.config
`-- model.sdf
```

从:spoiler[机构\cg组]官方文件获得.dae
需要修改车的初始位置[xzy_pose](rmu_gazebo_simulator/rmu_gazebo_simulator/config/gz_world.yaml)和世界初始位置[pose frame](rmu_gazebo_simulator/rmu_gazebo_simulator/resource/models/rmuc_2026/model.sdf)
用gazebo中的`pose`修改参数，启动仿真以启动重力来确定z值

# 建图
```bash
ros2 launch rmu_gazebo_simulator bringup_sim.launch.py
ros2 launch pb2025_nav_bringup rm_navigation_simulation_launch.py slam:=True 
# 键盘操控
ros2 run rmoss_gz_base test_chassis_cmd.py --ros-args -r __ns:=/red_standard_robot1/robot_base -p v:=3.0 -p w:=0.3
# call服务保存地图
ros2 service call /red_standard_robot1/map_saver/save_map nav2_msgs/srv/SaveMap \ "{map_url: '/home/ubuntu/map/wxm', image_format: 'pgm'}"
```
验证地图后需要移动到相应文件夹中

## 导航

```bash
ros2 launch pb2025_nav_bringup rm_navigation_simulation_launch.py \
world:=wxm \
slam:=False 
```