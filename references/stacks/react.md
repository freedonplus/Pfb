# React 18 + Vite + TypeScript 基线

## 版本基线
- React 18.3+、Vite 5+、TypeScript 5+（strict）、pnpm、Node 20 LTS
- react-router 6/7、Zustand（中小）/ Redux Toolkit（大型）、TanStack Query（服务端数据）
- 组件库：Ant Design 5（后台）/ shadcn-ui + Tailwind（定制风格）
- 工具链：ESLint 9 flat config、Prettier、Vitest + Testing Library、Playwright（E2E）

## 目录骨架
同 plan-template.md Web 基线，差异：
- `composables/` → `hooks/`（use 前缀自定义 Hook）
- `stores/` → Zustand store 或 RTK slice
- 页面级拆分：page/components、page/hooks

## 关键配置要点
- 路由懒加载：React.lazy + Suspense 包裹
- antd v5 为 ESM，按需天然生效；无需 babel-plugin-import
- vite manualChunks：react/react-dom、antd、echarts 独立 chunk

## 性能陷阱
- 列表项组件 memo；onClick 等回调用 useCallback 稳定引用，否则 memo 失效
- useMemo 只用于昂贵计算，滥用反而增加开销
- Context 放高频变化数据会全树重渲染 → 拆分 Context 或用 Zustand
- useEffect 依赖数组完整性：react-hooks/exhaustive-deps 必须开
- StrictMode 下 effect 双执行：副作用清理函数必须写全（定时器、订阅）

## 上线前 React 专项自查
- 无过期闭包：setInterval 内读取的 state 用 ref 或函数式更新
- 列表 key 用业务 id，禁止 index
- Error Boundary 包裹路由级组件
- 严格模式 double-invoke 无副作用泄漏
