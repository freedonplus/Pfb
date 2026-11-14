# 前端决策推导矩阵

阶段 1 结束后，按本表把「用户选择」推导成「方案结论」。方案书里的每个技术决策必须与本表一致；偏离时显式说明理由。

---

## 1. 平台 → 技术基线

| 平台选择 | 推导结论 |
| --- | --- |
| 网站 / H5 | Vite 构建；部署 = Nginx 静态托管 + CDN；必须出 `index.html` 缓存策略 |
| 移动 App · uni-app x | uvue/UTS 约束集生效（见 stacks/uniapp-x.md）；无 DOM、CSS 子集、UTS 严格类型 |
| 移动 App · Flutter / RN / 原生 | 只出「工程结构与发布清单」，UI 细节交给对应平台规范 |
| 微信小程序 | 分包加载（主包 ≤ 2M）是 P0；无浏览器 API，cookies/存储策略不同 |
| 桌面端 · Electron | 包体积大、自动更新用 electron-updater；Chromium 版本固定 → 兼容性问题少 |
| 桌面端 · Tauri | 体积小、用系统 WebView → 必须做 WebView 兼容性矩阵（见 stacks/desktop.md） |

## 2. 语言与框架 → 代码规范基线

| 选择 | 推导结论 |
| --- | --- |
| TypeScript | `strict: true`；接口返回定义类型；`any` 出现必须注释理由；类型检查进 CI |
| Vue 3 | `<script setup>` + Composition API；Pinia 状态；组合式函数抽复用逻辑 |
| React | 函数组件 + Hooks；状态库 Zustand/RTK；`memo/useMemo` 规范入队代码评审 |
| JavaScript | 仅在小型项目允许；必须配 ESLint 兜底 |

## 3. 项目类型 → 清单适用范围（核心分流）

| 项目类型 | 适用清单 |
| --- | --- |
| 后台管理 / 内部系统 | 仅 `production-checklist.md`；advanced 清单不启用（投入产出比低，方案中注明原因） |
| 公网 C 端常规 | 主清单 + advanced 的「一、二、三、四」（渲染/打包/网络/预加载管控） |
| 公网 C 端高流量 | 主清单 + advanced 全量 |
| 微前端项目 | 主清单 + advanced「五、微前端专属优化」 |

## 4. 渲染方式推导

| 信号 | 结论 |
| --- | --- |
| 后台/内部系统 | CSR 即可，不做 SSR（成本高、收益低） |
| SEO 强需求 + 内容型站点 | SSG 优先（构建期生成），其次 SSR |
| 高流量 C 端首页/活动页 | SSR/SSG + 骨架屏 + LCP 预加载（advanced 清单启用） |
| App 内嵌页 / 后台 | CSR + 路由懒加载 |

## 5. 缓存策略推导（P0 级，写错会导致发版不生效）

| 资源 | 结论 |
| --- | --- |
| `index.html` | `Cache-Control: no-cache`（或 max-age=0 + 协商缓存），**禁止强缓存** |
| 带 hash 的 JS/CSS/图片/字体 | `max-age=31536000, immutable` |
| 接口数据 | 只读查询可本地短缓存（TTL 秒级~分钟级），写操作相关接口禁止缓存 |
| 本地缓存登录态 | App/小程序用 storage；Web 端 token 放内存/会话存储优先，localStorage 需评估 XSS 风险 |

## 6. 请求层推导（线上事故高发区）

| 场景 | 结论 |
| --- | --- |
| 表单/支付提交 | 按钮 loading 防重复 + 前端 requestId 幂等辅助 |
| 搜索/输入联想 | debounce 300ms 起 |
| 滚动/resize | throttle，且组件销毁时注销监听 |
| 页面跳转 | AbortController 取消未完成请求，防旧响应覆盖新页面 |
| 401/token 过期 | 拦截器统一处理：刷新 token（无感）→ 失败跳登录 |
| 自动重试 | 仅幂等 GET 允许；POST 创建/支付类**禁止自动重试** |

## 7. 懒加载推导

| 对象 | 结论 |
| --- | --- |
| 路由 | 全部路由懒加载（除首页） |
| 图片 | 视口外图片懒加载；列表头像等小图可直载 |
| 弹窗/抽屉 | 异步组件，首次打开才加载 |
| 第三方重库（图表/编辑器/地图） | 按需动态 import，不进主 bundle |
| 子 tab / 长页面下段 | 可视区域进入后再渲染 |

## 8. 预加载策略推导（滥用 preload 反而拖慢首屏，是高阶管控项）

| 策略 | 适用 |
| --- | --- |
| `preconnect` | 后端 API 域名、CDN 域名（2~4 个为宜） |
| `preload` | 仅当前页面关键资源：首屏 JS、关键字体、LCP 图片 |
| `prefetch` | 用户高概率下一跳页面的路由 chunk（低优先级空闲时加载） |
| 红线 | preload 总数 ≤ 5 个；禁止 preload 非首屏图片/视频；CI 检查首屏资源瀑布图 |

