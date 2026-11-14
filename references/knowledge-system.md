# 大厂高频考点 → 架构决策映射

用途：让方案书与审核报告的每个关键决策都能落到原理层（大厂架构师标准：知其所以然）。
阶段 2 撰写方案、阶段 4 审核时按需加载；只需某一板块时可按标题 grep。

结构约定：每条考点 = 「30 秒原理」+「蓝图落点」（写到方案书哪一章）+「审核证据」（审代码时看什么）。

---

## 一、JavaScript 语言核心

### 1. 闭包
- 原理：函数与其词法作用域的组合，内部函数持有对外部变量的引用。
- 价值：模块私有化、组合式函数封装、柯里化。
- 风险：闭包持有大对象导致无法 GC（内存泄漏审查项）；循环中闭包共享变量（用 `let` 或 IIFE 隔离）。
- 蓝图落点：状态与数据流章节——hooks/composables 的清理逻辑。
- 审核证据：React `useEffect` 过期闭包（依赖数组缺失）；Vue 定时器/订阅闭包持有组件实例。

### 2. 事件循环（Event Loop）
- 原理：宏任务（script、setTimeout、setInterval、I/O、UI 事件）+ 微任务（Promise.then、queueMicrotask、MutationObserver）；每轮宏任务后清空微任务队列。Node.js 差异：分 timer/poll/check 阶段，`process.nextTick` 优先于 Promise 微任务。
- 蓝图落点：主线程保护章节——长任务（>50ms）拆分到 `requestIdleCallback` / `scheduler.yield`（或 `setTimeout` 分片）；非紧急逻辑让位输入响应。
- 审核证据：首屏同步大循环、大 JSON.parse、主线程排序统计；`await` 链上错误未捕获（unhandledrejection 监控项）。

### 3. 原型链与继承
- 原理：`__proto__` 指向构造函数的 `prototype`，属性沿链查找；`Object.create(null)` 无链。
- 蓝图落点：工具类设计避免过深继承链，优先组合（composables/hooks）。
- 审核证据：框架项目中少见；出现 class 继承 > 3 层记 P2。

### 4. 防抖与节流（手写必考）
- 原理：防抖 = 停止触发 N ms 后执行一次（搜索联想）；节流 = 固定频率执行（滚动、resize）。进阶版要支持：立即执行、取消 `cancel()`、返回值。
- 蓝图落点：请求层章节（对齐 decision-matrix §6：debounce 300ms 起；滚动 throttle + 销毁注销）。
- 审核证据：搜索框是否有防抖；滚动监听是否节流并在组件销毁时移除。
- 手写基线（方案书附录用）：

```js
// 防抖：默认尾部触发，leading=true 立即执行
function debounce(fn, wait = 300, leading = false) {
  let timer = null;
  const debounced = (...args) => {
    if (leading && !timer) fn(...args);
    clearTimeout(timer);
    timer = setTimeout(() => { timer = null; if (!leading) fn(...args); }, wait);
  };
  debounced.cancel = () => { clearTimeout(timer); timer = null; };
  return debounced;
}
// 节流：时间戳版（首尾都触发）推荐
function throttle(fn, wait = 200) {
  let prev = 0, timer = null;
  return (...args) => {
    const now = Date.now();
    if (now - prev >= wait) { prev = now; fn(...args); }
    else { clearTimeout(timer); timer = setTimeout(() => { prev = Date.now(); fn(...args); }, wait - (now - prev)); }
  };
}
```

### 5. 深拷贝
- 原理：`JSON.parse(JSON.stringify())` 缺陷——丢函数/undefined/Symbol、日期变字符串、NaN/Infinity 异常、循环引用直接抛错。
- 手写要点：递归 + `WeakMap` 记录已拷贝对象处理循环引用；按类型分支（Date/RegExp/Map/Set/TypedArray）。
- 蓝图落点：禁止在运行时对大数据做全量深拷贝（快照/撤销栈需评估结构共享或 immer）。
- 审核证据：搜索 `JSON.parse(JSON.stringify(` —— 大对象高频调用记 P1。

### 6. 其他高频手写（方案附录可引用的最小正确版）
- 发布订阅 EventEmitter：on/off/once/emit，注意 off 按引用匹配。
- Promise 并发控制（limit 并发池）、`Promise.all/race/allSettled`。
- LRU 缓存：`Map` 的插入顺序特性（get 时 delete+set 提权）。
- 数组扁平化去重、curry、`call/apply/bind/new/instanceof`。
- 快排 / TopK：大数据量 TopK 用小顶堆 O(n log k)。
- 异步红绿灯/调度器：async 循环 + sleep（多状态轮播、轮询策略）。

---

## 二、Vue（Vue2 + Vue3）

