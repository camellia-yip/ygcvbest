# 2026 年 JEPA × 驾驶论文梳理（1–11 月）

> 研究笔记 · 2026-11-24
>
> 范围：2026 年 1 月 1 日至 11 月 23 日提交到 arXiv、**明确使用 JEPA（或 JEPA 式潜空间预测）且以驾驶 / 车辆 / 交通为对象**的论文。不用 JEPA 的驾驶潜空间世界模型（如 DeepSight、Latent-WAM、DreamerAD、PlanWAM 等）不收录。
>
> 检索方式：arXiv API 关键词检索（JEPA / joint-embedding predictive / V-JEPA × driving / vehicle / traffic / NAVSIM / CARLA / nuScenes / LiDAR）加网页搜索补漏。**检索不保证完整**：JEPAhead 就没有出现在 API 结果里，是网页搜索补到的。
>
> 阅读深度：标 📖 的 9 篇读过方法和消融表；其余**只读了摘要和作者栏**。机构取自 arXiv HTML 版或 PDF 首页的作者栏。

## TL;DR

- 全年共收录 **36 篇**。端到端规划类 16 篇，其中 **13 篇集中在 8–11 月**，1–7 月以**表征预训练**和**用预测误差做"惊讶度"**的应用为主。
- 方法主线从"**在驾驶视频上预训练 V-JEPA，再接规划头**"（Drive-JEPA，1 月），转向"**以候选轨迹为条件推演未来潜变量，再打分 / 重排序**"（DA-WAM、World4Scorer、ActWorld-JEPA 等，8–11 月）。
- 规划类论文**以中国车企和高校为主**：小鹏、零跑、蔚来、阿法利、地平线、哈啰、长安、岚图、酷哇、造富智能。欧洲多做预训练和分析（Bosch、CARIAD、根特、英戈尔施塔特）；NYU / LeCun 组走 LiDAR 和"不训练策略、只用世界模型规划"的路线。
- **没有一篇正面检验"世界模型学到了 3D 物理"**。最接近的是四类：Auto-JEPA 的语义遮挡实验、ForeDrive 的未来替换干预、FactorJEPA 的干预敏感预测指标、巴西 UFSC 的车辆动力学物理量保留协议。另外 RPI 的 *Asleep at the Wheel* 给"惊讶度"类方法敲了警钟。

---

## 1. 端到端规划 / 世界-动作模型（核心）

