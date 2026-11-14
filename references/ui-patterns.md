# UI 风格选型与组件复用体系（StyleKit 风格映射 + uiverse.io 模式 + MDN 现代 CSS）

用途：阶段 2 撰写「UI 组件复用与设计系统」章节时加载；审核含自定义组件/动效的项目时按标题 grep。
定位：风格选型管「长什么样」，组件库管「能用」，uiverse 模式管「好看」，现代 CSS 管「怎么写才稳」——四层递进，不是互斥选项。

---

## 一、组件复用三层决策（写入方案书必带「选择 + 理由 + 否决项」）

| 层级 | 适用场景 | 准入规则 |
| --- | --- | --- |
| L1 成熟组件库 | 表单、表格、弹窗、日期选择等**重交互**组件 | 一律不自研（自研成本高且事故率高）；选型目录与默认推荐矩阵见 `component-libraries.md` |
| L2 自研业务组件 | 业务卡片、聚合面板 | 放 `components/`，设计 token 驱动 |
| L3 uiverse 式自研视觉组件 | 按钮/开关/加载动画/tooltips 等**轻交互高视觉**组件 | 允许抄改开源实现（uiverse.io / shadcn 生态 / tailwind 组件站），但必须过准入门槛 |

**L3 抄改准入门槛（四条全过才准进项目）：**
1. 零重依赖：纯 HTML/CSS（或项目已用的 Tailwind）实现，不允许夹带运行时 JS 库。
2. Token 化：颜色/圆角/间距全部映射到项目 CSS 变量（`--color-primary` 等），禁止硬编码色值。
3. 性能达标：只动 `transform/opacity`；`backdrop-filter`、大面积 `box-shadow` 需过 §四红线。
4. 无障碍底线：可交互元素可键盘聚焦（`:focus-visible`）、有 `prefers-reduced-motion` 降级、语义标签正确。

---

## 二、风格选型映射（StyleKit 151 风格体系）

素材来源：stylekit.top/zh/styles（151 种设计风格目录，每种附适用场景与视觉标签）。方案书先定主风格，再谈组件；风格只允许从映射表选，不得凭感觉造词。
提问阶段（interview.md 第 3 轮）按项目类型给 3~4 个候选风格让用户选，选择 + 理由 + 否决项写入方案书。

### 按项目类型的推荐风格

| 项目类型 | 推荐主风格（按优先级） | 视觉特征要点 | 技术要点 |
| --- | --- | --- | --- |
| 后台管理系统 | Corporate Clean（企业简洁风）> Notion 风格 > Dark Mode | 高可读性、一致性、低装饰 | 成熟组件库默认样式微调即可，L3 抄改组件 ≤ 5 个 |
| Dashboard / 数据工具 | macOS Vibrancy > Dark Mode > Corporate Clean | 深灰多层面板、克制 backdrop-blur | glass 预算集中给图表卡片 |
| SaaS / B2B 官网 | Modern Gradient > Soft UI > Apple 风 | 渐变背景、玻璃卡片、柔和阴影 | L3 组件 10~20 个，CTA 用发光边框 |
| 开发者工具 / 技术产品 | Dark Mode > Stripe 风 > macOS Vibrancy | 低对比层次、代码感配色、渐变网格 | 代码块与 mono 字体是第一公民 |
| C 端品牌 / 活动页 | Neo-Brutalist（新野兽派）> Geometric Bold > Modern Gradient | 粗黑边框、硬阴影、高对比撞色 | 实现成本低（无 blur 无渐变）；可及性天然好 |
| 电商零售 | Minimalist Flat > Apple 风 > 自然有机风 | 极简、留白、低噪 | 图片才是主角，UI 退后 |
| 文档 / 知识库 | Notion 风格 > Editorial > Minimalist Flat | 清晰文字层级、柔和 hover、衬线标题可选 | 长文排版（line-height、measure 45~75ch）是核心 |
| 创意表达 / 作品集 | Claymorphism > Vaporwave/Memphis/Pixel Art > 量化线场等实验风格 | 强个性、动效密集 | 仅限流量小的展示型页面；动效红线全量适用 |

### 风格混搭红线

- 主风格 **只允许 1 个**，辅助元素 ≤ 2 种（如 Modern Gradient 主 + 极少量 Neo-Brutalist 按钮点缀）；风格混搭超出即视觉分裂，审核记 P1。
- 重装饰风格（Claymorphism / Vaporwave / Memphis / 像素艺术）**禁止用于后台管理**——可读性与维护性双输。
- 品牌致敬风格（Stripe 风 / Apple 风 / Notion 风）：仅取布局与气质，配色 token 换成项目自己的品牌色，避免侵权观感。

