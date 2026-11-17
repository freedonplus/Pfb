# 能力池总目录（候选库与原生能力全量清单）

用途：**阶段 1.5 能力盘点的唯一依据**。写方案书前必须按本目录把候选一次性全部呈现给用户勾选。
数据基准：2026-11。体积为 gzip 后量级估算，进方案前以 bundlephobia / 实际构建产物核验。

---

## 零、使用规则（违反即视为流程错误）

1. **全量呈现，不做类型预设**：不得因为项目是"后台管理/内部系统"就跳过动画、3D、图表、表格以外的问询。后台恰恰是数据表格与图表最重的场景。
2. **本目录是候选池，不是推荐清单**：默认项标「默认」，但每一项都必须让用户看见并有机会选。
3. **用户不选 = 不引入**，方案书只写「明确不引入 + 一句理由」，不写"未考虑"。
4. **一组一议**：每个能力域单独让用户决策"要/不要/已定"，不要打包成一个"要不要用第三方库"的大问题。
5. **原生优先**：本目录第 §二 的原生能力清单先过一遍，原生能做的先排除引库。
6. **任何库进方案必须过 §一 四道闸**，未过闸的写入"否决项"。

---

## 一、准入四道闸（每项技术选型都要过）

| 闸 | 判定标准 | 不过闸怎么办 |
| --- | --- | --- |
| ① 体积 | < 30KB 可进主包；30–100KB 必须路由级懒加载；> 100KB 必须动态 `import()` + 加载态 + 降级方案 | 降级为懒加载，或换更轻替代，或改原生实现 |
| ② 许可证 | MIT / Apache-2.0 / BSD / ISC 放行；GPL / AGPL 闭源商用禁用（需法务确认）；商业授权库（AG Grid 企业版、Handsontable 非商用、Highcharts、TinyMCE 付费功能）必须标注价格与替代方案 | 换开源替代，或明确列入"需采购" |
| ③ 活性 | 近 12 个月有发版，或背后有活跃公司维护；细分领域放宽 star 门槛 | 列为"高风险依赖"，方案书标注维护风险 |
| ④ 平台兼容 | 小程序无 DOM、无 WebGL；uni-app x 为 CSS 子集；Tauri 用系统 WebView（旧内核）；Electron 内核固定 | 该库在目标平台直接标"不适用"，并给替代 |

---

## 二、原生能力优先清单（MDN 权威，引库前先过这遍）

**事实核验优先级：MDN 中文（developer.mozilla.org/zh-CN）> WHATWG/W3C 规范 > 库官方文档 > 博客/AI 记忆。**
HTML/CSS/JS 语言层问题一律先查 MDN——它记录了绝大多数被库"包装"掉的原生能力。

| 需求 | 原生方案（多年前就得引库的事，现在不用了） | 备注 |
| --- | --- | --- |
| 弹窗 / 抽屉 | `<dialog>` + `showModal()`，自带焦点陷阱、ESC 关闭、`::backdrop` | 替代大量 Modal 库；样式需自己写 |
| 浮层 / 下拉 / 气泡 | **Popover API**（`popover` 属性 + `popovertarget`），顶层渲染，无 z-index 战争 | 替代 tippy/popper 的多数场景 |
| 禁用交互区 | `inert` 属性，整块不可聚焦不可点 | 弹窗打开时锁背景的最优解 |
| 滚动动画 | `animation-timeline: scroll() / view()` CSS 滚动驱动动画 | 替代 ScrollTrigger 的简单场景 |
| 页面转场 | View Transitions API（`document.startViewTransition`） | 路由切换动画 |
| 长列表性能 | `content-visibility: auto` + `contain-intrinsic-size` | 千行内可免虚拟滚动 |
| 容器响应式 | `@container` 容器查询 | 替代 JS 测宽（ResizeObserver 仍要用于 canvas） |
| 表单校验 UI | `:user-valid` / `:user-invalid` / `:has()` | 替代大量校验样式 JS |
| 日期选择 | `<input type="date|time|datetime-local|month">` + `showPicker()` | 移动端原生体验最好 |
| 自动高度文本域 | `field-sizing: content`（新特性，需 `@supports`） | 渐进增强 |
| 深拷贝 | `structuredClone()` | 支持 Map/Set/Date/循环引用；不能克隆函数 |
| 超时与取消 | `AbortSignal.timeout(ms)` / `AbortSignal.any([...])` | 请求超时不用自己封 timer |
| 任务调度 | `scheduler.postTask()` / `scheduler.yield()` | 长任务让位输入响应 |
| 数字/货币/日期格式化 | `Intl.NumberFormat / DateTimeFormat / RelativeTimeFormat / Collator` | 替代 numeral、moment 的大部分用法 |
| URL 处理 | `URL` / `URLSearchParams` | 手写 query 拼接是事故源 |
| 图片懒加载 | `<img loading="lazy" decoding="async">` + `fetchpriority` | 优先于 JS 懒加载库 |
| HTML 注入安全 | `Element.setHTML()`（Sanitizer API）+ Trusted Types | 比手写 DOMPurify 调用更彻底 |
| 视图过渡的列表动画 | `@starting-style` + `transition-behavior: allow-discrete` | 元素出现/消失过渡 |
| 滚动行为 | `scroll-behavior: smooth` + `scroll-snap` + `overscroll-behavior` | 替代 smooth-scroll 库 |
| 模态内的滚动条抖动 | `scrollbar-gutter: stable` | 弹窗必备 |

