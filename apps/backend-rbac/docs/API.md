# API 接口文档

本文档提供完整的 REST API 接口说明和示例。

## 目录

- [认证接口](#认证接口)
- [用户管理接口](#用户管理接口)
- [菜单管理接口](#菜单管理接口)
- [角色管理接口](#角色管理接口)
- [用户权限接口](#用户权限接口)
- [通用规范](#通用规范)

---

## 通用规范

### 基础URL

```
开发环境: http://localhost:3000
生产环境: https://api.example.com
```

### 请求头

```http
Content-Type: application/json
Authorization: Bearer <access_token>
```

### 响应格式

#### 成功响应

```json
{
  "code": 200,
  "message": "success",
  "data": { ... }
}
```

#### 错误响应

```json
{
  "code": 400,
  "message": "错误信息",
  "error": "详细错误描述"
}
```

### HTTP 状态码

| 状态码 | 说明 |
|--------|------|
| 200 | 请求成功 |
| 201 | 创建成功 |
| 400 | 请求参数错误 |
| 401 | 未授权（未登录或 Token 失效） |
| 403 | 无权限访问 |
| 404 | 资源不存在 |
| 500 | 服务器内部错误 |

---

## 认证接口

### 1. 用户注册

**请求**

```http
POST /auth/register
Content-Type: application/json

{
  "username": "johndoe",
  "email": "john@example.com",
  "password": "SecurePass123!"
}
```

**响应**

```json
{
  "code": 201,
  "message": "注册成功",
  "data": {
    "id": 1,
    "username": "johndoe",
    "email": "john@example.com",
    "createdAt": "2024-01-07T10:00:00.000Z"
  }
}
```

---

### 2. 用户登录

**请求**

```http
POST /auth/login
Content-Type: application/json

{
  "email": "admin@example.com",
  "password": "123456"
}
```

**响应**

```json
{
  "code": 200,
  "message": "登录成功",
  "data": {
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expires_in": 86400,
    "user": {
      "id": 1,
      "username": "admin",
      "email": "admin@example.com",
      "roles": [
        {
          "id": 1,
          "name": "管理员",
          "code": "admin"
        }
      ]
    }
  }
}
```

---

### 3. Token 刷新

**请求**

```http
POST /auth/refresh
Authorization: Bearer <old_token>
```

**响应**

```json
{
  "code": 200,
  "message": "Token刷新成功",
  "data": {
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expires_in": 86400
  }
}
```

---

### 4. 退出登录

**请求**

```http
POST /auth/logout
Authorization: Bearer <token>
```

**响应**

```json
{
  "code": 200,
  "message": "退出登录成功"
}
```

---

## 用户管理接口

### 1. 获取所有用户

**权限要求**: `user:view`

**请求**

```http
GET /users?page=1&limit=10&keyword=john
Authorization: Bearer <token>
```

**查询参数**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| page | number | 否 | 页码，默认1 |
| limit | number | 否 | 每页数量，默认10 |
| keyword | string | 否 | 搜索关键词 |
| status | number | 否 | 状态筛选：1-启用，0-禁用 |

**响应**

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "items": [
      {
        "id": 1,
        "username": "admin",
        "email": "admin@example.com",
        "status": 1,
        "roles": [
          {
            "id": 1,
            "name": "管理员",
            "code": "admin"
          }
        ],
        "createdAt": "2024-01-01T00:00:00.000Z"
      }
    ],
    "total": 100,
    "page": 1,
    "limit": 10
  }
}
```

---

### 2. 获取用户详情

**权限要求**: `user:view`

**请求**

```http
GET /users/:id
Authorization: Bearer <token>
```

**响应**

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "id": 1,
    "username": "admin",
    "email": "admin@example.com",
    "nickname": "管理员",
    "avatar": "https://example.com/avatar.jpg",
    "status": 1,
    "roles": [...],
    "createdAt": "2024-01-01T00:00:00.000Z",
    "updatedAt": "2024-01-07T00:00:00.000Z"
  }
}
```

---

### 3. 创建用户

**权限要求**: `user:create`

**请求**

```http
POST /users
Authorization: Bearer <token>
Content-Type: application/json

{
  "username": "newuser",
  "email": "newuser@example.com",
  "password": "123456",
  "nickname": "新用户",
  "roleIds": [2]
}
```

**响应**

```json
{
  "code": 201,
  "message": "创建成功",
  "data": {
    "id": 10,
    "username": "newuser",
    "email": "newuser@example.com",
    "createdAt": "2024-01-07T10:00:00.000Z"
  }
}
```

---

### 4. 更新用户

**权限要求**: `user:edit`

**请求**

```http
PATCH /users/:id
Authorization: Bearer <token>
Content-Type: application/json

{
  "nickname": "更新后的昵称",
  "status": 1
}
```

**响应**

```json
{
  "code": 200,
  "message": "更新成功",
  "data": {
    "id": 10,
    "nickname": "更新后的昵称",
    "updatedAt": "2024-01-07T10:30:00.000Z"
  }
}
```

---

### 5. 删除用户

**权限要求**: `user:delete`

**请求**

```http
DELETE /users/:id
Authorization: Bearer <token>
```

**响应**

```json
{
  "code": 200,
  "message": "删除成功"
}
```

---

### 6. 为用户分配角色

**权限要求**: `user:assign-role`

**请求**

```http
POST /users/:id/roles
Authorization: Bearer <token>
Content-Type: application/json

{
  "roleIds": [1, 2]
}
```

**响应**

```json
{
  "code": 200,
  "message": "分配成功",
  "data": {
    "userId": 10,
    "roles": [
      { "id": 1, "name": "管理员" },
      { "id": 2, "name": "编辑" }
    ]
  }
}
```

---

## 菜单管理接口

### 1. 获取所有菜单（树形）

**权限要求**: `menu:view`

**请求**

```http
GET /menus/tree
Authorization: Bearer <token>
```

**响应**

```json
{
  "code": 200,
  "message": "success",
  "data": [
    {
      "id": 1,
      "name": "system",
      "title": "系统管理",
      "type": "directory",
      "icon": "setting",
      "sort": 1,
      "children": [
        {
          "id": 2,
          "parentId": 1,
          "name": "userManage",
          "title": "用户管理",
          "type": "menu",
          "path": "/system/user",
          "component": "/views/system/user/index",
          "permission": "user:view",
          "children": [
            {
              "id": 3,
              "parentId": 2,
              "name": "userCreate",
              "title": "新增用户",
              "type": "button",
              "permission": "user:create"
            }
          ]
        }
      ]
    }
  ]
}
```

---

### 2. 获取所有菜单（扁平）

**权限要求**: `menu:view`

**请求**

```http
GET /menus?type=menu&status=1
Authorization: Bearer <token>
```

**查询参数**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| type | string | 否 | 菜单类型筛选 |
| status | number | 否 | 状态筛选 |

**响应**

```json
{
  "code": 200,
  "message": "success",
  "data": [
    {
      "id": 1,
      "name": "system",
      "title": "系统管理",
      "type": "directory",
      "sort": 1
    },
    {
      "id": 2,
      "parentId": 1,
      "name": "userManage",
      "title": "用户管理",
      "type": "menu",
      "path": "/system/user",
      "component": "/views/system/user/index"
    }
  ]
}
```

---

### 3. 创建菜单

**权限要求**: `menu:create`

**请求示例：创建目录**

```http
POST /menus
Authorization: Bearer <token>
Content-Type: application/json

{
  "type": "directory",
  "name": "system",
  "title": "系统管理",
  "icon": "setting",
  "sort": 1
}
```

**请求示例：创建菜单**

```http
POST /menus
Authorization: Bearer <token>
Content-Type: application/json

{
  "type": "menu",
  "parentId": 1,
  "name": "userManage",
  "title": "用户管理",
  "path": "/system/user",
  "component": "/views/system/user/index",
  "icon": "user",
  "permission": "user:view",
  "isCached": true,
  "sort": 1
}
```

**请求示例：创建按钮**

```http
POST /menus
Authorization: Bearer <token>
Content-Type: application/json

{
  "type": "button",
  "parentId": 2,
  "name": "userCreate",
  "title": "新增用户",
  "permission": "user:create",
  "sort": 1
}
```

**响应**

```json
{
  "code": 201,
  "message": "创建成功",
  "data": {
    "id": 3,
    "type": "button",
    "name": "userCreate",
    "title": "新增用户",
    "permission": "user:create",
    "createdAt": "2024-01-07T10:00:00.000Z"
  }
}
```

---

### 4. 更新菜单

**权限要求**: `menu:edit`

**请求**

```http
PATCH /menus/:id
Authorization: Bearer <token>
Content-Type: application/json

{
  "title": "用户管理（更新）",
  "sort": 2,
  "status": 1
}
```

**响应**

```json
{
  "code": 200,
  "message": "更新成功",
  "data": {
    "id": 2,
    "title": "用户管理（更新）",
    "updatedAt": "2024-01-07T10:30:00.000Z"
  }
}
```

---

### 5. 删除菜单

**权限要求**: `menu:delete`

**请求**

```http
DELETE /menus/:id
Authorization: Bearer <token>
```

**响应**

```json
{
  "code": 200,
  "message": "删除成功（包含3个子菜单）"
}
```

---

## 角色管理接口

### 1. 获取所有角色

**权限要求**: `role:view`

**请求**

```http
GET /roles?page=1&limit=10
Authorization: Bearer <token>
```

**响应**

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "items": [
      {
        "id": 1,
        "name": "管理员",
        "code": "admin",
        "description": "系统管理员，拥有所有权限",
        "sort": 1,
        "status": 1,
        "menuCount": 25,
        "userCount": 3,
        "createdAt": "2024-01-01T00:00:00.000Z"
      }
    ],
    "total": 5,
    "page": 1,
    "limit": 10
  }
}
```

---

### 2. 获取角色详情

**权限要求**: `role:view`

**请求**

```http
GET /roles/:id
Authorization: Bearer <token>
```

**响应**

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "id": 1,
    "name": "管理员",
    "code": "admin",
    "description": "系统管理员",
    "sort": 1,
    "status": 1,
    "menus": [...],
    "createdAt": "2024-01-01T00:00:00.000Z"
  }
}
```

---

### 3. 创建角色

**权限要求**: `role:create`

**请求**

```http
POST /roles
Authorization: Bearer <token>
Content-Type: application/json

{
  "name": "编辑",
  "code": "editor",
  "description": "内容编辑人员",
  "sort": 2
}
```

**响应**

```json
{
  "code": 201,
  "message": "创建成功",
  "data": {
    "id": 3,
    "name": "编辑",
    "code": "editor",
    "createdAt": "2024-01-07T10:00:00.000Z"
  }
}
```

---

### 4. 为角色分配菜单

**权限要求**: `role:assign-menu`

**请求**

```http
POST /roles/:id/menus
Authorization: Bearer <token>
Content-Type: application/json

{
  "menuIds": [1, 2, 3, 4, 5]
}
```

**响应**

```json
{
  "code": 200,
  "message": "分配成功",
  "data": {
    "roleId": 3,
    "roleName": "编辑",
    "menuCount": 5
  }
}
```

---

### 5. 获取角色的菜单树

**权限要求**: `role:view`

**请求**

```http
GET /roles/:id/menus
Authorization: Bearer <token>
```

**响应**

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "roleId": 3,
    "roleName": "编辑",
    "menus": [
      {
        "id": 1,
        "name": "system",
        "title": "系统管理",
        "children": [...]
      }
    ]
  }
}
```

---

## 用户权限接口

### 1. 获取当前用户菜单树（动态路由）

**请求**

```http
GET /auth/menus
Authorization: Bearer <token>
```

**响应**

```json
{
  "code": 200,
  "message": "success",
  "data": [
    {
      "id": 1,
      "name": "system",
      "title": "系统管理",
      "type": "directory",
      "icon": "setting",
      "path": "/system",
      "redirect": "/system/user",
      "children": [
        {
          "id": 2,
          "name": "userManage",
          "title": "用户管理",
          "type": "menu",
          "path": "/system/user",
          "component": "/views/system/user/index",
          "icon": "user",
          "permission": "user:view",
          "meta": {
            "cached": true,
            "affix": false
          }
        }
      ]
    }
  ]
}
```

---

### 2. 获取当前用户权限标识列表

**请求**

```http
GET /auth/permissions
Authorization: Bearer <token>
```

**响应**

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "permissions": [
      "user:view",
      "user:create",
      "user:edit",
      "user:delete",
      "user:export",
      "role:view",
      "role:create",
      "role:assign-menu",
      "menu:view",
      "menu:manage"
    ]
  }
}
```