| 日期 | 论文 | 机构 | 做法 | 关键结果 |
|---|---|---|---|---|
| 01-29 | **Drive-JEPA** (2601.22032) | **小鹏汽车**；Virginia Tech、Purdue、南京大学 | 在大规模驾驶视频上预训练 V-JEPA ViT；以候选为中心的规划器，蒸馏模拟器生成的多模态轨迹 | NAVSIM v1 93.7 PDMS（v2 修订版），v2 87.8 EPDMS |
| 06-04 | **Vault** (2606.06219) | **清华大学** | 在 NAVSIM 图像上预训练 V-JEPA 2.1；在 VAE 潜空间单步生成候选，用"奖励门控正样本池"做无策略梯度的强化 | — |
| 07-31 | **Auto-JEPA** (2607.29031) | **清华大学** 车辆与运载学院 / 智能绿色车辆与交通全国重点实验室 | 冻结 V-JEPA 2；只预测"未来意图"嵌入（对齐未来自车轨迹的潜变量），用它到轨迹库里检索候选再选择 | 91.3 PDMS / 89.1 EPDMS；**语义遮挡：遮动态物体引起的意图变化是同面积随机遮挡的 2.97 倍；遮影响行驶的车辆会改变轨迹，遮无关车辆则基本不变** |
| 08-19 | 📖 **DA-WAM** (2608.19085) | **港科大（广州）**；**零跑汽车** | V-JEPA 2 + LoRA；每条候选轨迹各自预测一个未来潜变量，再多头打分；难负样本后训练 | 去掉未来预测 93.31 → 93.46 PDMS；Bench2Drive 也有结果 |
| 08-21 | 📖 **WA-JEPA** (2608.20974) | **阿法利智驾**；电子科大、东南、北邮、天大 | 在 nuPlan 视频上用"遮未来"方式重新预训练 V-JEPA 2.1；用 flow matching 联合去噪未来场景 token 和轨迹；4 路相机 | 91.7 EPDMS；HUGSIM 0.4462；只训动作 89.9 → 联合 91.7 |
| 09-17 | 📖 **MM-Future** (2609.20377) | **蔚来 NIO**；中科大 AGI 研究院、中山大学、北航 | DINOv2-S + LoRA 压成 MM-Token（EMA 目标，JEPA 式）；多组"场景-动作"假设共同演化；用对应的预测未来给候选打分 | 94.0 PDMS / 91.5 EPDMS；用预测未来打分 92.9 → 93.3 |
| 09-22 | 📖 **ForeDrive** (2609.26299) | **华中科技大学**（王兴刚组）；**哈啰** | DINOv3，只用当前前视图；JEPA 式 EMA 预测多时间跨度未来，注入 DiT 规划器；预测器不接收规划损失的梯度 | 89.9 PDMS；**干预实验：换成别的样本的未来比不给未来更伤碰撞指标；预测指标最好的设置规划最差** |
| 09-24 | **WALT** (2609.30436) | **地平线**；港科大、港中文、南邮、南开 | 冻结驾驶世界模型，学一个与之对齐的轨迹潜空间；系统比较 JEPA 与 REPA 两种对齐 | 89.4 → 89.8 PDMS |
| 09-28 | 📖 **AD-E2E-JEPA** (2609.34085) | **NYU**、**AMI Labs**（LeCun、Choromanska） | 系统评测 LeWM / DINO-WM / JEPA-WM；**不训练策略**，用世界模型推演 256 或 8192 条候选做目标条件规划；SIGReg 投影层提速 100 倍 | 零样本 72.9 EPDMS；投影层用于模仿学习时 80.2 → 85.4 |
| 09-29 | **World4Scorer** (2609.36438) | **清华 AIR**；**长安汽车**、HiThink、港科大（广州）、浙大、中科大等 | 打分器即以候选轨迹为条件的 JEPA 式预测器；**用模拟器给每条候选标注结果**，用真实未来锚定 | NAVSIM-v2 SOTA；Bench2Drive 闭环 |
| 10-08 | **SparseWAM** (2611.00044) | **上海交大 AutoLab**；**广州造富智能**、港科大（广州） | 从冻结的 DINOv2 压出每帧 16 个寄存器 token，在其中先想象未来再出动作 | 92.6 PDMS / 90.1 EPDMS；164 ms |
| 10-10 | 📖 **MORE** (2610.14871) | **武汉大学**；**酷哇机器人**、萨拉戈萨大学 | 在 V-JEPA 2 预训练阶段学"运动寄存器"（注入自车运动），作为世界模型和规划共用的状态接口；只用前视图 | 91.8 EPDMS；去掉未来预测 91.1 |
| 11-12 | 📖 **ActWorld-JEPA** (2611.16579) | **南洋理工**（吕辰组）；北交、清华、大工等 | 以候选为条件递归预测未来 + 关系残差重排序；基于 iPad | 94.1 PDMS / 91.6 EPDMS；⚠️ 最终选择器用 navtest 汇总反馈校准过 |
| 11-16 | **WTR-Drive** (2611.20510) | **岚图汽车** | 基于 Drive-JEPA：与候选无关的"世界记忆" + 候选相关分支 + 结构化风险监督 | 94.66 PDMS / 88.24 EPDMS |
| 11-20 | **DriveSTA** (2611.27515) | **南京大学**、北京大学、**EABOT.AI** | 共享 JEPA 编码器 + 离线生成并经模拟打分的备选轨迹 | NAVSIM、HUGSIM 闭环 SOTA（据摘要） |
| 11-20 | **Seeing Is Not Steering** (2611.27638) | **TH 奥格斯堡**、**TH 英戈尔施塔特**、**CARIAD**（大众） | 冻结 Drive-JEPA，用 VLM 陈述场景事实；学习式组件读了事实也没有可检测收益，规则校验才有用 | navhard +2.4 EPDMS（相对对照的优势统计上不确定） |

## 2. 表征预训练 / 感知 / 预测

