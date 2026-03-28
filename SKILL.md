---
name: geopolitical-elite-mapping
version: 1.0.0
description: "地缘政治精英网络图谱工具。适用于分析特定议题下的精英集团/派系网络（政治派系、财阀、军工集团、资源控制方），通过系统性调研构建利益关系网络，输出带置信度编码的交互式 HTML 图谱查看器 + 文字分析报告。触发词：「地缘政治图谱」「精英网络」「elite network」「geopolitical mapping」「权力网络分析」。"
---

# Geopolitical Elite Network Mapping Skill (v1.0.0)

## Overview

系统性生成指定地缘政治议题下的精英网络图谱。通过 6 个阶段的工作流，完成议题框架确认、行为体发现、关系网络调研、置信度审计、可视化输出和分析报告生成。

**核心特点**：
- 🔒 **强制议题确认**：每次运行前必须与用户确认议题范围、时间范围、分析粒度，确认后才开始调研
- 🎯 **强制置信度标注**：所有节点和边必须标注 HIGH/MED/LOW/INFERRED，置信度编码进图谱视觉本身（边的粗细/虚实、节点边框样式）
- 🌐 **多层关系建模**：主边（利益关系：贸易投资、战争冲突、资源控制）+ 注释层（条约框架、思想谱系）
- 📊 **叙事竞争意识**：地缘政治数据不透明、存在叙事竞争，没有 ground truth，置信度标注是诚实性的核心保障



## When to Use

当用户需要对一个**地缘政治议题**进行精英网络分析时使用：
- "分析中东石油地缘政治中的关键势力网络"
- "冷战时期军工复合体的精英图谱"
- "印太地区安全同盟网络分析"
- "全球稀土供应链的权力网络"

**不适用**：纯历史事件叙述（无网络结构）。

## 输入与输出

### 输入
用户提供一个地缘政治议题关键词或描述：`"中东石油地缘政治"` / `"印太安全同盟"` / `"全球半导体供应链权力博弈"`

### 输出
1. **`output/[Topic]_EliteNetwork_Viewer.html`** — 交互式图谱查看器（置信度编码进视觉，内置纪元筛选器）
2. **`output/[Topic]_EliteNetwork_Report.md`** — 文字分析报告（行为体简介 + 置信度审计表 + 关系验证 + 分析结论）
3. **`generate_elite_network.py`** — 图谱生成脚本（可复现、可迭代修改）

---

## 数据 Schema

> 节点类型表、边类型表、置信度规则详见 [data_schema.md](references/data_schema.md)
> 四级置信度系统（判定标准 + 证据要求 + 视觉编码）详见 [confidence_spec.md](references/confidence_spec.md)
> 可视化节点配色/边样式/置信度视觉编码/Legend 规范详见 [visual_spec.md](references/visual_spec.md)
> 关系类型判定标准 + 边界案例详见 [relation_types.md](references/relation_types.md)
> 地缘政治分析框架详见 [analysis_framework.md](references/analysis_framework.md)

---

## 搜索工具选择规范

在任何需要执行网页搜索的步骤中，按以下流程选择工具：

```
1. 尝试调用 tavily-search 技能
2. IF tavily-search 调用成功 → 使用结果
3. IF tavily-search 未安装 OR 调用报错（ImportError / HTTP 错误 / 超时）:
   → 记录降级原因
   → 调用系统原生 search_web 工具（相同 query）
   → 使用 search_web 结果
4. IF search_web 也失败 → 标记该搜索步骤为「未完成」，继续下一步
```

---

## 执行工作流

> [!CAUTION]
> 严格按照 Phase 0 → 1 → 2 → 3 → 4 → 5 的顺序执行。每个 Phase 完成后，在内部检查输出质量，再进入下一个 Phase。

> [!CAUTION]
> **强制机制 #1**：Phase 0 未经用户明确确认，禁止进入 Phase 1。
> **强制机制 #2**：Phase 3 中，任何无置信度标注的节点/边将被拒绝进入 Phase 4。

---

### Phase 0: 议题确认 & 分析框架生成 🔒

**目的**：理解用户需求，与用户确认分析的精确边界，生成定制化的分析框架。

> [!CAUTION]
> ⛔ **强制机制**：本 Phase 必须与用户完成至少一轮确认交互。禁止跳过。禁止自行假设议题边界。

