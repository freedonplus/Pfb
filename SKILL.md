---
name: prod-frontend-blueprint
description: Plan, design, audit, or review a production-grade frontend project (website / H5 / mobile app / mini-program / desktop). Use when the user wants to build a new frontend from scratch, scaffold a project structure, or audit an existing frontend against production standards. Covers platform & stack selection (Vue/React/TS/uni-app x/Flutter/Electron/Tauri/微信小程序), component libraries, directory skeleton, build & bundle optimization, resource loading, runtime performance, request-layer hardening, memory leaks, security, monitoring, and CI/CD. Trigger phrases include 生产级前端 / 前端方案 / 前端架构 / 前端脚手架 / 文件结构 / 上线前检查 / 前端审核 / 前端优化清单 / 性能优化 / 大厂级前端 / AI 前端方案. Decisions are grounded in big-tech interview fundamentals (closure / event loop / Proxy reactivity / Fiber / HTTP 1.1-3 / virtual list / SSE & MCP AI frontend) via a knowledge-system mapping; UI starts from design-style selection (StyleKit catalog mapped to project types) then a three-tier component-reuse strategy (mature library / self-built / uiverse-style adapted components) with MDN-aligned modern CSS and animation performance red-lines. Three routes — greenfield idea gets a multi-round interview plus a blueprint with directory skeleton; existing project without baseline gets a gap check (suggestions only, no code changes); project built by this skill gets a full audit report (结论先行). Frontend only, never writes backend code. Includes a 12-domain capability catalog — 3D/WebGL (Three.js, model-viewer), animation (CSS native, Motion, GSAP, anime.js, Lottie, Rive), charts (ECharts, Chart.js, D3, uPlot), data grids (DataTables.net, TanStack Table, AG Grid, vxe-table), UI and CSS (Cirrus UI, Bulma, Tailwind, shadcn), editors, maps, uploads, forms, state, tooling — plus an MDN-native-first checklist; every candidate is presented once and chosen by the user, never pruned by project type. 中文摘要：生产级前端方案规划与审核助手，新建出方案书+目录骨架、已有项目做差距检查、本技能产出的项目做审核报告；方案前强制全量呈现 12 类能力池（3D/动画/图表/数据表格/组件库/MDN 原生能力等）由用户逐组勾选，禁止按"后台/官网"等类型预设裁剪候选，只做前端不碰后端。
agent_created: true
---

# 生产级前端方案规划

## 核心原则

- **先问后写**：未完成提问阶段（含阶段 1.5 能力盘点）前，禁止直接输出方案或脚手架。
- **能力池一次全量呈现**：按 `references/library-catalog.md` 的 12 个能力域，把候选**一次性全部列出并加解释**（能力域 / 代表方案 / 体积 / 何时值得引入 / 已知坑），由用户逐组决定要或不要。
  - **禁止按项目类型预设裁剪**：不得因"这是后台管理系统"就跳过动画库、3D、图表、自研视觉组件；后台恰恰是数据表格（DataTables / TanStack Table / vxe-table）与图表（ECharts / Chart.js）最重的场景，效率型微交互同样成立。
  - **禁止用"通常不需要"代替询问**：默认倾向可以写，但必须让用户看到并有机会推翻。
  - 用户点名目录外的库时，按 `library-catalog.md` §一 四道闸（体积 / 许可证 / 活性 / 平台兼容）现场评估后纳入，标注「用户指定」。
