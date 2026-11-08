---
name: prod-frontend-blueprint
description: Plan, design, audit, or review a production-grade frontend project (website / H5 / mobile app / mini-program / desktop). Use when the user wants to build a new frontend from scratch, scaffold a project structure, or audit an existing frontend against production standards. Covers platform & stack selection (Vue/React/TS/uni-app x/Flutter/Electron/Tauri/微信小程序), component libraries, directory skeleton, build & bundle optimization, resource loading, runtime performance, request-layer hardening, memory leaks, security, monitoring, and CI/CD. Trigger phrases include 生产级前端 / 前端方案 / 前端架构 / 前端脚手架 / 文件结构 / 上线前检查 / 前端审核 / 前端优化清单 / 性能优化. Three routes — greenfield idea gets a multi-round interview plus a blueprint with directory skeleton; existing project without baseline gets a gap check (suggestions only, no code changes); project built by this skill gets a full audit report (结论先行). Frontend only, never writes backend code. 中文摘要：生产级前端方案规划与审核助手，新建出方案书+目录骨架、已有项目做差距检查、本技能产出的项目做审核报告，只做前端不碰后端。
agent_created: true
---

# 生产级前端方案规划

## 核心原则

- **先问后写**：未完成提问阶段前，禁止直接输出方案或脚手架。
- **分轮提问，每轮最多 4 个**：使用 AskUserQuestion 工具；每题 2–4 个选项，推荐项放第一位并标注「（推荐）」。
- **尊重已有信息**：对话、工作区代码、历史记忆中已明确的答案（如框架、语言）不重复提问。
- **用户放弃选择时**：用推荐值继续，在最终方案注明「采用推荐默认」，不追问、不停滞。
- **方案必须过检**：产出前对照 `references/production-checklist.md` 逐项查漏；面向公网 C 端高流量项目还须对照 `references/advanced-checklist.md`。凡涉及项未覆盖必须补齐或显式注明「不适用原因」。
- **输出必须套模板**：最终方案严格遵循 `references/plan-template.md` 的章节结构，必须包含「目录结构骨架」章节。
- **审核结论先行**：审核/差距报告第一段就是结论（通过率、阻断项数、是否可上线），随后才展开明细。
- **前后端边界是硬规则**：只交付前端侧内容（目录骨架、状态与数据流、请求层封装、构建与部署策略）。接口字段、鉴权逻辑、数据库等后端设计只记录为「后端侧需求」，不展开实现。
- **平台特定内容**：先确定平台与框架，再加载 `references/stacks/` 下对应文件；依赖清单、版本匹配、平台陷阱必须出自对应文件。
- **审核不代改**：审核模式只出报告与建议，不直接改代码；用户明确要求整改后才动手，且逐项说明影响面。

## 场景判定（任何请求的第一步）

开口回答前先判定场景，**不要默认走提问流程**。判定不清时用 AskUserQuestion 问一个二选一问题，不要猜。

| 场景 | 判定信号 | 动作 |
| --- | --- | --- |
| **A. 已有项目 · 无本技能基线** | 工作区有前端代码，但找不到本技能产出的方案书 / 目录骨架文档 / 上线清单 | 差距检查：按 `references/audit-checklist.md` 抽取关键组比对，输出「结论先行」的差距报告，**由用户自行决定改哪些**，不代改 |
| **B. 已有项目 · 本技能产出** | 存在方案书（含「目录结构骨架」「上线检查清单」章节） | 审核模式：走阶段 4，逐项出审核报告 |
| **C. 无项目 · 计划新建** | 无代码或只有想法（"我想做个 XX 网站/APP"） | 走阶段 0–3：分轮提问 → 方案书 + 目录骨架 → 检阅迭代 |

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

### 阶段 2 · 撰写方案
1. 加载平台对应的 stacks 参考（见文末索引）。
2. 按 `references/plan-template.md` 的章节骨架撰写；技术决策必须给出「选择 + 理由 + 否决项」三件套。
3. 「目录结构骨架」章节为必写项：给出可直接落地的目录树 + 每层职责说明。
4. 「上线检查清单」章节按 P0/P1/P2 标注，与 `references/production-checklist.md` 对齐；C 端高流量项目并入 `references/advanced-checklist.md` 的相关项。
5. 涉及缓存、懒加载、请求防重、预加载策略的结论必须与 `references/decision-matrix.md` 一致；偏离时显式说明理由。

### 阶段 3 · 检阅与迭代
- 方案落盘为 Markdown 文件并 present_files，不要只贴在对话里。
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

## 参考文件索引

| 文件 | 何时加载 |
| --- | --- |
| `references/interview.md` | 阶段 1 全程 |
| `references/decision-matrix.md` | 阶段 1 推导结论、阶段 2 撰写缓存/懒加载/请求层/预加载章节时 |
| `references/production-checklist.md` | 阶段 2 查漏、产出 P0/P1/P2 上线清单时（主清单，所有项目必过） |
| `references/advanced-checklist.md` | C 端高流量 / 微前端 / SSR 项目的高阶增强清单 |
| `references/audit-checklist.md` | 场景 A 差距检查、阶段 4 审核全程（结论先行报告模板） |
| `references/plan-template.md` | 阶段 2 全程（含目录结构骨架模板） |
| `references/stacks/vue3.md` | Vue 3 + Vite 项目 |
| `references/stacks/react.md` | React 项目 |
| `references/stacks/uniapp-x.md` | uni-app x（uvue/UTS）移动端项目 |
| `references/stacks/miniprogram.md` | 原生微信小程序项目 |
| `references/stacks/desktop.md` | Electron / Tauri 桌面端项目 |
