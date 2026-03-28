# Elite Network Editor — 产品 PRD

**产品定位：** 地缘政治精英网络图谱的主要交互工具。`.py` 脚本生成初稿，此后所有查看、编辑、注释、导出操作均在本编辑器完成。

**交付形式：** 单文件 `editor.html`，托管于现有 public repo 的 GitHub Pages。数据存储于独立 private repo，通过 GitHub Contents API 读写。

---

## 架构总览

```
public repo: katarism/geopolitical-elite-mapping
└── editor.html                  ← 本产品，GitHub Pages 托管

private repo: katarism/geopolitical-elite-mapping-data
└── {case_name}/
    └── data.json                ← 图谱数据
```

**运行时数据流：**
```
editor.html 加载
  → 读取 localStorage 中的 GitHub Token
  → GET /repos/{private}/contents/{case}/data.json  (GitHub API)
  → 解析 data.json，渲染图谱
  → 用户编辑
  → PUT /repos/{private}/contents/{case}/data.json  (GitHub API, 产生 commit)
```

---

## data.json 格式规范

此格式与 `generate_elite_network.py` 的 `export_html()` 函数输出兼容，并新增了编辑器专用字段。格式与具体 case 内容无关，适用于任何主题的网络图谱。

以下为字段定义，示例值均为占位符，不代表任何具体 case 内容：

```json
{
  "version": 2,
  "title": "<图谱标题，字符串>",
  "exported_at": "<ISO 8601 时间戳>",

  "colors": {
    "<node_type_key>": "<hex 颜色值>",
    "<node_type_key>": "<hex 颜色值>"
  },

  "eras": [
    {
      "id": "<era_id，如 era1>",
      "label": "<显示名称>",
      "range": [1900, 1950],
      "desc": "<纪元描述>"
    }
  ],

  "groups": [
    {
      "id": "<cluster_id>",
      "label": "<显示名称>",
      "node_ids": ["<node_id>", "<node_id>"]
    }
  ],

  "nodes": [
    {
      "id": "<唯一标识，仅字母数字下划线>",
      "label": "<显示名称，支持 \n 换行>",
      "type": "<对应 colors 中的某个 key>",
      "confidence": "<HIGH | MED | LOW | INFERRED>",
      "note": "<数据来源备注，由 .py 生成>",
      "era": ["<era_id>"],
      "user_note": "<用户探索笔记，默认空字符串>",
      "suspected": false
    }
  ],

  "edges": [
    {
      "source": "<源节点 id>",
      "target": "<目标节点 id>",
      "label": {
        "<era_id>": "<该纪元下的关系描述>",
        "<era_id>": "<该纪元下的关系描述>"
      },
      "confidence": "<HIGH | MED | LOW | INFERRED>",
      "direction": "<forward | backward | both>",
      "era": ["<era_id>"],
      "user_note": "<用户笔记，默认空字符串>",
      "suspected": false
    }
  ]
}
```

**字段说明：**

| 字段 | 来源 | 说明 |
|------|------|------|
| `colors` | `.py` 生成 | key 即节点类型枚举，value 为颜色。**编辑器从此字段动态生成「节点类型」下拉选项，不得硬编码类型列表** |
| `eras` | `.py` 生成 | **编辑器从此字段动态生成纪元复选框，不得硬编码纪元列表** |
| `groups` | `.py` 生成 | **编辑器从此字段动态生成「所属阵营」下拉选项，不得硬编码阵营列表** |
| `confidence` | `.py` 生成 | 枚举固定为四值：HIGH / MED / LOW / INFERRED，可硬编码 |
| `direction` | `.py` 生成 | 枚举固定为三值：forward / backward / both，可硬编码 |
| `user_note` | 编辑器维护 | `.py` 不生成此字段；M0b 导入时初始化为空字符串 |
| `suspected` | 编辑器维护 | `.py` 不生成此字段；M0b 导入时初始化为 false |

**与现有 viewer HTML 的关系：** viewer HTML 中注入的数据是此格式的子集（不含 `user_note` 和 `suspected`）。编辑器读取 data.json 时需容忍这两个字段缺失，缺失时视为默认值。

---

## 模块划分

编辑器由以下五个模块组成，可独立实现：

