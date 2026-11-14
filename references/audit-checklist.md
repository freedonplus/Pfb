# 前端审核清单（场景 A 差距检查 / 场景 B 审核共用）

用法：逐组过，每条带证据（`文件:行号` / 配置项 / 命令输出）。三态判定：通过 / 不通过 / 不适用（附理由）/ 待验证。
不适用的组整组跳过并留一句理由。

---

## Part 1 · 通用组（所有前端项目）

### G1 构建产物
- 生产构建开启：无 `debugger`、无业务 `console.log` 残留（搜 `console.log` 统计数量）
- Tree-Shaking 生效：package.json 依赖为 ESM；无全量 `import * as` 重库
- 路由懒加载：路由定义处使用动态 import
- 组件库按需引入：无全量 import（如 `import ElementPlus from 'element-plus'` 后 `app.use` 全注册）
- 产物 hash 命名：build 后文件名含内容 hash
- sourceMap：生产构建不输出或不上传（检查 build 配置）

### G2 资源缓存（查部署配置 nginx/对象存储/CDN）
- `index.html` 不强缓存（no-cache / max-age=0）
- hash 静态资源 `max-age=31536000, immutable`
- Gzip/Brotli 开启
- CDN 托管与刷新策略

### G3 请求层（线上事故高发）
- 提交按钮防重复：提交中有 loading 锁，接口返回前禁用
- 搜索/联想有 debounce；滚动/resize 有 throttle
- 页面跳转取消未完成请求（AbortController / 请求管理器）
- 401 统一处理：token 刷新 → 失败跳登录
- 自动重试仅限幂等 GET；创建/支付 POST 无自动重试
- 统一错误提示封装，未把原始堆栈抛给用户

### G4 内存泄漏（重点查常驻页面）
- 定时器在 onUnmounted / useEffect cleanup 清除
- 全局事件监听（window resize/scroll/keydown）注销
- ECharts / 地图 / 播放器实例 destroy
- 事件总线订阅取消

### G5 长列表与渲染
- 大列表分页或虚拟滚动
- 列表渲染使用稳定业务主键作 key（抽查 v-for/:key、map key）
- 动画只用 transform / opacity

### G6 安全
- HTTPS 强制跳转
- `v-html` / `dangerouslySetInnerHTML` 全部有过滤（DOMPurify 等）
- Token 不出现在 URL 参数
- 生产包无密钥/内部地址/敏感注释
- 高危操作二次确认

### G7 健壮性体验
- 列表空状态、接口异常兜底、全局错误边界
- 骨架屏或加载态
- 新版本发布提示刷新（或自动检测）

### G8 监控
- JS 异常捕获（window.onerror / unhandledrejection 或 SDK）
- 性能指标上报（Web Vitals）
- 错误率告警阈值

### G9 工程化
- ESLint + Prettier + 类型检查进 CI
- 发布带版本号
- 有预发布环境；生产不可直接改

### G10 UI 组件复用与动效（仅含自研视觉组件/重动效的项目适用）
- 页面实际风格与方案书选定的主风格一致，混搭辅助元素 ≤ 2 种（对照 ui-patterns.md §二映射表）
- 抄改组件（uiverse 等来源）色值已 token 化，无硬编码色号
- glass（backdrop-filter）层数 ≤ 1，无嵌套；低端机有降级
- 无限循环动画出视口暂停；`prefers-reduced-motion` 全局降级生效
- 动画属性仅 transform / opacity（抽查 hover/过渡 CSS）
- `will-change` 非常驻
- 弹窗处理 `overscroll-behavior`（滚动穿透）

### P-AI 流式 AI 前端（仅 AI 对话/大模型调用项目适用）
- 流式请求有 AbortController 取消；中断后状态复位、缓冲清空
- 流式 markdown 分块增量解析，无每 token 全文 re-parse
- 工具调用（Agent/MCP）有 UI 状态机与失败反馈
- 超长对话消息区用虚拟列表
- 断线/失败保留已生成内容，不整段清空

## Part 2 · 平台组（按项目平台选用）

### P-Vue3
- 大对象使用 shallowRef/shallowReactive（抽查大列表/图表数据）
- v-if / v-show 使用合理（高频显隐不滥用 v-if）
- 全局状态无巨量无用数据常驻

### P-React
- 重渲染热点组件有 memo/useMemo/useCallback（抽查大列表项组件）
- useEffect 依赖数组完整，无过期闭包引发的 bug
- Context 大对象未导致全树重渲染

### P-UniAppX（uvue / UTS）
- `<script setup>` 内函数/变量先声明后使用（Kotlin 局部作用域，无 JS 提升）
- 不在 `any` 上直接点属性；Promise `.catch` 回调不带非空类型注解
- CSS 仅用 uvue 支持子集：无 @keyframes、无 cubic-bezier、无元素选择器、无百分比 max-width
- scroll-view 长列表限制首屏渲染条数或改 list-view
- 页面 onShow 拉数据有节流/缓存，切 tab 不重复打接口
- 原生 tabBar 无法用自定义事件，如需点击反馈走 onTabItemTap

### P-MiniProgram
- 分包加载：主包 ≤ 2M，非首屏页面进分包
- setData 频率与数据量受控（不大对象高频 setData）
- 图片走 CDN 且带尺寸参数
- storage 大小 ≤ 10M 有管控

### P-Desktop
- 自动更新通道配置（electron-updater / Tauri updater）
- 主进程/渲染进程隔离，nodeIntegration 关闭（Electron）
- Tauri：系统 WebView 兼容性矩阵已验证（Win7 老 WebView2 等）
- 离线可用性与本地数据存储策略

## Part 3 · 报告模板（结论先行）

```markdown
# <项目名> 前端审核报告

> 版本：v<n> ｜ 日期：<YYYY-MM-DD> ｜ 基线：<方案书版本 / 无基线-差距检查>

## 结论（先说结论）

- 总体通过率：xx%（通过 a / 不通过 b / 不适用 c / 待验证 d）
- 阻断项 P0：n 个 → <可上线 / 不建议上线，须先修复 P0>
- 建议项 P1：n 个；记录项 P2：n 个
- 最小可上线集：<只修哪几项即可达到上线门槛>
- 基线问题：<n 个，方案本身与最佳实践冲突处；无则写「无」>

## 明细

### <分组名>（通过 x/y）
| 条目 | 判定 | 证据 | 级别 | 说明/建议 |
| --- | --- | --- | --- | --- |
| 路由懒加载 | ✅ 通过 | src/router/index.ts:12 动态 import | P0 | — |

## 整改建议（不代改）
按 P0 → P1 顺序列出可勾选整改项，每项注明影响面与回滚方式。

---
本次同步：方案书 n 处 / 清单 n 处 / 未同步 n 处（原因）
```

规则：
- 第一段永远先给结论与上线判断，明细在后。
- 无基线项目（场景 A）报告标题用「差距检查报告」，结论措辞为「建议项」而非「缺陷」。
- 报告落盘 Markdown 并 present_files；对话里只放结论摘要。
