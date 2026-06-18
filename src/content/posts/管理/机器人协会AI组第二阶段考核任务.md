---
title: 机器人协会AI组第二阶段考核任务
published: 2024-04-14
pinned: false
description: 机器人协会AI组第二阶段考核任务
tags: [协会,AI组]
category: 管理项目
draft: false
---
# 机器人协会AI组第二阶段考核任务

## 任务一：OpenCV巡线小车

### 规则

组队：可以与控制组组队完成，队伍人数要求：1-4人

时间：暂定

场地：具体地点暂定

赛道：白底黑线（循着黑线前进）

示意图（具体路径暂定）：

![image-20240317133812779](https://cdn.jsdelivr.net/gh/1944195627wb/images@main/images/image-20240317133812779.png)

比赛完成指标及计分排名：小车沿着赛道跑一圈，按照完成时间长短进行排名

内容：

AI组负责使用OpenCV（python、C++都可以）部署在香橙派（Orangepi）开发板处理图像后将处理信息通过串口通讯传送给控制端，完成比赛。

提示：

串口通讯python可以通过pyserial：[用 Python 玩转串口（基于 pySerial）_python打开串口-CSDN博客](https://blog.csdn.net/bryanwang_3099/article/details/120493736)

示例：

```python
import serial


def Connect():
    global ser
    ser= serial.Serial(port="/dev/ttyTHS0",baudrate=9600,timeout=0.5)
    if ser.isOpen():
        return True
    else:
        print("STM32 Serial Open Error!")
        return False


def DisConnect():
    ser.close()


def SendCommand(command):
    meg_tranform = messgae_tranform(command)
    ser.write(meg_tranform.encode('utf-8'))


def messgae_tranform(meg):
    if meg[1]=="0":
        meg_tranform="a"
    elif meg=="a1":
        meg_tranform = "b"
    elif meg=="a2":
        meg_tranform = "c"
    elif meg == "a3":
        meg_tranform = "d"
    elif meg == "b1":
        meg_tranform = "e"
    elif meg == "b2":
        meg_tranform = "f"
    elif meg == "b3":
        meg_tranform = "g"
    elif meg == "c1":
        meg_tranform = "h"
    elif meg == "c2":
        meg_tranform = "i"
    elif meg == "c3":
        meg_tranform = "j"
    return meg_tranform
```

C++可以通过下面的代码实现

```c++
//串口发送库
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <errno.h>
#include <unistd.h>
#include <termios.h>
#include <fcntl.h>
#include <iconv.h>
#include <string>
#include <iostream>



/*
* 打开串口
*/
int open_port(int com_port)
{
    int fd;
    /* 使用普通串口 */
    // TODO::在此处添加串口列表
    char* dev[] = { "/dev/ttyTHS0", "/dev/ttyUSB0" };
 
    //O_NDELAY 同 O_NONBLOCK。
    fd = open(dev[com_port], O_RDWR | O_NOCTTY);
    if (fd < 0)
    {
        perror("open serial port");
        return(-1);
    }
 
    //恢复串口为阻塞状态 
    //非阻塞：fcntl(fd,F_SETFL,FNDELAY)  
    //阻塞：fcntl(fd,F_SETFL,0) 
    if (fcntl(fd, F_SETFL, 0) < 0)
    {
        perror("fcntl F_SETFL\n");
    }
    /*测试是否为终端设备*/
    if (isatty(STDIN_FILENO) == 0)
    {
        perror("standard input is not a terminal device");
    }
 
    return fd;
}
 
/*
* 串口设置
*/
int set_uart_config(int fd, int baud_rate, int data_bits, char parity, int stop_bits)
{
    struct termios opt;
    int speed;
    if (tcgetattr(fd, &opt) != 0)
    {
        perror("tcgetattr");
        return -1;
    }
 
    /*设置波特率*/
    switch (baud_rate)
    {
    case 2400:  speed = B2400;  break;
    case 4800:  speed = B4800;  break;
    case 9600:  speed = B9600;  break;
    case 19200: speed = B19200; break;
    case 38400: speed = B38400; break;
    default:    speed = B115200; break;
    }
    cfsetispeed(&opt, speed);
    cfsetospeed(&opt, speed);
    tcsetattr(fd, TCSANOW, &opt);
 
    opt.c_cflag &= ~CSIZE;
 
    /*设置数据位*/
    switch (data_bits)
    {
    case 7: {opt.c_cflag |= CS7; }break;//7个数据位  
    default: {opt.c_cflag |= CS8; }break;//8个数据位 
    }
 
    /*设置奇偶校验位*/
    switch (parity) //N
    {
    case 'n':case 'N':
    {
        opt.c_cflag &= ~PARENB;//校验位使能     
        opt.c_iflag &= ~INPCK; //奇偶校验使能  
    }break;
    case 'o':case 'O':
    {
        opt.c_cflag |= (PARODD | PARENB);//PARODD使用奇校验而不使用偶校验 
        opt.c_iflag |= INPCK;
    }break;
    case 'e':case 'E':
    {
        opt.c_cflag |= PARENB;
        opt.c_cflag &= ~PARODD;
        opt.c_iflag |= INPCK;
    }break;
    case 's':case 'S': /*as no parity*/
    {
        opt.c_cflag &= ~PARENB;
        opt.c_cflag &= ~CSTOPB;
    }break;
    default:
    {
        opt.c_cflag &= ~PARENB;//校验位使能     
        opt.c_iflag &= ~INPCK; //奇偶校验使能          	
    }break;
    }
 
    /*设置停止位*/
    switch (stop_bits)
    {
    case 1: {opt.c_cflag &= ~CSTOPB; } break;
    case 2: {opt.c_cflag |= CSTOPB; }   break;
    default: {opt.c_cflag &= ~CSTOPB; } break;
    }
 
    /*处理未接收字符*/
    tcflush(fd, TCIFLUSH);
 
    /*设置等待时间和最小接收字符*/
    opt.c_cc[VTIME] = 1000;
    opt.c_cc[VMIN] = 0;
 
    /*关闭串口回显*/
    opt.c_lflag &= ~(ICANON | ECHO | ECHOE | ECHOK | ECHONL | NOFLSH);
 
    /*禁止将输入中的回车翻译为新行 (除非设置了 IGNCR)*/
    opt.c_iflag &= ~ICRNL;
    /*禁止将所有接收的字符裁减为7比特*/
    opt.c_iflag &= ~ISTRIP;
 
    /*激活新配置*/
    if ((tcsetattr(fd, TCSANOW, &opt)) != 0)
    {
        perror("tcsetattr");
        return -1;
    }
 
    return 0;
}




// 使用实例
int main()
{
    // begin::第一步，串口初始化
    int UART_fd = open_port(0);
	if (set_uart_config(UART_fd,115200, 8, 'E', 1) < 0)
	{
		perror("set_com_config");
		exit(1);
	}
    // end::串口初始化
    
    // begin::第二步，读下位机上发的一行数据
    char str[128]="123abc";
	char buff[1];
	int len = 6;
	// while (1)
	// {
	// 	if (read(UART_fd, buff, 1)) {
	// 		if (buff[0] == '\n') {
	// 			break;
	// 		}else {
	// 			str[len++] = buff[0];
	// 		}
	// 	}
	// }
    while(1)
    {printf("content:%s\n",str);
    // end::读下位机上发的一行数据
 
    // begin::第三步，向下位机发送数据
    write(UART_fd, str, len);
    // end::向下位机发送数据
    cv::waitKey(5);
    }
    return 0;
}

```

图像处理方法：不限于书上所学的方法，根据图像信息转化控制端所需的信息控制小车的方向进行巡线（方法不限于找中线，中点，角度等等）依照自身所需方法和具体实现流程与控制组队友共同决定和协商。

调车：因为香橙派板子资源有限，团队可以去场地用协会板子进行调试，当然可以自己买个板子进行学习，后续也会用到。

远程调试可以查看以下文章：[vscode连接SSH远程服务器（详细版）-CSDN博客](https://blog.csdn.net/qq_41646032/article/details/122304257)

打分细则：参与提交并成功运行代码：10分，巡线按照路程：0-40分，路程全部跑完，按照排名赋分：0-50分

## 任务二：答题卡评分

场地：线上提交代码（需要有必要的注释）、实现思路和流程，运行效果和视频

时间：4月14日晚10点之前交

要求：实现答题卡评分，正确答案（1:B，2：C，3：A，4：C，5：E），若答案与正确答案相同，那么用绿色将正确答案画出，显示答对，若错误将错误在已选答案用红色画出，并在正确答案出将正确答案用绿色画笔写出，并最后将正确率显示在GRADE栏处。

示例：

![](https://cdn.jsdelivr.net/gh/1944195627wb/images@main/images/202403171530115.png)

以下是测试图片：

![](https://cdn.jsdelivr.net/gh/1944195627wb/images@main/images/202403171538658.png)

打分细则：提交代码并成功运行:20分，能圈出正确和错误答案：40分，能将正确率在GRADE栏显示出来：40分。

# 总排名

平时分由各个组长按照平时表现按0-100进行赋分

加权分：任务1占40%，任务2占30%，平时分占30%

总排名：按照加权分按高至低依次排名

按照总排名筛选人选进入下一阶段

# 奖励

第一：奖励一块视觉嵌入式板子m2dock，可以进行学习和完成一些小项目，做比赛优先推荐

第二：奖励一块m1dock，做比赛优先推荐

第三：做比赛优先推荐