| 模块 | 说明 |
|------|------|
| M0 | 认证与数据加载 |
| M1 | 图谱渲染与浏览（基于现有 viewer_template） |
| M2 | 节点与连线编辑（增删改） |
| M3 | 注释与存疑标记 |
| M4 | 导出（Markdown / JSON / 截图） |

---

## M0：认证与数据加载

### 首次设置流程

用户首次打开 editor.html，显示设置界面（全屏遮罩，非侧边栏）：

```
┌─────────────────────────────────────┐
│   Elite Network Editor              │
│                                     │
│   GitHub Personal Access Token      │
│   [________________________________]│
│                                     │
│   Private Repo                      │
│   [katarism/_________________ ]     │
│                                     │
│   Case Name (子目录名)               │
│   [MiddleEastOil__________________] │
│                                     │
│          [连接并加载]                │
│                                     │
│   Token 仅存于本地 localStorage，   │
│   不会上传至任何服务器              │
└─────────────────────────────────────┘
```

**Token 权限要求：** `repo`（读写 private repo 内容）

**验证流程：**
1. 调用 `GET /repos/{owner}/{repo}` 验证 Token 和 repo 是否可访问
2. 调用 `GET /repos/{owner}/{repo}/contents/{case}/data.json`
   - 文件存在 → 解析并加载图谱
   - 文件不存在 → 提示「该 Case 尚无数据，是否从现有 HTML Viewer 导入初始数据？」（见 M0b）
3. 设置信息存入 localStorage：`gem_token`, `gem_repo`, `gem_case`

**再次打开：** 若 localStorage 已有设置，自动加载，跳过设置界面。提供「切换 Case」入口（左侧面板顶部齿轮图标）。

### M0b：从现有 HTML Viewer 导入初始数据

用于将 `.py` 生成的 HTML Viewer 转换为 `data.json` 写入 private repo。

**触发：** 首次加载时 data.json 不存在，或左侧面板「导入初始数据」按钮。

**操作：**
1. 用户上传或粘贴 Viewer HTML 文件内容
2. 编辑器用正则提取 HTML 中的 `{{NODES_JSON}}`、`{{EDGES_JSON}}`、`{{COLORS_JSON}}`、`{{GROUPS_JSON}}`、`{{ERAS_JSON}}` 注入点（实际运行时已是具体 JSON 值）
3. 组装为 `data.json` 格式
4. 调用 GitHub API 写入 private repo
5. 自动加载图谱

**提取逻辑参考：** HTML 中数据由 `const NODES_DATA = ...` / `const EDGES_DATA = ...` 等变量承载（见 viewer_template.html 的 JS 部分），用正则 `const NODES_DATA\s*=\s*(\[.*?\]);` 提取。

---

## M1：图谱渲染与浏览

**直接复用 viewer_template.html 的全部现有功能：**
- Cytoscape.js 渲染
- 三栏布局（左：过滤器+路径查询，中：画布，右：详情）
- 节点类型 / 置信度 / 纪元过滤
- 布局切换（grid / cose / circle / breadthfirst）
- 节点点击高亮邻居
- 连线悬停 tooltip
- 时间轴纪元切换
- 路径查询（F1）
- 存疑标记视觉样式（F6，见 M3）

**在 viewer 基础上新增的浏览功能：**
- 顶部状态栏显示当前 Case 名称、最后保存时间、未保存变更提示（红点）
- 左侧面板顶部新增「⚙️」按钮，点击可切换 Case 或重新设置

---

## M2：节点与连线编辑

### 编辑模式切换

顶部状态栏右侧新增切换按钮：

```
[👁 浏览模式]  [✏️ 编辑模式]
```

**浏览模式（默认）：** 与现有 viewer 行为一致，点击节点显示详情，不可修改。

**编辑模式：** 启用所有编辑功能，画布左上角提示文字变为「✏️ 编辑模式 · 双击空白处新增节点 · 双击节点编辑 · 右键删除」。

---

### M2a：新增节点

**触发：** 编辑模式下双击画布空白处。

**弹出节点编辑表单（Modal）：**

