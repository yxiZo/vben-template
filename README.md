<div align="center">
  <a href="https://github.com/vbenjs/vue-vben-admin">
    <img alt="VbenAdmin Logo" width="215" src="https://unpkg.com/@vbenjs/static-source@0.1.7/source/logo-v1.webp">
  </a>
  <br>
  <br>

[![license](https://img.shields.io/github/license/vbenjs/vue-vben-admin.svg)](LICENSE)

  <h1>Vben Admin 5.0 精简模板</h1>
</div>

**中文** | [English](./README.en.md)

## 简介

本项目是基于 [Vue Vben Admin 5.0](https://github.com/vbenjs/vue-vben-admin) 的精简模板。鉴于官方从 5.0 版本开始不再提供精简版本，本项目作为一个开箱即用的模板，帮助开发者快速启动项目。

Vue Vben Admin 是一个免费开源的中后台模板，使用最新的 `vue3`、`vite`、`TypeScript` 等主流技术开发，开箱即用的中后台前端解决方案。

## 为什么使用本模板？

官方 Vben Admin 5.0 包含了多个 UI 框架版本、完整的文档和演示代码，但对于实际项目来说，很多内容是不需要的。本模板已经按照官方精简指南完成了以下精简操作：

### 已完成的精简操作

#### 1. 应用精简
- ✅ 保留 `apps/web-antd` - 基于 Ant Design Vue 的主应用
- ❌ 删除 `apps/web-ele` - Element Plus 版本
- ❌ 删除 `apps/web-naive` - Naive UI 版本
- ❌ 删除 `apps/web-tdesign` - TDesign 版本
- ✅ 保留 `apps/backend-mock` - Mock 服务

#### 2. 演示代码精简
- ❌ 删除 `playground` 文件夹 - 完整的演示应用（包含所有功能展示和 E2E 测试）

#### 3. 文档精简
- ❌ 删除 `docs` 文件夹 - 完整的文档站点（文档可在线查看：https://doc.vben.pro/）

#### 4. GitHub 配置精简
- ❌ 删除 `.github` 文件夹 - GitHub Actions 工作流、Issue 模板等

#### 5. 部署脚本精简
- ❌ 删除 `scripts/deploy` 文件夹 - Docker 部署脚本

## 项目特点

- **单一 UI 框架**：仅保留 Ant Design Vue 版本，减少选择困难
- **精简依赖**：移除了其他 UI 框架的依赖，减少安装时间
- **保留核心功能**：保留了所有核心功能和常用组件
- **开箱即用**：可直接作为新项目的起点

## 快速开始

### 1. 获取项目代码

```bash
git clone https://github.com/your-username/vben-template.git
cd vben-template
```

### 2. 安装依赖

```bash
# 确保 Node.js 版本 >= 18
npm i -g corepack
pnpm install
```

### 3. 启动开发服务器

```bash
pnpm dev
```

### 4. 构建生产版本

```bash
pnpm build
```

## 可用命令

```bash
# 开发模式
pnpm dev

# 构建
pnpm build

# 预览构建产物
pnpm preview

# 类型检查
pnpm type-check

# 代码检查
pnpm lint

# 代码格式化
pnpm format
```

## 进一步精简

如果你还想进一步精简项目，可以参考以下操作：

### 删除不需要的路由及页面

在 `apps/web-antd/src/router/routes` 文件中：
- **core 路由**：如果只需要登录，可以删除注册、忘记密码等路由
- **模块路由**：可以删除 `demos`、`vben` 等演示路由
- 删除路由后，记得在 `apps/web-antd/src/views` 中删除对应的页面文件

### 删除不需要的组件

在 `packages/effects/common-ui/src/ui` 文件夹中，可以删除不需要的组件。

### 关闭 Mock 服务

如果不需要 Mock 服务：
1. 删除 `apps/backend-mock` 文件夹
2. 在 `apps/web-antd/.env.development` 中设置 `VITE_NITRO_MOCK=false`

### 删除其他文件

根据需要删除以下文件/文件夹：
- `.changeset` - 版本变更管理
- `.vscode` - VSCode 配置（如果使用其他编辑器）

## 在线预览

- [Vben Admin 官方文档](https://doc.vben.pro/)
- [Vben Admin 在线演示](https://vben.pro/)

测试账号：vben/123456

## 浏览器支持

推荐使用 `Chrome 80+` 浏览器进行本地开发

支持现代浏览器，不支持 IE

| [<img src="https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png" alt="Edge" width="24px" height="24px" />](http://godban.github.io/browsers-support-badges/)</br>Edge | [<img src="https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png" alt="Firefox" width="24px" height="24px" />](http://godban.github.io/browsers-support-badges/)</br>Firefox | [<img src="https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png" alt="Chrome" width="24px" height="24px" />](http://godban.github.io/browsers-support-badges/)</br>Chrome | [<img src="https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png" alt="Safari" width="24px" height="24px" />](http://godban.github.io/browsers-support-badges/)</br>Safari |
| :-: | :-: | :-: | :-: |
| last 2 versions | last 2 versions | last 2 versions | last 2 versions |

## 项目结构

```
vben-template
├── apps/
│   ├── web-antd/          # Ant Design Vue 主应用
│   └── backend-mock/      # Mock 服务
├── packages/              # 共享包
│   ├── effects/           # 业务逻辑
│   ├── locales/           # 国际化
│   └── ...                # 其他共享包
├── internal/              # 内部工具
├── scripts/               # 构建脚本
└── package.json           # 项目配置

```

## 如何贡献

欢迎提交 Issue 和 Pull Request！

**Pull Request 流程：**

1. Fork 本仓库
2. 创建你的分支：`git checkout -b feat/xxxx`
3. 提交你的修改：`git commit -am 'feat(功能): 添加 xxxxx'`
4. 推送你的分支：`git push origin feat/xxxx`
5. 提交 `pull request`

## Git 提交规范

参考 [vue](https://github.com/vuejs/vue/blob/dev/.github/COMMIT_CONVENTION.md) 规范 ([Angular](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/conventional-changelog-angular))

- `feat` 添加新功能
- `fix` 修复问题/BUG
- `style` 代码风格相关无影响运行结果的
- `perf` 优化/性能提升
- `refactor` 重构
- `revert` 撤销修改
- `test` 测试相关
- `docs` 文档/注释
- `chore` 依赖更新/脚手架配置修改等
- `ci` 持续集成
- `types` 类型定义文件更改

## 项目更新说明

### 本项目与上游的关系

本项目是基于 [Vue Vben Admin 5.0](https://github.com/vbenjs/vue-vben-admin) 的**下游精简模板**，会持续跟踪上游仓库的更新。

- **上游仓库**：vbenjs/vue-vben-admin（官方完整版）
- **本仓库**：精简后的模板版本

### 为什么项目可以轻松更新？

虽然本项目删除了大量文件（其他 UI 框架、文档、演示代码等），但由于采用了 **Monorepo** 架构，核心代码被抽离到独立的包中：

- `packages/@core` - 核心功能包
- `packages/effects` - 业务逻辑包
- `packages/@vben` - UI 组件库

**这些核心包与各个应用是独立的**，因此：
- ✅ 我们删除了 `apps/web-ele`、`apps/web-naive` 等应用，不影响核心包
- ✅ 我们删除了 `playground`、`docs`，也不影响核心包
- ✅ 当上游更新核心包的 bug 或新功能时，我们可以直接合并
- ✅ 大部分情况下只需要处理简单的冲突，或者完全无冲突

### 本项目的更新策略

作为模板维护者，我会：

1. **定期同步上游更新** - 关注官方的 release 和重要修复
2. **保持核心包同步** - 及时合并 `packages/` 下的更新
3. **维护精简版本** - 确保删除的文件不会在合并时恢复
4. **测试兼容性** - 确保更新后 `web-antd` 应用正常运行

### 如何从本模板创建你的项目？

当你使用本模板创建自己的项目时：

#### 方案一：Fork 或克隆本仓库（推荐新手）

```bash
# 克隆本仓库
git clone https://github.com/your-username/vben-template.git
cd vben-template

# 添加本仓库为远程源（用于后续获取模板更新）
git remote add template https://github.com/your-username/vben-template.git
```

#### 方案二：添加上游仓库（推荐高级用户）

如果你希望同时跟踪官方上游和本模板：

```bash
# 克隆本仓库
git clone https://github.com/your-username/vben-template.git
cd vben-template

# 添加官方上游仓库（可选，用于直接获取官方更新）
git remote add upstream https://github.com/vbenjs/vue-vben-admin.git

# 查看远程源
git remote -v
# origin    -> 你的仓库
# template  -> 本模板仓库
# upstream  -> 官方仓库（可选）
```

### 如何获取本模板的更新？

#### 1. 从本模板仓库获取更新

```bash
# 获取本模板的最新代码
git fetch template main

# 查看有什么更新
git log template/main

# 合并到你的分支
git merge template/main

# 如果有冲突，解决后提交
git add .
git commit -m "chore: 同步模板更新"
```

#### 2. 如果添加了官方上游仓库

```bash
# 获取官方上游的更新
git fetch upstream main

# 合并官方更新（需要谨慎处理冲突）
git merge upstream/main

# 解决冲突时注意：
# - packages/@core、packages/effects：优先保留上游代码
# - apps/web-antd：保留你的业务逻辑
# - 已删除的文件（web-ele、web-naive 等）：不要恢复
```

### 更新时的冲突处理建议

1. **核心包冲突** (`packages/@core`、`packages/effects`)
   - 优先保留上游/模板的代码
   - 这些包的更新通常包含 bug 修复和性能优化

2. **应用层冲突** (`apps/web-antd`)
   - 保留你的业务逻辑代码
   - 合并模板的配置更新和 bug 修复

3. **配置文件冲突** (`package.json`、`tsconfig.json`)
   - 依赖版本更新建议合并
   - 自定义配置需要保留

4. **已删除的文件**
   - 不要恢复 `apps/web-ele`、`apps/web-naive`、`apps/web-tdesign`
   - 不要恢复 `playground`、`docs`
   - 如果上游合并时恢复了这些文件，再次删除即可

### 推荐更新频率

- **本模板更新**：每 1-2 周检查一次
- **官方重大更新**：关注 [Vben Admin Releases](https://github.com/vbenjs/vue-vben-admin/releases)
- **安全修复**：应立即更新

## 相关链接

- [官方文档](https://doc.vben.pro/)
- [更新日志](https://github.com/vbenjs/vue-vben-admin/releases)
- [GitHub Discussions](https://github.com/vbenjs/vue-vben-admin/discussions)

## 许可证

[MIT © Vben-2020](./LICENSE)

## 致谢

本项目基于 [Vue Vben Admin](https://github.com/vbenjs/vue-vben-admin) 开发，感谢原作者和所有贡献者的辛勤付出。
