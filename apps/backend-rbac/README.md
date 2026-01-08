# RBAC 权限管理系统

> 基于 NestJS + TypeORM + JWT 的角色访问控制(RBAC)学习项目

[![NestJS](https://img.shields.io/badge/NestJS-11.x-E0234E?logo=nestjs)](https://nestjs.com/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-3178C6?logo=typescript)](https://www.typescriptlang.org/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## 📖 项目简介

本项目是一个完整的 RBAC（Role-Based Access Control，基于角色的访问控制）权限管理系统，通过实践帮助开发者深入理解**用户认证**、**授权**和**权限管理**的核心概念。

项目实现了基于菜单的权限管理体系，支持**动态路由**、**按钮级权限控制**和**细粒度权限验证**，是学习后端权限系统的完整参考案例。

### ✨ 核心特性

- 🔐 **JWT 认证** - 无状态 Token 认证，支持 Token 刷新
- 👥 **用户管理** - 用户注册、登录、角色分配
- 📁 **菜单管理** - 5种菜单类型（目录、菜单、按钮、内嵌、外链），树形结构管理
- 🎭 **角色管理** - 创建角色、分配菜单权限
- 🔒 **权限控制** - 基于菜单的权限标识，支持接口和按钮级权限验证
- 🌲 **动态路由** - 根据用户角色动态生成前端路由
- 🛡️ **守卫装饰器** - 优雅的权限验证方式（`@Roles()`、`@RequirePermissions()`）

### 🎯 适用人群

- 🚀 想要学习用户认证和授权机制的开发者
- 💼 需要了解 RBAC 权限体系的后端工程师
- 📚 希望掌握 NestJS 框架和 JWT 认证的学习者

## 🛠️ 技术栈

| 类别 | 技术 |
|------|------|
| **框架** | NestJS 11.x |
| **语言** | TypeScript 5.x |
| **认证** | JWT (JSON Web Token) + Passport |
| **ORM** | TypeORM |
| **数据库** | MySQL / PostgreSQL |
| **验证** | class-validator + class-transformer |
| **加密** | bcrypt |
| **文档** | Swagger / OpenAPI |

## 🚀 快速开始

### 环境要求

- Node.js >= 18
- pnpm >= 8
- MySQL >= 8.0 / PostgreSQL >= 14

### 安装

```bash
# 克隆项目
git clone <repository-url>
cd rbac-nest

# 安装依赖
pnpm install

# 配置环境变量（复制 .env.example 并修改）
cp .env.example .env

# 运行数据库迁移
pnpm run migration:run

# 填充初始数据（可选）
pnpm run seed
```

### 运行

```bash
# 开发模式
pnpm run start:dev

# 生产模式
pnpm run build
pnpm run start:prod
```

### 访问

- API 地址: http://localhost:3000
- Swagger 文档: http://localhost:3000/api

## 📚 文档导航

| 文档 | 说明 |
|------|------|
| [系统架构设计](docs/ARCHITECTURE.md) | RBAC 模型、数据库设计、模块结构、认证流程 |
| [菜单权限系统](docs/MENU_SYSTEM.md) | 5种菜单类型、字段详解、权限标识、实战示例 |
| [API 接口文档](docs/API.md) | 完整的 REST API 接口说明和示例 |
| [开发指南](docs/DEVELOPMENT.md) | 开发规范、关键实现、测试、开发计划 |

## 🏗️ 项目结构

```
rbac-nest/
├── src/
│   ├── auth/              # 认证模块（JWT、守卫、装饰器）
│   ├── users/             # 用户模块
│   ├── menus/             # 菜单模块
│   ├── roles/             # 角色模块
│   ├── common/            # 公共模块（拦截器、过滤器）
│   └── app.module.ts      # 根模块
├── docs/                  # 文档目录
├── test/                  # 测试文件
└── README.md              # 项目说明
```

## 🎬 核心功能演示

### 用户登录
```bash
curl -X POST http://localhost:3000/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@example.com","password":"123456"}'
```

### 获取用户菜单（动态路由）
```bash
curl http://localhost:3000/auth/menus \
  -H "Authorization: Bearer <your-token>"
```

### 创建菜单
```bash
curl -X POST http://localhost:3000/menus \
  -H "Authorization: Bearer <your-token>" \
  -H "Content-Type: application/json" \
  -d '{
    "type": "menu",
    "name": "userManage",
    "title": "用户管理",
    "path": "/system/user",
    "component": "/views/system/user/index",
    "permission": "user:view"
  }'
```

更多接口示例请查看 [API 文档](docs/API.md)。

## 🧪 测试

```bash
# 单元测试
pnpm run test

# E2E 测试
pnpm run test:e2e

# 测试覆盖率
pnpm run test:cov
```

## 📝 开发计划

项目按照以下阶段进行开发：

- [x] 基础项目搭建
- [ ] 用户认证模块（注册、登录、JWT）
- [ ] 菜单管理模块（CRUD、树形结构）
- [ ] 角色管理模块（角色-菜单关联）
- [ ] 权限验证（守卫、装饰器）
- [ ] 前端集成（动态路由、按钮权限）
- [ ] 测试与文档完善

详细开发计划请查看 [开发指南](docs/DEVELOPMENT.md)。

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

## 📄 许可证

本项目采用 [MIT](LICENSE) 许可证，仅用于学习目的。

## 🔗 相关链接

- [NestJS 官方文档](https://docs.nestjs.com/)
- [TypeORM 官方文档](https://typeorm.io/)
- [JWT 官方介绍](https://jwt.io/)
- [NIST RBAC 标准](https://csrc.nist.gov/projects/role-based-access-control)

---

**Happy Learning! 🎉**

如有问题，欢迎提 Issue 或查看 [文档](docs/)。
