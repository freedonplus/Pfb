# ProdFrontendBlueprint · 生产级前端蓝图

> 一个会先问你几轮、再给出可落地生产级前端方案的 Skill。不是技术栈清单生成器，也不是脚手架生成器。
>
> **v1.2**：新增 12 类能力池全量盘点（3D / 动画 / 图表 / 数据表格 / 组件库 / 编辑器 / 地图…），写方案前一次性列全由你勾选；引入 MDN 原生能力优先清单；提问扩至 8 轮并修掉条件题混排。

[![License](https://img.shields.io/badge/license-Apache--2.0-blue.svg)](LICENSE)
[![Skill](https://img.shields.io/badge/WorkBuddy-Skill-8b5cf6.svg)](#安装)
[![Platform](https://img.shields.io/badge/Web%20%7C%20H5%20%7C%20App%20%7C%20MiniProgram%20%7C%20Desktop-supported-success.svg)](#支持的平台与栈)

---

## 它解决什么问题

让 AI「设计一个生产级前端」，通常得到两种结果：一份把所有优化项都堆上的清单，或者一篇复述性能八股的文章。两者都不能用，因为**生产级不是「全都做」，而是「这个产品形态、这个流量量级，值得为哪些能力付出工程成本」**。

一个内部后台不该为了 LCP 去上 SSR，一个百万 UV 的活动页不上虚拟列表就是事故。这个 Skill 换了个做法：

1. **先问**。是网站还是 App、用 Vue 还是 React、TS 还是 JS、有没有组件库约束、日活多少、要不要 SSR —— 答案不同，方案差三倍。
2. **再推导**。每个答案对应明确的技术决策规则（构建工具、路由与懒加载策略、状态方案、缓存与预加载尺度、是否需要虚拟滚动），规则写在仓库里，可审计。
3. **最后写方案**。按固定章节输出，每条决策都带「选择 / 理由 / 否决项」，并且必须给出**可直接落地的目录结构骨架**。

## 它不是

- 不是脚手架生成器：产出方案书 + 目录骨架 + 关键配置片段，不代写业务页面。
- 不是后端设计器：**交付到「接口契约需求 + 请求层封装」为止**，不设计数据库、鉴权实现、服务端架构。
- 不是万能模板：不确定的地方它会追问，而不是编一个默认值；用户放弃选择时才用推荐值并显式标注。

---

## 先判场景，再决定做什么

不是所有请求都该从提问开始。技能第一步先判三种场景：

| 场景 | 触发信号 | 行为 |
| --- | --- | --- |
| **A. 已有项目 · 没按本技能设计** | 有代码，但找不到本技能产出的方案书 / 目录骨架文档 | 差距检查 → 输出「结论先行」的差距报告，**改不改由你决定**，不动你的代码 |
| **B. 已有项目 · 本技能产出** | 存在方案书（含目录骨架、上线清单章节） | 审核模式：按分组清单逐项出报告，分阻断 / 建议 / 记录三级 |
| **C. 无项目 · 计划新建** | 只有一个想法 | 分轮提问 → **能力池全量盘点** → 方案书 + 目录骨架 → 检阅迭代 |

A 与 B 的分界是**有没有基线**：有基线才能说「你没按方案做」，没基线只能说「这里是差距」。审核报告第一段永远是结论（通过率、阻断项、能不能上线），明细在后。

---

## 提问覆盖什么

`references/interview.md` 共 8 轮，每轮最多 4 题。**条件题单独成轮、按上一轮答案动态生成**——不会出现"还没选平台就被问 App 技术栈"的错位：

1. **平台** —— 网站 / H5 / 小程序 / 移动 App / 桌面端，项目性质，是否已有代码（只问平台，不放条件题）
2. **平台分支** —— 按第 1 轮结果只出相关一题：App 技术栈 / Web 目标环境 / 小程序类型 / 桌面方案，外加是否多端一套代码
3. **语言与框架** —— JS 还是 TS；Vue / React / Svelte；构建工具；包管理与工作区
4. **业务形态、数据压力与权限** —— 流量模型、**权限模型（仅登录态 / RBAC / 按钮级 / 多租户 / 对接 SSO）**、**表格与数据规模（常规 / 复杂表格 / 万行 / Excel 式）**、**可视化需求（统计图 / 看板 / 大屏 / 定制）**
5. **UI 与组件策略** —— 组件库、设计风格（StyleKit 映射）、**动效与交互密度（克制 / 标准 / 丰富 / 3D）**、主题与图标
6. **渲染与数据层** —— CSR / SSR / SSG、状态管理、请求层、国际化
7. **交付约束** —— **接口就绪度（决定 mock 与契约先行）**、**设计资源**、**存量约束（旧栈 / 旧路由 / 旧浏览器）**、**团队与交付对象**
8. **工程化与发布** —— 质量门禁、测试范围、CI/CD、监控

结束时输出两张回执表：**决策回执**（问题 → 你的选择 → 推导结论）+ **能力选型回执**（能力域 → 是否引入 → 库 → 加载方式 → 体积预算 → 否决项），确认后才写方案。

---

## 能力池：一次性全量呈现（v1.2 新增）

同类技能最容易犯的错，是按项目类型替你砍掉候选——"这是后台，不用动画库"、"内部系统跳过 SSR 那轮"。这个假设是错的：后台恰恰是数据表格（DataTables / TanStack Table / vxe-table）与图表（ECharts / Chart.js）用得最重的地方，效率型微交互也是实打实的需求。

所以 v1.2 增加 `references/library-catalog.md`，在写方案书前**强制**走一遍全量盘点：

| 组 | 内容 | 代表方案 |
| --- | --- | --- |
| A 3D / WebGL | 三维展示与图形渲染 | Three.js、`<model-viewer>`、PixiJS、Babylon、Rive、OGL |
| B 动画编排 | 转场、滚动叙事、微交互 | 纯 CSS、Motion、GSAP、anime.js、Lottie、AutoAnimate、Lenis |
| C 图表 | 看板、大屏、定制可视化 | ECharts、Chart.js、D3、uPlot、AntV、deck.gl |
| D 数据表格 | 服务端处理、虚拟滚动、Excel 式 | DataTables.net、TanStack Table、AG Grid、vxe-table、Handsontable |
| E 组件库 / CSS | 整套 UI 或纯 CSS 框架 | Cirrus UI、Bulma、Pico、Tailwind+DaisyUI、Radix/Ark（headless） |
| F 图标字体 | 图标与字体资源 | iconify、Lucide、Phosphor、IconPark、Fontshare |
| G 编辑器 | 富文本、代码、白板、公式 | Tiptap、Lexical、CodeMirror 6、Monaco、KaTeX、tldraw |
| H 地图 | 地理与三维地球 | 高德 / 百度 / 腾讯（含合规红线）、MapLibre、Leaflet、Cesium |
| I 文件媒体 | 上传、图片、PDF、Excel、音视频 | Uppy、Cropper.js、PDF.js、SheetJS、hls.js |
| J 表单交互 | 校验、拖拽、虚拟列表、日期 | zod/valibot、react-hook-form、dnd-kit、@tanstack/virtual、Day.js |
| K 状态请求 | 数据请求、全局状态、实时、离线 | TanStack Query、Pinia、Zustand、XState、Dexie |
| L 工程监控 | 构建、测试、Lint、体积、监控 | Vite/Rspack、Vitest、Playwright、MSW、Biome、Sentry |

每条都带**官方地址 / 体积量级 / 何时值得引入 / 已知坑与红线**，并按四道闸（体积 · 许可证 · 活跃度 · 平台兼容）判定。三层机制保证不失控：

- **全量呈现**：12 组先给总览表，再逐组问「按默认 / 我自选 / 不需要」，不允许用"通常不需要"代替询问。
- **原生优先**：`library-catalog.md` §二 列了 20 项"以前得引库、现在原生就行"的能力——`<dialog>`、Popover API、`inert`、`structuredClone`、`Intl`、`AbortSignal.timeout`、`content-visibility`、View Transitions 等，**MDN 中文（developer.mozilla.org/zh-CN）为第一权威**，原生能做的不引库。
- **反向挑战**：你的选择与业务冲突时（首屏指标严格却要上 Three.js 大场景），技能会当场算体积与降级成本，而不是沉默执行。

---

## 清单分档（这是它和普通清单的区别）

同一份清单套所有项目就会既浪费又漏项。本技能按三档使用：

| 分档 | 文件 | 适用 |
| --- | --- | --- |
| **主清单** | `references/production-checklist.md` | 所有生产前端必做：构建与体积、资源加载、运行时渲染、请求层防错、内存泄漏、安全、监控、工程化 |
| **高阶清单** | `references/advanced-checklist.md` | 仅公网 C 端高流量：CLS/LCP 专项、预加载精细化管控、HTTP/2-3、微前端、SSR 懒注水、弱网与低端机压测、SRI |
| **审核清单** | `references/audit-checklist.md` | 场景 A/B：G1–G10 通用组（含 UI/动效）+ P-AI 流式前端组 + 平台专属组（Vue3 / React / uni-app x / 小程序 / 桌面），含结论先行报告模板 |

清单项均带 **P0 / P1 / P2** 优先级，P0 是上线阻断项（防重复提交、请求取消、token 无感刷新、定时器与实例销毁、index.html 不强缓存、首屏体积、HTTPS 与基础 XSS 防护、异常监控）。

---

## 知识底座与 UI 体系（v1.1 新增）

方案不是清单复读——每个关键决策都能落到原理层，UI 每层都有目录可查：

| 维度 | 文件 | 覆盖内容 |
| --- | --- | --- |
| **大厂考点映射** | `references/knowledge-system.md` | 闭包/事件循环/原型链、Vue2 defineProperty vs Vue3 Proxy、React Hooks/Fiber、HTTP 1.1-3 演进、虚拟列表、Web Vitals 等高频考点 → 按「30 秒原理 + 蓝图落点 + 审核证据」映射成架构决策；附防抖节流等手写基线 |
| **AI 前端** | 同上 §七 + 模板第 7 章 | SSE vs WebSocket vs fetch 流式选型、增量 markdown 渲染、Agent/MCP 工具调用 UI 状态机、长会话虚拟列表、中断与断线兜底 |
| **风格选型** | `references/ui-patterns.md` §二 | StyleKit 151 种设计风格按项目类型映射（后台 → 企业简洁风/Notion 风，SaaS → 现代渐变风…），主风格只 1 个，混搭红线与可及性注意 |
| **组件复用三层** | 同上 §一/§三 | L1 成熟库（重交互不自研）/ L2 业务组件 / L3 uiverse 式抄改视觉组件（四条准入门槛：零重依赖、token 化、性能达标、无障碍底线） |
| **现代 CSS** | 同上 §五 | @layer / 容器查询 / :has() / subgrid / clamp() / View Transitions 等 14 项，全部带使用条件与 @supports 降级；动效性能红线（仅 transform/opacity、glass ≤ 1 层） |
| **组件库目录** | `references/component-libraries.md` | 60+ 组件库按端与框架分类（star 参考值）、默认推荐矩阵、活跃度三门槛（star≥3k 或大厂背书 / 近 6 个月发版 / issue 响应）、designeer.org 资源入口、动画库与模板站使用红线 |

---

## 支持的平台与栈

`references/stacks/` 每个文件包含依赖清单、版本匹配要点、目录分层、该栈特有的坑：

| 平台 | 文件 | 覆盖内容 |
| --- | --- | --- |
| Web · Vue | `stacks/vue3.md` | Vite + Vue3 + TS、路由懒加载、Pinia、shallowRef 大对象、v-if/v-show 取舍 |
| Web · React | `stacks/react.md` | 并发特性、memo/useMemo 边界、代码分割、状态选型 |
| 移动 · uni-app x | `stacks/uniapp-x.md` | UTS 严格编译红线、uvue CSS 子集、tabBar 无事件、hover 默认时长、真机性能基线 |
| 移动 · 微信小程序 | `stacks/miniprogram.md` | 分包与预下载、setData 优化、自定义组件、审核与体积限制 |
| 桌面 · Electron / Tauri | `stacks/desktop.md` | 主进程与渲染进程边界、IPC 安全、自动更新、包体与签名 |

`stacks/uniapp-x.md` 里沉淀的是真实项目踩过的坑（UTS 不许在 `any` 上点属性、`catch` 回调不能写注解、函数必须先声明后引用、uvue 禁 `@keyframes` 与百分比宽度、hover 默认 50ms 延迟），不是文档抄录。

---

## 输出长什么样

方案书章节骨架见 `references/plan-template.md`（完整版 19 章 / 精简版 7 章），必含：

- 技术选型与否决项（为什么不是另一套），版本号只写下限并附**核验日期**
- **目录结构骨架**（目录树 + 每层职责，可直接落地）
- **依赖与能力选型**（阶段 1.5 回执表：能力域 / 库 / 体积 / 加载方式 / 否决项 + 原生能力基线）
- 数据流与状态管理、请求层与错误处理策略
- UI 组件复用与设计系统（主风格选型、三层复用决策、token 层、抄改组件清单）
- AI 前端集成方案（仅 AI 项目：流式渲染、会话状态机、工具调用展示、上下文管理）
- 构建与产物体积预算、资源加载与缓存策略
- 性能目标（首屏 / 交互 / 包体）与对应手段
- 安全基线、监控与埋点、CI/CD 与发布回滚
- 上线检查清单（P0/P1/P2 勾选表）
- **后端侧需求**（只列需要后端配合的字段、接口、跨域、鉴权约定，不展开实现）

---

## 安装

把仓库内容放进 WorkBuddy 的技能目录：

```bash
git clone https://github.com/freedonplus/Pfb.git
cp -r Pfb ~/.workbuddy/skills/prod-frontend-blueprint
```

Windows 对应 `C:\Users\<你>\.workbuddy\skills\prod-frontend-blueprint`。目录名需与 `SKILL.md` 里的 `name: prod-frontend-blueprint` 一致，否则技能不会加载。

也可以在 WorkBuddy 里直接安装打包好的 zip（`dist/prod-frontend-blueprint.zip`）。

## 怎么触发

自然语言即可，典型说法：

- 「帮我规划一个生产级前端项目，做个 C 端电商网站」
- 「我要做一个 uni-app 的 App，先把工程骨架和目录结构定下来」
- 「审核一下这个前端项目能不能上线」
- 「这个后台系统还有哪些性能问题」

技能会先判场景：新建 → 开始分轮提问；已有项目 → 直接给差距或审核报告。

## 仓库结构

```
ProdFrontendBlueprint/
├── SKILL.md                        # 入口：核心原则、场景判定、阶段 0–4 工作流（含 1.5 能力盘点）
├── README.md
├── LICENSE                          # Apache-2.0
└── references/
    ├── interview.md                 # 分轮提问脚本（8 轮 + 能力盘点轮）
    ├── library-catalog.md           # 12 类能力池 + MDN 原生能力清单 + 四道准入闸（v1.2）
    ├── decision-matrix.md           # 选择 → 结论推导规则（14 组，含能力池选型）
    ├── knowledge-system.md          # 大厂高频考点 → 架构决策映射（含 AI 前端 §七）
    ├── ui-patterns.md               # 风格选型（StyleKit）+ 组件复用三层 + 现代 CSS
    ├── component-libraries.md       # 组件库目录 + 非组件能力库索引 + 设计资源入口 + 动画库红线
    ├── plan-template.md             # 方案书模板（19 章完整版 / 7 章精简版）
    ├── production-checklist.md      # 主清单（十一板块，P0/P1/P2）
    ├── advanced-checklist.md        # 高阶清单（C 端大流量专用）
    ├── audit-checklist.md           # 审核清单（G1–G10 + P-AI）+ 结论先行报告模板
    └── stacks/                      # vue3 / react / uniapp-x / miniprogram / desktop
```

## 与 ProdBackendBlueprint 的关系

同一套方法论的姐妹项目：[ProdBackendBlueprint](https://github.com/freedonplus/Pbb) 管后端（接口契约、数据一致性、部署形态），本项目管前端（构建、渲染、请求层、体验、发布）。两者边界清晰：前端技能把后端能力记录为「后端侧需求」，不越界设计服务端。

## License

Apache-2.0，允许商业使用。
