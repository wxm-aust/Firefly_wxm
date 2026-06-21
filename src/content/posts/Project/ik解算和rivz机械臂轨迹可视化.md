---
title: ik解算和rivz机械臂轨迹可视化
published: 2026-06-21
pinned: false
tags: [项目]
description: ik解算和rivz机械臂轨迹可视化
category: 作品集
draft: false
image: ./ik2.png
---

[【玩玩臂】ik解算和rviz可视化](https://www.bilibili.com/video/BV1uAjr6gEVF/?share_source=copy_web&vd_source=9f57ef04c5301dddf0856d1e16269ee7)

# 基于 ROS2 + ikpy 的 UR3e 机械臂矩形路径规划与可视化

  

> 本文介绍如何使用 ROS2 和 ikpy 库，让 UR3e 协作机械臂末端在 XY 平面内沿矩形路径运动，并在 RViz 中完成三维可视化。涵盖运动学建模、逆运动学求解、轨迹规划与关节状态控制全流程。

  

---

  

## 1. 项目背景

  

在机器人仿真与离线编程场景中，我们经常需要让机械臂末端执行预定义的轨迹。本文以 UR3e 为例，实现以下目标：

  

- 在 `base_link` 坐标系下定义一个矩形路径

- 通过逆运动学（IK）将笛卡尔坐标转换为关节角度

- 实时发布关节状态驱动机器人模型

- 在 RViz 中可视化路径、轨迹和机械臂状态

  

完整代码位于 `ur3e_rect_motion` 功能包中，机械臂 URDF 模型来自开源项目 [Universal_Robots_ROS2_Description](https://github.com/UniversalRobots/Universal_Robots_ROS2_Description)。

  

---

  

## 2. 整体架构

  

```

┌──────────────────────────────────────────────────┐

│                  rect_motion.launch.py            │

│  ┌──────────────┐  ┌──────────────┐  ┌─────────┐ │

│  │ robot_state  │  │ rect_motion  │  │  RViz2  │ │

│  │ _publisher   │  │    _node     │  │         │ │

│  └──────┬───────┘  └──────┬───────┘  └────┬────┘ │

│         │                 │               │       │

└─────────┼─────────────────┼───────────────┼───────┘

          │                 │               │

          ▼                 ▼               ▼

  /robot_description   /joint_states   可视化显示

  (URDF模型)           /rect_path

                       /rect_path_marker

```

  

启动文件 `rect_motion.launch.py` 同时启动三个节点：

  

| 节点 | 职责 |

|------|------|

| `robot_state_publisher` | 将 URDF 发布为 `/robot_description` 话题 |

| `rect_motion_node` | 路径规划、IK 求解、关节状态发布 |

| `rviz2` | 3D 可视化 |

  

---

  

## 3. 运动学模型构建

  

### 3.1 为什么不用 URDF 自动加载？

  

URDF 文件中包含完整的运动学信息，理论上可以直接解析。但 `ikpy` 的 URDF 加载器对某些 xacro 生成的 URDF 兼容性不够稳定，且手动构建可以让我们对每一帧的位姿有精确控制。

  

### 3.2 用 ikpy 构建 UR3e 运动链

  

UR3e 是典型的 6R 串联机械臂，我们按 DH 参数逐链路构建运动链：

  

```python

from ikpy.chain import Chain

from ikpy.link import URDFLink

  

def create_ur3e_chain():

    """根据UR3e的DH参数手动构建ikpy运动学链。"""

    pi = math.pi

    return Chain(name='ur3e', links=[

        # ① base_link → base_link_inertia（固定关节，绕Z旋转π对齐REP-103）

        URDFLink(

            name="base_link_inertia",

            origin_translation=[0, 0, 0],

            origin_orientation=[0, 0, pi],   # RPY欧拉角

            joint_type='fixed',

        ),

        # ② shoulder_pan_joint（旋转关节，绕Z轴）

        URDFLink(

            name="shoulder_pan_joint",

            origin_translation=[0, 0, 0.15185],

            origin_orientation=[0, 0, 0],

            rotation=[0, 0, 1],               # 旋转轴：Z轴

            joint_type='revolute',

        ),

        # ③ shoulder_lift_joint

        URDFLink(

            name="shoulder_lift_joint",

            origin_translation=[0, 0, 0],

            origin_orientation=[pi/2, 0, 0],   # 绕X轴旋转90°

            rotation=[0, 0, 1],

            joint_type='revolute',

        ),

        # ④ elbow_joint

        URDFLink(

            name="elbow_joint",

            origin_translation=[-0.24355, 0, 0],

            origin_orientation=[0, 0, 0],

            rotation=[0, 0, 1],

            joint_type='revolute',

        ),

        # ⑤ wrist_1_joint

        URDFLink(

            name="wrist_1_joint",

            origin_translation=[-0.2132, 0, 0.13105],

            origin_orientation=[0, 0, 0],

            rotation=[0, 0, 1],

            joint_type='revolute',

        ),

        # ⑥ wrist_2_joint

        URDFLink(

            name="wrist_2_joint",

            origin_translation=[0, -0.08535, 0],

            origin_orientation=[pi/2, 0, 0],

            rotation=[0, 0, 1],

            joint_type='revolute',

        ),

        # ⑦ wrist_3_joint

        URDFLink(

            name="wrist_3_joint",

            origin_translation=[0, 0.0921, 0],

            origin_orientation=[pi/2, pi, pi],

            rotation=[0, 0, 1],

            joint_type='revolute',

        ),

        # ⑧ flange（固定关节，过渡到法兰盘）

        URDFLink(

            name="flange",

            origin_translation=[0, 0, 0],

            origin_orientation=[0, -pi/2, -pi/2],

            joint_type='fixed',

        ),

        # ⑨ tool0（末端执行器坐标系）

        URDFLink(

            name="tool0",

            origin_translation=[0, 0, 0],

            origin_orientation=[pi/2, 0, pi/2],

            joint_type='fixed',

        ),

    ])

```

  

**关键参数说明：**

  

- `origin_translation`：相对于父连杆的平移量（米），取自 UR3e 官方 URDF

- `origin_orientation`：相对位姿的 RPY 欧拉角（弧度）

- `rotation`：旋转轴方向，`[0,0,1]` 表示绕 Z 轴旋转

- 固定关节（`joint_type='fixed'`）用于处理坐标系的变换，不参与 IK 求解

  

### 3.3 目标朝向设定

  

我们的目标是让末端 tool0 朝下（Z 轴与 base_link 的 Z 轴反向），这对应旋转矩阵：

  

```python

target_orientation = np.array([

    [1,  0,  0],

    [0, -1,  0],

    [0,  0, -1],

])

```

  

---

  

## 4. 逆运动学求解

  

### 4.1 IK 求解函数

  

```python

def _ik_solve(self, target_position, target_orientation=None,

              orientation_mode=None, initial_angles=None):

    """求解逆运动学，返回6个活动关节角度。"""

    if initial_angles is None:

        initial_angles = [0] * 6

  

    # 构建完整的ikpy角度向量（含固定关节）

    full_initial = [0] * len(self.chain.links)

    active_idx = [i for i, link in enumerate(self.chain.links)

                  if link.joint_type == 'revolute']

    for i, idx in enumerate(active_idx):

        if i < len(initial_angles):

            full_initial[idx] = initial_angles[i]

  

    # 调用ikpy求解

    ik_result = self.chain.inverse_kinematics(

        target_position=target_position,

        target_orientation=target_orientation,

        orientation_mode=orientation_mode,   # "all" 表示约束完整朝向

        initial_position=full_initial,       # 初始猜测值，影响收敛结果

    )

  

    # 提取活动关节角度

    active_angles = [ik_result[i] for i in active_idx]

    return active_angles, ik_result

```

  

### 4.2 核心技巧：使用上一帧作为初始猜测

  

IK 求解是一个迭代优化过程，`initial_position` 对收敛速度和结果质量影响很大。我们总是将上一帧的 IK 结果作为下一帧的初始猜测：

  

```python

# 对每个笛卡尔点求解IK

rect_joint_traj = []

prev_active = center_ik_active  # 从中心点IK结果开始

  

for point in cartesian_points:

    ik_active, ik_full = self._ik_solve(

        target_position=np.array(point),

        target_orientation=target_orientation,

        orientation_mode='all',

        initial_angles=prev_active,   # ← 关键：使用上一帧结果

    )

    rect_joint_traj.append(ik_active)

    prev_active = ik_active

```

  

这样做的好处是：

- 大幅提高 IK 求解速度

- 保证相邻帧之间关节角度连续，避免跳变

  

### 4.3 可达性验证

  

在规划前先对矩形中心点做 IK 求解，并验证精度：

  

```python

# 正向运动学验证

fk_center = self.chain.forward_kinematics(center_ik_full)

pos_err = np.linalg.norm(fk_center[:3, 3] - np.array([cx, cy, cz]))

ori_err = np.linalg.norm(fk_center[:3, :3] - target_orientation)

  

if pos_err > 0.01:

    self.get_logger().warn(f'中心点IK误差较大({pos_err:.4f}m)，目标可能不可达')

```

  

---

  

## 5. 轨迹规划

  

### 5.1 四阶段轨迹

  

```

阶段1: 初始保持 ──→ 阶段2: 过渡 ──→ 阶段3: 矩形运动 ──→ 阶段4: 保持末位姿

 (1.0s)          (2.0s)         (可变)              (∞)

```

  

**阶段1 — 初始保持**：在默认关节角 `[0, -π/2, 0, -π/2, 0, 0]` 停留，给系统初始化时间。

  

**阶段2 — 过渡**：在关节空间做线性插值，从初始位姿平滑过渡到矩形起点。

  

```python

def lerp_joint_angles(start, end, num_points):

    """在关节空间做线性插值。"""

    trajectory = []

    for i in range(num_points):

        t = i / num_points

        angles = [s + t * (e - s) for s, e in zip(start, end)]

        trajectory.append(angles)

    return trajectory

```

  

**阶段3 — 矩形运动**：在笛卡尔空间生成矩形路径点，逐点求解 IK。

  

**阶段4 — 完成后保持**：运动结束后保持末位姿，持续发布关节状态。

  

### 5.2 矩形路径生成

  

```python

# 矩形四个角点

hw, hh = w / 2, h / 2

corners = [

    [cx - hw, cy - hh, cz],  # 左下

    [cx + hw, cy - hh, cz],  # 右下

    [cx + hw, cy + hh, cz],  # 右上

    [cx - hw, cy + hh, cz],  # 左上

]

  

# 在每条边上按速度插值

cartesian_points = []

for _ in range(num_loops):

    for i in range(4):

        start = np.array(corners[i])

        end = np.array(corners[(i + 1) % 4])

        edge_len = np.linalg.norm(end - start)

        n_pts = max(int(edge_len / speed * rate), 2)

        for j in range(n_pts):

            t = j / n_pts

            cartesian_points.append((start + t * (end - start)).tolist())

```

  

---

  

## 6. 关节状态发布与控制

  

### 6.1 基础关节状态发布

  

```python

def _publish_joint_state(self, angles):

    js = JointState()

    js.header.stamp = self.get_clock().now().to_msg()

    js.name = [

        'shoulder_pan_joint', 'shoulder_lift_joint', 'elbow_joint',

        'wrist_1_joint', 'wrist_2_joint', 'wrist_3_joint',

    ]

    js.position = [float(a) for a in angles]

    self.joint_state_pub.publish(js)

```

  

### 6.2 wrist_3 叠加恒定角速度

  

为了在矩形运动的同时让末端执行器持续旋转（模拟打磨/抛光等场景），我们对 `wrist_3` 关节额外叠加一个随时间线性增长的偏移量：

  

```python

omega = self.get_parameter('wrist3_angular_velocity').value  # 30 rad/s

elapsed = (self.get_clock().now() - self.start_time).nanoseconds / 1e9

wrist3_offset = omega * elapsed

modified = list(angles)

modified[5] += wrist3_offset  # wrist_3_joint 是第6个关节 (index=5)

```

  

这样即使末端在矩形路径上运动，腕部关节也在持续旋转。

  

### 6.3 定时器驱动

  

使用 ROS2 的 Timer 以固定频率（默认 50Hz）发布关节状态：

  

```python

rate = self.get_parameter('publish_rate').value

self.timer = self.create_timer(1.0 / rate, self._timer_callback)

```

  

每个定时器周期取一个轨迹点并发布。运动完成后，保持末位姿继续发布。

  

---

  

## 7. RViz 可视化

  

### 7.1 长方体 Marker

  

用半透明绿色长方体标注矩形路径位置：

  

```python

marker = Marker()

marker.header.frame_id = 'base_link'

marker.type = Marker.CUBE

marker.pose.position.x = cx

marker.pose.position.y = cy

marker.pose.position.z = cz

marker.scale.x = rect_width    # X方向宽度

marker.scale.y = rect_height   # Y方向高度

marker.scale.z = rect_thickness  # Z方向厚度

marker.color.r = 0.0

marker.color.g = 0.8

marker.color.b = 0.2

marker.color.a = 0.4   # 半透明

```

  

### 7.2 末端轨迹 Path

  

实时记录末端走过的路径，以蓝色线条在 RViz 中显示：

  

```python

self.path_msg = Path()

self.path_msg.header.frame_id = 'base_link'

  

# 在运动阶段，每个周期追加一个位姿点

pose = PoseStamped()

pose.header.frame_id = 'base_link'

pose.pose.position.x = target[0]

pose.pose.position.y = target[1]

pose.pose.position.z = target[2]

self.path_msg.poses.append(pose)

self.path_pub.publish(self.path_msg)

```

  

### 7.3 RViz 配置

  

`rect_motion.rviz` 配置了以下显示项：

  

| 显示项 | 用途 |

|--------|------|

| Grid | XY 平面参考网格 |

| RobotModel | 显示 UR3e 机械臂 3D 模型 |

| Marker (`/rect_path_marker`) | 半透明矩形区域 |

| Path (`/rect_path`) | 末端运动轨迹 |

  

---

  

## 8. 运行方法

  

```bash

# 构建

cd ~/ros2_ws

colcon build --packages-select ur3e_rect_motion

source install/setup.bash

  

# 默认参数启动

ros2 launch ur3e_rect_motion rect_motion.launch.py

  

# 自定义参数

ros2 launch ur3e_rect_motion rect_motion.launch.py \

    center_x:=0.40 center_z:=0.25 \

    rect_width:=0.20 rect_height:=0.15 \

    speed:=0.08 num_loops:=5

```

  

启动后 RViz 会自动打开，可以看到：

- 绿色半透明长方体表示矩形路径区域

- 机械臂末端沿矩形边缘运动

- 蓝色轨迹线实时绘制末端走过的路径

- wrist_3 关节持续旋转

  

---

  

## 9. 总结

  

本文实现了一个完整的 ROS2 机械臂路径规划示例，核心要点：

  

1. **运动学建模**：手动构建 UR3e 的 ikpy 运动链，精确控制每个关节的位姿变换

2. **逆运动学求解**：使用 `orientation_mode='all'` 约束完整朝向，利用上一帧结果加速收敛

3. **轨迹规划**：四阶段设计（初始保持→过渡→运动→保持），笛卡尔空间路径 + 关节空间插值

4. **关节控制**：通过 `/joint_states` 话题驱动 `robot_state_publisher` 更新模型显示

5. **RViz 可视化**：Marker + Path 双重可视化，直观展示路径与轨迹

  

该框架可扩展至任意自定义路径（圆弧、样条曲线等），只需修改笛卡尔路径生成部分即可。