| 日期 | 论文 | 机构 | 做法 |
|---|---|---|---|
| 02-13 | **AD-LiST-JEPA** (2602.12540) | **NYU**（Choromanska） | 用 JEPA 在 LiDAR 上学时空世界模型，下游做占据补全与预测 |
| 03-12 | **Zero-Shot Cross-City Generalization** (2603.11417) | **NYU Tandon** | 比较 I-JEPA / DINOv2 / MAE 作为规划骨干的跨城市泛化；自监督骨干在跨城市时退化更小 |
| 03-25 | **Le MuMo JEPA** (2603.24327) | **根特大学 – imec** | 把 LeJEPA 扩展到 RGB + LiDAR 深度（或热成像），用融合 token 做瓶颈；在 Waymo / nuScenes 上做检测、深度探针 |
| 06-12 | **FLaRA** (2606.14380) | **佛罗伦萨大学**、**Verizon Connect** | 基于 V-JEPA 2 预测未来潜变量，再在预测的未来上做事故预警分类 |
| 08-02 | **FactorJEPA** (2608.01049) | 作者以个人身份完成（声明与所在公司无关） | 1000 小时、22 城市的南方国家高密度交通视频；把未来拆成布局 / 个体 / 交互三通道；指标含**干预敏感预测（Causal L1）** |
| 08-17 | **V-JEPA4A / Mask What Matters** (2608.17178) | **Bosch**、**弗莱堡大学** | 按显著性遮挡，在驾驶视频上预训练 V-JEPA；在 BDD100k 跟踪、Cityscapes、KITTI 深度上评估 |
| 11-05 | 📖 **JEPAhead** (2611.08657) | **Eldorado 研究所**、**坎皮纳斯大学**（巴西） | 冻结 V-JEPA 1 编码器，把预测器从补全改为因果预测；CARLA 上只做预测；语义线性探针 |

## 3. 用预测误差做"惊讶度" / 分析与评测

| 日期 | 论文 | 机构 | 内容 |
|---|---|---|---|
| 02-10 | **Online Monitoring for Automotive Time Series** (2602.09985) | **TH 英戈尔施塔特**（CARISSMA、AImotion Bavaria） | 用 JEPA 嵌入对目标状态时间序列做无标签异常监测 |
| 06-21 | **Zero-Label Scenario Complexity** (2606.28383) | 独立研究者 | 在 nuPlan 智能体状态上训最小 JEPA，用时间预测误差当场景复杂度分数 |
| 07-05 | **Geographic Diversity Beats Data Volume** (2607.04500) | 独立研究者（同上） | 上一篇的跨城市迁移：nuPlan → Argoverse 2，地理多样性比数据量更重要 |
| 08-02 | **Asleep at the Wheel** (2608.01336) | **RPI**（伦斯勒理工） | V-JEPA 预测误差做片段筛选，**看似有效其实是域偏移**；同一数据集内的公平基准上会失效 |
| 09-21 | 📖 **D-JEPA** (2609.24749) | **港科大（广州）**、BU、上海交大 | 通用的"决策对齐"潜空间世界模型；驾驶只测了 7 个场景，平均 PDMS 57.4 → 95 以上 |
| 09-26 | **What Do Latent Predictive Vehicle Representations Retain?** (2609.32512) | **圣卡塔琳娜联邦大学**（巴西） | 车辆动力学（CarMaker）JEPA 的物理量保留协议；**未训练编码器保留得略好；对小幅指令扰动的响应与模拟器不一致** |

## 4. 外围应用（路侧、V2X、驾驶员、VQA 等）

| 日期 | 论文 | 机构 | 内容 |
|---|---|---|---|
| 01-04 | **HanoiWorld** (2601.01577) | **河内科技大学**、Troy University | JEPA + RNN 世界模型，Highway-Env 上做强化学习控制 |
| 01-23 | **Semantic V2X Collision Prediction** (2601.17216) | **渥太华大学**、**Nokia Bell Labs** | 路侧单元用 V-JEPA 生成未来帧嵌入，通过 V2X 传给车辆做碰撞预测 |
| 04-19 | **Infrastructure-Centric World Models** (2604.17651) | **UMass Amherst** | 立场论文：路侧视角的世界模型 |
| 09-16 | **Sim-to-Real Traffic VQA** (2609.18562) | **胡志明市工业技术与教育大学** | AI City Challenge：冻结 V-JEPA + Llama 预测器 |
| 09-18 | **JEPA Guided Diffusion** (2609.21379) | **胡志明市工业技术与教育大学** | 冻结 V-JEPA 表征去条件化 Cosmos，生成交通视频 |
| 09-26 | **TriDrive** (2609.33000) | 南佛罗里达大学、NVIDIA、Purdue、湖南大学 | 驾驶员 / 车辆 / 道路联合预测；道路用冻结的 V-JEPA 2 潜变量 |
| 11-16 | **VISTA** (2611.20458) | **渥太华大学**、**Nokia Bell Labs** | 路侧 V-JEPA + 轻量语言推理，做通信高效的交通态势感知 |

---

## 5. 机构分布

