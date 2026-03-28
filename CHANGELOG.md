# Changelog

本项目遵循 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.0.0/) 规范。

---

## [Unreleased] - 2026-03-29

### Changed

- **编辑器移动端时间轴可读性优化**（`d0dbda3`）：时间轴 bar 高度 36px→40px，track 高度 18px→28px；移动端各 era 标签由横排改为上下叠放（时代名在上，年份在下），保留年份显示的同时消除换行影响阅读的问题

**路径查询交互与视觉增强**
- 路径查询选中节点时高亮其父节点（势力框），增强视觉反馈（`b8562bc`）
- 路径高亮节点增加文字颜色样式，提升路径可视化清晰度（`f9105ab`）
- 路径查询边标签样式优化（`fb1b9df`）

**移动端布局与体验**
- 修复 iOS Safari 输入框自动缩放问题（input font-size 设为 16px 阻止 Safari 自动缩放）（`fb1b9df`）
- 顶栏元素移动端适配，确保内容在 iOS 浏览器导航栏上方正常显示（`46aacf9`）
- 面板抽屉移动端布局与展开逻辑（`084236c`）
- 安全区 insets 适配 + Cytoscape 画布 resize 逻辑优化（`aba8c87`）
- 边表单节点搜索功能重构，保留选中状态（`3d0bbef`）

**文档**
- README 补充 GitHub Pages 托管编辑器的详细说明，降低移动端用户上手门槛（`74b9f74`、`46aacf9`）

---

## [1.0.0] - 2026-03-28

首个完整版本发布。从零到可发布工具，历经约 11 小时迭代。

### Added

**核心框架（阶段一，~11:07）**
- `SKILL.md`：AI 驱动的 6 阶段地缘政治分析方法论（议题确认 → 行为体发现 → 关系调研 → 置信度审计 → 可视化输出 → 报告生成）
- `generate_elite_network.py`：图谱生成核心脚本（基于 Graphviz + Cytoscape.js）
- `references/` 规范文档体系：data_schema、confidence_spec、visual_spec、relation_types、analysis_framework
- `scripts/wikidata_geopolitics_client.py`：Wikidata SPARQL 查询客户端，支持自动拉取行为体数据
- 首个案例：中东石油地缘政治精英网络（分析报告 + 可视化）

**查看器交互功能（阶段四，~16:22–17:28）**
- 交互式时间轴与 era 时代筛选（era 由每次调研自定义，随议题而异）
- 多维度 era 筛选：节点与边的 `era` 字段支持多纪元标注，配合时间轴过滤显示内容
- 路径查询系统：输入两个行为体，自动找出最短关系链
- 节点详情导出功能
- Toast 操作通知
- 移动端响应式布局与面板折叠

**交互式编辑器（阶段五–六，~18:14–20:32）**
- `Editor/editor.html`：纯前端单文件可视化编辑器，无需构建工具或本地服务器
- 右键菜单：空白处新增节点，节点上修改阵营、备注、头像
- 侧边栏边管理：searchable dropdown 搜索框建立/删除行为体关系
- 点击边直接编辑边属性
- 自动阵营吸附：拖拽节点自动归入对应势力框
- 从 AI 生成的 `_EliteNetwork_Viewer.html` 一键导入初始数据

**云端同步（阶段七，~21:xx）**
- 通过 GitHub API（Fine-grained Token）将编辑内容实时同步至 Git 仓库
- 支持 `Contents: Read and Write` 权限的细粒度 Token 配置

**发布基础设施（阶段三，~12:42–13:13）**
- `index.html`：GitHub Pages 项目落地页
- MIT License
- Live Demo 链接（中东石油案例）

---

### Changed

- 默认图谱布局由力导向（force-directed）改为网格（grid），提升初始可读性（`36d0841`）
- 边创建交互从"edge-pick 画布点击模式"改为"searchable dropdown 下拉搜索"，降低操作门槛（`d8a9edb`）

---

### Removed

- **SVG 静态输出**：彻底移除 Graphviz SVG 渲染产物，输出格式统一为交互式 HTML 查看器（`3254f18`）
  - 同步更新 `generate_elite_network.py`、`SKILL.md`、`visual_spec.md`、`.gitignore`
- 遗留的 `EliteNetwork_Viewer.html`（1968 行旧版查看器，已由模板化 `viewer_template.html` 替代）（`f7e9215`）
- 冗余的 `data.json` 配置文件（`dba1de7`）
- `EliteNetworkEditor_PRD.md`（PRD 完成使命，不纳入版本库）（`2a1b8c7`）
- `.obsidian/` 目录（不追踪编辑器配置）（`45edbed`）

---

### 架构演化轨迹

```
静态 SVG 产物（Graphviz 渲染）
    ↓ 12:46  删除遗留查看器，全面迁移 Cytoscape.js
交互式 HTML 查看器（只读）
    ↓ 16:xx  时间轴 / 多维过滤 / 路径查询
功能完整的分析型查看器
    ↓ 18:xx  Editor 诞生，导入初始数据
本地读写编辑器
    ↓ 21:xx  GitHub API 云同步
可持久化的个人知识图谱编辑器
```

---

### 核心设计决策

| 决策 | 原因 |
|---|---|
| SVG → HTML | 静态图无法支持探索式分析；Cytoscape.js 提供开箱即用的路径查询、布局算法和事件系统 |
| 查看器 + 编辑器双模块分离 | AI 生成是起点，人工持续积累才是核心价值；职责分离使两个模块可以独立演进 |
| 纯前端单文件 Editor | 零构建、零后端，浏览器双击即用；云同步是唯一需要配置的步骤，门槛可控 |
| Schema 驱动的 references/ 规范 | 将数据定义、置信度标准、关系类型从代码中剥离，AI 和人工都可以引用同一套语言 |
