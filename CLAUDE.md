# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

这是一个基于 Vue.js 2.6 的订阅配置转换 Web 界面，与 subconverter 后端配合使用。项目采用模块化架构，使用 Composition API 风格的 composables 来组织业务逻辑。

## 开发命令

```bash
# 安装依赖
yarn install

# 启动开发服务器（运行在 http://localhost:8080）
yarn serve

# 构建生产版本（输出到 dist 目录）
yarn build

# 运行代码检查
yarn lint
```

## 环境要求

- **Node.js**: 22.x（严格版本要求，定义在 package.json 的 engines 字段）
- **Yarn**: 1.22+

## 代码架构

### 目录结构

```
src/
├── components/          # 可复用组件
│   ├── ConfigUploadDialog.vue   # 配置上传对话框
│   ├── SvgIcon/                # SVG 图标组件
│   └── UrlParseDialog.vue      # URL 解析对话框
├── composables/        # 业务逻辑组合函数
│   ├── useSubscriptionForm.js  # 表单逻辑
│   ├── useSubscription.js      # 订阅链接生成
│   └── useUrlParser.js         # URL 解析逻辑
├── config/             # 配置文件
│   ├── client-types.js         # 客户端类型映射
│   ├── constants.js            # 常量定义
│   └── remote-configs.js       # 远程配置预设
├── plugins/            # Vue 插件配置
│   ├── axios.js               # Axios 实例配置
│   ├── clipboard.js           # 剪贴板功能
│   ├── device.js              # 设备检测
│   └── element-ui.js          # Element UI 按需引入
├── router/             # 路由配置
├── services/           # 服务层
│   ├── backendService.js       # 后端版本检查
│   ├── configUploadService.js  # 配置上传
│   └── shortUrlService.js      # 短链接生成
├── utils/              # 工具函数
│   ├── formatters.js           # 格式化工具
│   ├── search.js               # 搜索工具
│   ├── storage.js              # 本地存储
│   └── validators.js           # 表单验证
└── views/              # 页面组件
    └── Subconverter.vue        # 主页面（单页面应用）
```

### 核心架构模式

1. **Composables 模式**：使用 Vue 2 Composition API 风格（通过函数实现）将业务逻辑从组件中分离
   - `useSubscription.js`: 处理订阅链接生成的核心逻辑
   - `useUrlParser.js`: 处理短链接解析和重定向
   - `useSubscriptionForm.js`: 处理表单状态和验证

2. **服务层**：与外部 API 交互的逻辑封装在服务类中
   - `BackendService`: 获取 subconverter 后端版本
   - `ShortUrlService`: 生成短链接（使用 MyUrls 服务）
   - `ConfigUploadService`: 上传自定义配置到后端

3. **按需引入**：Element UI 组件按需引入，而非全局导入，减少打包体积

### 主要功能流程

1. **订阅转换流程**：
   - 用户输入订阅链接 → `useUrlParser` 解析短链接 → `useSubscription` 构建转换 URL → 调用 subconverter 后端

2. **URL 解析**：
   - 检测 URL 是否包含 "target" 参数
   - 如果不包含，使用 `fetch` with `redirect: follow` 解析短链接

3. **客户端类型**：
   - 支持多种代理客户端：Clash、Surge、QuantumultX、V2Ray、Trojan 等
   - 配置定义在 `src/config/client-types.js`

## 环境变量

可选的环境变量（创建 `.env` 文件）：

```env
# Subconverter 后端地址
VUE_APP_SUBCONVERTER_DEFAULT_BACKEND=https://api.wcc.best

# 项目链接
VUE_APP_PROJECT=https://github.com/CareyWang/sub-web

# Telegram 讨论组
VUE_APP_BOT_LINK=https://t.me/subconverter_discuss
```

## Vue 配置说明

- **SVG Sprite Loader**: 配置了 `svg-sprite-loader` 处理 `src/icons` 目录下的 SVG 图标
- **History 模式**: Vue Router 使用 history 模式，需要服务器配置 SPA 路由支持

## 相关依赖

- **subconverter 后端**: https://github.com/tindy2013/subconverter
- **MyUrls 短链接服务**: https://github.com/CareyWang/MyUrls（可选）
