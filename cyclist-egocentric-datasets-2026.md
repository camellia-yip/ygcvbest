# 2025–2026 骑行与电动两轮车第一视角数据集调研

更新日期：2026-09-21

## 1. 调研目的

本文整理近期公开的自行车、摩托车与电动两轮车第一视角数据，重点回答以下问题：

1. 数据是否真的是骑行者/两轮车的第一视角，而不是汽车摄像头拍摄骑车人；
2. 是否包含连续视频，以及视频、IMU、GPS、LiDAR、眼动等模态；
3. 是否适合驾驶行为理解、轨迹预测、BEV 感知或视频世界模型；
4. 截至 2026 年 9 月，数据是否真的可以获取，而不只是论文中声称“将会公开”。

这里将“电动两轮车”进一步区分为电动自行车、踏板式电动车和摩托车。不同数据集对 *bike*、*e-bike*、*scooter* 和 *motorized two-wheeler* 的使用并不统一，因此不能仅凭数据集名称判断车辆类型。

## 2. 结论摘要

截至 2026 年 9 月，真正同时满足“骑行者第一视角、连续视频、多模态传感器、行为或轨迹标注、公开下载”的数据集仍然很少。当前最值得优先考虑的是：

- **MOTOR**：目前最完整、最容易直接使用的两轮车驾驶行为视频数据集；
- **CVUTD**：最适合研究自行车第一视角到 BEV、跨视角跟踪和公制位置恢复；
- **Oxford RobotCycle**：传感器最完整，适合风险、注意力、定位和多模态交通研究，但需要学术身份申请；
- **IndiGo**：与电动踏板车第一视角最吻合，但完整数据尚未形成稳定的公开下载；
- **PanoCycle360**：目前最有代表性的中国道路骑行视角数据，但公开包是视频抽帧图像，而不是连续视频；
- **Cycling World Model Dataset**：体量小、下载方便，适合快速验证视频预测或生成式世界模型，但不适合作为严肃的轨迹预测基准。

目前仍没有一套骑行第一视角数据能直接达到 Argoverse 2 Motion Forecasting 或 Waymo Motion Dataset 的完整程度，即同时提供连续视频、地图、ego pose、全体交通参与者的稳定 ID、公制历史/未来轨迹和统一评测协议。

## 3. 核心数据集对比