**步骤**：

1. **议题解析**：基于用户输入，分析以下维度并生成 `issue_profile` 草案：
   ```json
   {
     "issue": "中东石油地缘政治",
     "scope": {
       "geographic": ["中东", "波斯湾", "北非"],
       "thematic": ["石油定价权", "运输通道控制", "产油国联盟"],
       "excluded": ["南美石油", "天然气（除非直接相关）"]
     },
     "time_range": {
       "start": "1973（石油危机）",
       "end": "2025",
       "focus_period": "2010-2025（重点关注）"
     },
     "granularity": {
       "level": "faction",
       "description": "以国家级派系/集团为主要节点，关键个人作为辅助标注",
       "expected_node_count": "20-40"
     },
     "known_anchor_actors": [
       "OPEC", "沙特王室（MBS 派系）", "美国页岩油集团",
       "俄罗斯能源集团", "伊朗革命卫队经济体系"
     ],
     "narrative_competition": [
       "\"石油武器\"叙事 vs \"市场驱动\"叙事",
       "\"什叶vs逊尼\" vs \"民族国家利益\"框架"
     ],
     "data_opacity_warning": [
       "沙特内部派系关系不透明",
       "伊朗革命卫队经济活动数据有限",
       "非公开军售关系"
     ],
     "search_templates": {
       "actor_discovery": "[议题] key actors stakeholders power brokers",
       "relation_discovery": "[actor A] [actor B] trade investment conflict alliance",
       "treaty_discovery": "[议题] treaty agreement pact framework",
       "ideology_discovery": "[议题] ideology doctrine school of thought influence"
     }
   }
   ```

2. **与用户确认** ⛔（强制，不可跳过）：
   - 展示 `issue_profile` 摘要
   - **必须明确询问并等待用户回答**以下四个问题：
     1. 议题范围是否正确？是否有需要排除或新增的地理/主题范围？
     2. 时间范围是否合适？重点关注时段是否正确？
     3. 分析粒度（国家级 / 派系级 / 个人级）是否符合需求？
     4. 锚点行为体列表是否需要补充或修改？
   - ⚠️ 用户未回复前，禁止进入 Phase 1

3. **根据用户反馈修正** `issue_profile`：如用户要求修改，更新后再次确认。

4. **纪元划分确认** ⚠️：基于议题时间范围，向用户提议 3-5 个关键纪元划分，并确认：
   - 纪元数量和分界年份是否合适？
   - 每个纪元的标签/描述是否准确？
   - 用户是否有特定关注的纪元转折点？

**Phase 0 交付物**：`issue_profile`（议题框架 + 纪元划分方案 + 搜索策略 + 锚点列表），经用户明确确认。

---

### Phase 1: 行为体发现 (Actor Discovery)

**目的**：系统性发现议题相关的核心精英集团、派系和关键行为体。

**步骤**：

1. **搜索核心国家行为体**：使用 `search_templates.actor_discovery`，查找议题中的关键国家及其内部权力派系。
   - ⚠️ 不仅关注国家整体，更关注**国家内部的权力派系和利益集团**

2. **搜索非国家行为体**：
   - 跨国企业/财阀集团（能源巨头、军工企业、金融集团）
   - 国际组织（联合国机构、区域组织、同盟体系）
   - 非正规行为体（代理武装、游说集团、智库影响网络）

3. **搜索关键人物**：掌握决策权的核心个人（国家领导人、企业领袖、军事指挥官）。
   - ⚠️ 关键人物作为辅助标注附属于其所属的集团/派系节点，一般不单独成为主节点
   - 例外：如果某人本身就是一个权力中心（如 MBS），可以单独成为节点

4. **搜索思想/意识形态节点**：影响议题走向的意识形态和理论框架。

5. **构建时间线**：提取议题中的 3-7 个关键转折点/事件年份。

6. **Wikidata 辅助查询**：使用 `scripts/wikidata_geopolitics_client.py` 查询政治实体的 Wikidata 条目，获取正式名称、成立时间、成员国等结构化信息。

7. **互动确认** ⚠️：向用户展示已发现的行为体列表 + 时间线，等待补充。
   - 特别提示用户：是否有被遗漏的重要行为体？