> 方案书里凡引用第 §二 的项，写"原生能力 + 浏览器基线要求"，不写成第三方方案。

---

## 三、能力域清单（12 组，逐组询问）

### A. 3D / WebGL / 图形渲染

解决：产品三维展示、配置器、展厅、游戏化、Shader 视觉特效。

| 库 | 地址 | 体积（约） | 何时值得引入 | 已知坑 / 红线 |
| --- | --- | --- | --- | --- |
| **Three.js** | threejs.org | 600KB / gzip ≈ 150KB（按需可降至 60KB 级） | 需要自建 3D 场景、材质、灯光、相机交互 | 必须按需 import + 动态加载；移动端 GPU 碎片化；首屏禁用，必须配静态图降级；**生态配套**：react-three-fiber、drei、three-stdlib |
| **`<model-viewer>`** | modelviewer.dev | ≈ 90KB | 只是展示 glTF/GLB 模型（转一圈、看细节） | **多数"产品 3D 展示"用它就够**，比 Three.js 省一个数量级；先问用户是不是这一类 |
| Babylon.js | babylonjs.com | 更大（≈ 800KB+） | 需要内置物理、材质系统、编辑器、游戏化 | 体积与学习成本高，非游戏向不建议 |
| PixiJS | pixijs.com | ≈ 130KB | 2D WebGL 批量精灵、粒子、2D 游戏 | 与 DOM 交互边界要设计；文字渲染需位图字体 |
| OGL | ogl.vercel.app | ≈ 40KB | 自定义 shader 的极简 WebGL 可视化 | 无场景图封装，几何/材质全手写 |
| Rive | rive.app | 运行时 ≈ 100KB（wasm） | 可交互的矢量角色/图标（状态机驱动） | 需设计师用 Rive 编辑器；构建产物要预加载 |
| CesiumJS | cesium.com/cesiumjs | 很大（≈ 1MB+） | 三维地理、地形、倾斜摄影 | 必须懒加载；底图合规见 §H |
| Shader 学习 | thebookofshaders.com | — | 团队自研 shader | 与库无关，属能力建设 |

**追问要点**：3D 是"展示型"还是"交互型"？展示型优先 `<model-viewer>`；移动端占比多少（决定是否值得做）；模型从哪来（glTF + Draco/Meshopt 压缩）。

### B. 动画与动效编排

解决：页面转场、滚动叙事、微交互、数据动画、加载态。

