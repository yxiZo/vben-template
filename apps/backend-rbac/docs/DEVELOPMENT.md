# 开发指南

本文档提供项目开发的详细指南，包括环境配置、开发规范、关键实现和测试方法。

## 目录

- [环境配置](#环境配置)
- [开发规范](#开发规范)
- [关键实现](#关键实现)
- [测试指南](#测试指南)
- [开发计划](#开发计划)

---

## 环境配置

### 1. 开发环境要求

| 工具 | 版本要求 |
|------|----------|
| Node.js | >= 18.0.0 |
| pnpm | >= 8.0.0 |
| MySQL | >= 8.0 |
| PostgreSQL | >= 14（可选） |
| Git | >= 2.30 |

### 2. 安装步骤

#### 克隆项目

```bash
git clone <repository-url>
cd rbac-nest
```

#### 安装依赖

```bash
# 使用 pnpm（推荐）
pnpm install

# 或使用 npm
npm install
```

#### 配置环境变量

创建 `.env` 文件：

```bash
cp .env.example .env
```

编辑 `.env` 文件：

```env
# 应用配置
PORT=3000
NODE_ENV=development

# 数据库配置
DB_HOST=localhost
DB_PORT=3306
DB_USERNAME=root
DB_PASSWORD=your_password
DB_DATABASE=rbac_db

# JWT 配置
JWT_SECRET=your-super-secret-key-change-in-production
JWT_EXPIRES_IN=1d

# 日志配置
LOG_LEVEL=debug
```

#### 初始化数据库

```bash
# 创建数据库
mysql -u root -p -e "CREATE DATABASE rbac_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;"

# 运行迁移
pnpm run migration:run

# 填充初始数据（可选）
pnpm run seed
```

#### 启动项目

```bash
# 开发模式（热重载）
pnpm run start:dev

# 生产模式
pnpm run build
pnpm run start:prod

# 调试模式
pnpm run start:debug
```

---

## 开发规范

### 1. 代码风格

#### TypeScript 规范

```typescript
// ✅ 推荐
export class UserService {
  constructor(
    @InjectRepository(User)
    private readonly userRepository: Repository<User>,
  ) {}

  async findAll(): Promise<User[]> {
    return await this.userRepository.find();
  }
}

// ❌ 不推荐
export class UserService {
  constructor(@InjectRepository(User) private userRepository: Repository<User>) {}

  async findAll() {
    return this.userRepository.find();
  }
}
```

#### 命名规范

| 类型 | 规范 | 示例 |
|------|------|------|
| 类名 | PascalCase | `UserService`, `JwtAuthGuard` |
| 接口名 | PascalCase + `I` 前缀 | `IUser`, `ICreateUserDto` |
| 变量/函数 | camelCase | `getUserById`, `isActive` |
| 常量 | UPPER_SNAKE_CASE | `JWT_SECRET`, `MAX_RETRIES` |
| 文件名 | kebab-case | `user.service.ts`, `jwt-auth.guard.ts` |

### 2. 目录结构规范

```
src/
├── [module]/
│   ├── entities/          # 实体（数据库模型）
│   ├── dto/               # 数据传输对象
│   ├── enums/             # 枚举类型
│   ├── interfaces/        # 接口定义
│   ├── [module].controller.ts   # 控制器
│   ├── [module].service.ts      # 服务
│   ├── [module].module.ts       # 模块
│   └── [module].spec.ts         # 测试文件
```

### 3. Git 提交规范

使用 Conventional Commits 规范：

```bash
# 格式：<type>(<scope>): <subject>

# 功能开发
git commit -m "feat(auth): add JWT refresh token feature"

# Bug 修复
git commit -m "fix(menu): fix tree building logic for nested menus"

# 文档更新
git commit -m "docs(readme): update installation guide"

# 代码重构
git commit -m "refactor(user): extract permission logic to service"

# 测试
git commit -m "test(role): add unit tests for role service"

# 样式调整
git commit -m "style(eslint): fix linting errors"

# 性能优化
git commit -m "perf(menu): add caching for menu tree query"
```

**Type 类型：**
- `feat`: 新功能
- `fix`: Bug 修复
- `docs`: 文档更新
- `style`: 代码格式调整
- `refactor`: 重构
- `perf`: 性能优化
- `test`: 测试
- `chore`: 构建/工具链更新

---

## 关键实现

### 1. JWT 认证守卫

```typescript
// src/auth/guards/jwt-auth.guard.ts
import { Injectable, ExecutionContext, UnauthorizedException } from '@nestjs/common';
import { AuthGuard } from '@nestjs/passport';
import { Reflector } from '@nestjs/core';
import { IS_PUBLIC_KEY } from '../decorators/public.decorator';

@Injectable()
export class JwtAuthGuard extends AuthGuard('jwt') {
  constructor(private reflector: Reflector) {
    super();
  }

  canActivate(context: ExecutionContext) {
    // 检查是否为公开路由
    const isPublic = this.reflector.getAllAndOverride<boolean>(IS_PUBLIC_KEY, [
      context.getHandler(),
      context.getClass(),
    ]);

    if (isPublic) {
      return true;
    }

    return super.canActivate(context);
  }

  handleRequest(err, user, info) {
    if (err || !user) {
      throw err || new UnauthorizedException('Token 无效或已过期');
    }
    return user;
  }
}
```

### 2. 角色守卫

```typescript
// src/auth/guards/roles.guard.ts
import { Injectable, CanActivate, ExecutionContext } from '@nestjs/common';
import { Reflector } from '@nestjs/core';
import { ROLES_KEY } from '../decorators/roles.decorator';

@Injectable()
export class RolesGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const requiredRoles = this.reflector.getAllAndOverride<string[]>(ROLES_KEY, [
      context.getHandler(),
      context.getClass(),
    ]);

    if (!requiredRoles) {
      return true;
    }

    const { user } = context.switchToHttp().getRequest();
    return requiredRoles.some((role) => user.roles?.includes(role));
  }
}
```

### 3. 权限守卫

```typescript
// src/auth/guards/permissions.guard.ts
import { Injectable, CanActivate, ExecutionContext, ForbiddenException } from '@nestjs/common';
import { Reflector } from '@nestjs/core';
import { PERMISSIONS_KEY } from '../decorators/permissions.decorator';

@Injectable()
export class PermissionsGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const requiredPermissions = this.reflector.getAllAndOverride<string[]>(
      PERMISSIONS_KEY,
      [context.getHandler(), context.getClass()],
    );

    if (!requiredPermissions) {
      return true;
    }

    const { user } = context.switchToHttp().getRequest();

    const hasPermission = requiredPermissions.every((permission) =>
      user.permissions?.includes(permission),
    );

    if (!hasPermission) {
      throw new ForbiddenException('权限不足');
    }

    return true;
  }
}
```

### 4. 装饰器实现

```typescript
// src/auth/decorators/roles.decorator.ts
import { SetMetadata } from '@nestjs/common';

export const ROLES_KEY = 'roles';
export const Roles = (...roles: string[]) => SetMetadata(ROLES_KEY, roles);

// src/auth/decorators/permissions.decorator.ts
import { SetMetadata } from '@nestjs/common';

export const PERMISSIONS_KEY = 'permissions';
export const RequirePermissions = (...permissions: string[]) =>
  SetMetadata(PERMISSIONS_KEY, permissions);

// src/auth/decorators/public.decorator.ts
import { SetMetadata } from '@nestjs/common';

export const IS_PUBLIC_KEY = 'isPublic';
export const Public = () => SetMetadata(IS_PUBLIC_KEY, true);

// src/auth/decorators/current-user.decorator.ts
import { createParamDecorator, ExecutionContext } from '@nestjs/common';

export const CurrentUser = createParamDecorator(
  (data: unknown, ctx: ExecutionContext) => {
    const request = ctx.switchToHttp().getRequest();
    return request.user;
  },
);
```

### 5. DTO 验证

```typescript
// src/users/dto/create-user.dto.ts
import { IsEmail, IsString, MinLength, MaxLength, IsArray, IsOptional } from 'class-validator';

export class CreateUserDto {
  @IsString()
  @MinLength(3)
  @MaxLength(50)
  username: string;

  @IsEmail()
  email: string;

  @IsString()
  @MinLength(6)
  password: string;

  @IsString()
  @IsOptional()
  @MaxLength(50)
  nickname?: string;

  @IsArray()
  @IsOptional()
  roleIds?: number[];
}
```

### 6. 异常过滤器

```typescript
// src/common/filters/http-exception.filter.ts
import {
  ExceptionFilter,
  Catch,
  ArgumentsHost,
  HttpException,
  HttpStatus,
} from '@nestjs/common';

@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse();
    const request = ctx.getRequest();
    const status = exception.getStatus();

    response.status(status).json({
      code: status,
      message: exception.message,
      error: exception.getResponse(),
      timestamp: new Date().toISOString(),
      path: request.url,
    });
  }
}
```

### 7. 响应拦截器

```typescript
// src/common/interceptors/transform.interceptor.ts
import {
  Injectable,
  NestInterceptor,
  ExecutionContext,
  CallHandler,
} from '@nestjs/common';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';

export interface Response<T> {
  code: number;
  message: string;
  data: T;
}

@Injectable()
export class TransformInterceptor<T>
  implements NestInterceptor<T, Response<T>>
{
  intercept(
    context: ExecutionContext,
    next: CallHandler,
  ): Observable<Response<T>> {
    return next.handle().pipe(
      map((data) => ({
        code: 200,
        message: 'success',
        data,
      })),
    );
  }
}
```

---

## 测试指南

### 1. 单元测试

```typescript
// src/users/users.service.spec.ts
import { Test, TestingModule } from '@nestjs/testing';
import { getRepositoryToken } from '@nestjs/typeorm';
import { UsersService } from './users.service';
import { User } from './entities/user.entity';

describe('UsersService', () => {
  let service: UsersService;

  const mockUserRepository = {
    find: jest.fn(),
    findOne: jest.fn(),
    create: jest.fn(),
    save: jest.fn(),
    delete: jest.fn(),
  };

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [
        UsersService,
        {
          provide: getRepositoryToken(User),
          useValue: mockUserRepository,
        },
      ],
    }).compile();

    service = module.get<UsersService>(UsersService);
  });

  it('should be defined', () => {
    expect(service).toBeDefined();
  });

  describe('findAll', () => {
    it('should return an array of users', async () => {
      const users = [{ id: 1, username: 'test' }];
      mockUserRepository.find.mockResolvedValue(users);

      expect(await service.findAll()).toEqual(users);
      expect(mockUserRepository.find).toHaveBeenCalled();
    });
  });
});
```

### 2. E2E 测试

```typescript
// test/auth.e2e-spec.ts
import { Test, TestingModule } from '@nestjs/testing';
import { INestApplication } from '@nestjs/common';
import * as request from 'supertest';
import { AppModule } from './../src/app.module';

describe('AuthController (e2e)', () => {
  let app: INestApplication;

  beforeEach(async () => {
    const moduleFixture: TestingModule = await Test.createTestingModule({
      imports: [AppModule],
    }).compile();

    app = moduleFixture.createNestApplication();
    await app.init();
  });

  it('/auth/login (POST)', () => {
    return request(app.getHttpServer())
      .post('/auth/login')
      .send({
        email: 'admin@example.com',
        password: '123456',
      })
      .expect(200)
      .expect((res) => {
        expect(res.body.data.access_token).toBeDefined();
      });
  });

  afterAll(async () => {
    await app.close();
  });
});
```

### 3. 测试覆盖率

```bash
# 运行测试并生成覆盖率报告
pnpm run test:cov

# 查看覆盖率报告
open coverage/lcov-report/index.html
```

---

## 开发计划

### 阶段一：基础认证模块 ✅

- [x] 配置 TypeORM 和数据库连接
- [x] 实现用户实体和数据表
- [x] 实现用户注册功能（邮箱验证、密码加密）
- [x] 实现用户登录功能（JWT Token 签发）
- [x] 实现 JWT 认证守卫
- [ ] 实现 Token 刷新机制

### 阶段二：菜单管理模块 ⭐

**当前阶段**

- [ ] 创建菜单实体和数据表
  - [ ] 定义菜单实体（Menu Entity）
  - [ ] 创建数据库迁移文件
  - [ ] 运行迁移创建表
- [ ] 实现菜单类型枚举
  - [ ] 定义 MenuType 枚举（directory, menu, button, iframe, link）
  - [ ] 定义 MenuStatus 枚举
- [ ] 实现菜单 DTO
  - [ ] CreateMenuDto（支持不同类型的字段验证）
  - [ ] UpdateMenuDto
- [ ] 实现菜单 Service
  - [ ] 菜单 CRUD 操作
  - [ ] 构建菜单树（buildMenuTree）
  - [ ] 根据用户角色获取菜单（getUserMenus）
  - [ ] 提取权限标识（getUserPermissions）
- [ ] 实现菜单 Controller
  - [ ] GET /menus/tree - 获取菜单树
  - [ ] GET /menus - 获取扁平菜单列表
  - [ ] POST /menus - 创建菜单
  - [ ] PATCH /menus/:id - 更新菜单
  - [ ] DELETE /menus/:id - 删除菜单
- [ ] 添加菜单字段验证
  - [ ] 根据菜单类型动态验证必填字段
  - [ ] 路径和组件验证

### 阶段三：角色与权限模块

- [ ] 创建角色实体和数据表
- [ ] 实现角色 CRUD 接口
- [ ] 实现角色-菜单关联表和关系
- [ ] 为角色分配菜单权限
- [ ] 获取角色的菜单树
- [ ] 实现用户-角色关联

### 阶段四：用户权限验证

- [ ] 在用户登录时加载角色和权限
- [ ] 实现 GET /auth/menus（获取用户菜单树）
- [ ] 实现 GET /auth/permissions（获取用户权限标识列表）
- [ ] 实现权限守卫（PermissionsGuard）
- [ ] 实现角色守卫（RolesGuard）
- [ ] 在控制器中使用 @RequirePermissions 装饰器

### 阶段五：前端集成（可选）

- [ ] 创建前端示例项目（Vue 3）
- [ ] 实现用户登录并存储 Token
- [ ] 获取用户菜单树并生成动态路由
- [ ] 获取用户权限标识列表
- [ ] 实现前端按钮权限控制（v-if="hasPermission(...)"）
- [ ] 实现前端路由守卫

### 阶段六：完善与优化

- [ ] 添加 Swagger API 文档
  - [ ] 安装 @nestjs/swagger
  - [ ] 为所有 DTO 添加 @ApiProperty 装饰器
  - [ ] 为所有 Controller 添加 @ApiTags
- [ ] 编写单元测试
  - [ ] MenusService 测试
  - [ ] RolesService 测试
  - [ ] AuthService 测试
- [ ] 编写 E2E 测试
  - [ ] 菜单管理接口测试
  - [ ] 角色管理接口测试
  - [ ] 权限验证测试
- [ ] 添加请求日志中间件
- [ ] 实现操作审计日志（创建/更新/删除记录）
- [ ] 性能优化
  - [ ] 菜单树查询缓存（Redis）
  - [ ] 权限标识缓存
- [ ] 添加数据库迁移脚本
- [ ] 编写种子数据（Seeder）
  - [ ] 初始用户（admin）
  - [ ] 初始角色（admin, editor, viewer）
  - [ ] 初始菜单数据

### 阶段七：高级功能（可选）

- [ ] 支持多因素认证（MFA）
- [ ] 实现资源所有权验证（只能操作自己的资源）
- [ ] 支持数据行级权限
- [ ] 实现菜单国际化（i18n）
- [ ] 添加菜单权限导入导出功能
- [ ] 实现菜单版本控制

---

## 常用命令

```bash
# 开发相关
pnpm run start:dev         # 开发模式
pnpm run start:debug       # 调试模式
pnpm run build             # 构建生产版本
pnpm run start:prod        # 生产模式

# 代码质量
pnpm run lint              # 代码检查
pnpm run lint:fix          # 自动修复
pnpm run format            # 代码格式化

# 测试相关
pnpm run test              # 单元测试
pnpm run test:watch        # 监听模式测试
pnpm run test:cov          # 测试覆盖率
pnpm run test:e2e          # E2E 测试

# 数据库相关
pnpm run migration:create  # 创建迁移文件
pnpm run migration:run     # 运行迁移
pnpm run migration:revert  # 回滚迁移
pnpm run seed              # 填充数据
```

---

## 常见问题

### 1. 端口被占用

```bash
# Windows
netstat -ano | findstr :3000
taskkill /PID <PID> /F

# Mac/Linux
lsof -i :3000
kill -9 <PID>
```

### 2. 数据库连接失败

- 检查 `.env` 配置是否正确
- 确认数据库服务已启动
- 确认用户名密码正确
- 检查防火墙设置

### 3. TypeORM 迁移失败

```bash
# 删除迁移表重新运行
DROP TABLE migrations;
pnpm run migration:run
```

---

## 学习资源

- [NestJS 官方文档](https://docs.nestjs.com/)
- [TypeORM 官方文档](https://typeorm.io/)
- [NestJS 中文教程](https://nestjs.bootcss.com/)
- [JWT 最佳实践](https://jwt.io/)
- [TypeScript 深入理解](https://www.typescriptlang.org/docs/)

---

[返回首页](../README.md) | [上一篇：API文档](API.md)