- **原生与 MDN 优先**：引库前先过 `library-catalog.md` §二 原生能力清单；HTML/CSS/JS 语言层问题以 MDN 中文（developer.mozilla.org/zh-CN）为第一权威，其次 WHATWG/W3C 规范，最后才是库文档与博客。原生能做的一律不引库，并在方案书写明浏览器基线要求。
- **分轮提问，每轮最多 4 个**：使用 AskUserQuestion 工具；每题 2–4 个选项，推荐项放第一位并标注「（推荐）」。
- **尊重已有信息**：对话、工作区代码、历史记忆中已明确的答案（如框架、语言）不重复提问。
- **用户放弃选择时**：用推荐值继续，在最终方案注明「采用推荐默认」，不追问、不停滞。
- **方案必须过检**：产出前对照 `references/production-checklist.md` 逐项查漏；面向公网 C 端高流量项目还须对照 `references/advanced-checklist.md`。凡涉及项未覆盖必须补齐或显式注明「不适用原因」。
- **输出必须套模板**：最终方案严格遵循 `references/plan-template.md` 的章节结构，必须包含「目录结构骨架」章节。
- **审核结论先行**：审核/差距报告第一段就是结论（通过率、阻断项数、是否可上线），随后才展开明细。
- **前后端边界是硬规则**：只交付前端侧内容（目录骨架、状态与数据流、请求层封装、构建与部署策略）。接口字段、鉴权逻辑、数据库等后端设计只记录为「后端侧需求」，不展开实现。
- **平台特定内容**：先确定平台与框架，再加载 `references/stacks/` 下对应文件；依赖清单、版本匹配、平台陷阱必须出自对应文件。
- **原理支撑（大厂架构师标准）**：方案书关键决策（请求层、缓存、渲染、长列表、动效、工程化、AI 流式）须落到原理层——引用 `references/knowledge-system.md` 的考点映射（闭包/事件循环/Proxy 响应式/Fiber/HTTP 演进/虚拟列表/SSE 与 MCP 等），写清「为什么这样选、失败场景是什么」，不做清单复读机。
- **UI 风格与复用分层**：涉及风格选型、组件库选型、自研视觉组件或设计系统时，按 `references/ui-patterns.md` 执行——风格从其 §二 StyleKit 映射表按项目类型选（主风格只 1 个）；组件库从 `references/component-libraries.md` 目录与默认推荐矩阵选（须过活跃度三门槛：star/大厂背书、近 6 个月发版、issue 响应）；组件按 L1 成熟库 / L2 业务组件 / L3 uiverse 式抄改组件三层决策，抄改组件必须过准入门槛；动效必须过其 §四 性能红线。
- **审核不代改**：审核模式只出报告与建议，不直接改代码；用户明确要求整改后才动手，且逐项说明影响面。

## 场景判定（任何请求的第一步）

开口回答前先判定场景，**不要默认走提问流程**。判定不清时用 AskUserQuestion 问一个二选一问题，不要猜。

| 场景 | 判定信号 | 动作 |
| --- | --- | --- |
| **A. 已有项目 · 无本技能基线** | 工作区有前端代码，但找不到本技能产出的方案书 / 目录骨架文档 / 上线清单 | 差距检查：按 `references/audit-checklist.md` 抽取关键组比对，输出「结论先行」的差距报告，**由用户自行决定改哪些**，不代改；顺带扫一遍 `library-catalog.md`，只在"现方案明显走弯路（如手写全能表格、全量引 lodash）"时给替代建议，不做选型推销 |
| **B. 已有项目 · 本技能产出** | 存在方案书（含「目录结构骨架」「上线检查清单」章节） | 审核模式：走阶段 4，逐项出审核报告；同时核验方案书是否具备「依赖与能力选型回执表」章节，缺失则记为基线问题 |
| **C. 无项目 · 计划新建** | 无代码或只有想法（"我想做个 XX 网站/APP"） | 走阶段 0–3：分轮提问 → **阶段 1.5 能力池全量盘点** → 方案书 + 目录骨架 → 检阅迭代 |

判定要点：

- A 与 B 的分界是**有无基线**。有基线 → 对照「基线 + 清单」审核；无基线 → 只能对照清单做差距检查，且必须说明「本项目从未按本技能设计，以下为差距而非缺陷」。
- 场景 A 禁止直接改代码，差距一律表述为「建议项」，并问用户「要按哪几项整改」。
- 场景 B 若发现基线本身与最佳实践冲突，单列为「基线问题」，与实现问题分开统计。
- 用户只贴一段代码问「这样行不行」时，按场景 A 处理：点到为止给建议，不展开全量审核。

