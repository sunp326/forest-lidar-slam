&lt;p align="center"&gt;
  &lt;h1 align="center"&gt;Forest LiDAR-Inertial SLAM with Trunk Skeleton Constraints&lt;/h1&gt;
  &lt;h3 align="center"&gt;融合树干骨架线几何约束的林区激光惯导SLAM方法&lt;/h3&gt;
  &lt;p align="center"&gt;
    &lt;a href="LICENSE"&gt;&lt;img src="https://img.shields.io/badge/License-MIT-yellow.svg" alt="License: MIT"&gt;&lt;/a&gt;
    &lt;img src="https://img.shields.io/badge/ROS-Noetic-blue.svg" alt="ROS Noetic"&gt;
    &lt;img src="https://img.shields.io/badge/C%2B%2B-17-blue.svg" alt="C++17"&gt;
  &lt;/p&gt;
&lt;/p&gt;

---

&gt; 🚧 **Code will be released upon paper acceptance.**  
&gt; 代码将在论文录用后开源，当前页面提供数据集下载与实验结果预览。

---

## 📖 Abstract

针对林业环境中全球导航卫星系统（GNSS）信号拒止、通用激光惯导同步定位与建图（SLAM）方法易受地形起伏、特征退化和累积漂移影响等问题，本文构建一种面向森林环境的激光雷达惯导SLAM方法。该方法基于迭代误差状态卡尔曼滤波（IESKF）实现前端位姿估计；通过分层切片与圆拟合提取树干中心骨架线并增量构建全局树干地图，在后端因子图中联合树干骨架线匹配约束与里程计因子，以抑制长期累积误差。

**关键词**：激光惯导SLAM；树干骨架线；迭代误差状态卡尔曼滤波；因子图优化；森林环境

---

## 🔧 Method Overview

### 系统框架

本系统主要由四个模块组成：点云预处理模块、激光雷达–惯导里程计模块、全局树干地图优化模块、全局因子图优化与地图更新模块。

&lt;p align="center"&gt;
  &lt;img src="assets/fig1_system_flowchart.png" alt="系统流程图" width="90%"&gt;
&lt;/p&gt;
&lt;p align="center"&gt;&lt;b&gt;图1 系统流程图&lt;/b&gt;&lt;/p&gt;

### 树干提取与骨架线生成

利用法向量滤波、统计离群点去除与欧几里得聚类分割树干点云；通过RANSAC圆柱拟合验证树干结构；沿Z轴以0.1m为层厚进行分层切片，采用最小二乘圆拟合提取截面中心，连接形成三维树干骨架线。

&lt;p align="center"&gt;
  &lt;img src="assets/fig2_trunk_extraction.png" alt="树干提取" width="80%"&gt;
&lt;/p&gt;
&lt;p align="center"&gt;&lt;b&gt;图2 树干提取结果&lt;/b&gt;&lt;/p&gt;

&lt;p align="center"&gt;
  &lt;img src="assets/fig3_skeleton_lines.png" alt="树干骨架线" width="80%"&gt;
&lt;/p&gt;
&lt;p align="center"&gt;&lt;b&gt;图3 树干骨架线&lt;/b&gt;&lt;/p&gt;

### 后端因子图优化

在后端采用GTSAM因子图优化，联合前端里程计因子与树干骨架线约束因子（点到线距离残差），并引入Huber鲁棒核抑制异常匹配，实现全局一致的位姿估计。

---

## 📊 Dataset

### 自采数据集：喜鹊梁林场

| 项目 | 说明 |
|:---|:---|
| **采集地点** | 河北省张家口市喜鹊梁林场 |
| **海拔** | 约 2000~2157 m |
| **林分类型** | 天然次生林与人工营造林，针阔混交林 |
| **主要树种** | 白桦、松树 |
| **树木间距** | 约 2.5~5.0 m |
| **平均胸径** | 约 0.3 m |
| **郁闭度** | 0.7~0.8 |
| **采集设备** | Livox MID-360 激光雷达 + IMU（手持式三维移动测量系统） |
| **采集时长** | 约 30 min（沿同一路径连续行走两圈） |
| **数据格式** | ROS bag（/livox/lidar, /livox/imu） |
| **点云频率** | 10 Hz |
| **IMU频率** | 200 Hz |

