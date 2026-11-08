# 微信小程序（原生）基线

## 版本与工程基线
- 微信开发者工具稳定版；基础库 ≥ 3.x；原生开发（WXML/WXSS/JS）或 Taro/uni-app 编译方案（跨端需求时）
- npm 支持需「构建 npm」步骤；TypeScript 需官方 miniprogram-ts 模板

## 目录骨架
```
├── app.js / app.ts     # 全局：onLaunch 预取、全局错误捕获
├── app.json            # 路由、分包、tabBar、权限声明
├── app.wxss            # 全局样式（克制，避免冗余）
├── config/             # env、常量
├── services/           # 接口层：request 封装 + 拦截
├── utils/              # 纯函数工具
├── components/         # 自定义组件
├── pages/<域>/<页>/    # 每页 4 件套 js/json/wxml/wxss
├── packages/<分包>/    # 非首屏业务分包
└── assets/             # 本地图标（小图）；大图一律 CDN
```

## 红线
- **主包 ≤ 2M、整包 ≤ 20M（分包后）**：非首屏页面全部进分包；分包预下载规则按需配置
- setData 是性能第一杀手：禁止大对象/高频 setData；只 set 变化字段路径
- 无浏览器 API：无 cookie（用 storage + 自定义 header 带 token）、无 window/document
- 图片必须 CDN + 尺寸参数；本地仅放小图标
- 登录走 code2session，敏感数据禁止前端解密逻辑硬编码密钥

## 性能要点
- 长列表：官方 recycle-view 或自研虚拟列表；禁止万级节点
- 页面 onShow 拉数据节流；低频页面用 onLoad + 手动刷新
- IntersectionObserver 做曝光/懒加载
- 预拉取（prefetch）数据接口：冷启动提前拉首屏数据

## 发布与合规
- 体验版 → 审核版 → 灰度发布（按比例）→ 全量；支持版本回退
- 隐私协议与用户授权（getUserProfile 已废弃，用头像昵称填写能力）
- 类目与接口权限（支付/定位/订阅消息）提前申请