### 1. 响应式原理对比
- Vue2：`Object.defineProperty` getter/setter 依赖收集。缺陷：新增/删除属性、数组下标与 length 不可监听（`$set`/$delete` 补丁）。
- Vue3：`Proxy` 拦截 13 种操作 + `WeakMap(targetMap)` 存依赖 + `effect` 调度。惰性响应式（`reactive/ref`），性能更好。
- 蓝图落点：状态设计——深层大对象避免整体 `reactive`（用 `shallowRef/shallowReactive`，见 stacks/vue3.md）。
- 审核证据：`reactive` 解构丢失响应式；`props` 直接改。

### 2. Composition API 动机
- 原理：Options API 逻辑按选项分散（同一功能的 data/methods/computed 分离）；Composition API 按功能聚合，逻辑以 composables 复用（对标 Hooks）。
- 蓝图落点：目录骨架——`composables/` 只放跨页面复用逻辑，页面私有逻辑放页面目录内。

### 3. v-if vs v-show / 生命周期 / keep-alive
- v-if 真实增删 DOM（一次性分支）；v-show 仅 CSS `display` 切换（高频显隐）。
- 生命周期：setup→onMounted→onUpdated→onUnmounted；keep-alive 场景用 onActivated/onDeactivated。
- 审核证据：keep-alive 页面在 activated 重复拉接口（无节流/缓存）；定时器在 onUnmounted 未清理。

---

## 三、React

### 1. Hooks 原理与陷阱
- 原理：函数组件无实例，状态存于 Fiber 节点的 hooks 链表，**依赖调用顺序**定位——所以不能写在条件/循环中。
- 闭包陷阱：`useEffect`/`useCallback` 捕获渲染时的值；依赖数组缺失 → 过期闭包 bug。
- 蓝图落点：`memo/useMemo/useCallback` 规范列入代码评审清单（decision-matrix §2）。
- 审核证据：hooks 出现在 `if` 内（直接报错项）；effect 依赖数组完整；大列表项组件有 memo。

### 2. Fiber 架构与调度
- 原理：虚拟 DOM 链表化，渲染拆成可中断的工作单元；时间分片 + 优先级调度（lanes），高优更新插队。
- 蓝图落点：复杂交互页（编辑器/画布）的大状态更新用并发特性或拆分状态下沉。
- 审核证据：输入框受控组件每 keystroke 触发整树重渲染热点（Context 大对象）。

### 3. 虚拟 DOM diff 与 key
- 原理：同层对比、单节点类型比较、`key` 匹配复用；key 用业务主键。
- 审核证据：`map` 用 index 作 key 且列表有增删（DOM 错乱风险，P0，对齐 decision-matrix §9）。

---

## 四、网络与浏览器

### 1. URL 输入到渲染全链路
- 原理：DNS → TCP 三次握手 → TLS → HTTP 请求 → 响应 → HTML 解析（DOM）/ CSS 解析（CSSOM）→ 渲染树 → 布局（重排）→ 绘制（重绘）→ 合成。JS 阻塞 DOM 解析（defer/async 区别）。
- 重排贵于重绘：几何变化触发重排；`transform/opacity` 只走合成。
- 蓝图落点：资源加载章节（关键 CSS 内联、script defer、LCP 元素优先）。
- 审核证据：循环内读写 offsetHeight 再改样式（强制同步布局）；动画用 top/left/width。

### 2. HTTP/1.1 vs HTTP/2 vs HTTP/3
- 原理：1.1 队头阻塞（管线化废弃）→ 2 多路复用（单 TCP、二进制分帧、HPACK 头压缩）但 TCP 层队头阻塞 → 3 基于 QUIC（UDP、0-RTT、连接迁移）。
- 蓝图落点：CDN/传输层要求（advanced-checklist §三），HTTP/2 下雪碧图反而负优化、域名拆分不再需要。
- 审核证据：HTTP/2 环境仍做资源域名分片、icon 雪碧图（记 P2 记录项）。

### 3. 浏览器缓存
- 原理：强缓存（`Cache-Control: max-age` / `Expires`）不发请求；协商缓存（`ETag/If-None-Match`、`Last-Modified/If-Modified-Since`）304。
- 蓝图落点：缓存策略章节（对齐 decision-matrix §5，index.html 禁强缓存是 P0）。

### 4. 跨域与安全
- 原理：同源策略；CORS（简单请求 vs 预检 OPTIONS：非简单头/方法触发）；代理转发只解决开发期。
- 安全：XSS（存储/反射/DOM 型，`v-html`/`dangerouslySetInnerHTML` 白名单过滤）、CSRF（SameSite Cookie、Token 头）。
- 蓝图落点：安全章节（对齐 decision-matrix §10）。
- 审核证据：`v-html` 无 DOMPurify；Cookie 缺 SameSite。

---

## 五、工程化

### 1. Webpack vs Vite
- 原理：Webpack dev 需全量打包后启动；Vite dev 用原生 ESM 按需编译（秒级冷启动）+ esbuild 预构建，生产走 Rollup（Vite 8 起可选 Rolldown）。
- 蓝图落点：构建选型（decision-matrix；存量 webpack 项目可 Rspack 平滑提速）。

### 2. Tree Shaking
- 原理：基于 ESM 静态结构分析，`package.json` 的 `sideEffects: false` 声明纯模块；CommonJS 动态导出无法 shake。
- 审核证据：依赖是 ESM、无 `import * as` 全量引入（audit G1）。

### 3. Monorepo
- 原理：pnpm workspace（硬链接省磁盘、依赖隔离）+ Turborepo（任务缓存、拓扑执行）+ changesets（版本与 changelog）。
- 蓝图落点：多包项目（组件库/多应用共享 utils）目录骨架改为 monorepo：`apps/*` + `packages/*`。

---

## 六、性能优化（大厂高频）

### 1. 虚拟列表
- 原理：只渲染可视区 + 上下缓冲区，scrollTop 换算起始索引，容器用 padding/transform 占位撑高。变体：定高（简单二分）与不定高（预估+修正）。
- 蓝图落点：长列表方案（对齐 decision-matrix §9：>5000 行必做）。
- 审核证据：万级行直接 v-for/map 全量渲染。

### 2. Web Vitals 指标
- 原理：FCP（首次内容绘制）、LCP（最大内容绘制，≤2.5s）、CLS（累积布局偏移，≤0.1）、INP（交互到下一帧，≤200ms，替代 FID）、TBT/TTI（可交互）。
- 优化手段映射：LCP→preload 关键资源/SSR/图片优先级 `fetchpriority`；CLS→图片预留宽高、字体 swap；INP→长任务拆分。
- 蓝图落点：性能监控章节（监控 SDK 上报 + 阈值告警）。

### 3. 懒加载 / 骨架屏 / 预渲染
- 蓝图落点：对齐 decision-matrix §7/§8 与 advanced-checklist §一、六。

---

## 七、AI 前端（2026 新增高频）

### 1. SSE vs WebSocket vs fetch ReadableStream
- 原理：SSE——单向服务端推送、HTTP/2 多路复用、自带重连（`Last-Event-ID` 续传）、文本协议；WebSocket——双向、独立协议、需自建心跳重连；fetch ReadableStream——LLM API 事实标准（请求头定制灵活，但断线续传要自己实现）。
- 蓝图落点：AI 对话类项目默认 `fetch + ReadableStream`（兼容主流 LLM 网关），协作白板类才用 WebSocket。
- 审核证据：流式请求是否有 AbortController 取消、断线重连与「生成中」状态兜底。

### 2. 流式渲染
- 原理：token 增量到达 → 追加缓冲区 → 增量解析渲染。陷阱：markdown 语法跨 chunk 截断（未闭合代码块）、整段重 parse 导致 O(n²) 渲染抖动。
- 蓝图落点：消息块 memo 化、按段落分块解析、完成后一次性终渲染替换。
- 审核证据：每 token 全文 re-parse；滚动跟随在用户上滑时仍强制拉底。

### 3. 上下文管理（RAG 前端侧）
- 原理：前端不跑向量检索，但要做上下文窗口管理——历史消息裁剪/摘要、长对话分页、引用来源展示。
- 蓝图落点：超长对话用虚拟列表渲染消息；上下文裁剪策略写入后端侧需求。

### 4. Agent Loop / MCP 协议
- 原理：Agent Loop = 模型调用 → 工具执行 → 结果回填 → 循环直到完成；MCP = 工具/资源/提示三类原语的标准协议（类似 USB-C for AI）。
- 蓝图落点：会话 UI 状态机（生成中/等待工具/失败/中断）；工具调用卡片（tool name + 参数 + 结果折叠展示）；中断按钮 = AbortController。
- 审核证据：工具调用失败是否有 UI 反馈；中断后状态是否复位、缓冲是否清空。

---

## 八、TypeScript

- 高频：泛型与工具类型（`Partial/Required/Pick/Omit/ReturnType`）、类型收窄（判别联合 + never 穷尽检查）、`strict` 各开关的意义。
- 蓝图落点：接口响应统一 `types/` 建模、API 层返回类型化；`any` 出现必须注释理由（decision-matrix §2）。
- 审核证据：接口函数无返回类型；`as` 断言滥用绕过检查。

---

## 九、Node.js（仅全栈边界时）

- 事件循环阶段（timers/poll/check）、`process.nextTick` 优先级、流与背压——本项目只用于 BFF/构建脚本参考，应用后端设计仍按「后端侧需求」记录，不展开。
