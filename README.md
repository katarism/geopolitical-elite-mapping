# 地缘政治关键团体网络图谱工具 (Geopolitical Key Actors Network Mapping)

*AI 驱动的地缘政治权力网络分析与可视化平台*

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Python 3.8+](https://img.shields.io/badge/Python-3.8%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Graphviz](https://img.shields.io/badge/Graphviz-required-orange)](https://graphviz.org/)

> **最近更新 (2026-03-28)**：可视化编辑器与 AI 辅助生成流程已完善。

一款地缘政治权力网络的脉络绘制工具。结合 **AI 自动化建模** 与 **交互式可视化编辑**，帮助您梳理复杂政治组织间的利益关系与权力结构。

**[在线演示 Live Demo](https://katarism.github.io/geopolitical-elite-mapping/Examples/MiddleEastOil/MiddleEastOil_EliteNetwork_Viewer.html)** &nbsp;|&nbsp; **[分析报告示例](https://github.com/katarism/geopolitical-elite-mapping/blob/main/Examples/MiddleEastOil/MiddleEastOil_EliteNetwork_Report.md)**

<!-- TODO: 添加图谱截图或 GIF 演示 -->
<!-- ![图谱预览](docs/assets/preview.png) -->

## 目录

1. [环境要求](#环境要求)
2. [方式一：AI 自动化建模](#方式一ai-自动化建模-agentic-workflow)
3. [方式二：交互式可视化编辑器](#方式二交互式可视化编辑器-visual-editor)
4. [项目结构](#项目结构)

## 环境要求

| 依赖 | 用途 | 安装方式 |
|------|------|----------|
| Python 3.8+ | 图谱生成脚本 | [python.org](https://www.python.org/) |
| Graphviz | SVG 渲染引擎 | macOS: `brew install graphviz` / Linux: `sudo apt-get install graphviz` |
| `graphviz`（Python） | Python Graphviz 绑定 | `pip install graphviz` |
| AI IDE（推荐） | 运行 Agentic Workflow | [Cursor](https://www.cursor.com/) / Gravity |

> **注**：可视化编辑器（`Editor/editor.html`）无需上述依赖，直接在浏览器中打开即可使用。

## 方式一：AI 自动化建模 (Agentic Workflow)

基于本项目内置的 `SKILL.md` 实现的端到端生成流程，推荐配合 **Cursor** 或 **Gravity** 等 AI IDE 使用。

### 快速开始

1. **获取仓库**：
   ```bash
   git clone https://github.com/katarism/geopolitical-elite-mapping.git
   cd geopolitical-elite-mapping
   ```

2. **在 AI IDE 中打开仓库**：使用 Cursor 或 Gravity 打开该文件夹。

3. **向 AI 提问**：输入一个地缘政治议题关键词，AI 将自动完成以下 6 个阶段：

   ```
   议题确认 → 行为体发现 → 关系调研 → 置信度审计 → 可视化输出 → 报告生成
   ```

### 输入示例

> "中东石油地缘政治" / "印太安全同盟" / "全球半导体供应链权力博弈"

### 输出产物

| 文件 | 说明 |
|------|------|
| `[Topic]_EliteNetwork.svg` | Graphviz 生成的 SVG 矢量图谱，置信度编码为视觉样式 |
| `[Topic]_EliteNetwork_Report.md` | 分析报告（行为体简介、置信度审计表、关系验证、分析结论） |
| `generate_elite_network.py` | 图谱生成脚本，可复现与手动迭代 |

## 方式二：交互式可视化编辑器 (Visual Editor)

AI 生成的图谱是起点，而非终点。编辑器让您将其转化为**持续生长的个人知识网络**——增补新行为体、修订关系判断、融入最新动态，随着您对议题理解的深入不断积累新知。编辑器为纯前端单文件应用，无需构建或本地服务器。

- **右键即刻编辑**：在空白处右键新增节点，在节点上右键修改阵营、备注或头像。
- **直观的连线管理**：通过侧边栏的"搜索+下拉"框，轻松建立人物间的复杂联系。
- **自动阵营吸附**：随意拖拽，系统会自动将节点划入对应的势力范围（阵营框）。
- **Git 云端同步**：修改通过 GitHub API 实时同步到您的仓库，确保数据安全。

### 开始使用

1. **获取 Token**：前往 [GitHub Token 申请页](https://github.com/settings/tokens?type=beta)，创建一个 `Fine-grained Token`（需勾选 `Contents: Read and Write` 权限）。
2. **打开并工作**：直接在浏览器中双击 `Editor/editor.html`，输入 Token 和仓库名，导入 Agentic Workflow 生成的 `[Topic]_EliteNetwork_Viewer.html` 即可开始。

## 项目结构

```
geopolitical-elite-mapping/
├── SKILL.md                           # AI 核心指令集（6 阶段分析方法论）
├── generate_elite_network.py          # 图谱生成核心脚本
├── viewer_template.html               # Cytoscape.js 交互查看器模板
├── index.html                         # 项目主页 / 查看器入口
├── cytoscape.min.js                   # Cytoscape.js 图形库（已打包）
├── Editor/
│   ├── editor.html                    # 可视化编辑器（纯前端单文件应用）
│   └── EliteNetworkEditor_PRD.md      # 编辑器产品需求文档
├── Examples/
│   └── MiddleEastOil/                 # 中东石油政治案例（SVG + 报告 + 查看器）
├── references/                        # 规范文档
│   ├── data_schema.md                 # 数据 Schema（节点/边类型定义）
│   ├── confidence_spec.md             # 置信度标注规范（HIGH / MED / LOW / INFERRED）
│   ├── visual_spec.md                 # 可视化样式规范
│   ├── relation_types.md              # 关系类型定义
│   └── analysis_framework.md          # 地缘政治分析框架说明
└── scripts/
    └── wikidata_geopolitics_client.py # Wikidata SPARQL 查询客户端
```

---

<div align="center">

**Geopolitical Elite Mapping** — 开源地缘政治分析工具

[MIT 许可证](LICENSE) &nbsp;|&nbsp; [在线演示](https://katarism.github.io/geopolitical-elite-mapping/Examples/MiddleEastOil/MiddleEastOil_EliteNetwork_Viewer.html) &nbsp;|&nbsp; [提交 Issue](https://github.com/katarism/geopolitical-elite-mapping/issues)

</div>
