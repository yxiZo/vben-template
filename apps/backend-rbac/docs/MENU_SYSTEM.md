# 菜单权限系统详解

本文档详细介绍基于菜单的权限管理系统，包括5种菜单类型、字段说明、权限标识和实战示例。

## 目录

- [菜单类型说明](#菜单类型说明)
- [菜单字段详解](#菜单字段详解)
- [菜单树构建](#菜单树构建)
- [权限标识提取](#权限标识提取)
- [实战代码示例](#实战代码示例)

---

## 菜单类型说明

本系统支持 **5 种菜单类型**，适用于不同的业务场景：

### 1. 目录 (Directory)

用于组织菜单结构，作为菜单分组容器，不对应实际页面。

**字段要求：**
- ✅ 必填：`name`、`title`、`type=directory`
- ✅ 可选：`icon`、`active_icon`、`active_path`、`sort`、`parent_id`、`redirect`
- ❌ 不需要：`component`、`permission`

**使用场景：** 系统管理、用户中心、数据分析等一级分类

**示例数据：**
```json
{
  "type": "directory",
  "name": "system",
  "title": "系统管理",
  "icon": "setting",
  "redirect": "/system/user",
  "sort": 1
}
```

---

### 2. 菜单 (Menu)

对应前端具体页面，需要配置路由和组件路径。

**字段要求：**
- ✅ 必填：`name`、`title`、`type=menu`、`path`、`component`
- ✅ 可选：`icon`、`permission`、`is_cached`、`is_hidden`、`badge_type`、`badge_content`
- ❌ 不需要：`iframe_url`、`link_url`

**使用场景：** 用户列表、角色管理、菜单管理等功能页面

**示例数据：**
```json
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

---

### 3. 按钮 (Button)

页面内的操作权限，不显示在菜单中，用于控制按钮显示和接口访问。

**字段要求：**
- ✅ 必填：`name`、`title`、`type=button`、`permission`、`parent_id`
- ❌ 不需要：`path`、`component`、`icon`、路由相关字段

**使用场景：** 新增、编辑、删除、导出等按钮操作

**权限标识命名规范：**
- `resource:action` 格式
- 示例：
  - `user:create` - 创建用户
  - `user:edit` - 编辑用户
  - `user:delete` - 删除用户
  - `user:export` - 导出用户
  - `role:assign` - 分配角色

**示例数据：**
```json
{
  "type": "button",
  "parentId": 2,
  "name": "userCreate",
  "title": "新增用户",
  "permission": "user:create",
  "sort": 1
}
```

---

### 4. 内嵌 (IFrame)

在系统内以 iframe 形式嵌入其他页面（内部地址）。

**字段要求：**
- ✅ 必填：`name`、`title`、`type=iframe`、`path`、`iframe_url`
- ✅ 可选：`icon`、`is_cached`、`is_affix`
- ❌ 不需要：`component`

**使用场景：** 嵌入内部报表系统、监控面板、可视化大屏等

**示例数据：**
```json
{
  "type": "iframe",
  "parentId": 1,
  "name": "dashboard",
  "title": "数据看板",
  "path": "/dashboard/iframe",
  "iframeUrl": "http://internal.example.com/dashboard",
  "icon": "dashboard",
  "isAffix": true,
  "sort": 1
}
```

---

### 5. 外链 (Link)

跳转到外部网站或系统。

**字段要求：**
- ✅ 必填：`name`、`title`、`type=link`、`link_url`
- ✅ 可选：`icon`、`is_hidden`
- ❌ 不需要：`path`、`component`、`permission`

**使用场景：** 跳转到外部文档、第三方系统、API 文档等

**示例数据：**
```json
{
  "type": "link",
  "name": "apiDocs",
  "title": "API 文档",
  "linkUrl": "https://api.example.com/docs",
  "icon": "document",
  "sort": 99
}
```

---

## 菜单字段详解

### 完整字段表

| 字段名 | 类型 | 必填 | 说明 | 适用类型 |
|--------|------|------|------|----------|
| **id** | number | 自动 | 主键ID | 全部 |
| **parent_id** | number | 否 | 父菜单ID，NULL表示根节点 | 全部 |
| **type** | enum | 是 | 菜单类型：directory/menu/button/iframe/link | 全部 |
| **name** | string | 是 | 菜单唯一标识，用于前端路由命名 | 全部 |
| **title** | string | 是 | 菜单显示标题 | 全部 |
| **sort** | number | 否 | 排序，数字越小越靠前，默认0 | 全部 |
| **status** | enum | 否 | 状态：1-启用，0-禁用，默认1 | 全部 |

### 路由相关字段

| 字段名 | 类型 | 必填 | 说明 | 适用类型 |
|--------|------|------|------|----------|
| **path** | string | 部分 | 前端路由地址，如 `/system/user` | menu, iframe |
| **component** | string | 部分 | 页面组件路径，如 `/views/user/index` | menu |
| **redirect** | string | 否 | 重定向路径 | directory, menu |

### 图标相关字段

| 字段名 | 类型 | 必填 | 说明 | 适用类型 |
|--------|------|------|------|----------|
| **icon** | string | 否 | 菜单图标（图标名称或 URL） | directory, menu, iframe, link |
| **active_icon** | string | 否 | 激活状态图标 | directory, menu |
| **active_path** | string | 否 | 高亮路径匹配规则 | directory, menu, iframe |

### 链接相关字段

| 字段名 | 类型 | 必填 | 说明 | 适用类型 |
|--------|------|------|------|----------|
| **iframe_url** | string | 是 | 内嵌页面地址 | iframe |
| **link_url** | string | 是 | 外链地址 | link |

### 权限相关字段

| 字段名 | 类型 | 必填 | 说明 | 适用类型 |
|--------|------|------|------|----------|
| **permission** | string | 部分 | 权限标识，如 `user:create` | menu, button |

### 徽章相关字段

| 字段名 | 类型 | 必填 | 说明 | 适用类型 |
|--------|------|------|------|----------|
| **badge_type** | enum | 否 | 徽章类型：dot-圆点，text-文本，number-数字 | directory, menu, iframe |
| **badge_content** | string | 否 | 徽章内容 | directory, menu, iframe |

### 配置相关字段

| 字段名 | 类型 | 默认值 | 说明 | 适用类型 |
|--------|------|--------|------|----------|
| **is_hidden** | boolean | false | 是否隐藏（不在菜单中显示） | 全部 |
| **is_cached** | boolean | false | 是否缓存页面（keep-alive） | menu, iframe |
| **is_affix** | boolean | false | 是否固定在标签栏 | menu, iframe |
| **hide_in_breadcrumb** | boolean | false | 在面包屑中隐藏 | 全部 |
| **hide_children_in_menu** | boolean | false | 隐藏子菜单 | directory |

---

## 菜单树构建

### 递归算法实现

```typescript
/**
 * 构建菜单树
 * @param menus 扁平菜单数组
 * @param parentId 父菜单ID，默认为null（根节点）
 * @returns 树形结构的菜单数组
 */
async buildMenuTree(menus: Menu[], parentId: number = null): Promise<Menu[]> {
  const tree: Menu[] = [];

  for (const menu of menus) {
    if (menu.parentId === parentId) {
      // 递归查找子菜单
      const children = await this.buildMenuTree(menus, menu.id);

      if (children.length > 0) {
        menu.children = children;
      }

      tree.push(menu);
    }
  }

  // 按 sort 字段排序
  return tree.sort((a, b) => a.sort - b.sort);
}
```

### 使用示例

```typescript
// 1. 从数据库获取所有菜单（扁平结构）
const allMenus = await this.menusRepository.find({
  where: { status: 1 },
  order: { sort: 'ASC' }
});

// 2. 构建树形结构
const menuTree = await this.buildMenuTree(allMenus);

// 输入（扁平）:
[
  { id: 1, parentId: null, name: "system", title: "系统管理" },
  { id: 2, parentId: 1, name: "user", title: "用户管理" },
  { id: 3, parentId: 2, name: "userCreate", title: "新增" }
]

// 输出（树形）:
[
  {
    id: 1,
    parentId: null,
    name: "system",
    title: "系统管理",
    children: [
      {
        id: 2,
        parentId: 1,
        name: "user",
        title: "用户管理",
        children: [
          {
            id: 3,
            parentId: 2,
            name: "userCreate",
            title: "新增"
          }
        ]
      }
    ]
  }
]
```

---

## 权限标识提取

### 核心逻辑

从用户的菜单树中提取所有权限标识，用于前端按钮控制和后端接口验证。

```typescript
/**
 * 提取用户权限标识
 * @param userId 用户ID
 * @returns 权限标识数组
 */
async getUserPermissions(userId: number): Promise<string[]> {
  // 1. 获取用户菜单树
  const menus = await this.getUserMenus(userId);

  // 2. 使用 Set 去重
  const permissions = new Set<string>();

  // 3. 递归提取权限标识
  const extractPermissions = (menuList: Menu[]) => {
    for (const menu of menuList) {
      // 如果菜单有权限标识，添加到集合
      if (menu.permission) {
        permissions.add(menu.permission);
      }

      // 递归处理子菜单
      if (menu.children && menu.children.length > 0) {
        extractPermissions(menu.children);
      }
    }
  };

  extractPermissions(menus);

  // 4. 返回数组
  return Array.from(permissions);
}
```

### 使用示例

```typescript
// 调用示例
const permissions = await menusService.getUserPermissions(1);

// 返回结果:
[
  "user:view",
  "user:create",
  "user:edit",
  "user:delete",
  "role:view",
  "role:manage",
  "menu:view",
  "menu:manage"
]
```

---

## 实战代码示例

### 1. 后端接口权限保护

```typescript
// src/users/users.controller.ts
import { Controller, Get, Post, Delete, UseGuards, Body, Param } from '@nestjs/common';
import { JwtAuthGuard } from '../auth/guards/jwt-auth.guard';
import { PermissionsGuard } from '../auth/guards/permissions.guard';
import { RequirePermissions } from '../auth/decorators/permissions.decorator';
import { UsersService } from './users.service';

@Controller('users')
@UseGuards(JwtAuthGuard, PermissionsGuard)
export class UsersController {
  constructor(private readonly usersService: UsersService) {}

  // 查看用户列表 - 需要 user:view 权限
  @Get()
  @RequirePermissions('user:view')
  findAll() {
    return this.usersService.findAll();
  }

  // 创建用户 - 需要 user:create 权限
  @Post()
  @RequirePermissions('user:create')
  create(@Body() createUserDto: CreateUserDto) {
    return this.usersService.create(createUserDto);
  }

  // 删除用户 - 需要 user:delete 权限
  @Delete(':id')
  @RequirePermissions('user:delete')
  remove(@Param('id') id: string) {
    return this.usersService.remove(+id);
  }

  // 导出用户 - 需要 user:export 权限
  @Get('export')
  @RequirePermissions('user:export')
  export() {
    return this.usersService.export();
  }
}
```

### 2. 前端按钮权限控制

#### Vue 3 Composition API

```vue
<!-- views/user/index.vue -->
<template>
  <div class="user-page">
    <div class="toolbar">
      <!-- 根据权限显示按钮 -->
      <a-button v-if="hasPermission('user:create')" type="primary" @click="handleCreate">
        <PlusOutlined /> 新增用户
      </a-button>

      <a-button v-if="hasPermission('user:export')" @click="handleExport">
        <ExportOutlined /> 导出
      </a-button>
    </div>

    <a-table :columns="columns" :data-source="users">
      <template #action="{ record }">
        <a-space>
          <a-button v-if="hasPermission('user:edit')" type="link" @click="handleEdit(record)">
            编辑
          </a-button>

          <a-popconfirm
            v-if="hasPermission('user:delete')"
            title="确定要删除吗？"
            @confirm="handleDelete(record.id)"
          >
            <a-button type="link" danger>删除</a-button>
          </a-popconfirm>
        </a-space>
      </template>
    </a-table>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { usePermission } from '@/hooks/usePermission';
import { useUserStore } from '@/stores/user';

// 权限hook
const { hasPermission } = usePermission();

// 用户数据
const users = ref([]);

// 加载用户列表
onMounted(async () => {
  if (hasPermission('user:view')) {
    users.value = await fetchUsers();
  }
});
</script>
```

#### 权限 Hook 实现

```typescript
// hooks/usePermission.ts
import { computed } from 'vue';
import { useUserStore } from '@/stores/user';

export function usePermission() {
  const userStore = useUserStore();

  /**
   * 检查是否拥有指定权限
   * @param permission 权限标识
   * @returns boolean
   */
  const hasPermission = (permission: string): boolean => {
    return userStore.permissions.includes(permission);
  };

  /**
   * 检查是否拥有指定权限中的任意一个
   * @param permissions 权限标识数组
   * @returns boolean
   */
  const hasAnyPermission = (...permissions: string[]): boolean => {
    return permissions.some(permission => hasPermission(permission));
  };

  /**
   * 检查是否拥有所有指定权限
   * @param permissions 权限标识数组
   * @returns boolean
   */
  const hasAllPermissions = (...permissions: string[]): boolean => {
    return permissions.every(permission => hasPermission(permission));
  };

  return {
    hasPermission,
    hasAnyPermission,
    hasAllPermissions,
  };
}
```

### 3. 前端路由动态加载

```typescript
// router/generateRoutes.ts
import { RouteRecordRaw } from 'vue-router';
import { Menu } from '@/types';

/**
 * 将菜单转换为路由配置
 * @param menus 菜单树
 * @returns 路由配置数组
 */
export function menusToRoutes(menus: Menu[]): RouteRecordRaw[] {
  return menus
    .filter(menu => menu.type !== 'button' && menu.type !== 'link') // 按钮和外链不生成路由
    .map(menu => {
      const route: RouteRecordRaw = {
        path: menu.path,
        name: menu.name,
        meta: {
          title: menu.title,
          icon: menu.icon,
          hidden: menu.isHidden,
          cached: menu.isCached,
          affix: menu.isAffix,
        },
      };

      // 根据类型设置组件
      if (menu.type === 'menu') {
        route.component = () => import(`@/views${menu.component}`);
      } else if (menu.type === 'iframe') {
        route.component = () => import('@/components/IFrameView.vue');
        route.meta.iframeUrl = menu.iframeUrl;
      } else if (menu.type === 'directory') {
        route.component = () => import('@/layouts/RouteView.vue');
        if (menu.redirect) {
          route.redirect = menu.redirect;
        }
      }

      // 递归处理子路由
      if (menu.children && menu.children.length > 0) {
        route.children = menusToRoutes(menu.children);
      }

      return route;
    });
}
```

```typescript
// router/index.ts
import { createRouter, createWebHistory } from 'vue-router';
import { useUserStore } from '@/stores/user';
import { menusToRoutes } from './generateRoutes';

const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/login',
      name: 'Login',
      component: () => import('@/views/login/index.vue'),
    },
    // 动态路由将在登录后添加
  ],
});

// 路由守卫
router.beforeEach(async (to, from, next) => {
  const userStore = useUserStore();

  if (to.path === '/login') {
    next();
    return;
  }

  // 检查是否已登录
  if (!userStore.token) {
    next('/login');
    return;
  }

  // 检查是否已加载菜单
  if (!userStore.menusLoaded) {
    try {
      // 1. 获取用户菜单
      const menus = await userStore.fetchUserMenus();

      // 2. 获取用户权限标识
      const permissions = await userStore.fetchUserPermissions();

      // 3. 生成路由
      const routes = menusToRoutes(menus);

      // 4. 动态添加路由
      routes.forEach(route => {
        router.addRoute(route);
      });

      userStore.menusLoaded = true;

      // 5. 继续导航
      next({ ...to, replace: true });
    } catch (error) {
      console.error('Failed to load menus:', error);
      next('/login');
    }
  } else {
    next();
  }
});

export default router;
```

### 4. 自定义指令（可选）

```typescript
// directives/permission.ts
import { Directive } from 'vue';
import { useUserStore } from '@/stores/user';

/**
 * 权限指令
 * 用法: <button v-permission="'user:create'">新增</button>
 */
export const permissionDirective: Directive = {
  mounted(el, binding) {
    const userStore = useUserStore();
    const { value } = binding;

    if (value) {
      const hasPermission = userStore.permissions.includes(value);

      if (!hasPermission) {
        // 移除元素
        el.parentNode?.removeChild(el);
      }
    }
  },
};

// main.ts 中注册
app.directive('permission', permissionDirective);
```

---

## 最佳实践

### 1. 权限标识命名规范

```
格式：资源:操作
示例：
  - user:view      查看用户
  - user:create    创建用户
  - user:edit      编辑用户
  - user:delete    删除用户
  - role:assign    分配角色
  - menu:manage    管理菜单
```

### 2. 菜单层级建议

- 建议不超过 **3 层**
- 结构：目录 → 菜单 → 按钮

### 3. 性能优化

```typescript
// 缓存菜单树查询结果
@Injectable()
export class MenusService {
  private readonly CACHE_TTL = 300; // 5分钟

  async getUserMenusWithCache(userId: number): Promise<Menu[]> {
    const cacheKey = `user:${userId}:menus`;

    // 尝试从缓存获取
    let menus = await this.cacheManager.get<Menu[]>(cacheKey);

    if (!menus) {
      // 缓存未命中，从数据库查询
      menus = await this.getUserMenus(userId);

      // 存入缓存
      await this.cacheManager.set(cacheKey, menus, this.CACHE_TTL);
    }

    return menus;
  }
}
```

---

## 扩展阅读

- [前端路由权限设计](https://router.vuejs.org/guide/advanced/navigation-guards.html)
- [按钮级权限控制最佳实践](https://www.youtube.com/watch?v=example)
- [菜单树性能优化](https://typeorm.io/select-query-builder#loading-relations)

---

[返回首页](../README.md) | [上一篇：系统架构](ARCHITECTURE.md) | [下一篇：API文档](API.md)