**Phase 1 交付物**：Actor 列表（15-40 个节点） + 事件时间线，经用户确认。

---

### Phase 2: 关系网络调研 (Relationship Investigation)

**目的**：调研所有行为体之间的关系，区分主边（利益关系）和注释层（条约、思想谱系），并为每条关系标注初步置信度。

> ⚠️ **关系类型规则**：所有关系必须按 [relation_types.md](references/relation_types.md) 分类。主边和注释层使用不同的视觉编码。

**步骤**：

1. **调研主边关系（利益关系）**：
   - **贸易/投资关系**：搜索行为体之间的贸易协定、投资项目、经济制裁
   - **战争/冲突关系**：搜索直接军事冲突、代理战争、武器供应、军事同盟
   - **资源控制关系**：搜索能源/矿产/粮食/航道等战略资源的控制与争夺

2. **调研注释层关系**：
   - **条约/协定框架**：搜索正式条约、非正式协议、同盟条款
   - **思想/意识形态谱系**：搜索意识形态传播路径、智库影响网络、学术-政策转化链

3. **初步置信度评估**：在调研过程中，为每条关系标注初步置信度：
   - 每条关系必须记录：证据来源数量、来源类型（官方文件/学术研究/新闻报道/分析推断）、是否存在叙事竞争
   - 参见 [confidence_spec.md](references/confidence_spec.md) 的判定标准

4. **叙事竞争标注**：当同一关系存在竞争性叙事时（如"A 入侵了 B" vs "A 发起了防御性行动"），必须在关系注释中标注两种叙事，不选边站。

5. **交叉验证**：对关键关系使用多源交叉验证。特别注意：
   - 单一来源的关系 → 最高 LOW 置信度
   - 来自冲突一方的来源 → 需要对方来源或中立来源交叉验证
   - 推断性关系（从公开行为推断的隐含联盟）→ 标注为 INFERRED

6. **关系完整性检查**：逐一检查行为体，确保核心行为体之间的关键关系不被遗漏。

7. **纪元标注** ⚠️：为每个节点和每条边标注 `era` 字段（所属纪元 ID 列表）：
   - 节点：该行为体在哪些纪元活跃？新崛起的行为体只标注近期纪元；长期存在的标注多个纪元
   - 边：该关系在哪些纪元活跃？新形成的关系只标注近期；已断裂的关系只标注历史纪元
- **按纪元拆分标签 (重要)** ⚠️：对于跨越多个纪元的复杂关系，推荐将 `label` 设置为字典格式，以定义每个纪元特有的事件。
  - 示例：`{"era1": "...", "era3": "..."}`
  - 这种方式可以消除长周期关系在详情面板中的历史信息冗余。

**Phase 2 交付物**：关系边列表（含初步置信度 + 证据来源 + 纪元标注） + 叙事竞争标注清单。

---

### Phase 3: 置信度审计 & 剪枝 (Confidence Audit & Pruning)

**目的**：对所有节点和边进行严格的置信度审计，清洗数据质量。

> [!CAUTION]
> ⛔ **强制机制**：本 Phase 结束时，所有节点和边都必须有明确的置信度标注。任何无标注项禁止进入 Phase 4。

**步骤**：

1. **置信度终审**：对每个节点和每条边，按 [confidence_spec.md](references/confidence_spec.md) 进行最终置信度判定：
   - **HIGH**（高置信度）：3+ 独立来源交叉验证，含官方文件或权威学术来源
   - **MED**（中等置信度）：2 个独立来源，或 1 个权威来源 + 合理推断
   - **LOW**（低置信度）：仅 1 个来源，或来源有偏见但信息有价值
   - **INFERRED**（推断）：无直接证据，从行为模式/已知事实推断

2. **节点剪枝**：
   - 保留：议题核心行为体 / 连接 2+ 个其他核心节点 / 控制关键资源 / 关键事件参与方
   - 删除：与议题边缘相关 / 仅有 INFERRED 关系的孤立节点 / 信息过度不足
   - ⚠️ 被删除节点如果有 MED+ 置信度的关系，不能直接删除，应降级为注释

3. **边剪枝**：
   - 保留全部 HIGH/MED 关系
   - LOW 关系：仅保留对理解核心权力结构有关键意义的
   - INFERRED 关系：仅保留对填补明显认知空白有关键意义的（需在图谱中明确标注为推断）

