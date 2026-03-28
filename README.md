# 地缘政治关键团体网络图谱工具 (Geopolitical Key Actors Network Mapping)

> 🟢 **[2026-03-28 更新] 🚀 重磅功能：可视化编辑器与 AI 辅助生成流程已完善！**

这是一个帮助您看懂地缘政治权力网络的脉络绘制器。它结合了 **AI 自动化建模** 与 **手动可视化微调**，帮助您在复杂的地缘政治组织及其背后的利益纽带中识破权力的形成逻辑。

---

## 🤖 方式一：AI 自动化建模 (Agentic Workflow)

这是基于本项目内置的 `SKILL.md` 实现的端到端生成流程。推荐使用 **Cursor** 或 **Gravity** 等 AI IDE 配合完成。

#### 成果示例
- **[中东石油地缘政治精英网络在线交互演示 (Live Demo)](https://katarism.github.io/geopolitical-elite-mapping/Examples/MiddleEastOil/MiddleEastOil_EliteNetwork_Viewer.html)**
- **[中东石油地缘政治精英网络深度分析报告 (Analysis Report)](https://github.com/katarism/geopolitical-elite-mapping/blob/main/Examples/MiddleEastOil/MiddleEastOil_EliteNetwork_Report.md)**

### 🚀 如何生成您的第一张图谱？

1. **获取仓库**:  
   ```bash
   git clone https://github.com/katarism/geopolitical-elite-mapping.git
   cd geopolitical-elite-mapping
   ```
2. **环境准备 (系统需安装 Graphviz)**:
   - **macOS**: `brew install graphviz`
   - **Linux**: `sudo apt-get install graphviz`
   - **Python 依赖**: `pip install graphviz`
3. **在 IDE 中打开仓库**: 使用 Cursor 或 Gravity 打开该文件夹。
4. **向 AI 提问**: 向 AI 提供一个地缘政治议题关键词或描述。
5. **AI 自动执行**: AI 将根据 `SKILL.md` 的规范，通过 6 个阶段（议题确认 -> 行为体发现 -> 关系调研 -> 置信度审计 -> 可视化输出 -> 报告生成）自动完成工作。

#### 输入示例
> "中东石油地缘政治" / "印太安全同盟" / "全球半导体供应链权力博弈"

#### 输出产物
- **`[Topic]_EliteNetwork.svg`**: Graphviz dot 生成的 SVG 矢量图谱（置信度编码进视觉）。
- **`[Topic]_EliteNetwork_Report.md`**: 文字分析报告（行为体简介 + 置信度审计表 + 关系验证 + 分析结论）。
- **`generate_elite_network.py`**: 图谱生成脚本（用于复现、后期手动迭代修改）。

---

## 🎨 方式二：交互式可视化编辑器 (Visual Editor)

当 AI 生成图谱后，您可以进入 `Editor/` 目录运行 `editor.html`，对结果进行精细化“上手”修改。

- **右键即刻编辑**: 在空白处右键新增节点，在节点上右键修改阵营、备注或头像。
- **直观的连线管理**: 通过侧边栏的“搜索+下拉”框，轻松建立人物间的复杂联系。
- **自动阵营吸附**: 随意拖拽，系统会自动帮您划入某个势力范围（阵营框）。
- **Git 云端同步**: 您的修改会通过 GitHub API 实时同步到您的 Git 仓库，确保数据安全。

### 如何开始使用编辑器？
1. **获取 Token**: 前往 GitHub 申请一个 `Fine-grained Token`（需勾选 `Contents (Read and Write)` 权限）。
2. **打开并工作**: 直接在浏览器中双击 `Editor/editor.html`，输入 Token 和仓库名，导入生成的 `data.json` 即可开工！

---

## 📂 文件夹资源说明

- `SKILL.md`: **AI 核心指令集**。详细描述了地缘政治分析的方法论与出图规范。
- `Editor/`: 可视化编辑器的所有前端逻辑与样式。
- `Examples/`: 包含“中东石油政治”等经典案例供参考。
- `generate_elite_network.py`: 负责从原始数据生成图谱的核心脚本。
- `index.html`: 交互式图谱查看器的门户。

---
© 2026 Geopolitical Elite Mapping Projects.  
本项目基于开源架构二次开发，遵循 MIT 协议。