- **百度网盘下载**：[https://pan.baidu.com/s/13X7PlluKVNIDeyO99Fneyw](https://pan.baidu.com/s/13X7PlluKVNIDeyO99Fneyw)（提取码：`1111`）

&gt; ⚠️ 本数据集仅供学术研究使用，使用请注明出处。

### 公开数据集

- [BotanicGarden Dataset](https://github.com/robotic-vision-lab/BotanicGarden) — 包含密林、河岸、狭窄小径、桥梁和草地等多种自然场景，提供高精度轨迹真值。

---

## 📈 Experimental Results

### BotanicGarden 公开数据集

在3条序列上的定量对比结果：

| Algorithm | APE (m) | RPE (%) |
|:---|:---:|:---:|
| **Ours** | **2.849** (均值) | **3.721** (均值) |
| FAST-LIO-SAM | 8.190 | 4.496 |
| FAST-LIO2 | 9.193 | 4.342 |
| LIO-SAM | 11.386 | 11.386 |

本文方法平均绝对位姿误差（APE）较 FAST-LIO2 降低 **69.0%**，较 LIO-SAM 降低 **74.6%**；平均相对位姿误差（RPE）较 FAST-LIO2 降低 **14.3%**，较 LIO-SAM 降低 **67.3%**。

&lt;p align="center"&gt;
  &lt;img src="assets/fig5_botanic_trajectory.png" alt="BotanicGarden轨迹对比" width="100%"&gt;
&lt;/p&gt;
&lt;p align="center"&gt;&lt;b&gt;图5 BotanicGarden数据集轨迹对比&lt;/b&gt;&lt;/p&gt;

### 自采林场数据集

| Algorithm | 终点三维闭合误差 (m) | 树干位置偏差 (m) | 重复区域平均配准RMSE (m) | 闭环轨迹偏差 (m) |
|:---|:---:|:---:|:---:|:---:|
| **Ours** | **2.28** | **0.13** | **0.69** | **1.85** |
| FAST-LIO-SAM | 7.12 | 6.64 | 5.35 | 5.68 |
| FAST-LIO2 | 11.84 | 9.11 | 8.74 | 9.77 |
| LIO-SAM | 21.73 | 26.14 | 23.10 | 25.43 |

本文方法的闭环轨迹偏差和重复区域平均配准RMSE 较 FAST-LIO2 分别降低 **81.1%** 和 **92.1%**，较 LIO-SAM 分别降低 **92.7%** 和 **97.0%**。

&lt;p align="center"&gt;
  &lt;img src="assets/fig6_selfcollected_trajectory.png" alt="自采林场轨迹对比" width="100%"&gt;
&lt;/p&gt;
&lt;p align="center"&gt;&lt;b&gt;图6 自采林场数据轨迹对比&lt;/b&gt;&lt;/p&gt;

### 重复区域点云一致性

&lt;p align="center"&gt;
  &lt;img src="assets/fig7_pointcloud_comparison.png" alt="点云局部细节对比" width="100%"&gt;
&lt;/p&gt;
&lt;p align="center"&gt;&lt;b&gt;图7 自采林场数据局部细节对比（蓝色：第一圈，橙色：第二圈）&lt;/b&gt;&lt;/p&gt;

---

## 🛠️ Environment & Dependencies

- Ubuntu 20.04
- ROS Noetic
- PCL (Point Cloud Library)
- Eigen3
- GTSAM 4.0+
- Livox ROS Driver

&gt; 详细安装与编译指南将在代码开源后补充。

---

## 📖 Citation

如果你使用了本代码或数据集，请引用：

```bibtex
@article{sun2026forest,
  title={融合树干骨架线几何约束的林区激光惯导SLAM方法},
  author={孙澎 and 钟若飞 and 陈蜜 and 孙振兴 and 史梦冉 and 李玉笙 and 王孝男},
  journal={},
  year={2026},
  publisher={},
  note={待发表}
}