| 库 | 地址 | 体积（约） | 何时值得引入 | 已知坑 / 红线 |
| --- | --- | --- | --- | --- |
| **CSS 原生** | MDN | 0 | 过渡、hover、状态切换、`@keyframes`、滚动驱动动画 | 永远是第一选择；CSS 能做的引库即负债 |
| **Motion** | motion.dev | ≈ 18KB（`motion/mini` 更小） | React/Vue 的布局动画、弹簧、手势、进退场编排 | 前身 Framer Motion；Vue 版 API 有差异；别在长列表逐项建实例 |
| **GSAP** | gsap.com | core ≈ 23KB gzip，插件按需 | 时间线编排、ScrollTrigger 滚动叙事、复杂序列 | 时间线能力最强；**必须 `registerPlugin` 按需**；无限动画出视口要暂停；许可证随版本变化需现场核验 |
| **anime.js** | animejs.com | ≈ 10KB | 轻量补间、SVG 路径动画、简单序列 | v4 起 ESM/tree-shaking；适合"只差一点点"的场景，别用它做滚动叙事 |
| Lottie | airbnb.io/lottie | ≈ 70KB（light 版更小） | 设计师 AE 导出动画、插画动效 | 文件体积与帧性能是主要成本；首屏大 Lottie 禁用；优先 `.lottie` 格式 |
| AutoAnimate | auto-animate.formkit.com | ≈ 3KB | 列表增删、展开收起的一行式动画 | 能力窄，复杂编排不够用 |
| Swiper | swiperjs.com | ≈ 40KB | 轮播、画廊、移动端滑动 | 与 CSS scroll-snap 重叠，简单轮播优先原生 |
| Embla Carousel | embla-carousel.com | ≈ 6KB | 轻量、可控的轮播 | 无内置 UI，需自己写样式 |
| Lenis | github.com/darkroomengineering/lenis | ≈ 8KB | 平滑滚动（品牌站、叙事页） | 与原生 `scroll-behavior` 冲突；无障碍需保留"关闭动效"降级 |
| Typed.js / Splitting.js | — | 小 | 文字逐字、拆分字符动效 | 仅装饰，注意 SEO 与读屏 |

**选型口诀**：CSS 能做 → 原生；要编排 → Motion（现代）/ GSAP（时间线最强）；SVG 路径 → anime.js；设计师交付 → Lottie/Rive；轮播 → Embla/Swiper。
**通用红线**（对齐 ui-patterns §四）：只动 `transform/opacity`；`backdrop-filter`/大面积 `box-shadow` 不做常驻动画；`will-change` 仅动画期挂；视口外暂停；`prefers-reduced-motion` 全局降级。

### C. 图表与数据可视化

解决：运营看板、大屏、报表、趋势对比、关系图、地理分布。

| 库 | 地址 | 体积（约） | 何时值得引入 | 已知坑 / 红线 |
| --- | --- | --- | --- | --- |
| **ECharts** | echarts.apache.org | 全量 ≈ 330KB，按需 100–150KB | 国内看板/大屏首选，图表类型最全，中文文档好 | 必须按需引入（`echarts/core` + 按需 chart/component）；实例必须 `dispose()`；大数据用 `dataZoom`/`appendData`/`progressive` |
| **Chart.js** | chartjs.org | ≈ 65KB | 常规图表、C 端轻量展示、API 简单 | 复杂联动与大屏能力弱；按需注册 controller/element |
| D3 | d3js.org | 全量 ≈ 90KB（分模块引入） | 定制可视化、非标准图形、数据驱动 DOM | 它不是"图表库"而是绘图原语，学习成本高；只在标准图表不够用时引 |
| Observable Plot | observablehq.com/plot | ≈ 60KB（含 D3 子集） | 探索性图表、快速出图 | 定制自由度低于 D3；生态偏英文 |
| uPlot | github.com/leeoniya/uPlot | ≈ 15KB | 百万点级时序、监控曲线 | 只做折线/柱状等基础图；样式定制受限 |
| TanStack Charts 等新秀 | tanstack.com | 视版本 | 需要 headless 图表 | 生态成熟度需现场核验 |
| AntV 系列 | antv.antgroup.com | G2 ≈ 200KB / G6 关系图 / F2 移动端 / L7 地理 | 关系统计、移动端、地理可视化 | 按场景选子包，勿整包引入 |
| deck.gl | deck.gl | 很大（≈ 400KB+） | 大规模地理/点云 GPU 可视化 | 必须懒加载；与 MapLibre/Cesium 搭配 |
| 大屏装饰 | DataV（Vue 生态） | 中 | 数字翻牌、边框装饰 | 视觉装饰非数据能力，别当图表库用 |