## 9. 大列表推导

| 数据量 | 结论 |
| --- | --- |
| < 500 行 | 普通渲染 + 分页 |
| 500–5000 行 | 分页或虚拟滚动二选一，视交互复杂度 |
| > 5000 行 / 无限滚动 | 虚拟滚动（vue-virtual-scroller / react-window / uni-app x 用 list-view）必做 |
| key 使用 | 稳定业务主键，**禁止无脑 index**（增删改列表会 DOM 错乱） |

## 10. 安全推导

| 项 | 结论 |
| --- | --- |
| HTTPS | 全站强制，HSTS 可选 |
| XSS | 框架默认转义之外，`v-html`/`dangerouslySetInnerHTML` 必须经 DOMPurify 等白名单过滤 |
| CSP | 公网 C 端与金融类必配；内部后台建议配 |
| Token | 禁止放 URL 参数；Web 端优先内存 + HttpOnly Cookie 方案（后端配合） |
| 敏感信息 | 生产构建剔除 console；禁止把密钥/内部地址打进包 |

## 11. 监控推导

| 项目类型 | 结论 |
| --- | --- |
| 内部工具 | console + 后端日志即可（可不做前端监控） |
| 公网 C 端 | JS 异常捕获（error/unhandledrejection）+ 资源加载失败 + Web Vitals 上报，错误率阈值告警 |
| 任何项目 | 全局错误兜底页 + 接口异常统一提示是 P1 |

## 12. UI 组件复用与风格选型推导（细则见 ui-patterns.md；组件库目录与默认推荐矩阵见 component-libraries.md）

| 信号 | 结论 |
| --- | --- |
| 组件库选型 | 端 + 框架匹配 → 活跃度三门槛（star ≥ 3k 或大厂背书 / 近 6 个月发版 / issue 响应）→ 风格匹配；默认推荐矩阵见 component-libraries.md §二（PC 后台 Vue3 → Element Plus；PC 后台 React → Ant Design；移动 H5 Vue → Vant；小程序 → Vant Weapp；金融移动 → Mand Mobile） |
| 库默认气质 ≠ 方案主风格 | 优先换库，不做第三方主题深改（深改主题的维护成本高于换库） |
| 后台管理 / 内部系统 | Corporate Clean / Notion 风 / Dark Mode 任选其一；L1 成熟组件库 + 默认样式，不引入自研视觉组件 |
| Dashboard / 数据工具 | macOS Vibrancy / Dark Mode；glass 预算集中给图表卡片 |
| SaaS / B2B 官网、开发者产品 | Modern Gradient / Soft UI / Stripe 风 / Apple 风；L1 底座 + L3 uiverse 式自研视觉组件 ≤ 20 个，全部 token 化收编 |
| C 端品牌 / 活动页 | Neo-Brutalist / Geometric Bold / Modern Gradient；动效只允许 transform/opacity；glass ≤ 1 层；无限动画出视口必须暂停 |
| 文档 / 知识库 | Notion 风 / Editorial；长文排版优先于视觉装饰 |
| 重动效需求（活动页/官网首屏） | 动效只允许 transform/opacity；glass ≤ 1 层；无限动画出视口必须暂停 |
| 风格混搭 | 主风格只 1 个，辅助元素 ≤ 2 种；重装饰风格（粘土拟态/蒸汽波/像素）禁止用于后台 |
| Neumorphism | 慎选：低对比可及性弱点，只用于卡片不用于表单；表单控件必须有非阴影状态提示 |
| 暗黑模式 | CSS 变量 token 唯一方案；组件库主题与自研 token 用同一开关 |
| 现代浏览器基线（无 IE/旧内核包袱） | 可用 @layer / 容器查询 / :has() / clamp()；@supports 渐进增强兜底 |
| 存量旧浏览器兼容 | 现代特性仅作增强层，基础体验不依赖新特性 |

## 13. AI 前端推导（原理见 knowledge-system.md §七）

| 场景 | 结论 |
| --- | --- |
| LLM 对话 / 流式生成 | fetch + ReadableStream（兼容主流网关），不用 SSE 库也不自建 WebSocket |
| 流式 markdown 渲染 | 按段落分块增量解析 + 消息块 memo；禁止每 token 全文 re-parse |
| 会话中断 | AbortController；中断后状态复位、缓冲清空、按钮恢复 |
| 超长对话历史 | 消息区虚拟列表渲染；上下文裁剪策略写入「后端侧需求」 |
| 工具调用（Agent/MCP） | UI 状态机：生成中/等待工具/失败/中断四态；tool call 卡片展示名称+参数+结果 |
| 断线/失败 | 自动重连仅用户可见开关触发；失败保留已生成内容 + 重试按钮，不整段清空 |
