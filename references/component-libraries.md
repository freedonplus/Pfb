# 组件库选型目录 + 设计资源入口

用途：阶段 1 第 5 轮组件库提问、阶段 2 撰写技术栈与「UI 组件复用与设计系统」章节时加载；审核时核验选型合理性。
数据基准：2026-11 整理（star 为参考值，选型时以实时数据与最近发版时间现场核查）。
边界：本文件只管"整套 UI 组件库与设计资源入口"；**图表、数据表格、3D、动画、编辑器、地图等能力库见 `library-catalog.md`**，此处只给索引，不重复维护体积与坑。

---

## 一、组件库选型四步法

1. **定端**：PC 后台 / 移动 H5 / 小程序 / 跨端（uni-app）——端不同目录完全不同。
2. **配框架**：与主框架严格同栈（Vue3 项目禁止 vue2 组件库；react 组件库不进 vue 项目）。
3. **核活跃度**（硬门槛）：star ≥ 3k 或大厂背书（字节 Semi/Arco、京东 NutUI、腾讯 TDesign、滴滴、众安）；近 6 个月有发版；issue 响应正常。三个条件缺一即降级为备选。
4. **配风格**：组件库默认气质与方案书主风格（ui-patterns.md §二）冲突时，优先换库而非深改主题——深改第三方主题的维护成本高于换库。

---

## 二、组件库目录（按端与框架）

### PC · React
| 库 | star | 定位 |
| --- | --- | --- |
| MUI（Material UI） | 87.5k | Material 生态最全，国际化产品 |
| Ant Design | 86.5k | 国内后台事实标准，企业级设计语言（推荐） |
| Chakra UI | 33.4k | 可及性优先，风格轻量，适合 C 端 |
| Fluent UI（微软） | 15.9k | 含 web-components 版，跨平台共享代码 |
| Semi Design（字节） | 7.1k | 设计师-开发者协作友好，抖音系 |
| Arco Design（字节） | 4k | 企业级，可定制主题强 |
| PrimeReact | 4.2k | 组件数量多（含付费模板生态） |

### PC · Vue
| 库 | star | 定位 |
| --- | --- | --- |
| Vuetify | 37.4k | Material 风格，vue2/vue3 双支持 |
| Element UI | 25.5k | 仅 vue2，存量项目维护用 |
| Element Plus | 20.4k | Element 的 vue3 版，国内后台标配（推荐） |
| Ant Design Vue | 18.3k | Ant Design 体系的 Vue 实现 |
| Naive UI | 13k | vue3 原生、TS 优秀、主题定制灵活，C 端友好（推荐） |
| OpenTiny（华为云） | 448 | 企业级，vue2/vue3/angular 多框架 |
| View UI Plus / iDux / BalmUI | <1k | 中后台，团队自用优先级低 |

### Mobile · Vue
| 库 | star | 定位 |
| --- | --- | --- |
| Vant（有赞） | 21.5k | 移动 H5 事实标准，vue2/vue3/微信小程序（推荐） |
| NutUI（京东） | 5.4k | 京东风格，vue2/vue3/react/taro 多端 |
| Varlet | 4.2k | vue3 + Material 风格移动端 |
| Mand Mobile（滴滴系） | 3.4k | 金融场景组件（数字键盘/身份证输入） |
| Cube-UI（滴滴） | 9.1k | vue2 移动端，维护趋缓 |
| Mint UI | 16.6k | 仅 vue2，存量项目 |

### 小程序 / uni-app
| 库 | star | 定位 |
| --- | --- | --- |
| Vant Weapp | — | 微信小程序事实标准（推荐） |
| TDesign（腾讯） | 2.7k | 多端（vue2/vue3/腾讯系小程序/react，taro/flutter 计划中）（推荐） |
| Lin UI | 3.9k | 原生小程序组件 |
| uView UI | 3.8k | uni-app（vue2）多端 |
| Thor UI | 2.3k | 微信小程序 + uniapp |
| MUI-DCloud | 13.4k | DCloud 系原生体验跨端框架（非组件库，架构级选型） |

### 框架无关 / Web Components
| 库 | star | 定位 |
| --- | --- | --- |
| Fluent UI web-components | 15.9k | 微软系，多框架共享 UI |
| quark-design | 1.7k | 下一代 Web Components，任意/无框架可用 |
| LULU UI | 2.6k | 原生 JS，面向 C 端用户侧产品 |
| OnsenUI | 8.7k | 移动混合 App（angular/react/vue 全家桶） |

### 默认推荐矩阵（写方案直接引用）
| 场景 | 首选 | 备选 |
| --- | --- | --- |
| PC 后台 · Vue3 | Element Plus | Ant Design Vue / Naive UI |
| PC 后台 · React | Ant Design | Semi Design / MUI |
| PC · Material 风格 | Vuetify（vue）/ MUI（react） | — |
| 移动 H5 · Vue | Vant | NutUI / Varlet |
| 移动 · 金融场景 | Mand Mobile | Vant（自行补金融组件） |
| 微信小程序 | Vant Weapp | TDesign 小程序版 / Lin UI |
| uni-app 跨端 | uView | MUI-DCloud（原生体验要求高时） |
| 多框架共享组件资产 | quark-design / Fluent web-components | 自建 Web Components |

> star 数据仅作量级参考；任何库进方案前必须现场核查「最近发版时间 + 开放 issue 趋势」。

---

## 二之补 · 非组件类能力库索引（详见 library-catalog.md）

组件库解决"页面骨架"，但真实项目的能力缺口通常在下面几类。**这四类是方案书里最常被漏掉的，阶段 1.5 必须逐项确认。**

