# ProdFrontendBlueprint · 生产级前端蓝图

> 一个会先问你几轮、再给出可落地生产级前端方案的 Skill。不是技术栈清单生成器，也不是脚手架生成器。

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
| **C. 无项目 · 计划新建** | 只有一个想法 | 分轮提问 → 方案书 + 目录骨架 → 检阅迭代 |

A 与 B 的分界是**有没有基线**：有基线才能说「你没按方案做」，没基线只能说「这里是差距」。审核报告第一段永远是结论（通过率、阻断项、能不能上线），明细在后。

---

## 提问覆盖什么

`references/interview.md` 共 6 轮，每轮最多 4 题，按平台与前置答案动态裁剪：

1. **平台与终端** —— 网站 / H5 / 原生 App / 小程序 / 桌面端，是否要 PC 与移动双适配
2. **语言与框架** —— JS 还是 TS；Vue / React / uni-app x / Flutter / Electron / Tauri
3. **组件库与样式** —— UI 库选型、是否允许自定义设计系统、CSS 方案与隔离方式
4. **规模与流量模型** —— 内部系统还是公网 C 端、日活量级、是否有大促峰值
5. **渲染与数据层** —— CSR / SSR / SSG、状态管理、请求层与缓存策略
6. **工程化与发布** —— 构建工具、包管理、CI、部署形态、灰度与回滚

结束时输出一张「决策回执表」（问题 → 你的选择 → 推导结论）供确认，避免方案写完才发现理解偏了。

---

## 清单分档（这是它和普通清单的区别）

同一份清单套所有项目就会既浪费又漏项。本技能按三档使用：

| 分档 | 文件 | 适用 |
| --- | --- | --- |
| **主清单** | `references/production-checklist.md` | 所有生产前端必做：构建与体积、资源加载、运行时渲染、请求层防错、内存泄漏、安全、监控、工程化 |
| **高阶清单** | `references/advanced-checklist.md` | 仅公网 C 端高流量：CLS/LCP 专项、预加载精细化管控、HTTP/2-3、微前端、SSR 懒注水、弱网与低端机压测、SRI |
| **审核清单** | `references/audit-checklist.md` | 场景 A/B：G1–G9 通用组 + 平台专属组（Vue3 / React / uni-app x / 小程序 / 桌面），含结论先行报告模板 |

清单项均带 **P0 / P1 / P2** 优先级，P0 是上线阻断项（防重复提交、请求取消、token 无感刷新、定时器与实例销毁、index.html 不强缓存、首屏体积、HTTPS 与基础 XSS 防护、异常监控）。

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

方案书章节骨架见 `references/plan-template.md`，必含：

- 技术选型与否决项（为什么不是另一套）
- **目录结构骨架**（目录树 + 每层职责，可直接落地）
- 数据流与状态管理、请求层与错误处理策略
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
├── SKILL.md                        # 入口：核心原则、场景判定、六阶段工作流
├── README.md
├── LICENSE                          # Apache-2.0
└── references/
    ├── interview.md                 # 分轮提问脚本（6 轮）
    ├── decision-matrix.md           # 选择 → 结论推导规则（11 组）
    ├── plan-template.md             # 方案书模板（含目录结构骨架章节）
    ├── production-checklist.md      # 主清单（十大板块，P0/P1/P2）
    ├── advanced-checklist.md        # 高阶清单（C 端大流量专用）
    ├── audit-checklist.md           # 审核清单 + 结论先行报告模板
    └── stacks/                      # vue3 / react / uniapp-x / miniprogram / desktop
```

## 与 ProdBackendBlueprint 的关系

同一套方法论的姐妹项目：[ProdBackendBlueprint](https://github.com/freedonplus/Pbb) 管后端（接口契约、数据一致性、部署形态），本项目管前端（构建、渲染、请求层、体验、发布）。两者边界清晰：前端技能把后端能力记录为「后端侧需求」，不越界设计服务端。

## License

Apache-2.0，允许商业使用。
