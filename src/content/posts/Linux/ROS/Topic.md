---
title: ROS话题
published: 2024-05-14
pinned: false
tags: [Linux,ROS]
category: 学习项目
draft: false
---


## 创建
在src目录下`catkin_create_pkg test_pkg roscpp rospy std_msgs`
## 编写
在`CMakeLists.txt`末尾加上
```cpp
add_executable(节点名 src/源代码文件名.cpp)

target_link_libraries(节点名

${catkin_LIBRARIES}

)
```
### demo1:==hello==
```cpp
#include<ros/ros.h>

#include<iostream>

using namespace ros;

int main(int argc, char *argv[])

{

    init(argc,argv,"文件名");

    printf("hello\n");

    while(ok()){

    }

    return 0;

}
```
### demo2:==publish==
```cpp
#include<ros/ros.h>

#include<iostream>

#include<std_msgs/Int16.h>
//可以输入std_msgs/后等待补全 选择合适的数据类型
using namespace ros;

int16_t a=1;

int main(int argc, char *argv[])

{
    init(argc,argv,"test1");
    
    NodeHandle nh;              //定义消息句柄
    Publisher pub = nh.advertise<std_msgs::Int16>("Topic_name",10);    
    //定义消息容器      为发布者类型              数据类型   容器名    容器长度
	//                                   与头文件相匹配          满了fifo
	
    Rate loop_rate(1);         //控制每秒循环1次
    while(ok()){
        std_msgs::Int16 msg;//消息结构体
        a++;
        msg.data = a;//为消息结构体中的值赋值
        pub.publish(msg);//消息放入容器发送

        loop_rate.sleep();     //sleep
    }
    return 0;
}
/*

rostopic  list   查看活跃的消息话题

rostopic echo /Topic_name  查看指定消息话题的内容

rostopic hz /Topic_name    查看指定消息话题的消息频率

*/
```
### demo3:==subscrib==
```cpp
#include<ros/ros.h>

#include<std_msgs/Int16.h>

using namespace ros;

void Callback(std_msgs::Int16 msg){

   // printf("%d\n",msg.data);      //发送消息到屏幕

    ROS_INFO("%d\n",msg.data);      //带白色时间戳发送

   // ROS_WARN("%d\n",msg.data);    //带黄色时间戳发送

}

int main(int argc, char  *argv[])
{
    init(argc,argv,"test2");

//    setlocale(LC_ALL,""); //发送中文时打开（修改显示编码方式）

    NodeHandle nh;//消息句柄

    Subscriber sub = nh.subscribe("Topic_name",10,Callback);//消息容器

    //                容器为订阅类型 订阅话题名 缓冲长度 回调函数
    while (ok())
    {
        spinOnce();//回头看
    }
    return 0;
}
```
## 运行
### 终端
`roscore`
`rosrun 包名 节点名`
### 批量运行
在某包下创建`1.launch`
```html
<launch>

    <node pkg="包名" type="节点名" name="节点名"/>

    <node pkg="atr_pkg" type="ma_node" name="ma_node" output="screen"/>
													指定输出为屏幕
													用于输出警告信息
	...
</launch>
```
执行`roslaunch 包名 1.launch`