**追问要点**：图表数量与复杂度（3 个静态图 vs 20 个联动图）；是否有大屏/投屏（影响 ECharts 首选）；是否需要导出图片/PDF；数据量级（万点以上看 uPlot/deck.gl 方向）。
**踩坑通用项**：SSR 下图表必须 client-only；`ResizeObserver` 而非 `window.resize`；切 tab 后需 `resize()`；主题切换要重新 `setOption` 或换主题实例。

### D. 数据表格（后台与数据产品的核心，优先级最高）

解决：分页列表、服务端排序筛选、固定列、行内编辑、树形表、Excel 式录入、导出。

| 库 | 地址 | 体积（约） | 何时值得引入 | 已知坑 / 红线 |
| --- | --- | --- | --- | --- |
| **DataTables.net** | datatables.net | core ≈ 35KB + 扩展 | 传统后台要"开箱全能"：服务端处理、固定列、列显隐、导出、行内编辑、复杂筛选 | 生态成熟、文档量大；2.x 已支持非 jQuery 模式，旧版依赖 jQuery；样式需配合其 CSS 主题；扩展按需引（Buttons/Select/Editor 部分收费） |
| **TanStack Table** | tanstack.com/table | ≈ 15KB | 现代栈（React/Vue/Solid/Svelte）要完全自控 UI 的复杂表格 | Headless：样式、虚拟滚动、拖拽全自己接（配 `@tanstack/virtual`）；学习曲线在"组合 API"思维 |
| AG Grid | ag-grid.com | 社区版大（≈ 300KB+） | 企业级：百万行、透视、聚合、Excel 级交互、行列分组 | **社区版 MIT，企业功能收费**（需评估预算）；体积大必须懒加载 |
| Handsontable | handsontable.com | 大 | Excel 式录入（粘贴、公式、单元格校验） | 商业授权，非商用需确认；体积大 |
| vxe-table | vxetable.cn | 中大 | Vue 生态全能表格：虚拟滚动、编辑、树表、合并 | 国产文档友好；版本升级有破坏性变更，锁版本 |
| Element Plus Table / Ant Design Table | — | 已含在组件库 | 常规后台列表（< 500 行、列不多） | 大数据量配虚拟滚动插件；固定列多时注意重绘性能 |
| Tabulator / Grid.js | tabulator.info / grid.js.org | 中 / 小 | 轻量替代、静态数据表 | 生态小于 DataTables |
| 虚拟滚动基座 | tanstack.com/virtual、vue-virtual-scroller、react-window、virtua | 5–15KB | 万行以上（配合上面任一表格） | 行高不固定时需预估+修正，否则滚动跳动 |

**选型口诀**：要"现成全能 + 服务端处理"→ DataTables；要"完全定制 + 现代栈"→ TanStack Table；要"Excel 体验"→ AG Grid/Handsontable（先算钱）；Vue 中后台求快 → vxe-table。
**通用红线**：万行必须虚拟滚动或服务端分页；列数 > 30 时避免每列一个响应式 watcher；导出大数据（> 5 万行）走后端生成，不在前端拼 Excel。

### E. UI 组件库与 CSS 框架

完整目录与默认推荐矩阵见 `component-libraries.md`（PC/移动/小程序/Web Components 四类）。此处只补**非主流但值得知道**的：

| 库 | 地址 | 体积（约） | 何时值得引入 | 已知坑 / 红线 |
| --- | --- | --- | --- | --- |
| **Cirrus UI** | cirrus-ui.netlify.app | 纯 CSS，≈ 30KB | 静态站、文档站、轻量后台、不想引 JS 框架时的整套 UI | 类 Bootstrap 的类名体系；生态小、组件更新慢、复杂交互需自己补 JS；适合"轻"，不适合"重" |
| Bulma | bulma.io | ≈ 30KB | 纯 CSS、Flexbox 语义化 | 无 JS 组件（下拉/弹窗要自备） |
| Pico.css | picocss.com | ≈ 10KB | 语义化 HTML 直接成型的极简站 | 定制空间小 |
| Tailwind CSS / UnoCSS | tailwindcss.com / unocss.dev | 产物 5–20KB | 高度定制视觉、设计与开发协作 | 类名可读性、团队规范必须提前定；禁止无约束堆类 |
| DaisyUI / shadcn-ui | daisyui.com / ui.shadcn.com | 小 / 按需 | 快速有设计感的组件层 | shadcn 是"复制进项目"模式，需自己维护升级 |
| Radix UI / Headless UI / Ark UI | radix-ui.com / headlessui.com / ark-ui.com | 按需 | 要无障碍与交互逻辑、不要视觉 | 只给行为不给样式，视觉全靠自研 |