---

### 3. 获取当前用户信息

**请求**

```http
GET /auth/profile
Authorization: Bearer <token>
```

**响应**

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "id": 1,
    "username": "admin",
    "email": "admin@example.com",
    "nickname": "管理员",
    "avatar": "https://example.com/avatar.jpg",
    "roles": [
      {
        "id": 1,
        "name": "管理员",
        "code": "admin"
      }
    ],
    "permissions": ["user:view", "user:create", ...],
    "lastLoginAt": "2024-01-07T09:00:00.000Z"
  }
}
```

---

## 错误码说明

| 错误码 | 说明 |
|--------|------|
| 1001 | 用户名或密码错误 |
| 1002 | 用户已存在 |
| 1003 | 用户不存在 |
| 1004 | Token 无效或已过期 |
| 1005 | 无权限访问 |
| 2001 | 菜单名称已存在 |
| 2002 | 菜单不存在 |
| 2003 | 无法删除有子菜单的菜单 |
| 3001 | 角色代码已存在 |
| 3002 | 角色不存在 |
| 3003 | 无法删除已分配给用户的角色 |

---

## Postman 集合

可以导入以下 Postman 集合快速测试 API：

```json
{
  "info": {
    "name": "RBAC API",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "item": [
    {
      "name": "Auth",
      "item": [
        {
          "name": "Login",
          "request": {
            "method": "POST",
            "url": "{{baseUrl}}/auth/login",
            "body": {
              "mode": "raw",
              "raw": "{\"email\":\"admin@example.com\",\"password\":\"123456\"}"
            }
          }
        }
      ]
    }
  ]
}
```

---

[返回首页](../README.md) | [上一篇：菜单权限系统](MENU_SYSTEM.md) | [下一篇：开发指南](DEVELOPMENT.md)