4. **聚类分组**：按利益阵营/地理区域/议题维度聚合为 3-8 个阵营或利益集群。
   - ⚠️ 分组依据应该是**利益关系和实际行为**，而非刻板的国家/种族/宗教分组
   - 同一国家的不同派系可以分属不同阵营

5. **置信度完整性检查** ⛔：逐一扫描——
   - 每个节点是否有置信度标注？→ YES 才能进入 Phase 4
   - 每条边是否有置信度标注？→ YES 才能进入 Phase 4
   - 是否有 INFERRED 关系未标注为 INFERRED？→ 必须修正
   - 置信度分布统计：HIGH / MED / LOW / INFERRED 各占多少比例？→ 记录到报告

**Phase 3 交付物**：审计后的 graph 数据（nodes + edges + groups + confidence_audit），所有节点/边均有置信度标注。

---

### Phase 4: 可视化输出 (Interactive Viewer)

**目的**：将图数据转化为高质量、带置信度视觉编码的交互式 HTML 图谱查看器。

**技术方案**：在 `generate_elite_network.py` 中用 Python 数据结构定义节点、边、阵营、纪元，调用 `export_html()` 将数据注入 `viewer_template.html`，生成纯前端可交互的 Cytoscape.js 查看器。无需安装 Graphviz 或任何额外依赖。

**步骤**：

1. **创建/更新 `generate_elite_network.py`**，定义以下数据结构：
   - `NODES`：节点列表，每个节点含 `id`、`label`、`type`、`group`、`confidence`、`era`、`desc` 等字段
   - `EDGES`：边列表，每条边含 `source`、`target`、`rel`、`label`、`confidence`、`era` 等字段
   - `GROUPS`：阵营列表（势力框），含 `id`、`label`、`color` 字段
   - `ERAS`：纪元列表，含 `id`、`label`、`range`、`desc` 字段

2. **置信度视觉编码** ⚠️（核心差异点）— 严格遵守 [confidence_spec.md](references/confidence_spec.md) + [visual_spec.md](references/visual_spec.md)：

   置信度通过节点/边的 `confidence` 字段传入，由 `viewer_template.html` 的 Cytoscape.js 样式自动渲染：

   **节点边框置信度编码**：
   | 置信度 | 边框样式 | 边框宽度 | 效果 |
   |--------|---------|----------|------|
   | HIGH | 实线 | 粗 | 传达高确定性 |
   | MED | 实线 | 中等 | 中等确定性 |
   | LOW | 虚线 | 细 | 提示不确定性 |
   | INFERRED | 点线 | 极细 | 明确标注为推断 |

   **边置信度编码**：
   | 置信度 | 线型 | 线宽 | 效果 |
   |--------|------|------|------|
   | HIGH | 实线 | 粗 | 传达高确定性 |
   | MED | 实线 | 中等 | 中等确定性 |
   | LOW | 虚线 | 细 | 提示不确定性 |
   | INFERRED | 点线 | 极细 | 明确标注为推断 |

3. **节点配色、边样式** — 严格遵守 [visual_spec.md](references/visual_spec.md) 和 [data_schema.md](references/data_schema.md)

4. **调用 `export_html()`** 生成查看器：
   ```python
   export_html(nodes=NODES, edges=EDGES, groups=GROUPS, eras=ERAS,
               title="议题名称", output_path="output/[Topic]_EliteNetwork_Viewer.html")
   ```