```
┌─────────────────────────────────────┐
│  新增节点                        [×]│
├─────────────────────────────────────┤
│  ID *          [____________________]│
│  (英文，唯一标识，如 SA_MBS)          │
│                                      │
│  显示名称 *    [____________________]│
│  (支持 \n 换行)                      │
│                                      │
│  类型 *        [下拉: state_elite ▼] │
│  置信度 *      [下拉: HIGH ▼]        │
│                                      │
│  活跃纪元      <从 eras 动态渲染复选框>   │
│                                      │
│  描述备注      [____________________]│
│               [____________________]│
│                                      │
│  所属阵营      [下拉: <从 groups 动态加载> ▼] │
│               (可选，选后自动加入该组)│
├─────────────────────────────────────┤
│              [取消]  [添加节点]      │
└─────────────────────────────────────┘
```

**字段验证：**
- ID 必填，仅允许字母数字下划线，不得与现有 ID 重复
- 显示名称必填
- 类型、置信度必填

**添加后：** 节点出现在画布中央（可拖拽），数据写入内存，顶部出现未保存提示。

---

### M2b：编辑节点

**触发：** 编辑模式下双击节点，或右键菜单「编辑节点」。

**弹出与新增相同的表单，字段预填当前值。**

ID 字段不可修改（改 ID 会破坏所有关联边）。若需改 ID，需先删除再重新添加。

---

### M2c：删除节点

**触发：** 编辑模式下右键节点 → 「删除节点」。

**确认对话框：**
```
删除「<节点显示名称>」？
此节点有 N 条关联连线，将一并删除。
[取消]  [确认删除]
```

---

### M2d：新增连线

**触发：** 编辑模式下，将鼠标悬停在源节点边缘，出现「+」拖拽把手，拖向目标节点后松开。

**弹出连线编辑表单：**

```
┌─────────────────────────────────────┐
│  新增连线                        [×]│
│  SA_MBS  →  OPEC_PLUS               │
├─────────────────────────────────────┤
│  置信度 *    [下拉: HIGH ▼]          │
│  方向 *      [下拉: both ▼]         │
│              (forward / backward / both) │
│                                      │
│  活跃纪元    ☐era1 ☐era2 ☑era3 ☑era4 │
│                                      │
│  关系描述（按纪元）                  │
│  era3: [________________________]   │
│  era4: [________________________]   │
│  (仅显示已勾选的纪元输入框)           │
├─────────────────────────────────────┤
│              [取消]  [添加连线]      │
└─────────────────────────────────────┘
```

---

### M2e：编辑连线

**触发：** 编辑模式下双击连线，或右键连线 → 「编辑连线」。

表单同新增，字段预填当前值。

---

### M2f：删除连线

**触发：** 编辑模式下右键连线 → 「删除连线」。无需确认（连线删除代价低，可通过撤销恢复）。

---

### M2g：撤销 / 重做

支持操作历史，快捷键 `Cmd/Ctrl+Z` 撤销，`Cmd/Ctrl+Shift+Z` 重做。

历史栈深度：50步。页面刷新后历史清空（不持久化）。

---

### M2h：保存

**触发：** 顶部「💾 保存」按钮，或 `Cmd/Ctrl+S`。

**流程：**
1. 将内存中当前数据序列化为 `data.json`
2. 调用 GitHub Contents API `PUT /repos/{owner}/{repo}/contents/{case}/data.json`
   - 需传入当前文件的 `sha`（从上次读取时缓存）
   - commit message 自动生成：`Update {case} - {timestamp}`
3. 成功：顶部未保存提示消失，显示「已保存 HH:MM」
4. 失败（如网络断开）：toast 报错，数据保留在内存，不丢失

**离线保护：** 若检测到网络断开，自动将当前数据备份至 localStorage `gem_unsaved_{case}`，下次联网时提示恢复。

---

## M3：注释与存疑标记

直接实现增强 PRD（F2）和存疑标记 PRD 的全部规格，以下仅列出与编辑器集成的差异点：

- **注释持久化：** 存入 `data.json` 的 `user_note` 字段（随保存写入 GitHub），不再使用 localStorage
- **存疑状态持久化：** 存入 `data.json` 的 `suspected` 字段，同上
- 两个 PRD 中涉及 localStorage 的所有描述，在编辑器中改为读写 `data.json` 内存对象，保存时一并提交

