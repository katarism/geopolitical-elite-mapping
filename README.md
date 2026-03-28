# 地缘政治精英网络图谱工具 (Geopolitical Elite Mapping Tool)

这是一个专为地缘政治研究设计的精英网络制图工具包。它利用 Graphviz 生成高精度的静态关系图，并利用 Cytoscape.js 生成支持交互、完全离线的 HTML 查看器。

> [!TIP]
> - **在线演示**: [中东石油地缘政治精英网络在线交互演示 (Live Demo)](https://katarism.github.io/geopolitical-elite-mapping/Examples/MiddleEastOil/MiddleEastOil_EliteNetwork_Viewer.html)
> - **分析报告**: [中东石油地缘政治精英网络深度分析报告 (Analysis Report)](https://github.com/katarism/geopolitical-elite-mapping/blob/main/Examples/MiddleEastOil/MiddleEastOil_EliteNetwork_Report.md)

## 🌟 核心功能

- **静态 SVG 生成**: 自动将置信度级别（HIGH/MED/LOW/INFERRED）编码为视觉样式（线宽、颜色、虚实），确保数据的透明度与诚实性。
- **交互式 HTML 查看器**: 一个功能强大且独立的查看系统：
  - **阵营聚类 (Compound Nodes)**: 自动将行为体划分为不同的战略或战术板块。
  - **动态筛选**: 支持按节点类型或置信度进行实时无损筛选。
  - **详情面板**: 点击实体可查看完整元数据、注释及关联关系列表。
  - **完全离线**: 所有依赖项（JS/CSS）均已内联，无需联网即可打开。
- **方法论集成**: 内置了从议题分析到置信度审计的完整地缘政治分析工作流（参考 `SKILL.md`）。

> [!TIP]
> **布局建议**: 在交互式查看器中，推荐在左侧侧边栏的“布局”下拉菜单中选择 **网格 (grid)**。对于具有复合节点（阵营划分）的复杂网络，网格布局能提供最清晰、最规整的模块化视觉呈现。

## 🛠 环境要求

- **Python**: 3.10 或更高版本。
- **Graphviz**: 必须安装 `dot` 命令并将其添加到系统环境变量 PATH 中。
  - macOS: `brew install graphviz`
  - Ubuntu: `sudo apt-get install graphviz`
  - Windows: [前往 Graphviz.org 下载](https://graphviz.org/download/)

## 🚀 快速上手

1. **克隆项目**:
   ```bash
   git clone https://github.com/katarism/geopolitical-elite-mapping.git
   cd geopolitical-elite-mapping
   ```

2. **运行生成脚本**:
   ```bash
   python3 generate_elite_network.py
   ```
   脚本将自动生成：
   - `[议题]_EliteNetwork.svg` (静态图谱)
   - `[议题]_EliteNetwork_Viewer.html` (交互查看器)

3. **自定义数据**:
   编辑 `generate_elite_network.py` 中的 `NODES`、`EDGES` 和 `GROUPS` 字典，即可开始映射你自己的研究领域。

## 📂 项目结构

- `generate_elite_network.py`: 核心引擎，负责数据解析与多格式输出。
- `viewer_template.html`: 交互式 UI 渲染引擎模块。
- `SKILL.md`: 地缘政治分析方法论与视觉规范标准。
- `references/`: 置信度定义与视觉编码的详细技术文档。
- `Examples/`: 已完成的地缘政治网络案例。

## 📜 引用与许可

本项目基于 **alphamao2** 的 [AlphaMao1/AlphaMao-technology-mapping](https://github.com/AlphaMao1/AlphaMao-technology-mapping.git) 二次开发，遵循 **MIT** 开源协议。

---
© 2026 Geopolitical Elite Mapping Projects.
