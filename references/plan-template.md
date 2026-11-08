# 生产级前端方案书模板

阶段 2 撰写时严格套用本章节结构（八股文要领）。每章的技术决策给出「选择 + 理由 + 否决项」三件套。

---

```markdown
# <项目名> 前端生产级方案书

> 版本：v1.0 ｜ 日期：<YYYY-MM-DD> ｜ 平台：<网站/App/小程序/桌面> ｜ 基线：prod-frontend-blueprint

## 1. 项目概述与平台决策
- 业务一句话、目标用户、流量模型（后台 / C 端常规 / C 端高流量）
- 平台与终端决策：选择 + 理由 + 否决项

## 2. 技术栈与版本基线
| 项 | 选择 | 理由 | 否决项（为什么不选备选） |
| --- | --- | --- | --- |
| 语言 | TypeScript 5.x | | |
| 框架 | Vue 3.4 / React 18 | | |
| 构建 | Vite 5 | | |
| 组件库 | Element Plus | | |
| 状态管理 | Pinia | | |
| CSS 方案 | scoped + 设计变量 | | |
| 包管理 | pnpm | | |

（依赖版本与平台陷阱必须出自 references/stacks/ 对应文件）

## 3. 目录结构骨架（必写）
给出可直接落地的目录树，每层注明职责。示例基线：

<按 stacks 对应文件的骨架改写，保留职责注释；增删目录要说明理由>

## 4. 路由与页面设计
- 路由表、权限守卫、懒加载策略
- 页面层级：pages / views 划分规则

## 5. 状态与数据流
- 全局状态范围（只放真正全局的：登录态、主题、字典）
- 服务端数据获取策略（请求库 / 缓存 / 失效机制）

## 6. 请求层规范（P0 章节）
- 拦截器：错误码表、401 无感刷新、统一提示
- 防重复提交、防抖节流、AbortController 取消
- 幂等与重试红线（POST 创建/支付禁止自动重试）

## 7. 构建与打包策略
- 分包策略：vendor 抽离、路由懒加载、组件库按需
- 资源处理：图片压缩/格式、字体子集化
- 构建产物检查：体积预算（首屏 JS ≤ <n>K Brotli）、sourceMap 归档策略

## 8. 资源加载与缓存策略（P0 章节）
- index.html 缓存策略、hash 资源长效缓存
- CDN 方案、Gzip/Brotli
- preconnect / preload / prefetch 清单（数量受限，注明理由）

## 9. 性能优化方案
- 首屏：骨架屏、LCP 元素、关键资源优先级
- 运行时：长列表方案、动画规范、主线程保护
- 内存泄漏防护清单（定时器/事件/第三方实例）
- C 端高流量项目在此并入 advanced-checklist 启用项

## 10. 安全方案
- HTTPS / CSP / XSS 过滤 / Token 存放 / 敏感信息管控

## 11. 监控与告警
- 异常捕获、性能指标、行为埋点、告警阈值
- 降级策略

## 12. 工程化与发布
- 代码规范门禁、测试范围、CI/CD 流水线、灰度与回滚

## 13. 兼容性与适配
- browserslist、polyfill 策略、多端适配、弱网测试

## 14. 上线检查清单（P0/P1/P2）
按 production-checklist.md 逐项核对，不适用的注明原因；
C 端项目追加 advanced-checklist.md 启用项。

## 15. 后端侧需求（只记录不实现）
- 需要后端配合的接口约定、缓存头、鉴权方式等

## 16. 变更记录
| 版本 | 日期 | 变更点 | 影响范围 |
```

---

## 目录结构骨架基线（按平台取用，放第 3 章）

### Web（Vue 3 + Vite + TS）
```
src/
├── api/            # 统一接口管理：按业务域分文件，集中维护请求函数
├── assets/         # 构建期处理的静态资源（图片/字体/全局样式）
├── components/     # 通用展示组件（无业务）
├── composables/    # 组合式函数：useXxx 复用逻辑
├── config/         # 常量与配置：api base、枚举、字典
├── directives/     # 自定义指令
├── layouts/        # 布局：带侧边栏 / 纯页面
├── pages/          # 页面（与路由一一对应）
│   └── user/
│       ├── index.vue        # 页面入口
│       ├── components/      # 页面私有组件
│       └── composables/     # 页面私有逻辑
├── plugins/        # 第三方插件封装（监控、埋点、组件库注册）
├── router/         # 路由表 + 守卫
├── stores/         # Pinia：只放真正全局的状态
├── styles/         # 设计变量、mixins、reset
├── types/          # 全局 TS 类型 / 接口响应类型
├── utils/          # 纯函数工具（无副作用）
├── App.vue
├── main.ts
└── env.d.ts
public/             # 原样拷贝的资源（不参与构建）
```

### React（Vite + TS）
同结构，差异：`components/` 同；`composables/` → `hooks/`；`stores/` → Zustand/RTK；`directives/` 不存在。

### uni-app x / 小程序 / 桌面
见 `references/stacks/` 对应文件中的骨架小节。