| 类型 | 机构（论文） |
|---|---|
| 中国车企 / 智驾公司 | 小鹏（Drive-JEPA）、零跑（DA-WAM）、阿法利（WA-JEPA）、蔚来（MM-Future）、哈啰（ForeDrive）、地平线（WALT）、长安（World4Scorer）、造富智能（SparseWAM）、酷哇（MORE）、岚图（WTR-Drive）、EABOT.AI（DriveSTA） |
| 中国 / 亚洲高校 | 清华（Vault、Auto-JEPA、World4Scorer）、港科大（广州）（DA-WAM、D-JEPA、SparseWAM、World4Scorer）、华科（ForeDrive）、上交（SparseWAM）、武大（MORE）、南大（DriveSTA）、南洋理工（ActWorld-JEPA） |
| 北美 | NYU / AMI Labs（AD-LiST-JEPA、Cross-City、AD-E2E-JEPA）、RPI（Asleep at the Wheel）、渥太华 + Nokia Bell Labs（V2X、VISTA）、UMass |
| 欧洲 | Bosch + 弗莱堡（V-JEPA4A）、CARIAD + TH 奥格斯堡 / 英戈尔施塔特（Seeing Is Not Steering、Online Monitoring）、根特 imec（Le MuMo）、佛罗伦萨 + Verizon（FLaRA） |
| 其他 | 巴西 UFSC、Eldorado / Unicamp；越南 HUST、HCMUTE；独立研究者 |

---

## 6. 和"验证 3D 物理"相关的观察

1. **全年没有一篇在驾驶视觉世界模型上做物理违例、深度 / 3D 位置探针或反事实渲染。**
2. **因果使用（对应验证方案的 E5）有两篇可直接参照**：
   - Auto-JEPA：遮挡影响行驶的车辆会改变意图和轨迹，遮挡无关车辆基本不变；
   - ForeDrive：把预测未来换成别的样本的、打乱时间跨度、用当前帧平铺、换成真值。
3. **"预测得准 ≠ 规划得好"被反复观察到**：ForeDrive 表 6、WTR-Drive（最佳候选分数更高，但被选中的轨迹分数反而可能更低）、D-JEPA 的 "decision-local prediction gap"。
4. **惊讶度方法要严格控制域偏移**：Asleep at the Wheel 证明跨数据集的"新奇度"主要来自域偏移。Jaiswal 的两篇用预测误差衡量复杂度，也应该用同样的标准复核。
5. **物理量保留可以直接借用 UFSC 的协议**（2609.32512）：把物理量保留、物理邻域结构、预测、局部指令响应分开测，并且始终用未训练编码器做对照。

## 参考链接

- Drive-JEPA https://arxiv.org/abs/2601.22032 · Vault https://arxiv.org/abs/2606.06219 · Auto-JEPA https://arxiv.org/abs/2607.29031
- DA-WAM https://arxiv.org/abs/2608.19085 · WA-JEPA https://arxiv.org/abs/2608.20974 · MM-Future https://arxiv.org/abs/2609.20377
- ForeDrive https://arxiv.org/abs/2609.26299 · WALT https://arxiv.org/abs/2609.30436 · AD-E2E-JEPA https://arxiv.org/abs/2609.34085
- World4Scorer https://arxiv.org/abs/2609.36438 · SparseWAM https://arxiv.org/abs/2611.00044 · MORE https://arxiv.org/abs/2610.14871
- ActWorld-JEPA https://arxiv.org/abs/2611.16579 · WTR-Drive https://arxiv.org/abs/2611.20510 · DriveSTA https://arxiv.org/abs/2611.27515 · Seeing Is Not Steering https://arxiv.org/abs/2611.27638
- AD-LiST-JEPA https://arxiv.org/abs/2602.12540 · Cross-City https://arxiv.org/abs/2603.11417 · Le MuMo JEPA https://arxiv.org/abs/2603.24327 · FLaRA https://arxiv.org/abs/2606.14380
- FactorJEPA https://arxiv.org/abs/2608.01049 · V-JEPA4A https://arxiv.org/abs/2608.17178 · JEPAhead https://arxiv.org/abs/2611.08657
- Online Monitoring https://arxiv.org/abs/2602.09985 · Scenario Complexity https://arxiv.org/abs/2606.28383 · Geographic Diversity https://arxiv.org/abs/2607.04500
- Asleep at the Wheel https://arxiv.org/abs/2608.01336 · D-JEPA https://arxiv.org/abs/2609.24749 · Vehicle Representations https://arxiv.org/abs/2609.32512
- HanoiWorld https://arxiv.org/abs/2601.01577 · V2X https://arxiv.org/abs/2601.17216 · Infrastructure WM https://arxiv.org/abs/2604.17651 · Sim-to-Real VQA https://arxiv.org/abs/2609.18562
- JEPA Guided Diffusion https://arxiv.org/abs/2609.21379 · TriDrive https://arxiv.org/abs/2609.33000 · VISTA https://arxiv.org/abs/2611.20458