### 可及性与性能注意（选型时同步检查）

- Neumorphism（新拟物）：双重阴影模拟立体 → 低对比是**已知可及性弱点**，输入控件必须有非阴影的第二状态提示（边框/文字）；只用于卡片不用于表单。**慎选**。
- Dark Mode：正文对比度 ≥ 4.5:1，纯黑背景配纯白文字会刺眼，用 `#111` 级深灰。
- 玻璃系（Glassmorphism / macOS Vibrancy）：背景不可控时模糊后可读性下降，正文区不用玻璃，玻璃只给容器/导航。
- 蒸汽波/霓虹系：大面积高饱和渐变在低端机掉帧，动画遵守 §四红线。

---

## 三、uiverse.io 高频组件模式库（实现要点 + 陷阱）

素材来源：uiverse.io（4500+ 开源组件，纯 HTML/CSS/Tailwind，MIT 协议，可商用）。项目里只挑 10~20 个，统一进设计系统，禁止各页面随手抄导致风格分裂。

### 1. 玻璃拟态（Glassmorphism）
- 实现要点：半透明背景 + `backdrop-filter: blur()` + 1px 半透明白边框。
- 陷阱：`backdrop-filter` 是合成层大成本操作。**红线：全屏/大面积玻璃 ≤ 1 层；禁止玻璃卡片嵌套玻璃卡片**；低端机（内存 ≤ 4G）降级为纯半透明色。
- 使用条件：导航栏、单张高亮卡片，仅此两类。

### 2. 发光边框 / 渐变描边（Glow / Gradient Border）
- 实现要点：两层元素 + `padding: 1px` + `linear-gradient` 背景裁切；或 `border-image`；动态流光用 `@property` 注册角度变量 + `conic-gradient` 过渡（现代写法，替代 JS）。
- 陷阱：持续旋转类动画必须只动一个元素且面积小；禁止对超大容器做无限发光动画（合成层压力 + 移动端耗电）。
- 使用条件：CTA 按钮、聚焦态、加载指示。

### 3. 动效按钮（3D / Ripple / Magnetic）
- 实现要点：hover 抬升用 `translateY` + 阴影变化；按压用 `scale`；ripple 用伪元素 `transform: scale()` 过渡（不要用 JS 改 left/top）。
- 陷阱：`will-change` 只在动画期间挂（进出场时加/删），常驻 `will-change` 反而占内存。
- 使用条件：每个页面主 CTA 只一个重动效；次级按钮纯色过渡。

### 4. 加载动画（Loaders / Spinners / 骨架屏）
- 实现要点：优先 SVG（`stroke-dashoffset` 环形）、CSS `animation`（transform）；骨架屏用 shimmer（渐变背景 `background-position` 过渡——这是少数允许 background 动画的情况，仍是合成友好）。
- 陷阱：loading 动画数量多时合并为共享组件；3D/CSS 大型粒子加载页禁止进首屏。

### 5. 开关 / 复选框 / 单选 / tooltips（无 JS 控件）
- 实现要点：`input + label` 关联、`:checked + label` 兄弟选择器、`accent-color` 原生控件低成本美化、tooltip 用 `:hover` + `opacity/transform`。
- 陷阱：自定义表单控件**必须保留原生 input**（可聚焦、可读屏），视觉层盖在上面；`:has(:checked)` 可做父级联动（现代方案）。

### 6. 卡片 Hover 效果
- 实现要点：图片缩放 `transform: scale(1.05)` + 内容位移 `translateY`；光斑跟随用 `--x/--y` CSS 变量 + JS 只更新变量值（一次 rAF 批量写，不逐帧改 style）。

---

## 四、动效性能红线（对齐 production-checklist §三，冲突时以红线为准）

| 红线 | 原因 |
| --- | --- |
| 动画属性仅限 `transform` / `opacity` | 只走合成，不触发重排重绘 |
| `filter: blur()` / `backdrop-filter` 常驻动画禁止 | 每帧重采样，低端机掉帧主力 |
| `box-shadow` 大面积扩散动画禁止 | 重绘成本高；改为伪元素 opacity 淡入淡出 |
| `will-change` 常驻禁止 | 内存占用 + 层数爆炸；仅动画期间挂 |
| 无限循环动画非可视区必须暂停 | `IntersectionObserver` 进出视口启停（离开视口 `animation-play-state: paused`） |
| 用户开启「减弱动态效果」时关闭装饰动画 | `@media (prefers-reduced-motion: reduce)` 全局降级，无障碍硬要求 |