## 工作流（严格按顺序执行）

### 阶段 0 · 平台判定
从用户输入或工作区推断目标平台（网站 / 移动 H5 / 原生 App / uni-app / 微信小程序 / 桌面端）；无法推断时作为第一轮的第一个问题。平台决定后续问题集与 stacks 参考文件。

### 阶段 1 · 分轮提问
按 `references/interview.md` 的轮次执行：平台与终端 → 语言与框架 → 组件库与样式 → 规模与流量模型 → 渲染与数据层 → 工程化与发布。
- 根据前面回答动态裁剪后续问题（例如选了「内部后台」可跳过 SSR/SSG 与高阶性能轮；选了「微信小程序」跳过构建工具选型）。
- 每轮开场用一句话说明本轮目的，再调用 AskUserQuestion。
- 全部轮次结束后，用一张紧凑的「决策回执表」（问题 → 用户选择 → 推导结论）向用户复述，确认无误再进入写作。推导规则见 `references/decision-matrix.md`。

### 阶段 1.5 · 能力池全量盘点（写方案书前的强制关卡）

**目的**：避免"按项目类型套默认模板"导致漏掉更合适的方案。这一步的输出是方案书第 4 章的唯一依据。
**依据**：`references/library-catalog.md`（12 个能力域 + 原生能力清单 + 准入四道闸）。**本阶段不得跳过，即使项目看起来很简单。**

1. **先给全貌**：输出一张 12 行能力域总览表（能力域 / 代表方案 / 本项目默认倾向 / 一句话价值 / 体积量级），让用户一次看全，而不是逐轮挤牙膏。
2. **再逐组问**：每轮 2–3 组，用 AskUserQuestion；选项为「按默认组合（推荐）」「我自选（在补充里点名）」「本项目不需要」。同一轮正文必须把该组的候选、体积、适用边界列清楚，**不能只说"要不要用动画库"这种空问**。
3. **点名即评估**：用户提到目录外的库（例如他习惯用某个小众图表库），按四道闸现场给出体积 / 许可 / 活性 / 平台结论，能用的写入回执表并标「用户指定」。
4. **反向挑战**：若用户的选择与业务需求冲突（如首屏指标严格却要上 Three.js 大场景、后台需要 10 万行导出却选了纯前端 xlsx），必须当场指出成本与替代方案，由用户决定，不许沉默执行。
5. **落回执表**：输出「能力选型回执表」——能力域 / 是否引入 / 选定库 / 加载方式（主包 · 路由懒加载 · 动态 import）/ 体积预算 / 平台限制 / 否决项。确认后才进入阶段 2。
6. **平台前置过滤**：小程序无 DOM、无 WebGL；uni-app x 为 CSS 子集；Tauri 走系统 WebView。呈现时对不适用项**直接标注不适用与替代**，不要让用户选了才发现用不了。

