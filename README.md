# 地缘政治精英网络图谱工具 (Geopolitical Elite Mapping Tool)

> 🟢 **[2026-03-28 更新] 🚀 全新加入：无代码可视化编辑器 (Visual Editor)！**
> 本项目现已内置功能强大的离线可视化编辑器，支持在浏览器中直接对生成的图谱进行零代码增删改查，并随时导出！详细说明见下方 **可视化编辑器 (Editor)** 章节。

这是一个专为地缘政治研究设计的精英网络制图工具包。它利用 Graphviz 生成高精度的静态关系图，并利用 Cytoscape.js 生成支持交互、完全离线的 HTML 查看器。

> [!TIP]
> - **在线演示**: [中东石油地缘政治精英网络在线交互演示 (Live Demo)](https://katarism.github.io/geopolitical-elite-mapping/Examples/MiddleEastOil/MiddleEastOil_EliteNetwork_Viewer.html)
> - **分析报告**: [中东石油地缘政治精英网络深度分析报告 (Analysis Report)](https://github.com/katarism/geopolitical-elite-mapping/blob/main/Examples/MiddleEastOil/MiddleEastOil_EliteNetwork_Report.md)

## 🎨 可视化编辑器 (Visual Editor)

在最新的更新中，我们在 `Editor/` 目录下为您提供了一个完全离线、所见即所得的图谱编辑器 `editor.html`。无论您是不熟悉 Python 脚本，还是希望在生成的底图上做快速微调，编辑器都能通过极度稳健的 UI 满足您的需求。

![可视化编辑器截图](Editor/screenshot.png) *(请在此路径存放您的实际编辑器截图)*

### 它能做什么？ (核心编辑功能)
- **零代码编辑**: 彻底脱离 Python 脚本。在左上角一键切换进入“✏️ 编辑模式”。
- **全方位节点管理**:
  - **右键极速操作**: 在画布空白处右键即可添加新节点；右键现有节点即可编辑标签、阵营、置信度，甚至追加长篇备注。
- **稳健的连线管理**:
  - **动态侧边栏编辑**: 在“节点详情”面版中，直接点击关系列表的文字即可修改该连线属性。彻底告别在画布上艰难瞄准、右键 1px 细丝连线的痛苦。
  - **下拉检索连线引擎**: 新增连线时，采用业界绝对稳健的“输入搜索+下拉双保险” Modal 弹窗。海量节点也能 100% 毫无差池地穿针引线。
- **动态阵营框归属**: 拖拽节点，它会自动被吸附计算或移出相应的阵营群组 (Compound Group)，无需重写任何底层关系逻辑。
- **多格式导出工作流**: 
  - 支持将手作修改后的图谱一键导出为标准的 `data.json`。
  - 支持直接将当前视图导出为高清无损 `PNG` 或 `SVG` 矢量图用于正式报告报告。

### 开始编辑：您需要准备什么？
1. **准备底包数据**: 无论是修正错漏，还是从零开始捏造，您都需要先运行一下主目录下的 `generate_elite_network.py` ，提取它生成的 `Viewer.html` 文件或 `data.json` 作为基座。
2. **准备 Git 仓库 (云储存)**: 编辑器利用 GitHub API 实现了数据的无缝云端存储与同步。您**必须准备一个 GitHub 仓库**（推荐 Private 项目以保护隐私）。
   - 为最大程度降低 Token 泄露风险，强烈建议您前往 GitHub Developer Settings 申请 **Fine-grained personal access token**，避免使用范围过大的 Classic Token。
   - 配置该 Token 时，在 **Repository access** 选项卡下选择 `Only select repositories`，并**仅指定**您用于存放数据的目标仓库。
   - 在 **Permissions -> Repository permissions** 层级中，找到 **Contents** 权限并将其设置为 `Access: Read and write`。其余所有权限均不需要。
3. **打开编辑器**: 双击使用现代浏览器打开 `Editor/editor.html`（纯前端 HTML 组件，通信直连 GitHub API，**绝无第三方服务端拦截，保护您的研究隐私**）。
4. **绑定与开工**:
   - 界面启动后会要求您配置上述您的 GitHub Token、首选数据仓库 (如 `owner/repo-name`) 以及想存放的 Case 子目录名称。
   - 随后按提示导入初始数据。您可以直接将 Python 生成的 `Viewer.html`、或是之前的 `data.json` 丢入解析框中。
   - 每次点击右上角的 **“💾 保存”**，您的改动将自动作为 commit 发送到您的 GitHub 个人私有仓库中！无需再忍受本地文件的散乱。同时支持随时导出本地 JSON 备份。

---

## 🌟 核心引擎功能 (HTML Viewer 引擎)

- **静态 SVG 生成**: 自动将置信度级别（HIGH/MED/LOW/INFERRED）编码为视觉样式（线宽、颜色、虚实），确保数据的透明度与诚实性。
- **交互式 HTML 查看器**: 一个功能强大且独立的查看系统：
  - **时代纪元分析 (Temporal Segmentation)**: 支持将网络拆分为不同的历史时期（如冷战、多极博弈、新秩序），捕捉权力杠杆的动态转移。
  - **按纪元注记**: 复杂的关系在不同纪元可拥有完全不同的事件描述，消除历史信息冗余。
  - **交互式时间线**: 底部集成水平时间线及侧边栏纪元筛选器，支持单纪元独显或多纪元叠加。
  - **阵营聚类 (Compound Nodes)**: 自动将行为体划分为不同的战略或战术板块。
  - **动态筛选**: 支持按节点类型或置信度进行实时无损筛选。
  - **详情面板**: 点击实体可查看完整元数据、注释及按纪元分组的关系列表。
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
