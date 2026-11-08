# Vue 3 + Vite + TypeScript 基线

## 版本基线
- Vue 3.4+、Vite 5+、TypeScript 5+（strict）、pnpm、Node 20 LTS
- vue-router 4、Pinia 2、Element Plus（后台）/ Vant 4（移动 H5）
- 工具链：ESLint 9（flat config）、Prettier、Vitest、rollup-plugin-visualizer

## 目录骨架
见 plan-template.md Web 基线。补充 Vue 特有约定：
- `composables/` 放跨页面复用的组合式函数；页面私有的放页面目录内
- `components/` 只放无业务展示组件；业务组件放 `pages/*/components/`

## 关键配置要点
- vite.config：build.rollupOptions.output.manualChunks 抽 vendor（element-plus、echarts 单独 chunk）
- 组件库按需：unplugin-vue-components + unplugin-auto-import
- CSS：Sass + scoped；设计变量集中在 styles/variables
- defineConfig 关闭 dev 专用代码；build.sourcemap=false（生产）

## 性能陷阱
- 大列表/图表数据用 shallowRef，不要整体响应式
- v-if vs v-show：高频显隐用 v-show；一次性分支用 v-if
- keep-alive 缓存的页面组件要处理 activated 重复拉数问题
- computed 里不要做重计算 + 大数组复制（会随依赖每次重建）
- watch 深拷贝大对象是内存与性能双坑

## 上线前 Vue 专项自查
- `<script setup>` 未使用变量会在模板静默失败 → TS + vue-tsc 检查
- v-for key 用业务 id
- 路由 beforeEach 守卫内异步操作必须有超时兜底，否则路由卡死
- ECharts 等实例 onUnmounted 时 dispose
