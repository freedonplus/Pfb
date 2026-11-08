# uni-app x（uvue / UTS）移动端基线

> 本文件沉淀了真实项目（RouteGo）的踩坑经验，uvue 安卓端（编译为 Kotlin）的约束与 H5 差异极大，方案与审核必须以此为依据。

## 版本与工程基线
- HBuilderX 最新版；uni-app x 编译器 5.x；pages.json 管理路由与原生 tabBar
- 语言 UTS（编译为 Kotlin）；UI 文件 `.uvue`；工具/数据层 `.uts`
- 接口层：`uni.request` 统一封装为 Promise + UTSJSONObject 解析
- 配置：`config/env.uts` 条件编译区分 H5/App 的 API base；支持 storage 运行时覆盖便于真机调试

## 目录骨架
```
├── App.uvue            # 全局样式 + onLaunch（预取/预热放这里）
├── pages.json          # 路由、tabBar、globalStyle
├── manifest.json
├── config/env.uts      # 环境配置（条件编译 + storage 覆盖）
├── data/
│   ├── types.uts       # 全部业务类型定义
│   └── api.uts         # 接口层：请求封装 + 字段映射 + 内存缓存
├── utils/              # 反馈、格式化等纯工具
├── pages/<域>/<页>.uvue
└── static/             # 原样打包的资源（音效、图标、tabbar 图）
```

## UTS 严格编译红线（Kotlin 规则，H5 编译不报但安卓必炸）
- **函数/变量必须先声明后使用**：`<script setup>` 内按 Kotlin 局部作用域编译，无 JS 函数提升。生命周期钩子（onLoad/onShow/onTabItemTap）调用的函数必须声明在钩子之前；推荐整体顺序：import → 变量/常量 → computed → 函数 → 生命周期钩子。
- **禁止在 `any` 上点属性**（`res.data`、`err.errMsg` 报"找不到名称"）：在类型化回调处取值再传入。
- **UTS 的 `any` 映射 Kotlin `Any`（非空）**：`res.data` 实际是 `Any?` → 参数类型写 `any | null`；`.catch` 回调写 `(e)` 让编译器推断，用 `(e as Error).message` 取消息，不要写 `(e: Error)` / `(e: any)`。
- 数组避免用 `shift()` 等边缘方法，优先 for/push 或整批重赋值。
- 安卓编译每轮最多只报约 4 个错误，修一批才会暴露下一批——排查同类问题时主动全量扫描，不要等下一轮报错。

## uvue CSS 子集红线
- 不支持：`@keyframes`、`cubic-bezier()`、元素选择器（`page{}`/`view{}`）、百分比 `max-width`、`text-decoration`（警告）
- 支持：类选择器、flex、transition（`ease`/`linear`）、百分比 width/height
- 进场动画用「初始透明 :style 绑定 + transition + 挂载后 setTimeout 切状态」替代 @keyframes
- `hover-class` 支持，但安卓默认 hover-start-time=50ms、hover-stay-time=400ms → 统一补 `hover-start-time="0" hover-stay-time="60"`
- 自绘 view 不触发系统点击音效与震动，需自行接入（vibrateShort + innerAudioContext 极短音效）

## 性能要点
- scroll-view 会把子节点一次性全建出来：长列表限制首屏渲染条数 + `@scrolltolower` 渐进加载，或改 list-view
- tab 页 onShow 拉数据必须节流/缓存，否则每次切 tab 打全套接口
- 常驻轮询页面加 onHide 停止 / onShow 恢复
- 启动页停留期间预取首屏数据（onLaunch 发起）
- 图片：服务器端提供小尺寸缩略图最佳；大图（800×500 PNG）在 96px 缩略场景解码内存浪费严重

## tabBar 与导航
- 原生 tabBar 是系统控件：不支持 custom、不触发页面 @click；点击反馈用 `onTabItemTap`（Android 4.0+）+ onShow 路径变化检测双保险
- 页面内跳 tab 用 `uni.switchTab`，子页面用 `uni.navigateTo`

## 调试经验
- HBuilderX dev server 不能脱离 IDE 手工拉起（缺 easycom、vite root 不含 index.html → 白屏）
- 真机 API base：127.0.0.1 指手机自己，必须用局域网 IP 或公网地址；env.uts 留 storage 覆盖口子
