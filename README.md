# Towards EmbodiedAI Research Collections

> [!IMPORTANT]
> **Language Switch:** **English** | [中文版本 / Chinese](./README.zh-CN.md)

Generated: 2026-06-13

Scope: a curated technical-intelligence and must-read paper collection for Embodied AI, Vision-Language-Action models, World Models, World Action Models, and Robot Foundation Models.

## 0. Overview

This repository collects structured research notes for Embodied AI. The current collection focuses on:

1. **VLA / Robot Foundation Models:** from RT-1, RT-2, OpenVLA, pi0, and GR00T to newer native-multimodal and flow-policy systems.
2. **World Models / World Action Models:** from world-model data engines and policy improvement to action-aware world modeling such as DreamZero, Fast-WAM, WALL-WM, and Discrete-WAM.
3. **Unified multimodal infrastructure:** systems such as Cosmos3 and Seed-RF that connect understanding, generation, simulation, action, or representation forcing inside a more unified modeling stack.
4. **Reusable reading artifacts:** each must-read paper keeps a Markdown technical note plus an HTML reading version for GitHub browsing, local reading, and future expansion.

## 1. Global Technical Intelligence Report

| Entry | Description |
|---|---|
| [Markdown report](./reports/vla-worldmodel-tech-intelligence/VLA_WorldModel_Tech_Intelligence_Report.md) | Multi-paper technical map covering VLA / World Model / WAM trends, taxonomies, organization timelines, dependency relations, idea clusters, and per-work cards. |
| [HTML source](./reports/vla-worldmodel-tech-intelligence/index.html) / [web preview](https://raw.githack.com/shuluoshu/Towards-EmbodiedAI-Research-Collections/main/reports/vla-worldmodel-tech-intelligence/index.html) | HTML reading version of the global report. |
| [Core diagrams source](./reports/vla-worldmodel-tech-intelligence/core-diagrams.html) / [web preview](https://raw.githack.com/shuluoshu/Towards-EmbodiedAI-Research-Collections/main/reports/vla-worldmodel-tech-intelligence/core-diagrams.html) | Redrawn mechanism diagrams for memorization and cross-paper comparison. |

## 2. Must-Read Papers

| Area | Paper / Work | Markdown | HTML |
|---|---|---|---|
| Omnimodal World Model | Cosmos3 | [README](./must-read-papers/Cosmos3/README.md) | [source](./must-read-papers/Cosmos3/cosmos3_report.html) / [web preview](https://raw.githack.com/shuluoshu/Towards-EmbodiedAI-Research-Collections/main/must-read-papers/Cosmos3/cosmos3_report.html) |
| Cosmos3 Code / Framework | Cosmos3 Code Review | - | [source](./must-read-papers/Cosmos3/analysis-cosmos-framework-and-Code-中文解读.html) / [web preview](https://raw.githack.com/shuluoshu/Towards-EmbodiedAI-Research-Collections/main/must-read-papers/Cosmos3/analysis-cosmos-framework-and-Code-中文解读.html) |
| Native Multimodal / Representation Learning | Seed-RF | [README](./must-read-papers/Seed-RF/README.md) | [source](./must-read-papers/Seed-RF/Seed-RF-faithful-bilingual.html) / [web preview](https://raw.githack.com/shuluoshu/Towards-EmbodiedAI-Research-Collections/main/must-read-papers/Seed-RF/Seed-RF-faithful-bilingual.html) |
| Event-grounded WAM | WALL-WM | [README](./must-read-papers/WALL-WM/README.md) | [source](./must-read-papers/WALL-WM/WALL-WM-faithful-bilingual.html) / [web preview](https://raw.githack.com/shuluoshu/Towards-EmbodiedAI-Research-Collections/main/must-read-papers/WALL-WM/WALL-WM-faithful-bilingual.html) |
| Autonomous Driving WAM | Discrete-WAM | [README](./must-read-papers/Discrete-WAM/README.md) | [source](./must-read-papers/Discrete-WAM/Discrete-WAM-faithful-bilingual.html) / [web preview](https://raw.githack.com/shuluoshu/Towards-EmbodiedAI-Research-Collections/main/must-read-papers/Discrete-WAM/Discrete-WAM-faithful-bilingual.html) |

## 3. Current Focus Index

| ID | Work | Category | Why it matters |
|---|---|---|---|
| P52 | Cosmos3 | Omnimodal World Model | Unifies language, image, video, audio, and action in a Mixture-of-Transformers backbone, representing a major Physical AI foundation-model direction. |
| P53 | WALL-WM | Event-grounded WAM | Replaces fixed action chunks with action-grounded semantic events, reframing the training unit for WAMs. |
| P54 | GigaWorld-Policy | Action-centered WAM | Jointly trains on video and action while decoding actions only at inference time, directly addressing real-time WAM deployment cost. |
| N01 | Seed-RF | Native Multimodal / Pixel-space Generation | Removes the frozen-VAE bottleneck and uses internal representation tokens to guide pixel-space flow generation. |
| N02 | Discrete-WAM | Autonomous Driving World-Policy Learning | Puts future visual states, ego actions, and high-level decisions into one discrete token-editing space. |

## 4. Repository Layout

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

## 5. Curation Principles

- Prioritize papers, official technical reports, official blogs, project pages, GitHub repositories, model cards, and benchmark pages.
- Separate confirmed works from ambiguous or weakly sourced items.
- Keep both a `README.md` technical note and the corresponding HTML reading artifact for each must-read paper whenever possible.
- Avoid committing PDF binaries; preserve official links or HTML reading versions instead to keep the repository lightweight.
