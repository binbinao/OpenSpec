# OpenSpec-CN 新手入门指南

本指南将带你完成一个完整的 OpenSpec 开发流程。

## 准备工作

### 1. 安装 OpenSpec-CN

```bash
npm install -g @studyzy/openspec-cn
# 或
pnpm add -g @studyzy/openspec-cn
```

### 2. 初始化项目

在项目根目录运行：

```bash
openspec-cn init
```

这会创建以下结构：

```
openspec/
├── specs/              # 单一事实来源（系统当前行为）
├── changes/           # 变更提案
└── config.yaml        # 项目配置
```

## 完整开发流程

### 流程概览

```
/opsx:propose ──► /opsx:apply ──► /opsx:archive
```

或者使用扩展工作流：

```
/opsx:new ──► /opsx:continue ──► /opsx:apply ──► /opsx:verify ──► /opsx:archive
```

---

## 第一步：创建变更提案

### 方式一：使用 /opsx:propose 命令

告诉AI助手你想做什么：

```
你：/opsx:propose 添加用户登录功能
```

AI会自动为你创建完整的变更结构。

### 方式二：手动创建变更

```bash
# 创建变更目录
mkdir -p openspec/changes/my-feature
```

### 变更包含的产物

每个变更文件夹包含以下产物：

| 产物 | 说明 | 必需 |
|------|------|------|
| `proposal.md` | 为什么要做？做什么？ | ✅ |
| `specs/` | 增量规范（新增/修改/删除需求） | ✅ |
| `design.md` | 技术方案和架构决策 | 可选 |
| `tasks.md` | 实施任务清单 | 可选 |

---

## 第二步：编写 proposal.md

这是变更的起点，说明"为什么"和"做什么"。

```markdown
# 提案：添加用户登录功能

## 背景
我们的应用需要用户登录功能，以便提供个性化体验。

## 目标
- 支持邮箱密码登录
- 实现会话管理
- 提供安全的认证流程

## 范围
- 前端登录表单
- 后端认证API
- JWT token管理

## 非目标
- 第三方登录（OAuth）
- 忘记密码功能
```

---

## 第三步：编写增量规范 (specs/)

在 `specs/` 目录下创建领域文件夹，每个领域一个 `spec.md`。

### 增量规范格式

```markdown
# <领域> 增量规范

## 新增需求

### 需求：用户登录
系统必须支持用户使用邮箱和密码登录。

#### 场景：成功登录
- 当 用户输入有效邮箱
- 当 用户输入正确密码
- 则 返回JWT token
- 并且 重定向到首页

#### 场景：登录失败
- 当 用户输入错误密码
- 则 显示错误信息
- 并且 不返回token

## 修改需求

### 需求：会话超时
系统必须在30分钟后使会话过期。
（之前为：60分钟）

## 移除需求

### 需求：记住我复选框
（已移除，改用长期token）
```

### 需求格式要点

- 每个需求必须有 **SHALL** 或 **必须** 关键字
- 每个场景必须包含 `当...则...` 格式
- 使用 Gherkin 风格：`当...当...则...`

---

## 第四步：编写 design.md

描述技术实现方案：

```markdown
# 设计文档：用户登录功能

## 技术选型

### 认证方案
- 使用 JWT 作为 token
- Access token: 30分钟有效期
- Refresh token: 7天有效期

### 存储方案
- 密码使用 bcrypt 哈希存储
- Token 存储在 httpOnly cookie 中

## 架构设计

### 前端
- 登录页面组件：src/pages/Login.tsx
- 认证Context：src/contexts/AuthContext.tsx

### 后端
- 登录API：POST /api/auth/login
- 中间件：authMiddleware.ts

## API 设计

### 登录接口
POST /api/auth/login
Request: { email, password }
Response: { token, user }

### 登出接口
POST /api/auth/logout
```

---

## 第五步：编写 tasks.md

将工作拆分为可执行的任务：

```markdown
# 任务清单

## 1. 后端认证服务

- [ ] 1.1 创建 User 模型
- [ ] 1.2 实现密码哈希验证
- [ ] 1.3 创建登录API端点
- [ ] 1.4 实现JWT生成
- [ ] 1.5 创建登出API端点

## 2. 前端登录界面

- [ ] 2.1 创建登录表单组件
- [ ] 2.2 添加表单验证
- [ ] 2.3 实现API调用
- [ ] 2.4 处理登录状态

## 3. 认证状态管理

- [ ] 3.1 创建AuthContext
- [ ] 3.2 实现token存储
- [ ] 3.3 添加自动刷新
- [ ] 3.4 保护需要认证的路由
```

---

## 第六步：验证变更

在实施前验证变更是否完整：

```bash
# 验证当前变更
openspec-cn validate

# 验证所有变更
openspec-cn validate --all

# 验证规范
openspec-cn validate --specs
```

### 常见验证错误

1. **缺少增量** - 变更必须包含 specs 目录
2. **需求格式错误** - 需求必须包含 SHALL/MUST/必须
3. **场景格式错误** - 场景必须有 `#### 场景:` 标题

---

## 第七步：查看变更状态

```bash
# 查看所有变更
openspec-cn list

# 查看特定变更状态
openspec-cn status --change my-feature

# 交互式查看
openspec-cn view
```

---

## 第八步：归档变更

当所有任务完成后，归档变更并更新主规范：

```bash
# 归档变更（跳过规范更新）
openspec-cn archive -y --skip-specs my-feature

# 完整归档（更新主规范）
openspec-cn archive my-feature
```

归档后：
- 增量需求合并到主 `specs/` 目录
- 变更移动到 `openspec/changes/archive/`

---

## 常用命令速查

| 命令 | 说明 |
|------|------|
| `openspec-cn init` | 初始化OpenSpec |
| `openspec-cn list` | 列出变更 |
| `openspec-cn list --specs` | 列出规范 |
| `openspec-cn status --change <name>` | 查看状态 |
| `openspec-cn validate --all` | 验证所有 |
| `openspec-cn show <name>` | 显示详情 |
| `openspec-cn archive -y --skip-specs` | 归档 |
| `openspec-cn config` | 配置 |

---

## 最佳实践

1. **从小变更开始** - 先尝试添加小功能，熟悉流程
2. **需求要具体** - 使用 SHALL/MUST，确保可测试
3. **场景要完整** - 覆盖成功、失败、边界情况
4. **任务要可执行** - 每个任务应该在1-2小时内完成
5. **及时归档** - 完成的变更尽快归档，保持目录整洁

---

## 下一步

- 阅读 [快速上手](./getting-started.md) 了解更多细节
- 查看 [命令参考](./commands.md)
- 了解 [工作流](./workflows.md)