---

## M4：导出

实现增强 PRD 的全部导出功能（F3 Markdown 导出、F4 截图、F5 JSON），以下是差异点：

### Markdown 导出
与增强 PRD 规格相同。

### JSON 导出（替代 F5）
编辑器的「导出笔记 JSON」直接导出完整 `data.json`（包含所有节点、边、注释、存疑状态），而不是仅导出笔记子集。用于备份或在另一台设备初始化使用。

导入时直接用 GitHub API 写回 private repo，无需合并逻辑（整体替换）。

### 截图
与增强 PRD 规格相同（html2canvas，含面板）。

### 导出为可分享 Viewer HTML

新增一个导出选项：**「生成只读 Viewer HTML」**

**用途：** 将当前编辑器中的数据（含注释、存疑标记）打包为独立的、可离线使用的 Viewer HTML，可分享给他人或存档。

**实现：**
1. 拉取 public repo 中的 `viewer_template.html`（或内嵌一份副本）
2. 将当前 `data.json` 中的数据注入模板占位符
3. 将 Cytoscape.js 内联（从 CDN 或内嵌副本）
4. 触发下载，文件名：`{case}_{date}_Viewer.html`

这样生成的 HTML 与 `.py` 脚本生成的格式完全一致，可直接部署到 GitHub Pages。

---

## UI 补充规范

### 顶部状态栏（新增，全宽，高度 36px）

```
[Elite Network Editor]  [MiddleEastOil ▼]  ●未保存    [👁浏览] [✏️编辑]    [💾保存]  [⚙️]
```

- 左：产品名
- 中左：当前 Case 名（点击切换）
- 中：未保存变更提示（有变更时显示红点+文字，保存后消失）
- 中右：模式切换
- 右：保存按钮 + 设置

### 右键菜单完整规格

**节点右键（浏览模式）：**
```
📋 查看详情
⚠️ 标记为存疑 / ✅ 取消存疑
```

**节点右键（编辑模式）：**
```
✏️ 编辑节点
🗑️ 删除节点
─────────────
⚠️ 标记为存疑 / ✅ 取消存疑
```

**连线右键（编辑模式）：**
```
✏️ 编辑连线
🗑️ 删除连线
─────────────
⚠️ 标记为存疑 / ✅ 取消存疑
```

**画布空白右键（编辑模式）：**
```
➕ 在此处新增节点
```

---

## 实施顺序

1. **M0**：认证 + GitHub API 读写 + 首次设置界面（这是一切的前提）
2. **M0b**：从现有 HTML 导入初始数据（让现有 viewer 能迁移进来）
3. **M1**：复用 viewer_template 渲染图谱（验证数据加载正确）
4. **M3**：注释 + 存疑标记（低风险，不涉及数据结构变更）
5. **M2**：编辑功能（节点/连线增删改，撤销，保存）
6. **M4**：导出功能

---

## 不在本次范围内

- 多人协作 / 冲突解决
- 阵营（groups）的图形化编辑（阵营增删改通过编辑节点的「所属阵营」字段间接管理）
- 纪元（eras）的增删改（通过直接编辑 data.json 处理）
- 版本历史浏览（借助 GitHub commit history，不在编辑器内实现）

---

## 给 Claude Code 的额外说明

1. GitHub Contents API 写入时必须携带 `sha` 参数（文件当前版本的 SHA），否则会报 409 冲突。每次读取文件时缓存 `sha`，写入时带上。

2. `viewer_template.html` 中数据变量名为 `NODES_DATA`、`EDGES_DATA`、`COLORS_DATA`、`GROUPS_DATA`、`ERAS_DATA`（见模板 JS 部分），M0b 提取时依此定位。

3. Cytoscape.js 实例在模板中通过 `window.cy` 暴露，编辑器的所有图谱操作通过 `window.cy` 进行。

4. 编辑模式下新增节点拖拽连线功能，可使用 Cytoscape.js 的 `edgehandles` 扩展，需额外引入。若不想引入额外依赖，可退化为：选中源节点后，右侧面板出现「从此节点新增连线」按钮，再点击目标节点完成创建。

5. Token 安全提示需在 UI 中明确展示，不要隐藏。
