# 多论文技术调研报告：VLA / World Model / World Action Model 技术脉络

生成日期：2026-05-10  
最近更新：2026-06-06  
使用 skill：`paper-abstract-compared`  
输入范围：用户给定的 VLA、World Model、World Action Model、机器人基础模型、动作模型、数据引擎、触觉/多模态相关 work。

配套可视化速记页：[`core-diagrams.html`](./core-diagrams.html)  
说明：该页面为每个工作补充了统一风格的“核心机制框图”，用于记忆和横向比较；框图是按论文/技术博客核心思想重画的结构图，不是论文原图搬运。

新增 must-read paper 入口：

| 工作 | Markdown 技术解读 | HTML 阅读版 |
| - | - | - |
| Seed-RF | [`Seed-RF/README.md`](../../must-read-papers/Seed-RF/README.md) | [源码](../../must-read-papers/Seed-RF/Seed-RF-faithful-bilingual.html) / [网页预览](https://raw.githack.com/shuluoshu/Towards-EmbodiedAI-Research-Collections/main/must-read-papers/Seed-RF/Seed-RF-faithful-bilingual.html) |
| WALL-WM | [`WALL-WM/README.md`](../../must-read-papers/WALL-WM/README.md) | [源码](../../must-read-papers/WALL-WM/WALL-WM-faithful-bilingual.html) / [网页预览](https://raw.githack.com/shuluoshu/Towards-EmbodiedAI-Research-Collections/main/must-read-papers/WALL-WM/WALL-WM-faithful-bilingual.html) |
| Discrete-WAM | [`Discrete-WAM/README.md`](../../must-read-papers/Discrete-WAM/README.md) | [源码](../../must-read-papers/Discrete-WAM/Discrete-WAM-faithful-bilingual.html) / [网页预览](https://raw.githack.com/shuluoshu/Towards-EmbodiedAI-Research-Collections/main/must-read-papers/Discrete-WAM/Discrete-WAM-faithful-bilingual.html) |

## 0. 总览结论

### 0.1 这一批工作的总体趋势

1. **VLA 的第一阶段是把机器人控制变成 token / language-conditioned imitation learning。** RT-1 把真实机器人数据规模化，RT-2 把 VLM/LLM 的互联网语义迁移到 action token，Open X-Embodiment / RT-X 进一步把跨机器人数据变成公共基础设施。

2. **第二阶段转向开源、可复现和可微调。** Prismatic VLMs 提供 VLM 设计空间与 OpenVLA 的视觉语言底座，OpenVLA 把 7B VLA、970K 机器人 episode、LoRA/量化微调与公开代码整合起来，成为后续很多 VLA 的事实参考点。

3. **第三阶段从 autoregressive action token 走向 diffusion / flow / continuous action。** π₀、RDT-1B、SmolVLA、From Flow to One Step 等工作说明：动作分布通常是多模态且连续的，flow matching / diffusion transformer 在 dexterous manipulation 和 action chunking 上更自然。

4. **第四阶段开始把 world model 接回 policy learning。** Genie 2/3、Ctrl-World、GigaWorld、VLAW、WMPO、DreamZero、Fast-WAM、Cosmos Policy、GigaWorld-Policy、WALL-WM 都在回答同一个问题：未来视频/世界预测到底应该作为数据引擎、训练表征、规划器，还是直接作为 action model。

5. **跨 embodiment 和 action representation 成为 2025-2026 的硬问题。** LAP、UniAct、X-VLA、Action Space Design、AgiBot GO-1、χ₀ / Kai0 都在围绕“动作空间如何统一、迁移、稳定部署”做文章。

6. **机器人基础模型逐渐从“能做 demo”转向“数据闭环 + 部署闭环”。** GR00T N1、AgiBot World、π₀.5、Gemini Robotics、Helix、Cosmos Policy、Cosmos3、LingBot/RynnVLA 等都把真实数据、合成数据、后训练、长时程评测和部署成本放在核心位置。

7. **2026-06 新增材料把 WAM 的焦点从“是否能想象未来”推进到“以什么粒度、什么成本、什么统一接口想象未来”。** Cosmos3 走 omnimodal backbone，WALL-WM 走 event-grounded 原子单元，GigaWorld-Policy 走 action-centered train-complex/infer-simple，Generalist GEN-1 则从目标驱动角度反对把自己限制在 VLA/world model 标签里。

### 0.2 最值得重点关注的工作

| 优先级 | ID | 工作 | 理由 |
| - | - | - | - |
| 高 | P01 | RT-1 | 真实机器人 Transformer policy 的规模化起点。 |
| 高 | P03 | RT-2 | VLA 概念的关键转折：web-scale VLM 知识迁移到 robot action。 |
| 高 | P04 | Open X-Embodiment / RT-X | 数据标准化和跨 embodiment scaling 的基础设施。 |
| 高 | P06 | OpenVLA | 开源 VLA 基线，后续大量工作围绕它比较或改造。 |
| 高 | P07 | π₀ | flow matching VLA 的代表作，直接影响 2025-2026 action model 线。 |
| 高 | P11 | GR00T N1 | humanoid foundation model 的系统化代表，双系统 VLA + diffusion action。 |
| 高 | P19 | Ctrl-World | robot world model 从生成视频走向 policy evaluation / improvement。 |
| 高 | P24 | VLAW | VLA policy 与 world model 迭代共改进，接近真实闭环。 |
| 高 | P42 | DreamZero | World Action Model / WAM 代表：从预测世界变成零样本策略。 |
| 高 | P43 | Fast-WAM | 对 WAM 提出关键反问：推理时是否真的需要想象未来。 |
| 高 | P40 | Cosmos Policy | 视频模型后训练成 policy，把 future image、value、action 融合到 latent diffusion。 |
| 高 | P52 | Cosmos3 | omnimodal world model，把 language/image/video/audio/action 统一到 MoT backbone，并开放模型、代码、数据和 benchmark。 |
| 高 | P53 | WALL-WM | event-grounded WAM，把训练原子从固定 action chunk 改成 action-grounded semantic event。 |
| 高 | P54 | GigaWorld-Policy | action-centered WAM，训练时联合 video/action，推理时只解码动作，直接回应实时部署瓶颈。 |


### 0.3 一张总表

| ID | 时间 | 标题 | 单位 / 团队 | 主类 | 关键贡献 | 状态 |
| - | -: | - | - | - | - | - |
| P01 | 2022-12 | RT-1 | Google Robotics | VLA / Embodied Foundation Model | 真实机器人数据上的 Robotics Transformer | 已确认 |
| P02 | 2023-03 | PaLM-E | Google / TU Berlin 等 | Native Multimodal / Embodied Reasoning | 把连续传感输入接入 LLM，做 embodied reasoning | 已确认 |
| P03 | 2023-07 | RT-2 | Google DeepMind | VLA | 把 web-scale VLM 知识转为 action token | 已确认 |
| P04 | 2023-10 | Open X-Embodiment / RT-X | Open X-Embodiment Collaboration | Data Engine / VLA | 22 robots、527 skills 的跨 embodiment 数据与 RT-X | 已确认 |
| P05 | 2024-02 | Prismatic VLMs | TRI / Stanford 等 | Native Multimodal | VLM 设计空间，OpenVLA 的底座之一 | 已确认 |
| P06 | 2024-06 | OpenVLA | Stanford / Berkeley / Google / TRI | VLA | 7B 开源 VLA，970K episodes，LoRA/量化部署 | 已确认 |
| P07 | 2024-10 | π₀ | Physical Intelligence | Diffusion / Flow Policy | VLM + flow matching action expert 的 generalist robot policy | 已确认 |
| P08 | 2024-10 | RDT-1B | Tsinghua / THU-ML | Diffusion Policy | 1B diffusion transformer，统一动作空间，双臂操控 | 已确认 |
| P09 | 2025-01 | FAST | Physical Intelligence | Action Tokenization / Efficiency | 面向高频动作序列的压缩/加速 action representation | 待深读 |
| P10 | 2025-02 | Helix | Figure AI | Humanoid VLA | humanoid 上的视觉-语言-动作闭环系统 | 官方 blog，未见论文 |
| P11 | 2025-03 | GR00T N1 | NVIDIA | Humanoid Foundation Model | System 2 VLM + System 1 diffusion transformer | 已确认 |
| P12 | 2025-03 | AgiBot World Colosseo / GO-1 | AgiBot / OpenDriveLab | Data Engine / VLA | 百万轨迹、217 任务、latent action generalist policy | 已确认 |
| P13 | 2025-03 | Gemini Robotics | Google DeepMind | VLA / Native Multimodal | 基于 Gemini 2.0 的 embodied multimodal VLA | 已确认 |
| P14 | 2025-04 | π₀.5 | Physical Intelligence | VLA / Open-world Generalization | co-training + hybrid multi-modal examples，提高野外泛化 | 已确认 |
| P15 | 2025-06 | SmolVLA | Hugging Face / LeRobot | Efficient VLA / Flow Policy | 450M 开源 VLA，flow matching action expert，异步推理 | 已确认 |
| P16 | 2024-12 | Genie 2 | Google DeepMind | World Model | action-controllable playable 3D world model | 官方 blog |
| P17 | 2025-08 | Genie 3 | Google DeepMind | World Model | 720p/24fps、分钟级一致性的交互世界模型 | 官方 blog |
| P18 | 2025-11 | GigaWorld-0 | GigaWorld Team | World Model / Data Engine | video + 3D world model 合成 embodied VLA 数据 | 已确认 |
| P19 | 2025-10 | Ctrl-World | Stanford / Berkeley line | World Model / Policy Improvement | 可控多视角 world model，用 imagination 评估和改进 policy | 已确认 |
| P20 | 2025-11 | WMPO | ByteDance Seed | World Model + RL | pixel world model + on-policy GRPO for VLA | 已确认 |
| P21 | 2025-11 | π\*0.6 / RECAP | Physical Intelligence | RL / Preference / Generalist Policy | pi 系列后训练/可纠正策略线 | 待深读 |
| P22 | 2025-12 | GR00T N1.6 | NVIDIA | Humanoid Foundation Model | N1 系列迭代，公开资源更偏 N1.7 | 待核验 |
| P23 | 2025-12 | χ₀ / Kai0 | OpenDriveLab / HKU MMLab | Deployment / Long-horizon Manipulation | model arithmetic + stage advantage + train-deploy alignment | 已确认 |
| P24 | 2026-02 | VLAW | Stanford / Berkeley line | VLA + World Model | policy 和 action-conditioned video world model 迭代共改进 | 已确认 |
| P25 | 2026-02 | LAP | Princeton / UC Berkeley line | Cross-Embodiment VLA | 用自然语言表达低层动作，实现零样本跨机器人迁移 | 已确认 |
| P26 | 2026-02 | BagelVLA | Tsinghua / ByteDance line | VLA + Visual Foresight | interleaved language planning、visual forecasting、action generation | 已确认 |
| P27 | 2026-01 | InternVLA-A1 | InternRobotics / InternVL line | Native Multimodal VLA | understanding + generation + action 的 MoT 统一模型 | 已确认 |
| P28 | 2026-02 | Action Space Design | 多机构 | Action Representation | 系统研究动作空间选择对 policy learning 的影响 | 已确认 |
| P29 | 2025-01 | UniAct | 2toINF | Action Representation | universal action 表征增强 embodied foundation model | 已确认但名称有歧义 |
| P30 | 2026-03 | From Flow to One Step | TUM / robotics line | Flow Policy / Distillation | CFM expert 蒸馏成单步实时多模态 trajectory policy | 已确认 |
| P31 | 2025-06 | SPMem | SPMem team | Memory / VLA | 空间-过程记忆增强长程 embodied task | 已确认 |
| P32 | 2025-11 | RynnVLA-002 | Alibaba DAMO | VLA | 产业侧 VLA 系列迭代 | 已确认但需深读 |
| P33 | 2026-04/05 | Being-H0.7 | BeingBeyond | Humanoid Foundation Model | humanoid foundation policy / project release | 已确认但需深读 |
| P34 | 2026 | π₀.7 / Steerable Policies | Physical Intelligence | Steerable Generalist Policy | 可控/可引导 generalist robot policy | 官方 blog，未见论文 |
| P35 | 2025-12 | Motus | Motus Robotics | Robotics Foundation Model | 运动/操控相关 foundation policy | 已确认但需深读 |
| P36 | 2026-01 | LingBot-VA | Robbyant / Ant | Vision-Action | 偏 VA 的真实机器人模型 | 已确认但需深读 |
| P37 | 2026-03 | ThinkJEPA | 未定 | Representation / World Model | JEPA 类思考/预测表征 | 已确认但需深读 |
| P38 | 2026-03 | LeWorldModel | Le-WM team | World Model | LeRobot/embodied world model 线 | 已确认但需深读 |
| P39 | 2026-03 | NEO-Unify | SenseNova | Native Multimodal | 原生多模态统一模型，机器人相关性弱 | 边界项 |
| P40 | 2026-01 | Cosmos Policy | NVIDIA / Stanford / Columbia line | Video Model Policy / Planning | 视频模型单阶段后训练成 robot policy + planning | 已确认 |
| P41 | 2026-02 | DreamZero | NVIDIA DIR | World Action Model | WAM 作为零样本 policy | 已确认 |
| P42 | 2026-03 | Fast-WAM | Shanghai AI Lab / Tsinghua line | World Action Model / Efficiency | 去掉推理时 future imagination，保留训练时 video co-training | 已确认 |
| P43 | 2026 | Genesis Engine | Genesis Embodied AI | Simulator / Generative Environment | 物理仿真与世界生成基础设施 | 已确认项目 |
| P44 | 2026-04 | GEN-1 / Beyond World Models & VLAs | Generalist AI | Robotics Foundation Model / Methodology | 从 scratch 训练 physical-interaction foundation model，强调目标驱动而非标签驱动 | 官方 blog，未见论文 |
| P45 | 2026-02 | UniVTAC | UniVTAC team | Tactile / Benchmark | 触觉-视觉-动作统一数据/benchmark | 已确认 |
| P46 | 2025-07 | Tactile-VLA | Tactile-VLA team | Tactile VLA | 触觉纳入 VLA 操控 | 已确认 |
| P47 | 2026-03 | RoboClaw | 未定 | Robotics / Manipulation | 机器人操控 work，需深读 | 已确认但需深读 |
| P48 | 2025-03 | CoT-VLA | CoT-VLA team | Reasoning VLA | chain-of-thought reasoning for VLA | 已确认 |
| P49 | 2025-07 | ThinkAct | ThinkAct team | Reasoning VLA | reasoning/action 分解路线 | 已确认 |
| P50 | 2026-01 | ACoT-VLA | AgiBotTech | Reasoning VLA | action chain-of-thought for VLA | 已确认 |
| P51 | 2025-06 | UniVLA | 多机构 | VLA / Latent Action | unified VLA / latent action 线 | 已确认 |
| P52 | 2026-06 | Cosmos3 | NVIDIA | Omnimodal World Model | language/image/video/audio/action 统一 MoT，开放模型、代码、合成数据和 Cosmos-HUE | 已确认 |
| P53 | 2026-06 | WALL-WM | X Square Robot Team / 自变量机器人 | Event-grounded WAM | semantic event 作为 video-action learning 原子，event mode + unified mode | 已确认 |
| P54 | 2026-03 | GigaWorld-Policy | GigaAI | Action-centered WAM | 基于 GigaWorld-0.5，训练时 video+action，推理只保留 action decoding | 已确认 |
| U01 | 未知 | NoVA-render | 未定 | 未定 | 名称不明确 | 不收录，待核验 |
| U02 | 未知 | MoH | 未定 | 未定 | 名称过短 | 不收录，待核验 |
| U03 | 未知 | DALI-R | 未定 | 未定 | 未确认可靠链接 | 不收录，待核验 |
| U04 | 未知 | 3D-mix for VLA | 未定 | 未定 | 未确认可靠链接 | 不收录，待核验 |
| U05 | 未知 | CAST | 未定 | 未定 | 名称歧义大 | 不收录，待核验 |
| U06 | 未知 | HyT | 未定 | 未定 | 名称过短 | 不收录，待核验 |
| U07 | 未知 | LaRA-VLA | 未定 | 未定 | 未确认可靠链接 | 不收录，待核验 |
| U08 | 2026-02 | RDT2 | 未定 | Diffusion Policy | 用户备注未确认公开 arXiv/blog | 暂不收录 |
| U09 | 2026-05 | GENE-26.5 | Genesis AI | 公司发布 | Reuters 新闻，不足以技术收录 | 暂不收录 |


## 1. 分类图谱

| 类别 | 包含论文 | 共同思想 | 代表差异 |
| - | - | - | - |
| Autoregressive / Token VLA | P01, P03, P06, P25 | 把动作离散化或语言化，用 LM/VLM 的 next-token 能力预测 action | RT-1 更像机器人 Transformer；RT-2/OpenVLA 依赖 web-scale VLM；LAP 把动作转为自然语言 |
| Flow / Diffusion Policy | P07, P08, P15, P30, P40 | 用连续生成模型拟合多模态动作分布 | π₀/SmolVLA 是 VLA action expert；RDT 是 diffusion foundation model；Cosmos Policy 把视频模型直接后训练为 policy |
| World Model as Data Engine | P16, P17, P18, P19, P52 | 用交互世界/视频/3D/omnimodal 模型替代或补充真实 rollout | Genie 更通用环境；Ctrl-World/GigaWorld 更贴近机器人操控数据；Cosmos3 把 action 纳入统一 world backbone |
| World Model + Policy Optimization | P20, P24, P40 | world model 不只是生成数据，而是参与 policy improvement / planning / RL | WMPO 强调 pixel world model + GRPO；VLAW 强调 policy/world model 迭代共改进；Cosmos Policy 强调 value/future image planning |
| World Action Model | P41, P42, P53, P54 | 显式建模 action-conditioned future，并把 world prediction 变成可执行 policy 能力 | DreamZero 强调零样本 policy；Fast-WAM/GigaWorld-Policy 证明训练时 video co-training 可以服务高效 action-only inference；WALL-WM 强调事件粒度 |
| Cross-Embodiment / Action Space | P04, P12, P25, P28, P29, P51 | 统一不同机器人、不同控制接口、不同动作维度 | RT-X 强调数据混合；LAP 语言化 action；Action Space Design 做系统消融；UniAct/UniVLA 走 latent/universal action |
| Humanoid / Physical Intelligence | P10, P11, P22, P33, P34, P36 | 面向 humanoid 或真实机器人长时程部署 | GR00T 更开放工程化；Helix/Figure 更偏官方系统演示；π 系列更偏 generalist manipulation |
| Reasoning VLA | P48, P49, P50 | 将推理步骤、任务分解或 CoT 显式接入 VLA | 需要警惕推理文本与真实动作闭环之间的 credit assignment |
| Tactile / Multimodal Embodiment | P45, P46 | 把触觉作为 action grounding 的关键模态 | 对高精度接触、灵巧手、材质判断有直接价值 |
| Goal-driven Robotics Foundation Model | P44 | 不按 VLA/world model 标签组织，而按物理 AGI 目标与数据/算力约束组织 | Generalist GEN-1 强调从 scratch 训练和大规模 physical interaction data |


## 2. 技术演化主线

### 2.1 Google / DeepMind 路线

| 时间 | 工作 | 类型 | 核心贡献 | 与前作关系 |
| - | - | - | - | - |
| 2022-12 | RT-1 | Robot Transformer | 真实机器人数据规模化，action token imitation | 起点 |
| 2023-03 | PaLM-E | Embodied multimodal LM | 把连续传感输入插入 LLM，多任务 embodied reasoning | 从 policy 扩展到 reasoning |
| 2023-07 | RT-2 | VLA | 把 VLM 语义迁移到 action token | RT-1 + PaLI/PaLM-E 思想合流 |
| 2023-10 | RT-X | Data / Cross-Embodiment | 跨机器人数据和模型迁移 | 把单团队数据变成开放协作 |
| 2025-03 | Gemini Robotics | VLA | Gemini 2.0 进入物理世界 | 从 RT 系列演进到 Gemini-native multimodality |
| 2024-12 / 2025-08 | Genie 2/3 | World Model | 可交互世界模型用于 agent training/evaluation | 从 action policy 外扩到 synthetic environment |


路线结论：Google 系列可以概括为 **真实机器人行为克隆 → embodied multimodal reasoning → web-scale VLA → 跨 embodiment 数据基础设施 → Gemini-native robotics → 可交互 world model**。

### 2.2 Physical Intelligence 路线

| 时间 | 工作 | 类型 | 核心贡献 | 与前作关系 |
| - | - | - | - | - |
| 2024-10 | π₀ | Flow VLA | VLM + flow matching action expert | 从 token action 转向连续 action generative policy |
| 2025-01 | FAST | Action representation | 高效动作 token/压缩表示 | 服务高频、长序列控制 |
| 2025-04 | π₀.5 | Open-world VLA | hybrid multi-modal examples 和现实泛化 | 扩大 π₀ 的部署半径 |
| 2025-11 | π\*0.6 / RECAP | Post-training / alignment | 后训练、纠错、偏好/可控策略 | 从 pretrain policy 走向 self-improvement |
| 2026 | π₀.7 / Steerable Policies | Steerable generalist policy | 可引导/可控的 generalist robot foundation model | 从泛化进一步走向可控部署 |


路线结论：PI 线可以概括为 **flow-based generalist policy → action representation efficiency → open-world generalization → post-training correction → steerability**。

### 2.3 NVIDIA 路线

| 时间 | 工作 | 类型 | 核心贡献 | 与前作关系 |
| - | - | - | - | - |
| 2025-03 | GR00T N1 | Humanoid foundation model | System 2 VLM + System 1 diffusion transformer | humanoid VLA 起点 |
| 2025-12 | GR00T N1.6 / N1.7 | Humanoid iteration | 模型与数据迭代 | 需要以公开 repo/HF 为准 |
| 2026-01 | Cosmos Policy | Video model policy | Cosmos-Predict2 单阶段后训练成 policy + future/value planning | 把 video foundation model 直接接 control |
| 2026-02 | DreamZero | World Action Model | WAM 作为零样本 policy | 从 VLA 转向 action-conditioned world prediction |
| 2026-06 | Cosmos3 | Omnimodal world model | language/image/video/audio/action 统一 MoT backbone | 把 VLM、video generator、world simulator、WAM 收束到同一 Physical AI backbone |


路线结论：NVIDIA 线可以概括为 **humanoid VLA → synthetic data + simulation → video model policy → world action model → omnimodal Physical AI backbone**。

### 2.4 Stanford / Berkeley / Chelsea Finn / Jianyu Chen 相关路线

| 时间 | 工作 | 类型 | 核心贡献 | 与前作关系 |
| - | - | - | - | - |
| 2024-02 | Prismatic VLMs | VLM design | 系统研究 VLM 设计空间 | OpenVLA 基座之一 |
| 2024-06 | OpenVLA | Open VLA | 7B 开源 VLA 基线 | 使后续 VLA 可复现 |
| 2025-10 | Ctrl-World | Robot world model | 多视角可控 imagination，用于 policy ranking / SFT | world model 开始服务 policy |
| 2026-02 | VLAW | VLA + world model | policy 和 world model online iterative improvement | 更接近闭环自改进 |
| 2026-02 | LAP | Cross-Embodiment | natural language action 表达 | 从结构统一转向语言统一 action |


路线结论：这条线可以概括为 **VLM 设计 → 开源 VLA → robot imagination → policy/world model 共训练 → action 表达迁移**。

### 2.5 GigaAI / GigaWorld 路线

| 时间 | 工作 | 类型 | 核心贡献 | 与前作关系 |
| - | - | - | - | - |
| 2025-11 | GigaWorld-0 | World model / data engine | video + 3D world model 合成 embodied VLA 数据 | 用 world model 扩展机器人数据供给 |
| 2026-03 | GigaWorld-Policy | Action-centered WAM | 基于 GigaWorld-0.5，训练时联合 video/action，推理时只保留动作解码 | 从数据生成器推进到实时策略模型 |

路线结论：GigaWorld 路线可以概括为 **世界生成数据引擎 → action-centered WAM → 实时闭环控制**。

### 2.6 Generalist AI 路线

| 时间 | 工作 | 类型 | 核心贡献 | 与前作关系 |
| - | - | - | - | - |
| 2026-04 | GEN-1 | Robotics foundation model | 约 99% 参数从 scratch 训练，强调足够数据下完全控制模型底座 | 不把自己定义成 fine-tuned VLA 或 world model |
| 2026-04 | Beyond World Models & VLAs | Methodology blog | 以目标驱动替代方法标签驱动，提出 99%+ success with about 1 hour robot data 等里程碑 | 给 GEN-1 的技术取向补充解释 |

路线结论：Generalist AI 路线可以概括为 **大规模 physical interaction data → 从 scratch 训练 → 不按 VLA/world model 标签约束架构 → 面向 physical AGI 的目标驱动优化**。

## 3. 前后依赖关系与技术演化

| 关系 | A | B | 说明 |
| - | - | - | - |
| 数据规模继承 | RT-1 | RT-2 | RT-2 继承 RT-1 的机器人控制问题，但用 web-scale VLM 扩展语义泛化。 |
| 多团队基础设施 | RT-2 | Open X-Embodiment / RT-X | RT-X 将单模型路线扩展成跨数据集、跨机器人协作。 |
| 开源化继承 | RT-X | OpenVLA | OpenVLA 依赖 Open X-Embodiment 数据并提供可复现模型/训练代码。 |
| 结构基座 | Prismatic VLMs | OpenVLA | OpenVLA 使用 Prismatic 系列思想作为视觉语言底座。 |
| 动作生成范式迁移 | OpenVLA | π₀ / SmolVLA | 从 autoregressive action token 逐渐转向 flow matching continuous actions。 |
| 数据引擎升级 | RT-X / AgiBot World | GigaWorld / Ctrl-World | 从收集真实数据到用 world model 合成和评估数据。 |
| world model 闭环 | Ctrl-World | VLAW | Ctrl-World 提供 imagination 改进 policy 的思路；VLAW 进一步迭代改进 policy 和 world model。 |
| WAM 反思 | DreamZero | Fast-WAM | DreamZero 强调 WAM 作为 policy；Fast-WAM 追问推理时显式 future imagination 是否必要。 |
| 视频模型 policy 化 | Genie / Cosmos | Cosmos Policy | 将视频模型的动态先验直接后训练成控制与规划能力。 |
| 统一 backbone 扩展 | Cosmos Policy | Cosmos3 | Cosmos Policy 是视频模型 policy 化案例；Cosmos3 将 action 与 language/image/video/audio 一起放进 omnimodal MoT backbone。 |
| 训练繁推理简 | Fast-WAM | GigaWorld-Policy | 两者都削弱推理时视频生成成本；GigaWorld-Policy 明确提出训练阶段联合 video/action、推理阶段 action-only decoding。 |
| 时间粒度重定义 | action chunk WAM | WALL-WM | WALL-WM 反对固定长度 chunk 作为学习原子，改用 action-grounded semantic event。 |
| 标签范式反思 | VLA / World Model | GEN-1 | Generalist AI 认为目标和约束比方法标签重要，GEN-1 不自称 fine-tuned VLA 或 world model。 |


## 4. 核心思想聚类

### Cluster A：Action Token / Language Action

代表：RT-1、RT-2、OpenVLA、LAP、UniAct、UniVLA。  
核心判断：这条路线最大优势是能继承 LM/VLM 的预训练知识，缺点是低层连续控制细节和高频闭环容易变成 action tokenization 工程问题。LAP 的自然语言动作非常激进，它不是再设计 tokenizer，而是让 action supervision 回到语言模型最擅长的分布。

### Cluster B：Flow / Diffusion Action Expert

代表：π₀、RDT-1B、SmolVLA、From Flow to One Step、Cosmos Policy。  
核心判断：这条路线承认机器人动作是连续、多峰、需要 chunking 的分布。关键矛盾从“会不会生成动作”变成“生成是否足够快、是否可闭环、是否稳定覆盖多模态动作”。

### Cluster C：World Model as Data Engine

代表：Genie 2/3、GigaWorld、Ctrl-World、DreamGen、VLAW。  
核心判断：单纯收集真实机器人数据太慢，world model 让 policy 可以在 imagination 中获得更多状态、失败和反事实。但最难的是 contact-rich manipulation 的物理细节，尤其是微小接触误差会直接造成 real robot failure。

### Cluster D：World Action Model

代表：DreamZero、Fast-WAM、Cosmos Policy、GigaWorld-Policy、WALL-WM、Cosmos3。  
核心判断：WAM 的野心比 world model 更大：不只是预测未来，而是把动作和未来状态联结成可执行 policy。Fast-WAM 和 GigaWorld-Policy 的结论尤其关键：世界预测可能更像训练表征的 regularizer 或 dense supervision，而不是推理时必须真的生成未来视频。WALL-WM 则指出另一个关键变量：学习原子不该总是固定长度 action chunk，而可以是语义一致的 executable event。

### Cluster E：Deployment / Robustness / Train-Deploy Alignment

代表：χ₀ / Kai0、Action Space Design、GR00T N1、Helix、Being-H0.7、π₀.7。  
核心判断：当模型走向真实部署，性能瓶颈往往从模型容量转移到分布一致性、动作空间、数据采样、后训练、纠错和系统集成。

## 5. 单篇技术卡片

### P01. RT-1: Robotics Transformer for Real-World Control at Scale

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project |
| 链接 | [https://arxiv.org/pdf/2212.06817](https://arxiv.org/pdf/2212.06817) / [https://robotics-transformer1.github.io/](https://robotics-transformer1.github.io/) |
| 团队 | Google Robotics |
| 发布时间 | 2022-12 |
| 主类 | VLA / Embodied Foundation Model |
| 副类标签 | real\_robot\_data, action\_token, transformer\_policy, instruction\_following |


一句话总结：RT-1 是把大规模真实机器人数据训练成通用 Transformer policy 的起点之一。

出发点：传统机器人 policy 往往依赖单任务、小数据和特定场景，泛化能力弱。RT-1 的关键问题是：如果像 NLP/vision 一样扩大数据和模型，机器人控制是否也会出现 scaling behavior。

核心方法：使用图像历史、自然语言指令和离散化动作作为输入输出，训练 Transformer 预测动作 token。它强调真实机器人 demonstrations 的规模和多任务覆盖，而不是只在仿真 benchmark 上优化。

模型结构：视觉编码器把图像转成 token，语言指令作为条件，Transformer 聚合历史观测与语言，输出离散动作维度。动作包括机械臂位置、旋转、夹爪等控制量。

数据与训练：核心是大规模真实机器人数据，覆盖多任务、多物体、多场景。训练目标是 imitation learning / behavioral cloning。

评测与 insight：证明数据规模和任务多样性可以改善真实机器人泛化。其局限是动作离散化和单一平台约束，后续 RT-X/OpenVLA 主要在跨 embodiment 和开放复现上继续推进。

与 VLA / WAM 关系：RT-1 还不是典型 web-scale VLA，但它奠定了“语言条件 + 视觉观测 + 动作 token”的基本模板。

### P02. PaLM-E: An Embodied Multimodal Language Model

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project + Google blog |
| 链接 | [https://arxiv.org/pdf/2303.03378](https://arxiv.org/pdf/2303.03378) / [https://palm-e.github.io/](https://palm-e.github.io/) / [https://research.google/blog/palm-e-an-embodied-multimodal-language-model/](https://research.google/blog/palm-e-an-embodied-multimodal-language-model/) |
| 团队 | Google / TU Berlin 等 |
| 发布时间 | 2023-03 |
| 主类 | Native Multimodal / Embodied Reasoning |
| 副类标签 | multimodal\_sentence, embodied\_reasoning, planning, transfer |


一句话总结：PaLM-E 把真实世界连续传感输入直接接入大语言模型，让 LLM 具备 embodied grounding 和多任务推理能力。

出发点：LLM 有强推理能力，但缺少和真实世界感知状态的直接连接。机器人任务需要把语言、图像、状态估计、规划步骤放进同一上下文。

核心方法：将视觉、连续状态估计和文本编码成 interleaved multimodal sentence，与预训练 LLM 端到端联合训练。它不只做机器人任务，还做 VQA、captioning 等视觉语言任务。

模型结构：输入端把不同模态投影到语言模型 token 空间，LLM backbone 负责跨模态推理，输出可以是语言计划、任务回答或 embodied 决策相关文本。

数据与训练：多任务训练，包含机器人 sequential manipulation planning、视觉问答、captioning 等。最大模型 PaLM-E-562B 体现了 scale 对 embodied reasoning 的影响。

评测与 insight：显示跨任务联合训练能带来正迁移，且模型规模越大越能保留通用语言能力。局限在于它更偏 reasoning 和 high-level planning，不是低层实时控制 policy。

与 VLA / WAM 关系：PaLM-E 是 RT-2/Gemini Robotics 的思想前身：把互联网级 multimodal reasoning 融入机器人，而不是从零训练 policy。

### P03. RT-2: Vision-Language-Action Models Transfer Web Knowledge to Robotic Control

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project + Google DeepMind blog |
| 链接 | [https://arxiv.org/pdf/2307.15818](https://arxiv.org/pdf/2307.15818) / [https://robotics-transformer2.github.io/](https://robotics-transformer2.github.io/) / [https://deepmind.google/blog/rt-2-new-model-translates-vision-and-language-into-action/](https://deepmind.google/blog/rt-2-new-model-translates-vision-and-language-into-action/) |
| 团队 | Google DeepMind |
| 发布时间 | 2023-07 |
| 主类 | VLA |
| 副类标签 | web\_knowledge\_transfer, action\_token, semantic\_generalization |


一句话总结：RT-2 是 VLA 路线的关键转折，把 web-scale VLM 的语义能力转成机器人 action。

出发点：机器人数据少且昂贵，单靠 robot demonstrations 很难覆盖真实世界语义长尾。RT-2 试图回答：VLM 从互联网学到的知识能否迁移到机器人控制。

核心方法：将机器人动作表示成文本 token 或可由 VLM 生成的符号序列，使 VLM 同时学习视觉语言任务和机器人动作预测。这样模型能把“看到、理解、行动”放在同一个序列建模框架中。

模型结构：以 PaLI / PaLM-E 风格的视觉语言模型为底座，输入图像和语言指令，输出 action token。它本质上把 action 作为另一种语言输出。

数据与训练：混合互联网视觉语言数据与机器人 trajectory 数据。关键不是机器人数据最大，而是让两类数据互相迁移。

评测与 insight：RT-2 在需要语义理解、符号推理、物体常识的任务上优于只用机器人数据的 policy。局限是 action token 的离散表示和推理时延。

与 VLA / WAM 关系：这是标准 VLA 命名真正成立的代表作。后续 OpenVLA、Gemini Robotics、LAP 都在继承或修正它的 action-as-language 思路。

### P04. Open X-Embodiment / RT-X

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project |
| 链接 | [https://arxiv.org/pdf/2310.08864](https://arxiv.org/pdf/2310.08864) / [https://robotics-transformer-x.github.io/](https://robotics-transformer-x.github.io/) |
| 团队 | Open X-Embodiment Collaboration |
| 发布时间 | 2023-10 |
| 主类 | Data Engine / Cross-Embodiment VLA |
| 副类标签 | cross\_embodiment, robot\_dataset, RT-X, transfer |


一句话总结：Open X-Embodiment 把 VLA 从单机器人实验推进到跨机器人数据基础设施。

出发点：每个实验室的数据、机器人和动作空间都不同，导致数据不能自然合并。要训练 robot foundation model，必须解决跨 embodiment 数据统一和迁移。

核心方法：整合 22 个机器人、527 个 skills 的多源数据，并训练 RT-X 模型研究跨数据迁移。它从数据层面证明不同机器人 demonstrations 可以产生正迁移。

模型结构：RT-X 沿用 RT 系列 Transformer/VLA 思路，但重点在多数据集融合和跨平台泛化。

数据与训练：多机构、多机器人真实数据，覆盖不同传感器、动作空间和任务。它的意义不仅是模型，而是数据格式和社区协作。

评测与 insight：证明跨 embodiment 训练能提高很多下游任务表现，但也暴露动作空间、观测格式、任务标签不统一的问题。

与 VLA / WAM 关系：后续 OpenVLA、SmolVLA、RDT、AgiBot、GigaWorld 都绕不开它提出的数据混合问题。

### P05. Prismatic VLMs

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + GitHub |
| 链接 | [https://arxiv.org/pdf/2402.07865](https://arxiv.org/pdf/2402.07865) / [https://github.com/TRI-ML/prismatic-vlms](https://github.com/TRI-ML/prismatic-vlms) |
| 团队 | TRI / Stanford 等 |
| 发布时间 | 2024-02 |
| 主类 | Native Multimodal / VLM Design |
| 副类标签 | VLM\_backbone, visual\_encoder, design\_space, open\_source |


一句话总结：Prismatic VLMs 系统研究视觉编码器、LLM、训练配方等 VLM 设计选择，并成为 OpenVLA 的重要底座。

出发点：很多 VLM 论文只报告单点模型，缺少对设计空间的可控比较。VLA 依赖 VLM 底座，因此底座质量会直接影响机器人泛化。

核心方法：在统一代码框架中比较视觉编码器、语言模型、融合方式和数据配方，释放 PRISM 模型与训练框架。

模型结构：通常由视觉 encoder、projector/resampler 和 LLM backbone 组成。对 VLA 的启发在于视觉 token 和语言 token 如何融合。

数据与训练：面向视觉语言任务的多阶段训练，不直接以机器人控制为主。

评测与 insight：它不是 VLA 论文，但对 OpenVLA 这类模型极重要，因为 OpenVLA 需要强、开源、可训练的 VLM backbone。

与 VLA / WAM 关系：它回答的是“VLA 的 V 和 L 底座怎么选”，不是动作学习本身。

### P06. OpenVLA

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project + GitHub + HuggingFace |
| 链接 | [https://arxiv.org/pdf/2406.09246](https://arxiv.org/pdf/2406.09246) / [https://openvla.github.io/](https://openvla.github.io/) / [https://github.com/openvla/openvla](https://github.com/openvla/openvla) / [https://huggingface.co/openvla/openvla-7b](https://huggingface.co/openvla/openvla-7b) |
| 团队 | Stanford / UC Berkeley / Google DeepMind / TRI |
| 发布时间 | 2024-06 |
| 主类 | VLA / Open Source Robot Foundation Model |
| 副类标签 | open\_source, Open\_X, LoRA, quantization, DINOv2, SigLIP |


一句话总结：OpenVLA 是第一个影响力很大的开源 7B VLA，显著降低了研究者复现和微调 VLA 的门槛。

出发点：RT-2 等早期 VLA 大多闭源，社区难以研究如何训练、微调和部署 VLA。OpenVLA 目标是把模型、代码、checkpoint、微调教程都公开。

核心方法：基于 Llama 2 语言模型，结合 DINOv2 和 SigLIP 视觉特征，训练在 Open X-Embodiment 的 970K 真实机器人 episodes 上。输出 robot actions，并支持多机器人控制和参数高效微调。

模型结构：视觉 encoder 融合多种预训练视觉特征，投影到 LLM token 空间，LLM 进行 autoregressive action prediction。动作仍然是 token 化输出。

数据与训练：Open X-Embodiment 数据为核心，训练 7B VLA。工程贡献包括 LoRA 微调、量化 serving 和 PyTorch 训练代码。

评测与 insight：OpenVLA 在 generalist manipulation 上优于更大闭源模型 RT-2-X 的若干设置，并展示消费级 GPU 上微调可能性。局限是高频连续控制和物理预测能力仍弱。

与 VLA / WAM 关系：OpenVLA 是 2024-2025 很多论文的 baseline 和改造对象，是 VLA 开源生态的基准线。

### P07. π₀: A Vision-Language-Action Flow Model for General Robot Control

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + blog + GitHub |
| 链接 | [https://arxiv.org/pdf/2410.24164](https://arxiv.org/pdf/2410.24164) / [https://www.pi.website/blog/pi0](https://www.pi.website/blog/pi0) / [https://github.com/Physical-Intelligence/openpi](https://github.com/Physical-Intelligence/openpi) |
| 团队 | Physical Intelligence |
| 发布时间 | 2024-10 |
| 主类 | Diffusion / Flow Policy / VLA |
| 副类标签 | flow\_matching, action\_chunking, VLM\_backbone, dexterous\_manipulation |


一句话总结：π₀ 把 VLA 与 flow matching action generation 结合，成为连续动作 generalist robot policy 的代表。

出发点：action token 适合继承 LLM 能力，但机器人控制本质上是连续、多模态、高频和接触丰富的。π₀ 试图让 VLA 继承语义知识，同时用 flow matching 更自然地生成动作轨迹。

核心方法：在预训练 VLM 上加 action expert，用 flow matching 生成连续动作。它训练在多平台、多任务、灵巧操作数据上，支持 zero-shot、语言指令、VLM high-level policy 调用和少量微调。

模型结构：VLM 负责视觉语言上下文理解，action expert 负责连续动作生成。与 autoregressive action token 相比，动作头更像 conditional generative model。

数据与训练：混合多机器人平台数据，包括单臂、双臂、移动操作等。训练目标包含 flow matching 对动作分布的建模。

评测与 insight：覆盖 laundry folding、table cleaning、box assembly 等复杂任务。核心 insight 是：VLA 不一定要把 action 完全语言化，连续生成模型可能更适合低层控制。

与 VLA / WAM 关系：π₀ 是 VLA 向 action model 转型的关键节点，后续 SmolVLA、π₀.5、π₀.7 都继承这条线。

### P08. RDT-1B: Robotics Diffusion Transformer

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project + GitHub |
| 链接 | [https://arxiv.org/pdf/2410.07864](https://arxiv.org/pdf/2410.07864) / [https://rdt-robotics.github.io/rdt-robotics/](https://rdt-robotics.github.io/rdt-robotics/) / [https://github.com/thu-ml/RoboticsDiffusionTransformer](https://github.com/thu-ml/RoboticsDiffusionTransformer) |
| 团队 | Tsinghua / THU-ML |
| 发布时间 | 2024-10 |
| 主类 | Diffusion Policy / Embodied Foundation Model |
| 副类标签 | diffusion\_transformer, bimanual, unified\_action\_space, action\_chunking |


一句话总结：RDT-1B 是面向双臂操控的 1B diffusion foundation model，强调统一动作空间和多机器人兼容。

出发点：双臂操控存在多峰动作分布和强协调需求，传统 BC 或 autoregressive token policy 很难稳定建模。RDT 试图用 diffusion transformer 提升动作分布表达能力。

核心方法：用 Diffusion Transformer 预测未来动作 chunk，输入包括语言、最多三视角 RGB、控制频率和 proprioception。统一动作空间包含末端、关节、位置、速度、移动底盘等物理量。

模型结构：多模态 encoder + diffusion transformer action decoder。输出是 64 步 action chunk。

数据与训练：预训练在 1M+ 多机器人 episodes 上，强调 MIT 许可、开放模型和代码。

评测与 insight：强项在 bimanual manipulation 和多平台适配。局限是对未见过的新机器人平台仍通常需要小数据微调。

与 VLA / WAM 关系：RDT 是 diffusion policy foundation model，不一定依赖 LLM 推理，但对动作模型和统一动作空间非常重要。

### P09. FAST

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + Physical Intelligence blog |
| 链接 | [https://arxiv.org/pdf/2501.09747](https://arxiv.org/pdf/2501.09747) / [https://www.pi.website/research/fast](https://www.pi.website/research/fast) |
| 团队 | Physical Intelligence |
| 发布时间 | 2025-01 |
| 主类 | Action Representation / Deployment Efficiency |
| 副类标签 | action\_tokenization, fast\_inference, high\_frequency\_control |


一句话总结：FAST 关注 VLA/robot policy 中动作序列表示和推理效率，是 π 系列向高频控制落地的重要配套。

出发点：generalist policy 往往输出长 action chunk，直接生成连续高维动作会带来时延、压缩和稳定性问题。

核心方法：围绕动作序列压缩、tokenization 或快速生成机制设计，让模型在保持表达能力的同时更适合实际控制频率。

模型结构：更像 action representation / tokenizer 层，而不是完整 VLA 架构。

数据与训练：依赖 robot demonstrations 和 π 系列数据/模型生态。

评测与 insight：重点看推理速度、动作保真度和下游任务成功率之间的 trade-off。

与 VLA / WAM 关系：它回答“动作如何被高效表达”，是所有 VLA/WAM 部署必须解决的问题。

### P10. Helix

| 项目 | 内容 |
| - | - |
| 类型 | Figure AI official blog |
| 链接 | [https://www.figure.ai/news/helix](https://www.figure.ai/news/helix) |
| 团队 | Figure AI |
| 发布时间 | 2025-02 |
| 主类 | Humanoid VLA / Deployment |
| 副类标签 | humanoid, fleet\_data, system\_integration |


一句话总结：Helix 是 Figure 对 humanoid 端到端 VLA/控制系统的官方技术发布，但公开论文细节有限。

出发点：humanoid 需要把语言指令、视觉感知、双手协调和全身控制接进一个可部署系统。

核心方法：官方信息更偏系统级描述，强调单一神经网络或统一模型控制 humanoid 完成多任务。相比论文，缺少可复现实验和消融。

模型结构：可理解为高层视觉语言理解 + 低层动作控制的 humanoid policy，但具体训练细节未公开。

数据与训练：官方没有像 OpenVLA/GR00T 那样公开完整数据规模与训练配方。

评测与 insight：适合作为产业侧 humanoid VLA 方向信号，不适合作为严格论文基准。

与 VLA / WAM 关系：与 VLA/embodied control 强相关，但技术可信度依赖官方披露，不能和 arXiv 论文同等权重。

### P11. GR00T N1

| 项目 | 内容 |
| - | - |
| 类型 | arXiv / NVIDIA research page |
| 链接 | [https://arxiv.org/pdf/2503.14734](https://arxiv.org/pdf/2503.14734) / [https://research.nvidia.com/publication/2025-03\_nvidia-isaac-gr00t-n1-open-foundation-model-humanoid-robots](https://research.nvidia.com/publication/2025-03_nvidia-isaac-gr00t-n1-open-foundation-model-humanoid-robots) |
| 团队 | NVIDIA |
| 发布时间 | 2025-03 |
| 主类 | Humanoid Foundation Model / VLA |
| 副类标签 | dual\_system, diffusion\_transformer, humanoid, synthetic\_data, human\_video |


一句话总结：GR00T N1 是 humanoid robot foundation model 的系统化代表，使用 System 2 VLM 理解环境、System 1 diffusion transformer 实时生成动作。

出发点：humanoid 机器人需要处理多任务、双臂、语言指令、真实世界变化和数据稀缺。单纯 imitation baseline 难以泛化。

核心方法：双系统架构：视觉语言模块负责环境和指令理解，diffusion transformer 负责流畅动作生成，二者端到端耦合训练。训练数据混合真实机器人轨迹、人类视频和合成数据。

模型结构：System 2 类 VLM 输出高层语义/状态表示，System 1 类 action diffusion module 输出实时运动控制。

数据与训练：异构数据金字塔，包括 real robot、synthetic、human video。NVIDIA 还配套 Isaac/Omniverse/Cosmos 数据生成工具。

评测与 insight：在多种仿真 benchmark 与 Fourier GR-1、1X humanoid 上展示语言条件双臂操控。关键 insight 是 humanoid foundation model 必须同时解决模型结构和数据来源。

与 VLA / WAM 关系：GR00T N1 仍是 VLA，但它后续自然连接到 Cosmos Policy / DreamZero 等 world/video model policy 线。

### P12. AgiBot World Colosseo / GO-1

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project + blog + GitHub |
| 链接 | [https://arxiv.org/pdf/2503.06669](https://arxiv.org/pdf/2503.06669) / [https://opendrivelab.com/AgiBot-World/](https://opendrivelab.com/AgiBot-World/) / [https://agibot-world.com/blog/go1](https://agibot-world.com/blog/go1) / [https://github.com/OpenDriveLab/AgiBot-World](https://github.com/OpenDriveLab/AgiBot-World) |
| 团队 | AgiBot / OpenDriveLab |
| 发布时间 | 2025-03 |
| 主类 | Data Engine / VLA |
| 副类标签 | million\_trajectories, latent\_action, dexterous, long\_horizon |


一句话总结：AgiBot World 用百万级真实轨迹和 GO-1 generalist policy 推动机器人数据规模化。

出发点：Open X-Embodiment 虽然重要，但真实工业/家庭场景还需要更大规模、更标准化、更高质量的数据采集。

核心方法：构建包含 1M+ trajectories、217 tasks、多个部署场景的大规模 manipulation platform，并提出 GO-1 policy 利用 latent action representations 提高数据利用率。

模型结构：GO-1 侧重 generalist policy 与 latent action 表征，服务跨任务和 dexterous/long-horizon tasks。

数据与训练：标准化采集 pipeline + human-in-the-loop verification，覆盖 gripper、dexterous hand、visuo-tactile sensors。

评测与 insight：报告相对 Open X-Embodiment 预训练有显著提升，并在复杂真实任务上超过 RDT 等 prior。核心 insight 是数据质量和规模仍是 embodied AI 的第一性变量。

与 VLA / WAM 关系：它是 data engine 线的关键节点，也给 GigaWorld、Kai0、UniVLA 等后续工作提供数据生态。

### P13. Gemini Robotics

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + official page |
| 链接 | [https://arxiv.org/pdf/2503.20020](https://arxiv.org/pdf/2503.20020) / [https://deepmind.google/models/gemini-robotics/](https://deepmind.google/models/gemini-robotics/) |
| 团队 | Google DeepMind |
| 发布时间 | 2025-03 |
| 主类 | Native Multimodal VLA |
| 副类标签 | Gemini\_2, multimodal\_reasoning, robot\_control, generalization |


一句话总结：Gemini Robotics 把 Gemini 2.0 的 multimodal reasoning 推进到机器人控制，是 Google 从 RT 系列走向 Gemini-native robotics 的标志。

出发点：RT-2 证明 VLM 知识能转化为动作，但新一代 multimodal foundation model 有更强视频、空间、推理和指令能力。

核心方法：基于 Gemini 2.0 训练 advanced VLA，用图像、语言和机器人状态控制不同机器人完成复杂任务。

模型结构：Gemini multimodal backbone + action head / robotics adaptation。官方强调 generality、interactivity 和 safety。

数据与训练：包含机器人 demonstrations、多模态数据和 Gemini 系列预训练能力。

评测与 insight：强调对新环境、新指令和复杂任务的适应。公开技术细节比 RT/OpenVLA 少，但方向重要。

与 VLA / WAM 关系：它是 native multimodality 到 embodied control 的代表，后续 on-device Gemini Robotics 进一步走向本地部署。

### P14. π₀.5

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + blog |
| 链接 | [https://arxiv.org/pdf/2504.16054](https://arxiv.org/pdf/2504.16054) / [https://www.pi.website/blog/pi05](https://www.pi.website/blog/pi05) |
| 团队 | Physical Intelligence |
| 发布时间 | 2025-04 |
| 主类 | VLA / Open-world Generalization |
| 副类标签 | open\_world, co\_training, hybrid\_multimodal\_examples, dexterity |


一句话总结：π₀.5 试图回答 VLA 在实验室外到底能泛化到多远。

出发点：π₀ 展示了 generalist policy 潜力，但真实世界任务分布更开放，环境、物体、指令和干扰都更多。

核心方法：通过 co-training 和 hybrid multi-modal examples 扩大模型对真实世界任务的覆盖，提升长时程与灵巧任务泛化。

模型结构：延续 π₀ 的 VLM + flow action policy 思路，但训练数据和任务覆盖更开放。

数据与训练：混合真实机器人、多模态样例和可能的互联网/视频先验，强调 open-world generalization。

评测与 insight：关注陌生环境和实际有用任务，而不只是 benchmark。局限是部分系统细节仍由官方 blog 描述。

与 VLA / WAM 关系：它是 flow VLA 从可行性走向开放部署的重要中间版本。

### P15. SmolVLA

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + Hugging Face blog + docs |
| 链接 | [https://arxiv.org/pdf/2506.01844](https://arxiv.org/pdf/2506.01844) / [https://huggingface.co/blog/smolvla](https://huggingface.co/blog/smolvla) / [https://huggingface.co/docs/lerobot/smolvla](https://huggingface.co/docs/lerobot/smolvla) |
| 团队 | Hugging Face / LeRobot |
| 发布时间 | 2025-06 |
| 主类 | Efficient VLA / Flow Policy |
| 副类标签 | 450M, flow\_matching, asynchronous\_inference, community\_data, open\_source |


一句话总结：SmolVLA 是轻量开源 VLA 的代表，用 450M 参数、社区数据和 flow matching action expert 做低门槛机器人控制。

出发点：OpenVLA/π₀ 等模型虽强，但成本较高。社区需要能在消费级硬件训练、微调和部署的 VLA。

核心方法：SmolVLM-2 作为 VLM backbone，action expert 使用 Flow Matching Transformer。额外设计视觉 token reduction、layer skipping、interleaved attention 和异步推理。

模型结构：VLM 负责视觉语言理解，flow action expert 负责动作 chunk 生成。异步推理让机器人执行当前动作时并行计算下一段动作。

数据与训练：只用 LeRobot 社区共享且 license 兼容的数据，强调任务标注和相机视角标准化。

评测与 insight：在 LIBERO、Meta-World、SO100/SO101 等任务上胜过更大模型或强 baseline，并报告约 30% 更快响应和 2x throughput。

与 VLA / WAM 关系：它是“VLA 应该可复现、可部署、可社区共建”的代表。

### P16. Genie 2

| 项目 | 内容 |
| - | - |
| 类型 | Google DeepMind official blog |
| 链接 | [https://deepmind.google/blog/genie-2-a-large-scale-foundation-world-model/](https://deepmind.google/blog/genie-2-a-large-scale-foundation-world-model/) |
| 团队 | Google DeepMind |
| 发布时间 | 2024-12 |
| 主类 | World Model / Simulator |
| 副类标签 | action\_controllable\_world, playable\_3D, counterfactual\_rollout, agent\_training |


一句话总结：Genie 2 是可由人或 agent 控制的 3D foundation world model，目标是生成训练和评估 embodied agents 的交互环境。

出发点：训练 general embodied agents 需要大量、多样且安全的环境，真实/手工仿真环境成本高。

核心方法：从单张 prompt image 生成可交互 3D 世界，用键鼠动作控制，模型预测 action-conditioned future frames。

模型结构：官方描述为 autoregressive latent diffusion world model：视频经 autoencoder 到 latent frames，再由大 transformer dynamics model 用 causal mask 建模。

数据与训练：大规模视频数据训练，使用 classifier-free guidance 增强 action controllability。

评测与 insight：展示 counterfactual trajectories、long horizon memory、SIMA agent acting inside world model。局限是交互时长、物理一致性和精确可控性仍有限。

与 VLA / WAM 关系：它不是 robot policy，但为 world model as environment 的路线奠定了方向。

### P17. Genie 3

| 项目 | 内容 |
| - | - |
| 类型 | Google DeepMind official blog |
| 链接 | [https://deepmind.google/blog/genie-3-a-new-frontier-for-world-models/](https://deepmind.google/blog/genie-3-a-new-frontier-for-world-models/) |
| 团队 | Google DeepMind |
| 发布时间 | 2025-08 |
| 主类 | World Model / Interactive Environment |
| 副类标签 | real\_time\_world, 720p, 24fps, promptable\_events, agent\_training |


一句话总结：Genie 3 把交互 world model 推到实时 720p/24fps 和分钟级一致性，进一步接近 agent training environment。

出发点：Genie 2 的交互时长和一致性有限，离真实 agent 训练环境还有距离。

核心方法：从文本 prompt 生成动态可导航环境，并支持 promptable world events。核心能力包括视觉记忆、一致性和实时交互。

模型结构：官方未公开完整论文，但可视为更强的 action-conditioned generative world model。

数据与训练：基于大规模视频/世界建模数据，具体配方未公开。

评测与 insight：技术指标包括 720p、24fps、几分钟一致性。它的重要性在于将 world model 从“视频生成”推向“可交互世界接口”。

与 VLA / WAM 关系：对 VLA 的直接价值是 synthetic curriculum 和闭环评估；对 WAM 的价值是 action-conditioned dynamics 的大模型化。

### P18. GigaWorld-0

| 项目 | 内容 |
| - | - |
| 类型 | arXiv |
| 链接 | [https://arxiv.org/pdf/2511.19861](https://arxiv.org/pdf/2511.19861) |
| 团队 | GigaWorld Team |
| 发布时间 | 2025-11 |
| 主类 | World Model / Data Engine |
| 副类标签 | video\_generation, 3D\_generation, Gaussian\_splatting, synthetic\_data, FP8 |


一句话总结：GigaWorld-0 把 world model 明确定位为 VLA 数据引擎，结合视频生成和 3D 物理一致性来合成 embodied training data。

出发点：真实机器人数据昂贵且难扩展，纯视频生成又可能缺少几何和物理一致性。

核心方法：两个组件协同：GigaWorld-0-Video 生成多样、纹理丰富、时序一致的视频；GigaWorld-0-3D 结合 3D generative modeling、Gaussian Splatting、系统辨识和可执行 motion planning 确保几何/物理可用。

模型结构：world generation pipeline + GigaTrain 高效训练框架，服务下游 VLA 如 GigaBrain-0。

数据与训练：用合成 embodied data 训练 VLA，并使用 FP8、sparse attention 降低训练成本。

评测与 insight：关键 claim 是不经真实机器人 interaction 也能提高真实机器人泛化和成功率。需要重点看生成数据与真实评测之间的 causal link。

与 VLA / WAM 关系：这是 world model as data engine 的典型代表，对未来数据飞轮很关键。

### P19. Ctrl-World

| 项目 | 内容 |
| - | - |
| 类型 | arXiv |
| 链接 | [https://arxiv.org/pdf/2510.10125](https://arxiv.org/pdf/2510.10125) |
| 团队 | Yanjiang Guo / Lucy Shi / Jianyu Chen / Chelsea Finn |
| 发布时间 | 2025-10 |
| 主类 | World Model / Policy Evaluation and Improvement |
| 副类标签 | controllable\_world\_model, multi\_view, DROID, imagination\_rollout, SFT |


一句话总结：Ctrl-World 是为机器人操控设计的可控多视角 world model，用 imagination 来评估和改进 generalist robot policies。

出发点：真实 rollout 评估和收集纠错数据成本太高，现有 world model 又难以支持现代 generalist policy 的多视角、细粒度动作和长时程一致性。

核心方法：构建 controllable multi-view world model，使用 frame-level action conditioning 和 pose-conditioned memory retrieval。模型可生成 novel scenario / new camera placement 下超过 20 秒的一致轨迹。

模型结构：action-conditioned video/world model + memory retrieval + multi-view rendering/prediction。

数据与训练：DROID 数据集，约 95k trajectories、564 scenes。

评测与 insight：可无真实 rollout 排名 policy performance；通过合成成功 trajectory 做 SFT，policy success 提升 44.7%。这说明 world model 可以做 evaluator 和 data generator。

与 VLA / WAM 关系：它是从 VLA 到 WAM 的桥：动作条件未来预测已经直接服务 policy 改进。

### P20. WMPO

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project |
| 链接 | [https://arxiv.org/pdf/2511.09515](https://arxiv.org/pdf/2511.09515) / [https://wm-po.github.io/](https://wm-po.github.io/) |
| 团队 | ByteDance Seed |
| 发布时间 | 2025-11 |
| 主类 | World Model + RL / Policy Optimization |
| 副类标签 | pixel\_world\_model, GRPO, on\_policy, self\_correction, lifelong\_learning |


一句话总结：WMPO 用 pixel-based world model 让 VLA 在 imagination 中做 on-policy GRPO，减少真实机器人 RL 成本。

出发点：VLA 依赖 expert demonstrations，难以从失败中学习；真实机器人 RL 又样本复杂度高。

核心方法：训练 pixel world model，而不是只做 latent world model，使 imagined trajectories 与 VLA 从 web-scale images 学到的视觉特征对齐。然后在 imagination 中进行 on-policy GRPO。

模型结构：VLA policy + pixel world model + GRPO optimization loop。

数据与训练：仿真和真实机器人任务，重点是 world model 中 rollout 生成和 policy update。

评测与 insight：报告样本效率、性能、自纠错、泛化和 lifelong learning 提升。关键在于它把 world model 作为 RL 环境，而不是只做数据增强。

与 VLA / WAM 关系：WMPO 是 VLA + world model + RL 闭环的重要范式。

### P21. π\*0.6 / RECAP

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + PI blog |
| 链接 | [https://arxiv.org/pdf/2511.14759](https://arxiv.org/pdf/2511.14759) / [https://www.pi.website/blog/pistar06](https://www.pi.website/blog/pistar06) |
| 团队 | Physical Intelligence |
| 发布时间 | 2025-11 |
| 主类 | Post-training / Alignment / Generalist Policy |
| 副类标签 | correction, preference, steerability, pi\_series |


一句话总结：π\*0.6 / RECAP 可视为 π 系列从预训练 policy 走向纠错、后训练和可控部署的一步。

出发点：通用机器人 policy 只靠 supervised imitation 容易复制数据中的错误，也缺少对用户目标、失败恢复和偏好约束的适配。

核心方法：根据用户给定名称推断，RECAP 更可能强调 correction / recaption / recap-style feedback 机制，用于增强 policy 对失败和目标的可解释修正能力。

模型结构：延续 π 系列 VLA/flow policy，但加入后训练或反馈信号。

数据与训练：需深读论文确认训练目标、反馈来源和是否使用 preference/RL。

评测与 insight：重点应看是否相对 π₀.5 在长时程、可控性或失败恢复上有显著提升。

与 VLA / WAM 关系：它属于 VLA post-training 线，和 world model 线并行解决真实部署的可靠性。

### P22. GR00T N1.6 / N1.7

| 项目 | 内容 |
| - | - |
| 类型 | GitHub / HuggingFace public resources |
| 链接 | [https://github.com/NVIDIA/Isaac-GR00T](https://github.com/NVIDIA/Isaac-GR00T) / [https://huggingface.co/nvidia/GR00T-N1.7-3B](https://huggingface.co/nvidia/GR00T-N1.7-3B) |
| 团队 | NVIDIA |
| 发布时间 | 2025-12 附近 |
| 主类 | Humanoid Foundation Model |
| 副类标签 | open\_weight, humanoid, post\_training |


一句话总结：GR00T N1.6 当前公开证据不如 N1/N1.7 清晰，应以 NVIDIA Isaac GR00T repo 和 HuggingFace N1.7 为准跟踪。

出发点：N1 发布后，NVIDIA 持续迭代 humanoid foundation model、训练数据和部署工具链。

核心方法：延续 N1 的 VLA + diffusion action architecture，可能在数据、模型规模、部署和后训练上迭代。

模型结构：参考 N1/N1.7 公开资源。

数据与训练：需核验 release notes。

评测与 insight：作为版本跟踪项，不建议在严肃报告里和 arXiv 论文同等权重。

与 VLA / WAM 关系：属于 humanoid VLA 系列工程迭代。

### P23. χ₀ / Kai0

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + HuggingFace + project |
| 链接 | [https://arxiv.org/pdf/2602.09021](https://arxiv.org/pdf/2602.09021) / [https://huggingface.co/OpenDriveLab-org/Kai0](https://huggingface.co/OpenDriveLab-org/Kai0) |
| 团队 | OpenDriveLab / HKU MMLab |
| 发布时间 | 2025-12 project / 2026-02 arXiv |
| 主类 | Deployment / Long-horizon Manipulation |
| 副类标签 | model\_arithmetic, stage\_advantage, train\_deploy\_alignment, garment\_manipulation |


一句话总结：χ₀ / Kai0 把真实长时程布料操作的瓶颈归结为分布不一致，并用 model arithmetic、stage advantage 和 train-deploy alignment 提升鲁棒性。

出发点：长时程真实机器人任务失败往往不是模型不够大，而是 demonstrations、policy inductive bias 和部署执行分布不一致。

核心方法：三根技术柱：Model Arithmetic 用权重空间合并吸收不同 demonstration 分布；Stage Advantage 提供阶段感知 dense progress signal；Train-Deploy Alignment 用时空增强、heuristic DAgger corrections 和 temporal chunk-wise smoothing 缩小部署差距。

模型结构：更像 robustness framework，可接在已有 policy 上，重点是训练/部署对齐模块。

数据与训练：据公开模型卡，20 小时数据、8 A100 条件下实现高可靠布料任务，并展示双臂团队长时程运行。

评测与 insight：强调 24 小时连续运行和相对 π₀.5 的成功率提升。值得重点看其评测是否可复现、任务是否狭窄。

与 VLA / WAM 关系：它提醒 VLA/WAM 落地时，分布一致性可能比继续堆模型更重要。

### P24. VLAW

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project |
| 链接 | [https://arxiv.org/pdf/2602.12063](https://arxiv.org/pdf/2602.12063) / [https://sites.google.com/view/vlaw-arxiv](https://sites.google.com/view/vlaw-arxiv) |
| 团队 | Yanjiang Guo / Tony Lee / Lucy Shi / Jianyu Chen / Percy Liang / Chelsea Finn |
| 发布时间 | 2026-02 |
| 主类 | VLA + World Model |
| 副类标签 | iterative\_improvement, action\_conditioned\_video, synthetic\_rollout, real\_robot |


一句话总结：VLAW 用真实 rollout 迭代提升 action-conditioned video world model，再用更好的 world model 生成数据改进 VLA policy。

出发点：world model 物理细节不足，尤其缺少失败案例和接触细节；但真实机器人 rollout 又太贵。

核心方法：迭代算法：收集真实 policy rollout，提升 world model fidelity；再用 world model 生成 supplemental synthetic rollouts，训练/微调 VLA。

模型结构：VLA policy + action-conditioned video generation model + iterative data loop。

数据与训练：真实机器人下游任务；使用生成 rollouts 补充训练。

评测与 insight：报告 base policy 绝对成功率提升 39.2%，其中生成 synthetic rollouts 带来 11.6% 提升。它的重要性在于把 world model 从离线生成器变成可迭代改进的学习伙伴。

与 VLA / WAM 关系：VLAW 是 VLA 和 WAM/World Model 结合得最直接的一类工作。

### P25. LAP: Language-Action Pre-Training

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project |
| 链接 | [https://arxiv.org/pdf/2602.10556](https://arxiv.org/pdf/2602.10556) / [https://lap-vla.github.io/](https://lap-vla.github.io/) |
| 团队 | Princeton / UC Berkeley line |
| 发布时间 | 2026-02 |
| 主类 | Cross-Embodiment VLA |
| 副类标签 | language\_action, zero\_shot\_transfer, action\_representation |


一句话总结：LAP 把低层机器人动作直接表示成自然语言，使 VLA 能零样本迁移到未见过的机器人 embodiment。

出发点：多 embodiment 预训练后，VLA 仍常绑定训练过的机器人和动作空间，需要 expensive fine-tuning。

核心方法：Language-Action Pre-training 将 low-level action 直接写成自然语言，和 VLM 的输入输出分布对齐，不需要 learned tokenizer 或 embodiment-specific architecture。

模型结构：LAP-3B 以语言动作格式统一 action prediction 和 VQA。

数据与训练：语言化动作监督 + VQA / co-training。

评测与 insight：在多个 novel robots 和 manipulation tasks 上达到 50%+ zero-shot success，约为 prior strongest VLA 的 2x。关键 insight 是 action representation 可以用语言分布解决一部分跨 embodiment gap。

与 VLA / WAM 关系：它是 action model 表达路线的重要分叉，对 world action model 的动作条件接口也有启发。

### P26. BagelVLA

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project |
| 链接 | [https://arxiv.org/pdf/2602.09849](https://arxiv.org/pdf/2602.09849) / [https://cladernyjorn.github.io/BagelVLA.github.io/](https://cladernyjorn.github.io/BagelVLA.github.io/) |
| 团队 | Tsinghua / ByteDance line |
| 发布时间 | 2026-02 |
| 主类 | VLA + Visual Foresight |
| 副类标签 | interleaved\_generation, residual\_flow\_guidance, long\_horizon, MoT |


一句话总结：BagelVLA 把语言规划、视觉预测和动作生成放进统一 interleaved sequence，用 Residual Flow Guidance 降低视觉 foresight 的时延。

出发点：很多 VLA 只做语言规划或只做视觉预测，无法在长时程操控中同时推理任务和预测物理后果。

核心方法：初始化自统一理解/生成模型，以 interleaved tokens 同时建模文本推理、视觉预测和动作。RFG 从当前观测出发做 single-step denoising，提取下一关键帧残差视觉特征来指导动作生成。

模型结构：Mixture-of-Transformers，包括语言、视觉、动作三个专家。

数据与训练：仿真 + 真实 benchmark，多阶段任务。

评测与 insight：在长时程、多阶段任务上显著优于 baseline。关键在于把 visual foresight 变成低延迟 action-conditioning feature，而不是昂贵完整视频生成。

与 VLA / WAM 关系：BagelVLA 处于 VLA 和 WAM 中间：它不一定完整 rollout 世界，但把视觉未来预测嵌入动作生成。

### P27. InternVLA-A1

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + GitHub + HuggingFace |
| 链接 | [https://arxiv.org/pdf/2601.02456](https://arxiv.org/pdf/2601.02456) / [https://github.com/InternRobotics/InternVLA-A1](https://github.com/InternRobotics/InternVLA-A1) / [https://huggingface.co/InternRobotics/InternVLA-A1-3B](https://huggingface.co/InternRobotics/InternVLA-A1-3B) |
| 团队 | InternRobotics / InternVL line |
| 发布时间 | 2026-01 |
| 主类 | Native Multimodal VLA |
| 副类标签 | understanding\_generation\_action, MoT, synthetic\_real, visual\_foresight |


一句话总结：InternVLA-A1 用 Mixture-of-Transformers 统一 understanding、generation 和 action，试图补足传统 VLA 缺少物理动态预测的问题。

出发点：MLLM-based VLA 语义强但不懂物理动态；video world model 会预测未来但语义 grounding 不稳。

核心方法：三个专家分别负责场景理解、视觉未来生成和动作执行，通过统一 masked self-attention 交互。

模型结构：基于 InternVL3 和 Qwen3-VL，实例化 2B/3B 模型。

数据与训练：InternData-A1 + AgiBot-World，超过 533M frames 的 synthetic-real hybrid data。

评测与 insight：在 12 个真实机器人任务和仿真 benchmark 上，相对 π₀、GR00T N1.5 有提升，尤其动态场景如传送带分拣。

与 VLA / WAM 关系：这是 native multimodal VLA 与 world model 融合的典型新结构。

### P28. Demystifying Action Space Design for Robotic Manipulation Policies

| 项目 | 内容 |
| - | - |
| 类型 | arXiv |
| 链接 | [https://arxiv.org/pdf/2602.23408](https://arxiv.org/pdf/2602.23408) |
| 团队 | 多机构 |
| 发布时间 | 2026-02 |
| 主类 | Action Representation / Evaluation |
| 副类标签 | delta\_action, joint\_space, task\_space, empirical\_study |


一句话总结：Action Space Design 系统研究机器人 action space 对 imitation policy 学习的影响，是很多 VLA 论文容易忽略的底层变量。

出发点：VLA 论文常把动作空间当工程细节，但不同 action representation 会改变优化景观、稳定性和泛化。

核心方法：大规模 empirical study 比较 delta action、absolute action、joint-space、task-space 等设计。

模型结构：不是新 backbone，而是系统消融不同 action interface。

数据与训练：多 manipulation policy 和 benchmark。

评测与 insight：delta action prediction 往往提升性能；joint/task space 在控制稳定性和泛化上各有优势。

与 VLA / WAM 关系：这是所有 VLA/WAM 必须补课的工程科学问题。

### P29. UniAct

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + GitHub |
| 链接 | [https://arxiv.org/pdf/2501.10105](https://arxiv.org/pdf/2501.10105) / [https://github.com/2toinf/uniact](https://github.com/2toinf/uniact) |
| 团队 | 2toINF |
| 发布时间 | 2025-01 |
| 主类 | Action Representation / Embodied Foundation Model |
| 副类标签 | universal\_action, latent\_action, cross\_embodiment |


一句话总结：UniAct 关注 universal actions，用统一动作表征增强 embodied foundation model 的跨任务/跨平台能力。

出发点：不同机器人动作维度和控制方式不一致，阻碍数据混合和 foundation model 扩展。

核心方法：构造统一 action 表征或 latent action space，把不同 embodiment 的动作对齐到共享空间。

模型结构：action tokenizer / latent action module + VLA/backbone policy。

数据与训练：多 embodiment 数据混合训练。

评测与 insight：重点看跨 embodiment transfer 和少样本适配。

与 VLA / WAM 关系：它给 WAM 的 action conditioning 也提供统一接口思路。

### P30. From Flow to One Step

| 项目 | 内容 |
| - | - |
| 类型 | arXiv |
| 链接 | [https://arxiv.org/pdf/2603.09415](https://arxiv.org/pdf/2603.09415) |
| 团队 | TUM / robotics line |
| 发布时间 | 2026-03 |
| 主类 | Flow Policy / Distillation |
| 副类标签 | CFM, IMLE, one\_step\_policy, real\_time\_control, multimodal\_trajectory |


一句话总结：该工作把 Conditional Flow Matching expert 蒸馏成单步 student，解决 flow policy 推理慢和单步蒸馏模式坍塌的问题。

出发点：flow/diffusion policy 表达力强，但 ODE/denoising 迭代推理慢，不利于高频 closed-loop control。

核心方法：用 IMLE 和双向 Chamfer distance 进行 distribution distillation，让 student 单步生成仍保留多模态轨迹分布。

模型结构：CFM expert → one-step student，统一 perception encoder 融合 RGB、depth、point cloud、proprioception。

数据与训练：多模态 trajectory demonstrations。

评测与 insight：强调实时 receding-horizon replanning 和动态扰动鲁棒性。

与 VLA / WAM 关系：这是 action model 加速的关键方向，能直接服务 π₀/SmolVLA 类 flow action head。

### P31. SPMem

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project + GitHub |
| 链接 | [https://arxiv.org/pdf/2506.05284](https://arxiv.org/pdf/2506.05284) / [https://spmem.github.io/](https://spmem.github.io/) / [https://github.com/spmem/spmem](https://github.com/spmem/spmem) |
| 发布时间 | 2025-06 |
| 主类 | Memory / Embodied Foundation Model |
| 副类标签 | spatial\_memory, procedural\_memory, long\_horizon, embodied\_agent |


一句话总结：SPMem 代表 memory-augmented embodied policy 路线，核心价值在于让模型不仅看当前帧，还能维护空间和过程记忆。

出发点：长时程任务里，VLA 常因只依赖短上下文而丢失物体位置、已完成步骤和失败历史。SPMem 关注如何把空间记忆和过程记忆作为可查询状态接入 embodied decision。

核心方法：构建可更新的 memory module，把环境中空间实体、任务阶段、动作历史和过程知识沉淀下来，并在后续决策时检索。它更偏系统增强模块，而不是替代底层 action head。

模型结构：感知/语言编码器 + memory writer/reader + policy/action module。关键要看 memory 的写入粒度、检索条件和是否可微训练。

数据与评测：建议重点看是否在真实机器人或长时程仿真任务上证明比单纯加长上下文更有效。

与 VLA / WAM 关系：SPMem 补的是 VLA 的长期状态问题；如果和 world model 结合，memory 可以成为 imagination rollout 的 persistent state。

### P32. RynnVLA-002

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + GitHub |
| 链接 | [https://arxiv.org/pdf/2511.17502](https://arxiv.org/pdf/2511.17502) / [https://github.com/alibaba-damo-academy/RynnVLA-002](https://github.com/alibaba-damo-academy/RynnVLA-002) |
| 团队 | Alibaba DAMO Academy |
| 发布时间 | 2025-11 |
| 主类 | Unified VLA + World Model |
| 副类标签 | autoregressive\_action\_world\_model, continuous\_action\_transformer, LIBERO, LeRobot |


一句话总结：RynnVLA-002 把 VLA action model 和 world model 放进同一框架，让未来图像预测和动作生成互相增强。

出发点：单独的 VLA 可能不懂环境动力学，单独的 world model 又不一定能输出高质量动作。RynnVLA-002 试图把两者互相监督。

核心方法：world model 用 action 和视觉输入预测未来图像状态，学习环境物理；VLA model 从图像观测生成动作，同时增强视觉理解并支持 world model 的图像生成。

模型结构：相对 WorldVLA，加入 continuous Action Transformer、wrist camera input/generation 和 state input。整体更像 autoregressive action-world model。

数据与评测：公开信息显示其在 LIBERO 上达到 97.4% success，不经预训练也表现强；真实 LeRobot 实验中，集成 world model 使整体成功率提升约 50%。

与 VLA / WAM 关系：这是 WAM 方向的直接代表之一，和 VLAW、Motus、DreamZero 同属“VLA 与 world model 合流”路线。

### P33. Being-H0.7

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project |
| 链接 | [https://arxiv.org/pdf/2605.00078](https://arxiv.org/pdf/2605.00078) / [https://research.beingbeyond.com/being-h07](https://research.beingbeyond.com/being-h07) |
| 团队 | BeingBeyond / academic authors |
| 发布时间 | 2026-04/05 |
| 主类 | Latent World-Action Model |
| 副类标签 | latent\_query, future\_aware\_reasoning, dual\_branch, egocentric\_video |


一句话总结：Being-H0.7 用 latent world-action reasoning 替代显式未来帧 rollout，在保持未来感知能力的同时降低部署成本。

出发点：VLA 容易学 shortcut mapping，缺少 dynamics/contact/task progress 表征；像素级未来预测又昂贵且包含很多与动作无关的视觉细节。

核心方法：在 perception 和 action 之间插入 learnable latent queries，作为紧凑 reasoning interface。训练时用 dual-branch：prior branch 从当前上下文推断 latent，posterior branch 用未来观测 embedding 替代 query，两者在 latent reasoning space 对齐。

模型结构：deployable prior branch + training-only posterior branch + action policy。推理时丢弃 posterior，不生成未来画面。

数据与评测：跨 6 个仿真 benchmark 和多样真实任务评测，主张达到 SOTA 或可比表现。

与 VLA / WAM 关系：它和 Fast-WAM 观点接近：未来预测的价值可以压到 latent training signal，而不是推理时真的 rollout 视频。

### P34. π₀.7 / Steerable Policies

| 项目 | 内容 |
| - | - |
| 类型 | Physical Intelligence official blog |
| 链接 | [https://www.pi.website/blog/pi07](https://www.pi.website/blog/pi07) |
| 团队 | Physical Intelligence |
| 发布时间 | 2026 |
| 主类 | Steerable Generalist Policy |
| 副类标签 | steerability, post\_training, user\_control, pi\_series |


一句话总结：π₀.7 / Steerable Policies 是 PI 系列从“泛化执行”进一步走向“可引导、可控制、可校正”的信号。

出发点：真实机器人不只要完成任务，还要根据人类偏好、约束、风格或在线反馈调整行为。

核心方法：目前主要依据官方 blog，技术上应关注是否引入 reward/preference、在线纠错、用户 steering signal 或 policy editing。

模型结构：大概率延续 π 系列 VLA + flow action expert，但新增 steerability/post-training 接口。

数据与评测：需等待论文或更完整技术说明。当前不能把它和 arXiv 论文同等比较。

与 VLA / WAM 关系：这是 VLA alignment / controllability 线，和 world model 线共同解决部署可靠性。

### P35. Motus

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project + HuggingFace |
| 链接 | [https://arxiv.org/pdf/2512.13030](https://arxiv.org/pdf/2512.13030) / [https://motus-robotics.github.io/motus](https://motus-robotics.github.io/motus) |
| 团队 | Tsinghua / robotics line |
| 发布时间 | 2025-12 |
| 主类 | Unified Latent Action World Model |
| 副类标签 | MoT, UniDiffuser\_scheduler, optical\_flow, latent\_action, inverse\_dynamics |


一句话总结：Motus 是统一 latent action world model，把理解、视频生成、动作、逆动力学和 joint video-action prediction 放到一个 MoT/UniDiffuser 风格框架中。

出发点：理解、world modeling 和 control 分离会损失共享运动信息，也难以利用异构大规模数据。

核心方法：Mixture-of-Transformer 集成 understanding、video generation、action 三个专家；UniDiffuser-style scheduler 允许在 world model、VLA、inverse dynamics、video generation、video-action joint prediction 等模式间切换。

模型结构：Tri-model joint attention + optical-flow-derived latent actions + three-phase training pipeline + six-layer data pyramid。

数据与评测：报告在仿真中相对 X-VLA 提升约 15%、相对 π₀.5 提升约 45%，真实场景提升约 11-48%。

与 VLA / WAM 关系：Motus 是 WAM / unified multimodal policy 的重要形态：用 latent action 把视频和控制真正耦合。

### P36. LingBot-VA / Causal World Modeling for Robot Control

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project |
| 链接 | [https://arxiv.org/pdf/2601.21998](https://arxiv.org/pdf/2601.21998) / [https://technology.robbyant.com/lingbot-va](https://technology.robbyant.com/lingbot-va) |
| 团队 | Robbyant / Ant line |
| 发布时间 | 2026-01 |
| 主类 | Video World Model + Robot Control |
| 副类标签 | causal\_world\_model, autoregressive\_diffusion, shared\_latent\_space, closed\_loop\_rollout |


一句话总结：LingBot-VA 用视频 world modeling 与 policy execution 同时训练，强调 actions 和 visual dynamics 的因果关系。

出发点：vision-language pretraining 是机器人学习一条路线，video world modeling 可能是另一条独立基础，因为它能想象动作导致的近未来变化。

核心方法：autoregressive diffusion framework 同时学习 frame prediction 和 policy execution；共享 latent space 统一 vision/action tokens；closed-loop rollout 用 ground-truth observations 持续获得环境反馈；异步推理并行动作预测和电机执行。

模型结构：MoT 架构 + shared latent space + closed-loop rollout pipeline。

数据与评测：仿真和真实场景，强调长时程 manipulation、post-training 数据效率和 novel configuration 泛化。

与 VLA / WAM 关系：它是 WAM/VA 路线，不以语言为唯一核心，而是把 visual dynamics 作为控制基础。

### P37. ThinkJEPA

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project + GitHub |
| 链接 | [https://arxiv.org/pdf/2603.22281](https://arxiv.org/pdf/2603.22281) |
| 发布时间 | 2026-03 |
| 主类 | Latent World Model / Representation |
| 副类标签 | JEPA, VLM\_guidance, dual\_temporal\_pathway, hand\_manipulation |


一句话总结：ThinkJEPA 用 VLM reasoning 指导 JEPA latent world model，使短窗口 dense dynamics 预测获得长时程语义。

出发点：JEPA 类 latent world model 擅长局部动态预测，但短观察窗口会让模型偏向低层外推，缺少长时程语义。

核心方法：dual-temporal pathway：dense JEPA branch 学细粒度运动和交互，稀疏采样的 VLM thinker branch 提供长时程语义指导；hierarchical pyramid representation extraction 将 VLM 多层表征转为 latent prediction guidance。

模型结构：JEPA predictor + VLM thinker + hierarchical guidance module。

数据与评测：手部操控轨迹预测，优于 VLM-only 和 JEPA-only baseline，并改善长时程 rollout。

与 VLA / WAM 关系：它更偏 world representation，不直接输出动作，但可作为 WAM/VLA 的 dynamics-aware 表征模块。

### P38. LeWorldModel

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project |
| 链接 | [https://arxiv.org/pdf/2603.19312](https://arxiv.org/pdf/2603.19312) / [https://le-wm.github.io/](https://le-wm.github.io/) |
| 团队 | Mila / NYU / Brown 等 |
| 发布时间 | 2026-03 |
| 主类 | Latent World Model / JEPA |
| 副类标签 | end\_to\_end\_JEPA, SIGReg, pixel\_based, fast\_planning |


一句话总结：LeWorldModel 是稳定的 end-to-end pixel JEPA world model，用极简 two-loss objective 解决 representation collapse。

出发点：现有 JEPA/world model 训练常依赖复杂多项 loss、EMA、冻结 encoder 或辅助监督，工程脆弱。

核心方法：只用 next-embedding prediction loss 和 Gaussian latent regularizer，后者通过随机投影和 normality test 风格约束防止 collapse。

模型结构：encoder + latent predictor + SIGReg regularizer。约 15M 参数，可在单卡几小时训练。

数据与评测：2D/3D control tasks；计划速度相对 foundation-model-based world model 最高 48x，并通过 probing / surprise evaluation 验证 latent 含物理结构。

与 VLA / WAM 关系：它说明 world model 未必必须巨大；紧凑 latent dynamics 仍可能对 control 有价值。

### P39. NEO-Unify

| 项目 | 内容 |
| - | - |
| 类型 | HuggingFace blog + GitHub |
| 链接 | [https://huggingface.co/blog/sensenova/neo-unify](https://huggingface.co/blog/sensenova/neo-unify) / [https://github.com/OpenSenseNova/SenseNova-U1](https://github.com/OpenSenseNova/SenseNova-U1) |
| 团队 | SenseNova |
| 发布时间 | 2026-03 |
| 主类 | Native Multimodal Model |
| 副类标签 | unified\_multimodal, general\_foundation\_model, boundary\_item |


一句话总结：NEO-Unify 更偏原生多模态统一模型，不是典型 robotics VLA，但其 unified token/backbone 思想可能影响未来 embodied control。

出发点：多模态模型希望统一理解与生成，但 action/control 是否成为一等模态仍不明确。

核心方法：依据公开 blog，重点在统一多模态能力和 SenseNova-U1 生态。

模型结构：需要看是否包含 image/video/text/audio generation-understanding 统一架构。

数据与评测：当前缺少机器人 action benchmark。

与 VLA / WAM 关系：边界项。只有当它明确接入 action tokens、robot observations 或 control benchmark 时，才应升为核心项。

### P40. Cosmos Policy

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + NVIDIA project |
| 链接 | [https://arxiv.org/pdf/2601.16163](https://arxiv.org/pdf/2601.16163) / [https://research.nvidia.com/labs/dir/cosmos-policy/](https://research.nvidia.com/labs/dir/cosmos-policy/) |
| 团队 | NVIDIA / Stanford / Columbia line |
| 发布时间 | 2026-01 |
| 主类 | Video Model Policy / Planning |
| 副类标签 | Cosmos\_Predict2, latent\_diffusion\_action, future\_image, value\_prediction, planning |


一句话总结：Cosmos Policy 将预训练视频模型单阶段后训练成 robot policy，同时生成 action、future image 和 value 以支持规划。

出发点：视频生成模型有强物理/时序先验，但机器人工作常需多阶段后训练或新架构才能接入 action。

核心方法：不改架构地后训练 Cosmos-Predict2，让 robot actions 编码为 latent frames，在视频模型 latent diffusion 过程中直接生成动作。未来状态图像和 expected cumulative rewards 也被编码为 latent frames，用于 test-time planning。

模型结构：pretrained video model + single-stage robot post-training + latent action/future/value heads encoded in video latent space。

数据与评测：LIBERO、RoboCasa 和真实 ALOHA 双臂任务；公开摘要报告 LIBERO 98.5%、RoboCasa 67.1% 平均成功率，并优于强 diffusion policy 和 VLA baseline。

与 VLA / WAM 关系：这是 video foundation model policy 化的代表，和 DreamZero/Fast-WAM 一起定义 2026 WAM 讨论核心。

### P41. DreamZero / World Action Models are Zero-shot Policies

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project |
| 链接 | [https://arxiv.org/pdf/2602.15922](https://arxiv.org/pdf/2602.15922) / [https://dreamzero0.github.io/](https://dreamzero0.github.io/) |
| 团队 | NVIDIA Deep Imagination Research |
| 发布时间 | 2026-02 |
| 主类 | World Action Model |
| 副类标签 | WAM, video\_diffusion, zero\_shot\_policy, action\_conditioned\_future |


一句话总结：DreamZero 提出 World Action Model 可以直接作为零样本策略，挑战传统 VLA 先理解再动作的范式。

出发点：VLA 映射 observation/instruction 到 action，但不一定显式理解动作造成的世界变化。WAM 认为 action-conditioned dynamics 本身可以提供更强泛化。

核心方法：利用 video diffusion/world modeling 学习 physical motion 和 action-conditioned future，并从未来预测中导出可执行策略。

模型结构：video diffusion world/action model + policy extraction / zero-shot action selection。

数据与评测：重点看 novel environment / embodiment 下相对 VLA 的泛化优势。

与 VLA / WAM 关系：这是 WAM 命题最直接的代表：world model 不是辅助，而是 policy 本身。

### P42. Fast-WAM

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project |
| 链接 | [https://arxiv.org/pdf/2603.16666](https://arxiv.org/pdf/2603.16666) / [https://yuantianyuan01.github.io/FastWAM/](https://yuantianyuan01.github.io/FastWAM/) |
| 发布时间 | 2026-03 |
| 主类 | World Action Model / Efficiency |
| 副类标签 | video\_cotraining, no\_test\_time\_rollout, real\_time\_policy, LIBERO, RoboTwin |


一句话总结：Fast-WAM 证明 WAM 的主要收益可能来自训练时 video modeling，而不是推理时显式生成未来视频。

出发点：imagine-then-execute WAM 推理时需要迭代视频 denoising，时延高，不适合实时控制。

核心方法：保留训练时 video co-training，但测试时跳过 future prediction，直接执行 action。通过多个 variant 分离 video modeling during training 和 test-time future generation 的贡献。

模型结构：训练阶段 action+video 多任务；推理阶段 direct policy。

数据与评测：LIBERO、RoboTwin 和真实任务；报告 190ms latency，比 imagine-then-execute WAM 快 4x+。

与 VLA / WAM 关系：这是 WAM 部署路线的关键论文，提示“世界预测作为训练表征”可能比“推理时想象未来”更实用。

### P43. Genesis Engine

| 项目 | 内容 |
| - | - |
| 类型 | project + GitHub |
| 链接 | [https://genesis-embodied-ai.github.io/](https://genesis-embodied-ai.github.io/) / [https://github.com/Genesis-Embodied-AI/genesis-world](https://github.com/Genesis-Embodied-AI/genesis-world) |
| 发布时间 | 2026 |
| 主类 | Simulator / Generative Environment |
| 副类标签 | physics\_engine, simulation, robot\_learning\_data\_engine |


一句话总结：Genesis Engine 是面向 embodied AI 的仿真/世界生成基础设施，适合放在 world model data engine 与 sim2real 之间看。

出发点：机器人训练需要可扩展、可控、物理一致的环境；传统仿真精确但资产和场景扩展慢，生成世界模型灵活但物理可靠性不足。

核心方法：公开项目与代码指向物理仿真和世界生成基础设施，重点在可编程环境、物理交互和机器人学习接口。

模型结构：更像 engine / platform，而非单一 VLA model。

数据与评测：应看是否支持大规模并行、可微物理、传感器模拟、数据导出和 sim2real benchmark。

与 VLA / WAM 关系：它可能成为 VLA/WAM 的训练环境和数据工厂，而不是直接 policy。

### P44. GEN-1 / Beyond World Models & VLAs

| 项目 | 内容 |
| - | - |
| 类型 | Generalist AI official blog / methodology blog |
| 链接 | [https://generalistai.com/blog/apr-02-2026-GEN-1](https://generalistai.com/blog/apr-02-2026-GEN-1) / [https://generalistai.com/blog/beyond-world-models](https://generalistai.com/blog/beyond-world-models) |
| 团队 | Generalist AI |
| 发布时间 | 2026-04 |
| 主类 | Robotics Foundation Model / Methodology |
| 副类标签 | from_scratch_training, physical_interaction_data, goal_driven_research, official_blog |


一句话总结：GEN-1 是 Generalist AI 的 physical-interaction foundation model 路线，Beyond World Models & VLAs blog 则解释了为什么他们不愿把模型简单归类为 VLA 或 world model。

出发点：Generalist 的核心观点是“目标比方法标签重要”。他们认为如果有足够数据和算力，robotics model 不必依赖把现有 VLM fine-tune 成 VLA，也不必把 world model 作为唯一目标，而应围绕可测量物理能力里程碑优化。

核心方法：blog 明确说 GEN-1 约 99% 参数从 scratch 训练，不是 fine-tuned VLM 加 action head，也不是“只是 world model”，而是面向 physical interaction 的 native foundation model。它强调过去两年一直在组合 VLA、world model 和其他训练思想，但最终按能力目标组织系统，而不是按论文标签组织系统。

模型结构：公开 blog 没有给出足够 architecture 细节；可确认的信息是从 scratch training、physical interaction data 和 native model design。它与 OpenVLA/π₀/Cosmos3 这类公开论文相比，缺少模块级结构、loss、数据配方和 ablation。

数据与评测：Beyond World Models & VLAs 提到 Generalist 已有超过 50 万小时 physical interaction data，并提出一个具体里程碑：约 1 小时 task-specific robot data 下达到 99%+ success rate 会具备商业可行性。这个目标很有参考价值，但当前 blog 没有给出完整 benchmark 表。

与 VLA / WAM 关系：它是“反标签化”的重要产业观点：VLA、world model、WAM 都只是工具，真正目标是 physical AGI 的能力曲线。作为方法论和产业信号很值得读，但在没有论文/技术报告前，不应和 OpenVLA、π₀、Cosmos3、WALL-WM 这类材料同等证据权重。

### P45. UniVTAC

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project + GitHub |
| 链接 | [https://arxiv.org/pdf/2602.10093](https://arxiv.org/pdf/2602.10093) / [https://univtac.github.io/](https://univtac.github.io/) / [https://github.com/univtac/UniVTAC](https://github.com/univtac/UniVTAC) |
| 发布时间 | 2026-02 |
| 主类 | Tactile / Benchmark / Simulator |
| 副类标签 | visuo\_tactile, contact\_rich, tactile\_encoder, simulation\_data |


一句话总结：UniVTAC 是统一 visuo-tactile 数据生成、学习和 benchmark 平台，解决接触丰富任务中触觉数据稀缺问题。

出发点：很多插入、装配、灵巧操作只靠视觉不稳定，但真实触觉数据采集昂贵且缺少统一评测平台。

核心方法：仿真生成三类常用 visuo-tactile sensor 的可控接触交互数据，训练 UniVTAC Encoder，并构建 8 个代表性触觉操控任务 benchmark。

模型结构：simulation platform + visuo-tactile encoder + benchmark tasks。

数据与评测：公开摘要报告 UniVTAC Encoder 在 benchmark 上平均成功率提升 17.1%，真实机器人实验提升 25%。

与 VLA / WAM 关系：触觉是 VLA 走向 contact-rich manipulation 的关键缺口，UniVTAC 可作为 tactile grounding 数据引擎。

### P46. Tactile-VLA / TacLVA

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project |
| 链接 | [https://arxiv.org/pdf/2507.09160](https://arxiv.org/pdf/2507.09160) / [https://jialeihuang.github.io/tactileVLA.github.io/](https://jialeihuang.github.io/tactileVLA.github.io/) |
| 发布时间 | 2025-07 |
| 主类 | Tactile VLA |
| 副类标签 | tactile\_token, contact\_reasoning, manipulation |


一句话总结：Tactile-VLA 把触觉作为 VLA 的一等输入模态，目标是提升接触丰富操作的稳定性。

出发点：视觉能看到外观和几何，但无法稳定判断接触力、滑动、插入误差和材质交互。

核心方法：将 tactile observations 与视觉、语言、动作统一编码，训练模型在 manipulation task 中利用接触信息决策。

模型结构：视觉 encoder + tactile encoder + language/action policy。关键问题是触觉时序如何 token 化，以及 tactile feature 是否参与 action head。

数据与评测：应关注真实触觉传感器、仿真触觉数据、跨物体/跨材质泛化。

与 VLA / WAM 关系：它扩展了 VLA 的模态边界；如果未来 WAM 要预测 contact dynamics，触觉几乎不可或缺。

### P47. RoboClaw

| 项目 | 内容 |
| - | - |
| 类型 | arXiv |
| 链接 | [https://arxiv.org/pdf/2603.11558](https://arxiv.org/pdf/2603.11558) |
| 发布时间 | 2026-03 |
| 主类 | Robotic Manipulation |
| 副类标签 | manipulation, benchmark\_or\_policy, needs\_deep\_read |


一句话总结：RoboClaw 当前确认有公开 arXiv，但需要进一步深读确认它是 policy、hardware、benchmark 还是 manipulation framework。

出发点：从命名看可能与抓取/夹爪/操作稳定性相关。

核心方法：需读原文确认。

模型结构：未在本轮核验到足够一手细节。

数据与评测：应重点看是否有真实机器人实验、动作空间、任务集合和失败分析。

与 VLA / WAM 关系：暂作为 manipulation 相关候选，不进入核心 VLA/WAM 主线判断。

### P48. CoT-VLA

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project |
| 链接 | [https://arxiv.org/pdf/2503.22020](https://arxiv.org/pdf/2503.22020) / [https://cot-vla.github.io/](https://cot-vla.github.io/) |
| 发布时间 | 2025-03 |
| 主类 | Reasoning VLA |
| 副类标签 | chain\_of\_thought, task\_decomposition, language\_reasoning |


一句话总结：CoT-VLA 尝试把 chain-of-thought 式中间推理引入 VLA，以增强复杂任务规划和语言 grounding。

出发点：端到端 VLA 在多步骤任务中容易缺少显式任务分解，模型可能直接把图像映射到动作而忽略高层计划。

核心方法：引入文本或结构化 reasoning trace，使模型在动作前先生成/利用中间任务推理。

模型结构：VLM/VLA backbone + CoT reasoning head/context + action head。

数据与评测：关键要看 CoT 是人工标注、自动生成还是自监督，以及是否提升真实 closed-loop success。

与 VLA / WAM 关系：它属于 reasoning/action coupling 线。风险是 CoT 好看但不一定改善低层动作 credit assignment。

### P49. ThinkAct

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project |
| 链接 | [https://arxiv.org/pdf/2507.16815](https://arxiv.org/pdf/2507.16815) / [https://jasper0314-huang.github.io/thinkact-vla/](https://jasper0314-huang.github.io/thinkact-vla/) |
| 发布时间 | 2025-07 |
| 主类 | Reasoning VLA |
| 副类标签 | think\_then\_act, long\_horizon, planning\_control |


一句话总结：ThinkAct 走“先思考再行动”的 VLA 路线，将高层 reasoning 和低层 action 分解以应对复杂长时程任务。

出发点：VLA 如果把 reasoning 和 action 完全混在同一 token 序列里，可能在长时程任务中难以稳定对齐。

核心方法：显式分离或交替建模 thinking phase 与 acting phase，让模型在执行前建立任务语义和阶段计划。

模型结构：reasoning module / thought token + action generation module。

数据与评测：要看推理时延、错误 thought 对 action 的影响、真实任务成功率。

与 VLA / WAM 关系：它和 BagelVLA 的语言规划思路相关，但不一定包含视觉未来预测。

### P50. ACoT-VLA

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + GitHub |
| 链接 | [https://arxiv.org/pdf/2601.11404](https://arxiv.org/pdf/2601.11404) / [https://github.com/AgibotTech/ACoT-VLA](https://github.com/AgibotTech/ACoT-VLA) |
| 团队 | AgibotTech |
| 发布时间 | 2026-01 |
| 主类 | Reasoning VLA |
| 副类标签 | action\_chain\_of\_thought, agibot, reasoning\_control |


一句话总结：ACoT-VLA 把 CoT 进一步动作化，关注 action chain-of-thought 如何辅助 VLA 控制。

出发点：文本 CoT 可能和动作执行脱节，action-oriented CoT 更可能直接表达阶段、子动作和控制意图。

核心方法：将 action reasoning trace 与 policy learning 结合，可能使用 AgiBot 数据生态和开源实现。

模型结构：VLA backbone + action CoT representation + action decoder。

数据与评测：应重点看是否在长时程、多阶段任务上优于普通 VLA 和 CoT-VLA。

与 VLA / WAM 关系：它属于 reasoning-action interface 研究，能与 world model 的未来状态预测互补。

### P51. UniVLA

| 项目 | 内容 |
| - | - |
| 类型 | arXiv + project |
| 链接 | [https://arxiv.org/pdf/2506.19850](https://arxiv.org/pdf/2506.19850) / [https://robertwyq.github.io/univla.github.io/](https://robertwyq.github.io/univla.github.io/) |
| 发布时间 | 2025-06 |
| 主类 | Unified VLA / Latent Action |
| 副类标签 | unified\_policy, latent\_action, cross\_embodiment, world\_model\_related |


一句话总结：UniVLA 是统一 VLA / latent action 线的重要工作，建议和 UniAct、X-VLA、AgiBot GO-1 一起读。

出发点：VLA 的跨任务和跨 embodiment 泛化受制于动作表征、数据异构和训练目标不统一。

核心方法：从命名和相关生态看，重点在统一视觉语言动作建模与 latent/universal action 表征。

模型结构：VLA backbone + unified action representation / latent action module。

数据与评测：需要深读确认其训练数据、benchmark 和是否开源。

与 VLA / WAM 关系：它位于 VLA 和 action representation 主线，是理解 2025 年 cross-embodiment VLA 的补充材料。

### P52. Cosmos3: Omnimodal World Models for Physical AI

| 项目 | 内容 |
| - | - |
| 类型 | NVIDIA technical report + code/model/dataset/benchmark release |
| 链接 | [https://research.nvidia.com/labs/cosmos-lab/cosmos3/technical-report.pdf](https://research.nvidia.com/labs/cosmos-lab/cosmos3/technical-report.pdf) |
| 资料备注 | PDF 已用于本地抽取分析；仓库内不收录 PDF，保留官方链接 |
| 团队 | NVIDIA |
| 发布时间 | 2026-06-01 |
| 主类 | Omnimodal World Model / Native Multimodal / WAM backbone |
| 副类标签 | language_image_video_audio_action, MoT, flow_matching, OpenMDW, Cosmos_HUE, RoboArena |

一句话总结：Cosmos3 是 NVIDIA 把 VLM、视频生成器、world simulator 和 world-action model 收束到同一 omnimodal MoT backbone 的 Physical AI 技术报告。

出发点：Physical AI 需要同时理解世界、生成世界、预测动作后果和输出可执行动作；把 VLM、video model、WAM、simulator 拆成多个模型会造成表示割裂、系统复杂和迁移成本。Cosmos3 的核心问题是：能不能用同一个 scalable backbone 处理 language、image、video、audio、action。

核心方法：Cosmos3 将语言、视觉、音频、动作都编码到统一 token 空间，并用 Mixture-of-Transformers 处理。AR subsequence 负责 reasoning / next-token prediction，diffusion subsequence 负责非语言模态的 denoising generation；action 被视为核心模态，而不是额外附加 head。

模型结构：模态特定 encoder + 统一 token arrangement + MoT dual-tower layer。Reasoner tower 处理 AR tokens，Generator tower 处理 diffusion tokens，两者共享 self-attention 交互。视觉理解使用 ViT 类 encoder，视觉生成使用 video VAE，action 使用专门 tokenization，非语言生成用 flow matching 预测 velocity。

训练数据与训练方式：报告区分 reasoner training、generator training、text-to-image post-training、image-to-video post-training 和 robot policy post-training。开源资源包括 Cosmos3-Super、Cosmos3-Nano、Cosmos3-Nano-Policy-DROID，以及 SDG-PhyxSim、SDG-RobotSim、SDG-DriveSim、SDG-SynHuman、SDG-Warehouse 等合成数据。

评测与 insight：技术报告称 post-trained Cosmos3 在 Artificial Analysis open-source T2I/I2V 上排名靠前，并在 RoboLab / RoboArena robot policy benchmark 上达到当时 open leaderboard 第一。Robot policy post-training 使用 DROID，模型 15Hz 输出 32-step action horizon，推理时跳过 video-latent decoding 降低开销。

与 VLA / WAM 关系：Cosmos3 是 Cosmos Policy 的上位统一化：不是只把视频模型后训练为 policy，而是让 action 成为 omnimodal world model 的原生模态。它对 WAM 的长期影响在于提供统一 backbone，但短期风险是模型过大、训练复杂、action domain specialization 仍需后训练。

核心框图速记：`language/image/video/audio/action → modality encoders → AR + diffusion token arrangement → MoT reasoner/generator → understanding/generation/world simulation/policy`。

### P53. WALL-WM: Carving World Action Modeling at the Event Joints

| 项目 | 内容 |
| - | - |
| 类型 | technical report + GitHub |
| 链接 | [https://x2robot.com/api/files/file/WALL-WM.pdf](https://x2robot.com/api/files/file/WALL-WM.pdf) / [https://github.com/X-Square-Robot/wall-x](https://github.com/X-Square-Robot/wall-x) |
| 资料备注 | PDF 已用于本地抽取分析；仓库内不收录 PDF，保留官方链接 |
| 团队 | X Square Robot Team / 自变量机器人 |
| 发布时间 | 2026-06-01 |
| 主类 | Event-grounded World Action Model |
| 副类标签 | semantic_event, variable_length_execution, staircase_decoding, view_interaction_attention, Muon_optimizer |

一句话总结：WALL-WM 的核心贡献是把 WAM 的学习原子从固定 action chunk 改成 action-grounded semantic event，让语言、视频和动作在更自然的事件边界上对齐。

出发点：多数 WAM/VLA 用固定长度 action chunk 训练，但语言描述的是目标和事件，视觉按连续动力学演化，动作在控制频率上执行。固定 chunk 会把三个时间尺度硬切到同一个窗口，导致短时相关拟合、覆盖预训练视觉语义先验，并削弱长时程组合泛化。

核心方法：WALL-WM 用 event-grounded WAM pretraining：为视频和动作段配 event captions，把 reach、grasp、lift、move、place 这类语义一致的 executable behavior 作为训练单元。它支持两个推理模式：event mode 由 VLM/人/agent 给下一事件描述并执行 variable-length video-action segment；unified mode 用 VLM + Staircase Decoding 生成 event-structured latent reasoning，仍可做固定长度 chunk inference。

模型结构：视频 tower 继承 Wan-style flow matching，动作 tower 是 action DiT，并在每层 cross-attend 到匹配的视频 DiT layer features。多视角部分用 View-Interaction Self-Attention、Camera RoPE、sight-cone mask 和 tube patch masking 强化 3D-aware cross-view consistency。

训练数据与训练方式：数据生态由 event-level captions、cluster-balanced sampling 和 Muon optimizer 大规模预训练支撑。训练分视频 flow-matching、event-centric action dynamics、event-conditioned inference 和 latent CoT / Staircase Decoding 等部分。

评测与 insight：WALL-WM 在 embodied video generation benchmark 上相对 Wan baselines 改善 physical plausibility、interaction quality 和 trajectory accuracy。真实机器人评测使用 Task Progress 而非单纯 binary success；Generalization suite 中 event-mode 平均 Task Progress 53.75，对比 DreamZero 28.50、π0.5 24.00 和 WALL-WM-U-Scratch 18.50。ablation 中 event-mode + view interaction 将 Reasoning Manipulation 平均分从 32.6 提到 71.6，将 Generalization 从 22.0 提到 53.75。

与 VLA / WAM 关系：WALL-WM 是对 WAM 粒度的关键修正。Fast-WAM/GigaWorld-Policy 主要关注推理成本，WALL-WM 关注学习原子和语义边界：如果 action/video/language 的边界切错了，模型再大也容易学到 chunk shortcut。

核心框图速记：`global instruction + multi-view video → event captions / event segments → video-action flow matching + action DiT → event mode variable-length execution 或 unified chunk inference`。

### P54. GigaWorld-Policy

| 项目 | 内容 |
| - | - |
| 类型 | project page + arXiv + models/code links |
| 链接 | [https://gigaai-research.github.io/GigaWorld-Policy/](https://gigaai-research.github.io/GigaWorld-Policy/) |
| 团队 | GigaAI |
| 发布时间 | 2026-03 |
| 主类 | Action-centered World Action Model |
| 副类标签 | GigaWorld_0_5, train_complex_infer_simple, action_only_decoding, causal_mask, real_time_control |

一句话总结：GigaWorld-Policy 是 action-centered WAM：训练时让 action prediction 受 future video dynamics 监督，推理时完全丢弃未来视频预测，只保留动作生成以满足实时控制。

出发点：现有 WA/WAM 常同时推理未来视觉动态和动作，带来两类瓶颈：一是视频预测链路太长导致推理成本高；二是视觉和运动表征耦合过强，使动作预测质量受未来视频预测质量牵制。GigaWorld-Policy 的问题意识与 Fast-WAM 一致，但更工程化地提出“训繁推简”。

核心方法：基于轻量 world model GigaWorld-0.5 构建。训练阶段通过 causal mask 统一建模 action tokens 与 future visual tokens，让动作预测受未来视觉动态提供的高密度监督；推理阶段舍弃视频预测分支，只保留 action decoding。架构上将 visual observations、robot states、action sequences 映射到共享 embedding，由单一 Transformer backbone 处理。

训练数据与训练方式：三阶段 pipeline：第一阶段用海量互联网视频训练通用世界模型；第二阶段用千小时级多源操作视频做具身场景适配，覆盖 first-person、real-robot、simulation；第三阶段用少量动作标注真机数据对齐 observation-action-future visual 因果映射。

评测与 insight：项目页报告相对 baseline 实现 10x inference speedup，单次推理 0.36 秒，同时任务成功率提升 35%。在四类真实机器人任务中，平均成功率接近 85%，相对 Cosmos Policy 等主流 WA 模型约 55% 提升近 30 个百分点；并强调相对 Motus 推理速度 10x。

与 VLA / WAM 关系：GigaWorld-Policy 是 Fast-WAM 方向的强补充：世界预测不一定要在推理时显式生成，但训练时未来视觉动态仍是关键监督。它和 WALL-WM 的区别是：GigaWorld-Policy 改推理路径，WALL-WM 改训练粒度。

核心框图速记：`internet/robot/sim video → GigaWorld-0.5 world pretrain → action+future visual joint training → inference drops video branch → action-only real-time policy`。

## 6. 对 VLA / World Model / World Action Model 的迁移启发

1. **不要只比较模型大小，要比较 action interface。** RT-2/OpenVLA 的 action token、π₀/SmolVLA 的 flow action expert、LAP 的 language action、RDT 的 unified physical action space，代表的是完全不同的控制接口。

2. **world model 的价值不止“生成好看的未来视频”。** Ctrl-World 用它 ranking policy 和合成成功轨迹，WMPO 用它做 on-policy RL，VLAW 用它与 policy 迭代共进，Fast-WAM 则说明它也可能只在训练时作为表征约束就够了。

3. **跨 embodiment 的核心不是把所有数据混在一起，而是对齐动作语义。** Open X-Embodiment 解决数据混合，LAP/UniAct/Action Space Design 解决动作表示，χ₀/Kai0 解决部署分布一致性。

4. **真实部署会惩罚“只在 benchmark 上漂亮”的模型。** 长时程、接触丰富、动态场景、布料、humanoid 都会暴露时延、分布漂移、动作平滑、失败恢复等问题。

5. **未来的强路线很可能是三件事合体：VLA policy + world model data engine + post-training/RL。** 单靠大规模 BC 或单靠 world model 都不够，真正的系统会把真实 rollout、合成 rollout、失败挖掘、RL/preference 和部署监控接成闭环。

6. **新增 WAM 工作显示“推理时是否生成未来视频”已经不是唯一关键，粒度和接口同样重要。** Fast-WAM/GigaWorld-Policy 说明 future video 更像训练阶段 dense supervision；WALL-WM 说明固定长度 chunk 可能是错误训练原子；Cosmos3 说明最终可能要把 action 作为 omnimodal backbone 的原生模态。

## 7. 值得继续跟进的问题

1. WAM 推理时到底需不需要显式 future imagination，还是 video prediction 只需要作为训练辅助任务？

2. action token、language action、latent action、flow action expert 哪一种最适合跨 embodiment？

3. world model 生成数据如何避免把错误物理规律灌进 policy？

4. VLA 的 reasoning 文本是否真的参与控制，还是只是解释层面的副产物？

5. 触觉、力觉、proprioception 如何与 native multimodal backbone 统一？

6. 长时程任务里，phase / memory / hierarchy 是否比继续扩大 backbone 更有效？

7. 真实机器人 RL 的 sample efficiency 能否靠 pixel world model / video foundation model 实质突破？

8. WAM 的训练原子应该是 fixed-length chunk、latent action、language action，还是 WALL-WM 式 semantic event？

9. Omnimodal backbone 如 Cosmos3 是否会统一 VLA/WAM/world simulator，还是会因为后训练成本过高而只成为强 initialization？

10. Generalist AI 的从 scratch 训练路线如果有足够 physical interaction data，是否会削弱 web-scale VLM prior 在机器人里的必要性？

## 8. 资料来源索引

本报告优先使用用户提供的一手链接，并补充核验了以下公开页面：

- RT-1: [https://arxiv.org/pdf/2212.06817](https://arxiv.org/pdf/2212.06817)

- PaLM-E: [https://arxiv.org/pdf/2303.03378](https://arxiv.org/pdf/2303.03378)

- RT-2: [https://arxiv.org/pdf/2307.15818](https://arxiv.org/pdf/2307.15818)

- Open X-Embodiment: [https://arxiv.org/pdf/2310.08864](https://arxiv.org/pdf/2310.08864)

- Prismatic VLMs: [https://github.com/TRI-ML/prismatic-vlms](https://github.com/TRI-ML/prismatic-vlms)

- OpenVLA: [https://openvla.github.io/](https://openvla.github.io/) / [https://huggingface.co/openvla/openvla-7b](https://huggingface.co/openvla/openvla-7b)

- π₀: [https://arxiv.org/pdf/2410.24164](https://arxiv.org/pdf/2410.24164) / [https://www.pi.website/blog/pi0](https://www.pi.website/blog/pi0)

- RDT-1B: [https://github.com/thu-ml/RoboticsDiffusionTransformer](https://github.com/thu-ml/RoboticsDiffusionTransformer)

- GR00T N1: [https://research.nvidia.com/publication/2025-03\_nvidia-isaac-gr00t-n1-open-foundation-model-humanoid-robots](https://research.nvidia.com/publication/2025-03_nvidia-isaac-gr00t-n1-open-foundation-model-humanoid-robots)

- AgiBot World: [https://opendrivelab.com/AgiBot-World/](https://opendrivelab.com/AgiBot-World/)

- Gemini Robotics: [https://deepmind.google/models/gemini-robotics/](https://deepmind.google/models/gemini-robotics/)

- SmolVLA: [https://huggingface.co/blog/smolvla](https://huggingface.co/blog/smolvla)

- Genie 2: [https://deepmind.google/blog/genie-2-a-large-scale-foundation-world-model/](https://deepmind.google/blog/genie-2-a-large-scale-foundation-world-model/)

- Genie 3: [https://deepmind.google/blog/genie-3-a-new-frontier-for-world-models/](https://deepmind.google/blog/genie-3-a-new-frontier-for-world-models/)

- Ctrl-World: [https://arxiv.org/pdf/2510.10125](https://arxiv.org/pdf/2510.10125)

- GigaWorld-0: [https://arxiv.org/pdf/2511.19861](https://arxiv.org/pdf/2511.19861)

- WMPO: [https://wm-po.github.io/](https://wm-po.github.io/)

- VLAW: [https://sites.google.com/view/vlaw-arxiv](https://sites.google.com/view/vlaw-arxiv)

- LAP: [https://lap-vla.github.io/](https://lap-vla.github.io/)

- RynnVLA-002: [https://github.com/alibaba-damo-academy/RynnVLA-002](https://github.com/alibaba-damo-academy/RynnVLA-002)

- Being-H0.7: [https://research.beingbeyond.com/being-h07](https://research.beingbeyond.com/being-h07)

- Motus: [https://motus-robotics.github.io/motus](https://motus-robotics.github.io/motus)

- LingBot-VA: [https://technology.robbyant.com/lingbot-va](https://technology.robbyant.com/lingbot-va)

- ThinkJEPA: [https://arxiv.org/pdf/2603.22281](https://arxiv.org/pdf/2603.22281)

- LeWorldModel: [https://le-wm.github.io/](https://le-wm.github.io/)

- Cosmos Policy: [https://research.nvidia.com/labs/dir/cosmos-policy/](https://research.nvidia.com/labs/dir/cosmos-policy/)

- DreamZero: [https://dreamzero0.github.io/](https://dreamzero0.github.io/)

- Fast-WAM: [https://yuantianyuan01.github.io/FastWAM/](https://yuantianyuan01.github.io/FastWAM/)

- Cosmos3: [https://research.nvidia.com/labs/cosmos-lab/cosmos3/technical-report.pdf](https://research.nvidia.com/labs/cosmos-lab/cosmos3/technical-report.pdf)

- WALL-WM: [https://x2robot.com/api/files/file/WALL-WM.pdf](https://x2robot.com/api/files/file/WALL-WM.pdf) / [https://github.com/X-Square-Robot/wall-x](https://github.com/X-Square-Robot/wall-x)

- GigaWorld-Policy: [https://gigaai-research.github.io/GigaWorld-Policy/](https://gigaai-research.github.io/GigaWorld-Policy/)

- Generalist AI / Beyond World Models & VLAs: [https://generalistai.com/blog/beyond-world-models](https://generalistai.com/blog/beyond-world-models)

- UniVTAC: [https://univtac.github.io/](https://univtac.github.io/)