### F. 图标 / 字体 / 图形资源

| 资源 | 地址 | 说明 |
| --- | --- | --- |
| iconify | iconify.design | 20 万+ 图标聚合，按需 tree-shake，一个入口多套图标 |
| Lucide / Phosphor / Tabler / Heroicons | lucide.dev / phosphoricons.com / tabler.io/icons / heroicons.com | 主流开源图标集，风格各异 |
| IconPark（字节） | iconpark.oceanengine.com | 可配置线面/粗细的国产图标库 |
| Fontshare / Google Fonts | fontshare.com / fonts.google.com | 免费商用字体；中文必须子集化（subfont） |
| unDraw / Storyset | undraw.co / storyset.com | 可换色插画 |
| Hero Patterns | heropatterns.com | SVG 背景纹理 |

### G. 富文本 / 编辑器 / 图形编辑

| 库 | 地址 | 体积 | 何时值得引入 | 坑 |
| --- | --- | --- | --- | --- |
| Tiptap | tiptap.dev | 按需（核心小） | 现代富文本首选，headless、可扩展 | 扩展生态部分为付费；协同需配 Yjs |
| Lexical（Meta） | lexical.dev | 中 | 高性能编辑器、复杂自定义节点 | 生态较新，文档在补齐 |
| Slate | slatejs.org | 中 | 高度定制编辑器 | 需自己处理规范化，坑多 |
| Quill / wangEditor | quilljs.com / wangeditor.com | 小 | 常规富文本、国产轻量（中文排版友好） | wangEditor 维护节奏需核验 |
| CodeMirror 6 | codemirror.net | ≈ 150KB 起 | 代码/JSON/SQL 编辑、轻量 | 语言包按需；比 Monaco 轻一个量级 |
| Monaco | microsoft.github.io/monaco-editor | ≈ 2MB+ | 需要 VS Code 级体验（智能提示、diff） | 必须懒加载 + worker 配置；移动端不可用 |
| KaTeX / MathJax | katex.org / mathjax.org | 小 / 大 | 数学公式渲染 | KaTeX 快但语法子集；MathJax 全但慢 |
| tldraw / Excalidraw | tldraw.dev / excalidraw.com | 中大 | 白板、手绘风绘图 | 许可条款需核验（tldraw 有商用限制） |
| AntV X6 | x6.antv.antgroup.com | 中 | 流程图、DAG 编排 | 与 React/Vue 集成需封装 |

### H. 地图 / 地理

| 库 | 地址 | 何时值得引入 | 坑 / 合规红线 |
| --- | --- | --- | --- |
| 高德 JS API / 百度地图 GL / 腾讯地图 | lbs.amap.com / lbsyun.baidu.com / lbs.qq.com | 国内业务定位、路线、POI | 需 key 与配额；**中国地图必须使用合规底图与审图号，禁止用境外底图绘制中国版图，边界与九段线不得自行简化** |
| MapLibre GL | maplibre.org | 开源矢量地图，Mapbox 的开源替代 | 需自备矢量瓦片源 |
| Mapbox GL JS | mapbox.com | 设计感强的定制地图 | v2 起按量计费 + token |
| Leaflet | leafletjs.com | 轻量 2D 地图、简单打点 | 不做 3D/矢量瓦片高级效果 |
| Cesium / deck.gl | 见 A 组 | 三维地理、大规模可视化 | 体积大，必须懒加载 |

### I. 文件 / 媒体 / 导出