> 本红线只管纯 CSS 动效；动画库（GSAP / Motion / Three.js）的引入条件与体积红线见 `component-libraries.md` §三。

---

## 五、现代 CSS 特性落地（MDN 对齐，含使用条件与降级）

原则：**渐进增强**——新特性做增强层（`@supports` 检测），基础体验必须在不支持的浏览器完整可用；browserslist 里 90%+ 覆盖的特性可免检测直接用。

| 特性 | 用途 | 使用条件与降级 |
| --- | --- | --- |
| 自定义属性 + 设计 token | 主题化唯一入口：`--color-*` / `--spacing-*` 全部集中在 `styles/tokens.css` | 基线能力，必用；暗黑模式切换只换 token 不改组件 |
| `light-dark()` | 亮暗双色值一行声明 | 需配 `color-scheme`；旧浏览器双写 CSS 变量兜底 |
| `@layer` 级联层 | 三方组件库 > 项目工具类 > 组件 scoped > reset 的优先级治理 | 替代 `!important` 与选择器权重军备竞赛；从项目第一天就用（后期迁移成本高） |
| 容器查询 `@container` | 组件随**父容器**尺寸响应（同一组件在侧栏/主区不同表现） | 现代浏览器可用；`@supports (container-type: inline-size)` 包裹；组件库外层挂 `container-type` |
| `:has()` 父选择器 | 表单校验联动 `:has(:invalid)`、卡片 `:has(img:hover)` | 可用但注意是「全局重计算」——禁止长列表每项都用；降级为 JS class 切换 |
| `subgrid` | 表单标签/内容列跨行对齐 | `@supports (grid-template-rows: subgrid)` 内启用 |
| CSS 嵌套 | 组件内就近书写，替代预处理器 | Vite/esbuild 原生支持；注意 `&` 显式写法兼容性更好 |
| `clamp()` / `min()` / `max()` | 流体字号、安全间距，免断点多套媒体查询 | 基线能力，必用 |
| `color-mix()` | hover 态 = `color-mix(in srgb, var(--color-primary), black 10%)`，免人工配色表 | 现代浏览器可用，降级为静态色值 |
| 逻辑属性（`margin-inline` 等） | 国际化 RTL 一套布局 | 新代码默认用；存量的不强改 |
| 滚动驱动动画 `animation-timeline` | 滚动进度条、阅读指示 | Chrome 系可用；降级为 IntersectionObserver + class |
| View Transitions API | 路由切换页面级过渡（SSR/MSPA 感） | `@supports (view-transition-name: a)` 或 `document.startViewTransition` 检测，渐进启用 |
| `accent-color` / `:focus-visible` | 原生控件低成本品牌化、聚焦环 | 必用，无降级成本 |
| `scrollbar-gutter` / `overscroll-behavior` | 弹窗出现不抖动、防滚动穿透 | 弹窗组件必备 |

---

## 六、设计系统落地分层（方案书「UI 组件复用与设计系统」章节骨架）

1. **token 层**：`styles/tokens.css`——颜色/间距/圆角/阴影/字号/动效时长，全站唯一来源，明暗主题各一份值。
2. **原子组件层**：L3 uiverse 式组件收编于此（每个组件标注来源链接与抄改日期，便于回溯上游修复）。
3. **业务组件层**：`components/`，只消费 token，不允许写死色值。
4. **模式层**：布局模式（列表页/表单页/详情页）文档化，页面只拼模式。
- 红线：绕过 token 直接写色值的 PR 审查打回；组件库暗黑模式与自研 token 主题用同一切换开关。

---

## 七、审核证据点（审核含自研视觉组件的项目时抽检）

- 风格一致性：页面实际风格与方案书选定的主风格一致，混搭元素 ≤ 2 种（对照 §二映射表）
- Neumorphism 表单控件是否有非阴影状态提示（可及性）
- glass 组件嵌套层数；是否对低端机降级。
- 无限动画是否可视区外暂停；`prefers-reduced-motion` 是否生效。
- 抄改组件的色值是否已 token 化（搜 `#[0-9a-f]{3,6}` 于组件内的硬编码）。
- hover 动画属性是否只有 transform/opacity。
- 弹窗是否处理 `overscroll-behavior` 与 `scrollbar-gutter` 抖动。