| 能力 | 首选 | 备选 | 何时不该引组件库自带方案 | 详见 |
| --- | --- | --- | --- | --- |
| 数据表格 | DataTables.net（开箱全能）/ TanStack Table（headless 定制） | AG Grid（企业级，付费评估）/ vxe-table（Vue）/ 组件库自带 Table + 虚拟滚动 | 需要服务端处理、固定列、列显隐、导出、万行虚拟滚动时，组件库自带表格通常不够 | `library-catalog.md` §D |
| 图表 | ECharts（看板/大屏，中文场景） | Chart.js（轻量常规图）/ D3（定制）/ uPlot（百万点时序） | 组件库的 Chart 封装能力最弱，几乎都要另选 | §C |
| 3D / WebGL | `<model-viewer>`（仅展示模型） | Three.js（自建场景）/ PixiJS（2D 粒子）/ Rive（交互矢量） | 组件库不含 3D，必须单独决策 | §A |
| 动画编排 | 纯 CSS（默认） | Motion（布局/手势）/ GSAP（时间线/滚动叙事）/ anime.js（SVG 轻量） | 组件库自带 transition 只够 hover 级 | §B |
| 纯 CSS 框架（不引 JS） | Cirrus UI（cirrus-ui.netlify.app） | Bulma / Pico.css / Tailwind + DaisyUI | 静态站、文档站、轻后台：引 JS 组件库属于过度 | §E |
| Headless 可及性原语 | Radix UI（React）/ Ark UI（多框架） | Headless UI / shadcn-ui（复制进项目） | 要完全自控视觉又要无障碍时，带视觉的组件库是负担 | §E |
| 富文本 / 代码编辑 | Tiptap（富文本）/ CodeMirror 6（代码） | Lexical / wangEditor / Monaco（重） | 组件库不含，需独立选型 | §G |
| 地图 | 高德 JS API（国内，需合规底图） | MapLibre GL / Leaflet | 组件库不含；中国地图有合规红线 | §H |

---

## 三、设计资源入口总表（方案书引用素材时逐类取用）

### 总目录：designeer.org（422 个精选工具，含 132 位 design engineer 名录）
| 分类 | 数量 | 代表资源 | 方案书怎么用 |
| --- | --- | --- | --- |
| Inspiration · Galleries | 78 | curated.design、Refero（styles.refero.design）、Cosmos、Framer 画廊 | 风格选型阶段找参考截图，附进方案书 |
| Components · 组件库 | 109 | shadcn、ForgeUI、RewampUI、uiverse.io | L3 抄改组件来源（过 ui-patterns §一准入门槛） |
| Components · 动画库 | 11 | GSAP、Motion（motion.dev）、Remotion | 见下方「动画库选型红线」 |
| Dev Tools · Agents & MCP | 35 | Claude Code、Cursor 等 | AI 辅助开发工具链推荐，与方案 AI 章节衔接 |
| Design Assets · Icons | 30 | Lucide、Phosphor、iconify | 图标库选型（interview.md 第 5 轮） |
| Design Assets · Fonts/Color/Shaders | 19 | Fontshare、OKLCH、color.review、Book of Shaders、Three.js / React Three Fiber | 字体子集化、取色器、WebGL 特效进阶 |
| Utilities | 61 | SVGOMG、tldraw、Excalidraw | 工具类按需 |

### 动画库选型红线
- **先问 CSS 能不能做**：hover、过渡、简单入场一律纯 CSS，0 字节；引库只解决"CSS 做不到"的部分（时间线编排、物理弹簧、滚动叙事、SVG 路径）。
- 进 bundle 前评估体积：GSAP 必须 `registerPlugin` 按需（core ≈ 23KB gzip）；Motion ≈ 18KB 且 API 更现代，多数场景优先 Motion；anime.js ≈ 10KB 适合轻量场景。
- 动画库只做编排，不做"逐项实例化"：长列表内禁止每行一个动画实例，离开视口销毁或复用（见 ui-patterns §四）。
- WebGL / Three.js 只用于首屏 hero 级场景，必须懒加载 + 低端机降级静态图；**纯模型展示优先 `<model-viewer>`（≈90KB），不要上 Three.js**。
- 许可证现场核验：GSAP 随版本调整过授权方式，商业项目落地前核对官方 pricing/license 页；Lottie/Rive 的运行时与编辑器授权范围不同。

### 模板站：mobanwang.com（10000+ HTML/Bootstrap5 模板）
- 适用场景：仅需展示型企业官网、无交互后台、预算极小时——直接选模板改造，方案书走「轻量路线」。
- 边界红线：模板改造同样过 production-checklist 全部 P0（缓存策略、懒加载、安全头、SEO 基础）；禁止作为生产级中后台/复杂 C 端的地基。
- 与 StyleKit/uiverse 的分工：模板站给「整页起点」，StyleKit 给「风格方向」，uiverse 给「组件零件」。

### 其余常驻入口
- uiverse.io：4500+ 开源视觉组件（L3 抄改来源，见 ui-patterns.md §三）。
- stylekit.top/zh/styles：151 种设计风格目录（见 ui-patterns.md §二）。
- MDN Web/CSS：现代 CSS 规范唯一权威（见 ui-patterns.md §五）。
- interview.busyhe.com：大厂考点知识体系（见 knowledge-system.md）。

---

## 四、审核证据点

- 所选组件库与主框架同栈且满足活跃度三门槛（star/发版/issue）
- 组件库按需引入（对齐 audit G1，禁止全量 `app.use` 注册）
- 深改第三方主题的项目：抽检主题覆盖完整性（暗黑模式、新增组件版本升级后的主题回归）
- 动画库使用点是否过「动画库选型红线」