5. **布局验证** — 生成查看器后按 [调试 checklist](references/visual_spec.md#6-调试-checklist) 检查

**Phase 4 交付物**：`output/[Topic]_EliteNetwork_Viewer.html` 交互式查看器 + 生成脚本。
   - 由 `export_html()` 自动生成，内置纪元筛选器 + 时间线控件
   - 用户可通过左侧「📅 时代纪元」筛选器或底部时间线切换纪元视图
   - 支持多纪元叠加、单纪元独显、全部纪元（长曝光模式）

---

### Phase 5: 分析报告 & 质量验证 📊

**目的**：生成地缘政治分析报告，让用户了解图谱的可信度、完整度和分析结论。

**步骤**：

1. **生成分析报告** (`output/[Topic]_EliteNetwork_Report.md`)：

   **报告结构**（必须包含所有章节）：

   #### 一、议题概述
   - 议题定义 + 分析框架 + 时间范围 + 分析粒度

   #### 二、行为体清单表
   ```
   | 行为体 | 类型 | 阵营 | 置信度 | 简介 | 关键来源 |
   ```

   #### 三、关系网络概述
   - 核心利益关系总结
   - 主要冲突轴线
   - 关键同盟结构

   #### 四、权力结构分析
   详见 [analysis_framework.md](references/analysis_framework.md)：
   - 权力中心识别
   - 利益冲突热点
   - 关键脆弱节点（移除某节点会导致网络重组的关键连接点）
   - 叙事竞争图景

   #### 五、置信度审计报告 ⚠️（必须包含）
   - 置信度分布统计：HIGH / MED / LOW / INFERRED 各占多少
   - 数据不透明区域标注
   - 叙事竞争影响评估

   #### 六、关系验证表 ⚠️（必须包含）
   ```
   | 行为体 A | 行为体 B | 关系类型 | Source 1 (URL) | Source 2 (URL) | 置信度 | 叙事竞争? |
   ```

   #### 七、局限性声明
   - 数据来源局限
   - 时间范围局限
   - 分析框架的偏见可能性
   - INFERRED 关系的依据和风险

2. **质量统计**：
   - 节点覆盖率（核心行为体中有多少被纳入）
   - 关系调研完成率
   - 多源验证率
   - 平均置信度

**Phase 5 交付物**：
- `output/[Topic]_EliteNetwork_Viewer.html` — 交互式图谱查看器（Phase 4 生成，Phase 5 最终确认）
- `output/[Topic]_EliteNetwork_Report.md` — 文字分析报告（含置信度审计 + 关系验证表 + 局限性声明）

---

## 依赖技能

| 技能 | 用途 | 必需 |
|------|------|------|
| `tavily-search` | **首选搜索**：环境缺失则降级为 `search_web` | 可选（有 fallback） |

### 辅助脚本

| 脚本 | 用途 |
|------|------|
| `scripts/wikidata_geopolitics_client.py` | 查询 Wikidata 政治实体信息（组织、条约、政治人物） |

### 参考文档

| 文档 | 内容 |
|------|------|
| [data_schema.md](references/data_schema.md) | 节点/边类型定义、置信度标注规则 |
| [confidence_spec.md](references/confidence_spec.md) | 四级置信度系统（判定标准 + 证据要求 + 视觉编码） |
| [visual_spec.md](references/visual_spec.md) | Graphviz 节点配色、边样式、置信度视觉编码、Legend 规范 |
| [relation_types.md](references/relation_types.md) | 关系类型判定标准 + 边界案例 |
| [analysis_framework.md](references/analysis_framework.md) | 地缘政治分析框架（权力结构、冲突热点、脆弱节点） |

---

## 重要原则

1. **强制议题确认**：⛔ Phase 0 必须与用户完成至少一轮确认。禁止自行假设议题边界。
2. **强制置信度标注**：⛔ 所有节点和边必须标注 HIGH/MED/LOW/INFERRED。无标注项禁止进入图谱。
3. **置信度即视觉**：置信度编码进图谱的边粗细/虚实和节点边框样式，不是附加注释。
4. **叙事竞争意识**：地缘政治数据存在叙事竞争。当出现竞争性叙事时标注双方观点，不选边站。
5. **诚实性优先**：不确定时标注低置信度或 INFERRED，绝不编造关系或伪装确定性。
6. **利益关系为主**：主边聚焦贸易投资、战争冲突、资源控制等利益关系。条约和思想谱系作为注释层。
7. **适度密集**：目标节点数 20-50 个（视议题复杂度调整）。
8. **按利益分组**：聚类分组基于利益关系和实际行为，不基于刻板的国家/种族/宗教标签。
9. **时间线必备**：左侧必须有时间轴锚定关键事件。
10. **中文标注**：组织/人名保留原文名 + 中文注释。
11. **可视化标准**：白底 + 置信度视觉编码，通过交互式 HTML 查看器呈现。
12. **局限性透明**：报告必须包含局限性声明，明确数据来源受限、分析框架偏见等。