| 需求 | 推荐 | 地址 | 体积 | 坑 |
| --- | --- | --- | --- | --- |
| 上传（大文件/分片/断点续传） | Uppy | uppy.io | 中 | 分片策略需后端配合 |
| 上传（轻量美化） | FilePond / react-dropzone | pqina.nl/filepond / react-dropzone.js.org | 小 | — |
| 图片压缩（前端） | browser-image-compression | github.com/Donaldcwl/browser-image-compression | 小 | 大图耗 CPU，放 Worker |
| 图片裁剪 | Cropper.js | fengyuanchen.github.io/cropperjs | 小 | 移动端手势需调 |
| 图片处理（Node/构建期） | sharp | sharp.pixelplumbing.com | 服务端 | 与前端无关，CI 预处理用 |
| PDF 预览 | PDF.js | mozilla.github.io/pdf.js | ≈ 350KB | 必须懒加载；移动端内存 |
| PDF 生成 | pdf-lib / jsPDF | pdf-lib.js.org / github.com/parallax/jsPDF | 中 | 中文字体需内嵌，包体激增 → 优先后端生成 |
| Excel 导入导出 | SheetJS（xlsx）/ exceljs | sheetjs.com / github.com/exceljs/exceljs | 中 | 大数据走后端；日期/公式坑多 |
| 视频播放 | hls.js / video.js | github.com/video-dev/hls.js / videojs.com | ≈ 150KB / 中 | HLS 必用；Safari 原生支持可省 |
| 音频波形 | wavesurfer.js | wavesurfer.js.org | 中 | 大文件预解码耗时 |
| 二维码 | qrcode / qr-scanner | github.com/soldair/node-qrcode / github.com/nimiq/qr-scanner | 小 | 扫描需摄像头权限兜底 |

### J. 表单 / 校验 / 交互增强

| 需求 | 推荐 | 地址 | 体积 | 坑 |
| --- | --- | --- | --- | --- |
| Schema 校验 | zod / valibot | zod.dev / valibot.dev | ≈ 14KB / ≈ 3KB | valibot 更小但生态较新；zod v4 性能已改善 |
| 表单管理（React） | react-hook-form | react-hook-form.com | 小 | 与 UI 库 Controller 配合 |
| 表单管理（Vue） | vee-validate / FormKit | vee-validate.logaretm.com / formkit.com | 小 / 中 | FormKit 视觉强、定制受限 |
| 拖拽排序 | SortableJS / dnd-kit / vue-draggable-plus | sortablejs.github.io/Sortable / dndkit.com | ≈ 13KB / ≈ 20KB | 跨列表拖拽与虚拟滚动同时用要额外处理 |
| 虚拟列表 | @tanstack/virtual / virtua | tanstack.com/virtual / github.com/inokawa/virtua | 5–15KB | 不定高需预估+修正 |
| 日期 | Day.js / date-fns / Temporal polyfill | day.js.org / date-fns.org | ≈ 3KB / 按需 | 简单格式化优先 `Intl`；Temporal 是未来标准 |
| 工具函数 | es-toolkit（推荐）/ lodash-es / radash | es-toolkit.dev / lodash.com | 按需 | lodash 全量引入是常见体积事故；用 `lodash-es` + 按需 |
| 数字/货币输入 | 原生 + `inputmode` | MDN | 0 | 优先原生，避免引库 |
| 剪贴板/下载 | Clipboard API / `showSaveFilePicker` | MDN | 0 | 需用户手势触发 |

### K. 状态 / 数据请求 / 实时

| 需求 | 推荐 | 地址 | 坑 |
| --- | --- | --- | --- |
| 请求基础 | axios / ofetch / ky | axios-http.com / unjs.io | fetch 封装 + 拦截器 + 取消即可覆盖多数场景 |
| 服务端状态 | TanStack Query / SWR / Pinia Colada | tanstack.com/query / swr.vercel.app | 与全局 store 职责分离：服务端数据不塞 store |
| 全局状态（Vue） | Pinia | pinia.vuejs.org | 只放真正全局（登录态/主题/字典） |
| 全局状态（React） | Zustand / Jotai / Valtio | zustand.pmnd.rs / jotai.org / valtio.pmnd.rs | 按"读多写少/细粒度订阅"选型 |
| 复杂流程 | XState | stately.ai | 状态机适合多步表单/订单流程；小场景过度设计 |
| 实时 | socket.io / 原生 WebSocket / SSE | socket.io | AI 流式见 knowledge-system §七；socket.io 需服务端同协议 |
| 本地存储 | Dexie（IndexedDB）/ localforage / idb-keyval | dexie.org | 大数据用 IndexedDB，别塞 localStorage |
| 离线 | Workbox / PWA | developer.chrome.com/docs/workbox | 缓存策略要防"旧版本永不更新" |

