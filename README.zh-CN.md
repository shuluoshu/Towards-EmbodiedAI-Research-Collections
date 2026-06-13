# Towards EmbodiedAI Research Collections

> [!IMPORTANT]
> **Language Switch / 语言切换：** [English](./README.md) | **中文**

生成日期：2026-06-13

定位：Embodied AI / VLA / World Model / World Action Model / Robot Foundation Model 技术情报与 must-read paper 汇总库。

## 0. 总览

这个仓库用于沉淀具身智能方向的结构化技术笔记，当前重点覆盖：

1. **VLA / Robot Foundation Model：** 从 RT-1、RT-2、OpenVLA、π₀、GR00T 到新一代 native multimodal / flow policy。
2. **World Model / World Action Model：** 从 world model 数据引擎、policy improvement，到 DreamZero、Fast-WAM、WALL-WM、Discrete-WAM 等 action-aware world modeling。
3. **统一多模态基础设施：** Cosmos3、Seed-RF 等把 understanding、generation、simulation、action 或 representation forcing 统一到同一模型范式中。
4. **可复用阅读材料：** 每篇重点论文保留 Markdown 技术解读和 HTML 阅读版本，便于 GitHub 浏览、离线阅读和后续继续扩展。

## 1. 全局技术情报报告

| 入口 | 内容 |
|---|---|
| [Markdown 总报告](./reports/vla-worldmodel-tech-intelligence/VLA_WorldModel_Tech_Intelligence_Report.md) | VLA / World Model / WAM 多论文技术脉络、分类图谱、组织路线、依赖关系、技术卡片 |
| [HTML 阅读版源码](./reports/vla-worldmodel-tech-intelligence/index.html) / [网页预览](https://raw.githack.com/shuluoshu/Towards-EmbodiedAI-Research-Collections/main/reports/vla-worldmodel-tech-intelligence/index.html) | 与 Markdown 总报告对应的 HTML 阅读版 |
| [核心框图源码](./reports/vla-worldmodel-tech-intelligence/core-diagrams.html) / [网页预览](https://raw.githack.com/shuluoshu/Towards-EmbodiedAI-Research-Collections/main/reports/vla-worldmodel-tech-intelligence/core-diagrams.html) | 为重点工作重画的统一风格机制框图 |

## 2. Must-Read Papers

| 方向 | 论文 / 工作 | Markdown | HTML |
|---|---|---|---|
| Omnimodal World Model | Cosmos3 | [README](./must-read-papers/Cosmos3/README.md) | [源码](./must-read-papers/Cosmos3/cosmos3_report.html) / [网页预览](https://raw.githack.com/shuluoshu/Towards-EmbodiedAI-Research-Collections/main/must-read-papers/Cosmos3/cosmos3_report.html) |
| Cosmos3 Code / Framework | Cosmos3 Code Review | - | [源码](./must-read-papers/Cosmos3/analysis-cosmos-framework-and-Code-中文解读.html) / [网页预览](https://raw.githack.com/shuluoshu/Towards-EmbodiedAI-Research-Collections/main/must-read-papers/Cosmos3/analysis-cosmos-framework-and-Code-中文解读.html) |
| Native Multimodal / Representation Learning | Seed-RF | [README](./must-read-papers/Seed-RF/README.md) | [源码](./must-read-papers/Seed-RF/Seed-RF-faithful-bilingual.html) / [网页预览](https://raw.githack.com/shuluoshu/Towards-EmbodiedAI-Research-Collections/main/must-read-papers/Seed-RF/Seed-RF-faithful-bilingual.html) |
| Event-grounded WAM | WALL-WM | [README](./must-read-papers/WALL-WM/README.md) | [源码](./must-read-papers/WALL-WM/WALL-WM-faithful-bilingual.html) / [网页预览](https://raw.githack.com/shuluoshu/Towards-EmbodiedAI-Research-Collections/main/must-read-papers/WALL-WM/WALL-WM-faithful-bilingual.html) |
| Autonomous Driving WAM | Discrete-WAM | [README](./must-read-papers/Discrete-WAM/README.md) | [源码](./must-read-papers/Discrete-WAM/Discrete-WAM-faithful-bilingual.html) / [网页预览](https://raw.githack.com/shuluoshu/Towards-EmbodiedAI-Research-Collections/main/must-read-papers/Discrete-WAM/Discrete-WAM-faithful-bilingual.html) |

## 3. 当前重点工作索引

| ID | 工作 | 主类 | 为什么值得读 |
|---|---|---|---|
| P52 | Cosmos3 | Omnimodal World Model | 把 language / image / video / audio / action 统一到 MoT backbone，代表 Physical AI 基础模型融合方向。 |
| P53 | WALL-WM | Event-grounded WAM | 用 action-grounded semantic event 替代固定 action chunk，重新定义 WAM 的训练原子。 |
| P54 | GigaWorld-Policy | Action-centered WAM | 训练时联合 video/action，推理时 action-only decoding，回应 WAM 实时部署成本。 |
| N01 | Seed-RF | Native Multimodal / Pixel-space Generation | 移除冻结 VAE 瓶颈，用内部 representation tokens 支撑 pixel-space flow generation。 |
| N02 | Discrete-WAM | Autonomous Driving World-Policy Learning | 把未来视觉、ego action 与决策统一到离散 token editing 空间。 |

## 4. 组织方式

```text
.
├── README.md
├── README.zh-CN.md
├── must-read-papers/
│   ├── Cosmos3/
│   ├── Seed-RF/
│   ├── WALL-WM/
│   └── Discrete-WAM/
└── reports/
    └── vla-worldmodel-tech-intelligence/
        ├── VLA_WorldModel_Tech_Intelligence_Report.md
        ├── index.html
        └── core-diagrams.html
```

## 5. 维护原则

- 优先收录论文、官方技术报告、官方博客、项目页、GitHub 仓库、模型卡和 benchmark 页面。
- 明确区分“已确认工作”和“待核验/边界项”，避免把名称相似但来源不足的条目混入主线。
- 每个 must-read paper 尽量保留 `README.md` 技术解读和对应 HTML 阅读材料。
- 不提交 PDF 二进制文件，报告中保留官方链接或 HTML 阅读版，保持仓库轻量。