| 数据集 | 年份 | Ego 平台与视角 | 规模与模态 | 主要标注/任务 | 当前公开状态 | 对本研究的价值 |
|---|---:|---|---|---|---|---|
| [MOTOR](https://varuniiith.github.io/MOTOR-Dataset/) | 2026 | 摩托车/踏板车骑手；前、后、头盔、眼动第一视角 | 16 名骑手，1,629 个行为片段，25+ 小时；视频、眼动、音频、GPS、IMU、速度和倾角 | 直行、变道、超车、转弯、掉头、穿行、避障、分心、停车、违规、近碰及合法性 | **可直接下载**；[Hugging Face](https://huggingface.co/datasets/varunpaturkar/MOTOR)，约 106 GB，CC BY-NC 4.0 | 驾驶行为识别、意图预测、风险事件识别；目前最成熟的行为数据候选 |
| [Cross-View Urban Traffic Dataset（CVUTD）](https://arxiv.org/abs/2606.07708) | 2026 | 自行车车把 GoPro 第一视角 + 同步无人机俯视 | 8 个城市路口；4K/30fps；432,000 个 street-view 帧、144,000 个 drone-view 帧 | 两视角目标轨迹、跨视角 ID 对应、无人机公制坐标、ego-to-BEV | **可直接下载**；[Hugging Face](https://huggingface.co/datasets/prakharbh/CrossViewUrbanTrafficDataset)，约 52 GB，CC BY-NC-ND 4.0 | 第一视角到 BEV、轨迹恢复、跨视角跟踪；与 BEV/trajectory 研究最贴近 |
| [Cycling World Model Dataset](https://huggingface.co/datasets/georgiyozhegov/cycling) | 2026 | 自行车车把第一视角 | 2 小时 26 分；1,099 个 8 秒片段；8fps、192×108；约 120 MB | 无精细行为、地图或轨迹标注；目标是未来帧预测 | **可直接下载**；MIT | 小成本视频预测、Diffusion/JEPA/world model 原型；不适合最终基准 |
| [IndiGo](https://link.springer.com/article/10.1007/s44430-025-00013-1) | 2025 | 电动踏板车；头盔相机/IMU，车体前后双目及侧向 ToF | 头盔 8MP RGB+IMU、前后 ZED 2i、左右 ToF、GNSS-RTK、车体 IMU、点云 | 加减速、转向、变道及安全/风险行为；ROS2/SVO2 多模态记录 | **部分公开**；论文提供代表性 preview，完整数据未形成稳定公开入口 | 与“电动两轮车第一视角”最吻合，适合行为、感知和车辆动力学，但获取风险较高 |
| [PanoCycle360](https://www.nature.com/articles/s41597-026-07128-z) | 2026 | 上海道路，头盔 Insta360 X4，360°骑行者视角 | 原始采集为 4K/60fps 视频；公开 10,055 张 3840×1920 图像、102,171 个框 | Person、Car、Cyclist、E-Bike Rider、Cargo Tricycle 等 9 类目标检测 | **公开的是抽帧图像**；[Zenodo/代码入口](https://github.com/Feng-LChen/PanoCycle360)，CC BY 4.0 | 中国道路域、360°盲区和电动车目标感知；不宜直接用于时序预测 |
| [Oxford RobotCycle](https://ori-mrg.github.io/robotcycle-dataset/) | 2025 | 骑行者背包式多传感器 + Project Aria 眼镜 | 70+ km；Aria/眼动、双目和单目相机、2 个 LiDAR、INS | 位姿、风险、轨迹跟踪、交通流、注意力、压力分析 | **申请访问**；[学术邮箱申请](https://ori-mrg.github.io/robotcycle-dataset/download/)，CC BY-NC-SA 4.0 | 多模态最丰富，适合重建轨迹、风险场景和人的注意行为 |

## 4. 数据集详细说明

### 4.1 MOTOR：驾驶行为建模的首选

MOTOR 是 ICRA 2026 的 motorized two-wheeler 数据集，采集于高密度、弱车道约束的印度交通环境。每个片段最多包含四路同步视频：车辆前视、后视、骑手头盔视角和眼动仪视角，并配有 GPS、陀螺仪、加速度和速度等遥测信号。

它与普通自动驾驶数据集最大的区别，是标注对象不是“画面中的其他骑车人”，而是 **ego 骑手自己正在执行的行为**。这使它适合以下任务：

- 两轮车动作与驾驶意图识别；
- 变道、超车、转弯、避障和穿行预测；
- 近碰、违规和安全风险分类；
- 视频、眼动和车辆动力学的多模态融合；
- 基于历史观测预测下一阶段 rider maneuver。

需要注意：MOTOR 对应的是摩托车或踏板式两轮车，不应在论文中直接称为“自行车数据集”。当前公开的是已切分、带行为标签的片段；项目方说明完整的未切分长视频仍会后续发布。

### 4.2 CVUTD：第一视角到 BEV 与轨迹恢复的首选

CVUTD 在德国 Regensburg 的 8 个真实路口同步拍摄车把第一视角和无人机俯视视频。无人机视角不仅作为辅助画面，还提供交通参与者的公制坐标和跨视角身份对应。

它支持两个明确任务：

1. **Cross-view identity matching**：判断街景视频里的目标对应无人机视角中的哪个目标；
2. **Ego-to-BEV prediction**：从自行车第一视角估计周围交通参与者在 BEV 中的公制位置或占用分布。

对于轨迹预测研究，CVUTD 的意义在于可以用无人机轨迹作为第一视角视觉的监督信号。它仍不是一个现成的 motion forecasting benchmark：如果要训练 MTR、Wayformer 或 trajectory JEPA，需要继续构造历史/未来窗口、地图元素和统一的数据划分。

### 4.3 Cycling World Model Dataset：快速生成式实验

该数据集于 2026 年 7 月在 Hugging Face 发布，包含 1,099 个车把第一视角短片。它体量小、MIT 许可、下载和解码成本低，适合：

- 未来帧预测；
- Video JEPA 或 masked latent prediction 的最小验证；
- Diffusion、自回归视频模型或 video tokenizer 调试；
- 消费级 GPU 上的数据管线验证。

它的局限同样明显：分辨率只有 192×108、帧率 8fps、路线重复、没有相机标定、地图、目标 ID 或行为标签。因此可以作为 smoke test，不能单独承担论文的主要实验结论。

### 4.4 IndiGo：电动踏板车数据，但公开程度不足

IndiGo 使用电动踏板车采集印度非结构化交通。其传感器配置包括：

- 骑手头盔：8MP RGB 相机和 IMU；
- 车辆前后：ZED 2i 双目相机；
- 车辆侧后方：两套 ToF 相机；
- GNSS-RTK、车把和车尾 IMU；
- RGB、深度、点云、速度和行为标签。

它是目前最接近“电动车端自动驾驶/ADAS 数据”的候选，但需要谨慎引用：论文不同章节对采集时长、里程和 session 数量的统计并不完全一致，而且当前可核验的公开入口主要是代表性 preview。正式实验前必须以实际取得的数据清单、许可协议和传感器时间同步质量为准。

### 4.5 PanoCycle360：中国道路域的感知数据

PanoCycle360 主要在上海采集，原始视频来自安装在骑行者头盔顶部的 360°相机。场景包括城市道路、郊区、骑行绿道、桥梁、道路立交、园区、轮渡和铁路周边，并显式标注 E-Bike Rider 与 Cargo Tricycle。

但 Zenodo 当前公开的三个压缩包分别对应 YOLO、COCO 和 VOC 格式，核心内容都是抽帧图像及检测标注，并非原始连续视频。因而它适合目标检测、领域自适应和 360°感知，而不适合直接训练动作预测、轨迹预测或视频世界模型。

### 4.6 Oxford RobotCycle：高质量多模态研究数据

RobotCycle 的优势不是行为标签数量，而是传感器和研究问题完整：视觉、LiDAR、INS、眼动、路网、风险与基础设施信息可以被联合分析。它特别适合：

- 骑行者定位与 ego-motion；
- 基础设施、交通交互和主观/客观风险之间的关系；
- 注意力与压力建模；
- 交通流、周围目标轨迹及安全事件分析；
- 多模态自监督表征学习。

限制是下载需要高校或科研机构身份，且 CC BY-NC-SA 许可限制商业用途。

## 5. 接近但不完全满足要求的数据集

### BikeActions（2026）

[BikeActions](https://arxiv.org/abs/2601.10521) 使用配备一台 RGB、两个 LiDAR 和 RTK-GNSS 的 FUSE-Bike 采集，共约 1.3 小时、46,180 个同步帧，并整理出 852 个动作样本。任务聚焦行人和骑车人的 Walking、Standing、Riding、Turning-L、Turning-R 等动作。

当前[公开仓库](https://github.com/Intelligent-Vehicles-Lab-HM/bikeActions)主要提供处理后的 3D skeleton JSON、数据划分、训练代码和权重，而不是完整的原始 RGB/LiDAR 连续视频。因此它更适合作为动作识别或 intention baseline，而不能直接当作完整骑行第一视角视频集。

### BikeScenes（2025/2026）

[BikeScenes](https://github.com/tudelft-iv-students/bikescenes-lidarseg) 从 TU Delft 的 SenseBike 采集 3,021 个连续 LiDAR 扫描，并提供对应图像、位姿、标定和 29 类语义标签。数据可直接下载，适合自行车端 LiDAR 分割和轻量感知研究，但体量较小，公开形式也不是连续视频文件。

### AuRa（2025，期刊卷期为 2026）

[AuRa](https://github.com/ovgu-mtk/aura_dataset) 从自动化货运自行车视角采集城市、园区和行人区域，提供目标框和语义分割标签。公开仓库只有样例，约 5.9 GB 的完整数据需要联系作者申请。它更适合检测和分割，不适合行为或轨迹预测。

### Street-to-Simulator（2026）

[Street-to-Simulator](https://www.frontiersin.org/journals/computer-science/articles/10.3389/fcomp.2026.1853976/full)包含 10 名参与者在维也纳相同 1.4 km 路线上完成真实骑行和 VR 骑行的数据。每位参与者佩戴 6 个 120Hz IMU，并记录 30fps 头戴第一视角视频。它适合研究踏频、平衡、头部运动和 VR/真实行为差异，但不是交通场景轨迹预测基准。

### AllTheDocks（2024，作为较早基线）

[AllTheDocks](https://arxiv.org/abs/2404.10528)包含伦敦骑行者采集的视频、GPS、加速度计、陀螺仪、路面粗糙度和主观安全感标签。它比 2026 年的新数据更早，但仍是研究骑行舒适度、道路质量和风险感知的重要参照。

## 6. 国内公开情况

在当前能够核验的公开入口中，尚未发现哈啰、美团单车、青桔或九号等国内头部两轮出行企业在 2026 年发布可直接下载的“骑行者第一视角连续交通视频基准”。

国内商业数据服务商确实在展示大规模第一视角采集能力，但多数属于：

- 室内操作或具身智能数据，而非道路骑行；
- 少量样片或数据能力展示，而非可复现 benchmark；
- 按客户需求付费采集和交付；
- 共享单车 OD/GPS 轨迹，而非同步第一视角视频。

因此，目前中国道路域中最可靠的公开候选仍是 PanoCycle360，但它是学术团队数据，且公开版本仅包含抽帧图像。

## 7. 按研究任务选择数据集

| 研究目标 | 首选 | 次选 | 说明 |
|---|---|---|---|
| 骑手动作/意图预测 | MOTOR | BikeActions | MOTOR 有真实 ego 行为与多模态信号；BikeActions 更偏周围 VRU 动作 |
| 电动两轮车感知与行为 | IndiGo | MOTOR | IndiGo 的 ego 平台明确为电动踏板车，但当前获取不稳定 |
| 第一视角到 BEV | CVUTD | RobotCycle | CVUTD 已有同步无人机世界坐标；RobotCycle 需要自行建立监督任务 |
| 周围交通参与者轨迹恢复 | CVUTD | RobotCycle | 两者都有形成公制轨迹的条件，但仍需重新整理预测窗口 |
| 视频 JEPA/世界模型预训练 | MOTOR、Cycling World Model Dataset | RobotCycle | 前两者已有连续视频；RobotCycle 需要申请且预处理复杂 |
| 中国道路目标感知 | PanoCycle360 | 自采数据 | PanoCycle360 有中国电动车/三轮车类别，但只有图像 |
| 多模态风险与注意力研究 | RobotCycle | MOTOR | RobotCycle 有眼动和风险分析；MOTOR 有行为、合法性和近碰 |
| LiDAR 自行车端感知 | RobotCycle | BikeScenes | BikeScenes 小但开箱即用，RobotCycle 规模和模态更丰富 |

## 8. 对轨迹预测与 JEPA 研究的具体建议

第一视角视频数据不应直接替代 HetroD、Argoverse 2 或 INTERACTION 这样的公制轨迹数据。更合理的组合方式是：

1. **视觉/视频表征预训练**：在 MOTOR、RobotCycle 或 Cycling World Model Dataset 上训练或适配 Video JEPA；
2. **BEV 几何监督**：用 CVUTD 验证 latent 是否保留了周围目标的公制空间结构；
3. **异质轨迹建模**：继续以 HetroD 一类带公制轨迹和地图的数据作为 motion forecasting 主基准；
4. **跨域或安全验证**：将电动两轮车/自行车第一视角数据用于域外测试、危险事件检索和表示质量诊断；
5. **生成式未来建模**：区分“未来视频生成”和“公制轨迹概率预测”，两者不能只凭视觉效果互相替代。

如果目标是训练 MTR、Wayformer 或 trajectory JEPA，数据至少应具备：相机标定、ego pose、地图、连续目标 ID、公制坐标、历史/未来窗口和场景级 train/val/test 划分。按这一标准，CVUTD 最接近可转换的第一视角数据，MOTOR 更适合意图/行为分类，PanoCycle360 则主要是感知预训练数据。

## 9. 建议的最小实验组合

在不承担过高数据工程成本的情况下，可以采用以下组合：

- **主轨迹数据**：HetroD；
- **视频自监督预训练/行为任务**：MOTOR；
- **第一视角到公制 BEV 验证**：CVUTD；
- **中国道路感知域外测试**：PanoCycle360；
- **快速 world model 调试**：Cycling World Model Dataset。

这个组合能够把“异质交通轨迹预测”和“骑行第一视角视频表征”连接起来，同时避免将只有检测框或只有短视频的数据误当成标准轨迹预测数据。

## 10. 主要资料入口

- [MOTOR 项目页](https://varuniiith.github.io/MOTOR-Dataset/) / [数据下载](https://huggingface.co/datasets/varunpaturkar/MOTOR) / [论文](https://arxiv.org/abs/2605.22550)
- [CVUTD 论文](https://arxiv.org/abs/2606.07708) / [数据下载](https://huggingface.co/datasets/prakharbh/CrossViewUrbanTrafficDataset)
- [Cycling World Model Dataset](https://huggingface.co/datasets/georgiyozhegov/cycling)
- [IndiGo 论文与数据说明](https://link.springer.com/article/10.1007/s44430-025-00013-1)
- [PanoCycle360 论文](https://www.nature.com/articles/s41597-026-07128-z) / [代码与数据入口](https://github.com/Feng-LChen/PanoCycle360)
- [Oxford RobotCycle 项目页](https://ori-mrg.github.io/robotcycle-dataset/) / [数据申请](https://ori-mrg.github.io/robotcycle-dataset/download/)
- [BikeActions 论文](https://arxiv.org/abs/2601.10521) / [公开仓库](https://github.com/Intelligent-Vehicles-Lab-HM/bikeActions)
- [BikeScenes 论文与数据](https://github.com/tudelft-iv-students/bikescenes-lidarseg)
- [AuRa 论文与样例](https://github.com/ovgu-mtk/aura_dataset)
- [Street-to-Simulator 2026](https://www.frontiersin.org/journals/computer-science/articles/10.3389/fcomp.2026.1853976/full)
- [AllTheDocks](https://arxiv.org/abs/2404.10528)

## 11. 使用限制提醒

- CC BY-NC、CC BY-NC-SA 数据通常仅限非商业研究；
- CVUTD 使用 CC BY-NC-ND 4.0，重新分发修改后的数据时需特别检查许可边界；
- RobotCycle 需要机构身份并遵守隐私和删除请求机制；
- 对包含人脸、车牌、眼动和 GPS 的数据，应保留原数据集的匿名化与隐私约束；
- 论文中应明确区分“直接公开”“填写表单后访问”“联系作者”“只公开样例”和“只公开抽帧/派生数据”。