### L. 工程 / 质量 / 监控

| 需求 | 推荐 | 地址 | 坑 |
| --- | --- | --- | --- |
| 构建 | Vite / Rspack / Turbopack | vitejs.dev / rspack.dev | 存量 webpack 用 Rspack 平滑迁移 |
| 单测 | Vitest | vitest.dev | 与 Vite 配置共用 |
| E2E | Playwright / Cypress | playwright.dev / cypress.io | Playwright 多浏览器更稳 |
| 组件测试 | Testing Library | testing-library.com | 测行为不测实现 |
| 接口 mock | MSW | mswjs.io | 契约先行时必用（见 interview 第 7 轮接口就绪度） |
| Lint/格式化 | ESLint + Prettier / Biome | eslint.org / biomejs.dev | Biome 一套替两者，生态较新 |
| 未使用依赖检测 | knip | knip.dev | 上线前清依赖 |
| 体积分析 | rollup-plugin-visualizer / size-limit | 各自 GitHub | CI 加体积预算门禁 |
| 错误监控 | Sentry / 各家 RUM（ARMS、腾讯 RUM） | sentry.io | 必须配 sourcemap 上传 + 脱敏 |
| 性能指标 | web-vitals | github.com/GoogleChrome/web-vitals | 对齐 knowledge-system §六.2 阈值 |
| 无障碍检测 | axe-core / pa11y | deque.com/axe | CI 集成，别只靠人工 |
| 组件文档 | Storybook / Ladle | storybook.js.org / ladle.dev | 中大型项目才值得 |

---

## 四、阶段 1.5 呈现脚本（照此执行）

**开场白（对用户说的话，不要省略）**：

> 方案书定稿前，我先把可用的第三方能力按 12 类列给你。这里的判断标准不是"这个项目类型通常用什么"，而是"你的项目实际需不需要"。后台项目同样可能要用动画库做效率型微交互、用 DataTables 承载服务端表格、用 ECharts 做看板。逐组告诉我：要 / 不要 / 已经定了。

**呈现方式（硬要求）**：

1. 先给一张**能力域总览表**（12 行：能力域 / 代表方案 / 本项目默认倾向 / 一句话价值），让用户先看全貌。
2. 再按组问：每轮 2–3 组，用 AskUserQuestion，选项为「默认组合（推荐）」「自选（我在补充里说）」「不需要」，并在同一轮的文本里把该组的具体候选与体积列清楚。
3. 用户在补充里点名了目录外的库 → 现场按 §一 四道闸评估，写入方案并标注"用户指定"。
4. 全部回答结束后，输出「能力选型回执表」：能力域 / 是否引入 / 选定库 / 体积预算 / 加载方式（主包 / 懒加载 / 动态 import）/ 平台限制 / 否决项。
5. 回执表确认后再进入阶段 2 写方案书。

**每条选型在方案书里必须写成三件套**：选择 + 理由（对应哪个业务需求）+ 否决项（为什么不选备选）+ 加载策略（进主包还是懒加载）。

---

## 五、收录与扩充规则（让目录可持续）

- 新增条目四要素：**官方地址 / 体积量级 / 适用与不适用 / 已知坑**。缺一不收。
- 优先收录两类：① 能替代重库的原生能力；② 细分领域的事实标准。
- 每季度核对一次：是否停更、是否有更优替代、许可证是否变化（尤其 GSAP、AG Grid、Handsontable、tldraw、TinyMCE、Mapbox 这类许可会变的）。
- 体积与活性以现场核验为准：bundlephobia（体积）、npm 发版记录（活性）、官方 pricing 页（许可）。

**待补清单（明确留白，勿凭记忆补写）**：
- WebGPU / WebAssembly 工具链（wgpu、emscripten、wasm-bindgen）
- AI 前端 SDK（各家 LLM SDK、Vercel AI SDK、流式 UI 组件）
- 低代码/搭建器（amis、lowcode-engine、craft.js）
- 国际化（i18next、vue-i18n、FormatJS）
- 打印与票据（print-js、escpos）
- 语音与音视频通话（WebRTC、声网、TRTC）
- 无障碍测试与读屏兼容
- 图表 WebGL 进阶（deck.gl 已列，需补 WebGPU 路径）
