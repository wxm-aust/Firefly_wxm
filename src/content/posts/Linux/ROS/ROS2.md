---
title: ROS2话题
published: 2024-05-14
pinned: false
tags: [Linux,ROS]
category: 学习项目
draft: false
---

|                           |        |          |
| ------------------------- | ------ | -------- |
| ros2 node list            | 查看节点列表 |          |
| ros2 node info /node_name | 查看节点信息 | 订阅、发布了什么 |
|                           |        |          |


|                                                    |          |                 |
| -------------------------------------------------- | -------- | --------------- |
| ros2 ropic echo /topic_name                        | 打印话题内容   |                 |
| ros2 topic info /topic_name                        | 打印话题信息   | 消息接口类型，发布者订阅者数量 |
| ros2 interface show struct_name                    | 查看消息接口规则 |                 |
| ros2 topic pub /topic_name struct_name "{context}" | 发布临时话题   |                 |


|                                                    |          |              |
| ------------------------------------------------- | -------- | ------------ |
| ros2 setvice list                                  | 打印服务列表   | -t表示显示服务接口类型 |
| ros2 interface show setvice_n                      | 查看服务接口规则 | 包含请求和返回      ros2 service call /setvice_name 消息接口  "{context}" 息接口  |          |              |


|                                            |        |
| ------------------------------------------ | ------ |
| ros2 param list                            | 打印参数列表 |
| ros2 param describe /node_name param_name  | 打印参数描述 |
| ros2 param get /node_name param_name       | 获取参数值  |
| ros2 param set /node_name param_name value | 设置参数值  |