### 阶段 2 · 撰写方案
1. 加载平台对应的 stacks 参考（见文末索引）。
2. 原理表述对照 `references/knowledge-system.md`（请求层/缓存/长列表/事件循环等章节的「30 秒原理 + 蓝图落点」），保证决策有原理依据；项目含 AI 对话/大模型调用/流式输出时必读其 §七，并使用模板的「AI 前端集成方案」章节。
3. 含 UI 选型、风格选型（StyleKit 映射）、自研组件、设计系统或动效设计时加载 `references/ui-patterns.md`，产出「UI 组件复用与设计系统」章节（风格选择也写「选择 + 理由 + 否决项」）；涉及组件库选型时同时加载 `references/component-libraries.md`（目录 + 默认推荐矩阵 + 活跃度门槛），设计资源（图标/动画库/模板）按需引用其 §三入口总表。
4. **「依赖与能力选型」章节直接由阶段 1.5 的回执表生成**（对齐 `references/plan-template.md` 第 4 章）：逐项写「选择 + 理由（对应哪个业务需求）+ 否决项 + 加载方式 + 体积预算」；未引入的候选只汇总成一行"明确不引入"及理由，不逐条铺陈。任何引用的原生能力（`library-catalog.md` §二）须写明浏览器基线。
5. 按 `references/plan-template.md` 的章节骨架撰写；技术决策必须给出「选择 + 理由 + 否决项」三件套。
6. 「目录结构骨架」章节为必写项：给出可直接落地的目录树 + 每层职责说明。
7. 「上线检查清单」章节按 P0/P1/P2 标注，与 `references/production-checklist.md` 对齐；C 端高流量项目并入 `references/advanced-checklist.md` 的相关项。
8. 涉及缓存、懒加载、请求防重、预加载策略的结论必须与 `references/decision-matrix.md` 一致；偏离时显式说明理由。

### 阶段 3 · 检阅与迭代
- 方案落盘为 Markdown 文件并 present_files，不要只贴在对话里。落盘位置遵守交付目录规范：全栈项目写入共享交付根 `docs/blueprint/frontend/`，并在同一份 `docs/blueprint/INDEX.md` 登记索引行（与 `prod-backend-blueprint` 共用，规则见「配对技能协同」）；独立前端项目默认写入 `docs/blueprint/frontend/` 或用户指定目录。
- 明确请用户检阅：「以上方案请检阅，指出要调整的章节」。
- 用户提出修改 → 局部修订并重新呈现差异；确认后定稿。
- 定稿后询问是否进入实施（脚手架搭建/页面编码），不要默认开始写代码。

### 阶段 4 · 审核（场景 B；场景 A 复用同一清单但只出建议）

1. **建基线**：读方案书、目录骨架、上线清单，记下版本号与日期；基线缺失的先说明缺哪几份。
2. **逐组过清单**（`references/audit-checklist.md`）：按项目平台取对应小节，不适用的组整组跳过并留一句理由。
3. **每条带证据**：`文件:行号` / 配置项 / 命令输出。三态判定：通过 / 不通过 / 不适用（附理由）。无法验证的标「待验证」，**不得臆断为通过**。
4. **分级**：阻断 P0（安全、数据错误、不可回滚发布、重复提交造成资损）/ 建议 P1 / 记录 P2。
5. **出报告**：结论先行——第一段给通过率、P0/P1/P2 数量、是否可上线；明细按组展开；落盘 Markdown 并 present_files。
6. **不代改**：报告结束后问「要整改哪些项」，用户勾选后按阻断 → 建议顺序处理，每项改动前说明影响面与回滚方式。

### 实时同步（审核与整改期间强制，不攒到最后）

- 方案书、目录骨架、上线清单任一处偏离实现时先登记偏差，再按用户选择回写。
- 每次同步更新方案书版本号 + 日期 + 变更点。
- 审核报告末尾固定记一行：`本次同步：方案书 n 处 / 清单 n 处 / 未同步 n 处（原因）`。

## 配对技能协同（与 prod-backend-blueprint）

本技能与 `prod-backend-blueprint` 是一对：后端技能管后端与契约源头，本技能管前端侧实现。全栈项目时按以下机制配合，**边界不放宽**（本技能依旧不写后端代码）。

