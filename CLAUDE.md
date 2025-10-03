# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目架构

这是一个基于 Turbo 的 monorepo 架构 SaaS 应用，使用 Next.js 14 App Router。

### 核心技术栈

- **框架**: Next.js 14 (App Router), TypeScript
- **样式**: Tailwind CSS, Shadcn/ui, Framer Motion
- **状态管理**: Zustand, TanStack Query
- **认证**: Clerk (默认), NextAuth.js (可选实现)
- **数据库**: PostgreSQL, Kysely (查询构建器), Prisma (Schema 管理)
- **支付**: Stripe
- **邮件**: React Email, Resend
- **包管理**: Bun (默认), npm (备用)
- **Monorepo**: Turbo

### 目录结构

```
saasfly/
├── apps/
│   ├── nextjs/           # 主 Next.js 应用
│   └── auth-proxy/       # 认证代理服务
├── packages/
│   ├── api/             # API 路由和 tRPC 服务
│   ├── auth/            # 认证相关工具
│   ├── common/          # 共享工具和类型
│   ├── db/              # 数据库 Schema 和工具
│   ├── stripe/          # Stripe 集成
│   └── ui/              # 共享 UI 组件库
├── tooling/             # 开发工具配置
└── turbo/              # Turbo 生成器配置
```

## 常用开发命令

### 项目管理
```bash
# 安装依赖
bun install

# 启动开发服务器 (并行启动所有服务)
bun run dev

# 仅启动 Web 应用 (排除 Stripe)
bun run dev:web

# 构建项目
bun run build

# 类型检查
bun run typecheck

# 代码检查
bun run lint

# 代码格式化
bun run format

# 修复代码格式
bun run format:fix
```

### 数据库操作
```bash
# 推送数据库 schema 变更
bun run db:push

# 生成 Prisma 客户端
cd packages/db && bun run db:generate

# 格式化数据库 schema
cd packages/db && bun run format
```

### 包管理
```bash
# 清理 node_modules
bun run clean

# 清理工作空间
bun run clean:workspaces

# 检查依赖版本一致性
bun run check-deps

# 生成新的包/组件
bun run gen
```

## 核心开发工作流

### 1. 环境配置
- 复制 `.env.example` 到 `.env.local`
- 配置必要的环境变量：数据库 URL、认证密钥、Stripe 密钥等
- 运行 `bun run db:push` 初始化数据库

### 2. 开发流程
- 使用 `bun run dev:web` 启动开发服务器
- 新功能在相应的包中开发
- UI 组件在 `packages/ui` 中开发
- API 路由在 `packages/api` 或 `apps/nextjs/src/app/api` 中开发

### 3. 数据库变更
- 修改 `packages/db/prisma/schema.prisma`
- 运行 `bun run db:push` 应用变更
- 如需生成客户端，运行 `cd packages/db && bun run db:generate`

## 重要配置说明

### Turbo 配置
- 使用 `turbo.json` 管理构建管道
- 支持并行开发和缓存优化
- 工作空间依赖通过 `workspace:*` 引用

### Next.js 配置
- 启用严格模式和 MDX 支持
- 配置跨域图片域名
- 支持热重载本地包
- 输出模式为 `standalone`

### 认证系统
- 默认使用 Clerk 进行用户认证
- NextAuth.js 实现存在于 `feature-nextauth` 分支
- 认证配置在 `packages/auth` 中

### UI 组件系统
- 基于 Radix UI 和 Tailwind CSS
- 组件通过独立导出路径支持按需导入
- 包含动画组件和 3D 效果支持

## 部署说明

### Vercel 部署
- 支持一键部署到 Vercel
- 需要配置环境变量
- 使用 Vercel Postgres 作为数据库选项

### 本地部署
- 构建：`bun run build`
- 启动：`bun run start`
- 确保所有环境变量已配置

## 特殊注意事项

- 项目使用 Bun 作为主要包管理器，但也支持 npm
- 认证系统在 2025年6月后默认使用 Clerk
- 管理后台功能仍在 Alpha 阶段
- 支持国际化 (i18n)
- 包含完整的分析和监控集成 (Vercel Analytics, PostHog)