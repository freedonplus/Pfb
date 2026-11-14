# 组件库选型目录 + 设计资源入口

用途：阶段 1 第 3 轮组件库提问、阶段 2 撰写技术栈与「UI 组件复用与设计系统」章节时加载；审核时核验选型合理性。
数据基准：2026-11 整理（star 为参考值，选型时以实时数据与最近发版时间现场核查）。

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

## 三、设计资源入口总表（方案书引用素材时逐类取用）

### 总目录：designeer.org（422 个精选工具，含 132 位 design engineer 名录）
| 分类 | 数量 | 代表资源 | 方案书怎么用 |
| --- | --- | --- | --- |
| Inspiration · Galleries | 78 | curated.design、Refero（styles.refero.design）、Cosmos、Framer 画廊 | 风格选型阶段找参考截图，附进方案书 |
| Components · 组件库 | 109 | shadcn、ForgeUI、RewampUI、uiverse.io | L3 抄改组件来源（过 ui-patterns §一准入门槛） |
| Components · 动画库 | 11 | GSAP、Motion（motion.dev）、Remotion | 见下方「动画库选型红线」 |
| Dev Tools · Agents & MCP | 35 | Claude Code、Cursor 等 | AI 辅助开发工具链推荐，与方案 AI 章节衔接 |
| Design Assets · Icons | 30 | Lucide、Phosphor、iconify | 图标库选型（interview.md 第 3 轮） |
| Design Assets · Fonts/Color/Shaders | 19 | Fontshare、OKLCH、color.review、Book of Shaders、Three.js / React Three Fiber | 字体子集化、取色器、WebGL 特效进阶 |
| Utilities | 61 | SVGOMG、tldraw、Excalidraw | 工具类按需 |

### 动画库选型红线
- 进 bundle 前评估体积：GSAP 按需 `registerPlugin`，Motion 优先（体积小、API 现代）。
- 动画库只做 CSS 做不了的（时间线编排、物理弹簧、滚动编排）；hover/过渡一律纯 CSS。
- 长列表内动画项禁止逐项挂动画库实例（内存与 GC 压力）；离开视口销毁或复用。
- WebGL/Three.js 仅用于首屏 hero 级场景，必须懒加载 + 低端机降级静态图（对齐 advanced-checklist 精神）。

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