1. **契约是唯一输入源**：后端技能产出的 OpenAPI 契约 + 错误码表 + 字段规范（统一响应体 `{code,message,data,traceId}`、金额用分、ID 字符串化、ISO8601 带时区、`Idempotency-Key`、`/api/v1`）落盘于 `docs/blueprint/api/`。本技能的「请求层与数据流」章节必须以该契约为输入实现封装与字段映射，**不自造字段、不猜错误码**；契约缺失时登记为「后端侧需求」，建议用户调用 `prod-backend-blueprint` 先产出契约，再继续前端方案。
2. **交付目录与索引共享**：全栈项目共用同一个交付根 `docs/blueprint/`。本技能写入 frontend/（方案书、目录骨架、上线清单）；后端技能写入 plan / api / audit / decisions / ops。`INDEX.md` 同一份文件，两个技能各自登记/更新自己的行，触发信号写清面向哪一侧（「实施前-后端」「联调时-前端」）。
3. **mock 联调**：契约可导入 Apifox/Postman 生成 mock——优先基于契约生成 mock 服务，禁止手写与契约漂移的假数据；契约行更新日期变化即视为漂移，须重新生成 mock 与请求层映射。
4. **移交规则**：方案或审核中识别出后端侧诉求（接口字段、鉴权逻辑、数据库、错误码新增）→ 登记为「后端侧需求」移交清单，并建议用户调用 `prod-backend-blueprint` 处理；反之，后端技能会把前端侧需求移交给本技能。移交只登记不代写。
5. **配对基线识别**：场景判定时若工作区存在后端技能产出的基线（方案书含 `blueprint-meta` 指纹、或 `docs/blueprint/INDEX.md` 同时登记了两侧文档），提示「检测到全栈项目配对基线」，审核时执行第 6 条。
6. **全栈审核的契约对齐检查**：全栈项目审核时，后端技能审契约正确性（错误码完备、鉴权、幂等、金额单位、超时），本技能审契约消费完备性（错误码处理覆盖、字段映射、请求防重）；两侧报告各自落盘于 `docs/blueprint/audit/`（文件名带 `-backend` / `-frontend` 后缀），并各含一节「契约对齐检查」——字段名/类型/错误码枚举/分页结构/时间格式两侧是否一致，不一致记为阻断项。

## 参考文件索引

| 文件 | 何时加载 |
| --- | --- |
| `references/interview.md` | 阶段 1 全程 + 阶段 1.5 能力盘点提问脚本 |
| `references/library-catalog.md` | **阶段 1.5 全程必载**（12 类能力池 + MDN 原生能力清单 + 四道准入闸）；阶段 2 写依赖选型章节；阶段 4 核验选型是否过闸 |
| `references/decision-matrix.md` | 阶段 1 推导结论、阶段 2 撰写缓存/懒加载/请求层/预加载章节时 |
| `references/knowledge-system.md` | 阶段 2 撰写原理性决策（请求层/缓存/渲染/长列表/工程化/动效）与 AI 前端项目（§七必读）；阶段 4 审核时作为原理判定依据；按需可按板块标题 grep |
| `references/ui-patterns.md` | 涉及设计风格选型（StyleKit 151 风格映射）、组件库选型、自研视觉组件（uiverse 式抄改）、设计系统、现代 CSS 特性与动效的方案撰写与审核 |
| `references/component-libraries.md` | 阶段 1 第 5 轮组件库提问、阶段 2 技术栈章节组件库选型、审核时核验选型活跃度；含按端与框架的组件库目录、默认推荐矩阵、designeer.org 资源入口总表与动画库红线 |
| `references/production-checklist.md` | 阶段 2 查漏、产出 P0/P1/P2 上线清单时（主清单，所有项目必过） |
| `references/advanced-checklist.md` | C 端高流量 / 微前端 / SSR 项目的高阶增强清单 |
| `references/audit-checklist.md` | 场景 A 差距检查、阶段 4 审核全程（结论先行报告模板） |
| `references/plan-template.md` | 阶段 2 全程（含目录结构骨架模板） |
| `references/stacks/vue3.md` | Vue 3 + Vite 项目 |
| `references/stacks/react.md` | React 项目 |
| `references/stacks/uniapp-x.md` | uni-app x（uvue/UTS）移动端项目 |
| `references/stacks/miniprogram.md` | 原生微信小程序项目 |
| `references/stacks/desktop.md` | Electron / Tauri 桌面端项目 |
