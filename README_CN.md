# Dokploy Deploy Action

English | [中文文档](README_CN.md)

一个 GitHub Action，用于自动部署应用到 Dokploy 平台。支持最新版本的 Dokploy API，使用 `x-api-key` 头部进行身份验证。

## 功能特性

- 🔍 自动从项目名称和应用名称查找应用程序 ID
- 🚀 支持应用程序 (applications) 和 Compose 项目部署
- 🔑 使用最新的 `x-api-key` 头部认证方式
- ✅ 详细的错误处理和状态反馈
- 🛡️ 自动屏蔽敏感数据输出

## 使用方法

### 基本用法

```yaml
name: Deploy to Dokploy

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Dokploy
        uses: Crokily/dokploy-deploy-action@v1.0.1
        with:
          dokploy_url: 'https://dokploy.example.com'
          api_key: ${{ secrets.DOKPLOY_API_KEY }}
          project_name: 'my-project'
          application_name: 'my-app'
```

### 输入参数

| 参数名 | 描述 | 是否必填 | 示例 |
|--------|------|----------|------|
| `dokploy_url` | Dokploy 服务器的基础 URL | ✅ | `https://dokploy.example.com` |
| `api_key` | Dokploy API 密钥 | ✅ | `${{ secrets.DOKPLOY_API_KEY }}` |
| `project_name` | Dokploy 项目名称 | ✅ | `my-project` |
| `application_name` | Dokploy 应用程序名称或 Compose 名称 | ✅ | `my-app` |

## 设置说明

### 1. 获取 API 密钥

1. 登录到您的 Dokploy 面板
2. 转到 **Settings** → **My Profile** 页面
3. 创建新的 API 密钥
4. 复制生成的密钥

### 2. 配置 GitHub Secrets

1. 在您的 GitHub 仓库中，转到 **Settings** → **Secrets and variables** → **Actions**
2. 点击 **New repository secret**
3. 名称：`DOKPLOY_API_KEY`
4. 值：粘贴您从 Dokploy 获取的 API 密钥
5. 点击 **Add secret**

### 3. 确定项目和应用程序名称

- **项目名称**：在 Dokploy 面板中显示的项目名称
- **应用程序名称**：项目下的应用程序名称或 Compose 服务名称

## 工作流程

1. **环境准备**：确保运行环境安装了 `jq` 工具
2. **获取应用程序 ID**：
   - 调用 Dokploy API 获取所有项目列表
   - 根据项目名称和应用程序名称查找对应的应用程序 ID
   - 支持普通应用程序和 Compose 项目
3. **触发部署**：
   - 使用找到的应用程序 ID 调用部署 API
   - 返回部署状态和结果

## 示例配置

### 部署单个应用程序

```yaml
- name: Deploy Application
  uses: Crokily/dokploy-deploy-action@v1.0.1
  with:
    dokploy_url: 'https://dokploy.mycompany.com'
    api_key: ${{ secrets.DOKPLOY_API_KEY }}
    project_name: 'frontend'
    application_name: 'web-app'
```

### 部署 Compose 项目

```yaml
- name: Deploy Compose Project
  uses: Crokily/dokploy-deploy-action@v1.0.1
  with:
    dokploy_url: 'https://dokploy.mycompany.com'
    api_key: ${{ secrets.DOKPLOY_API_KEY }}
    project_name: 'backend'
    application_name: 'api-stack'
```

### 多环境部署

```yaml
name: Multi-Environment Deploy

on:
  push:
    branches: [ main, staging ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        environment: [staging, production]
        include:
          - environment: staging
            branch: staging
            project_name: 'my-app-staging'
          - environment: production
            branch: main
            project_name: 'my-app-prod'
    
    if: github.ref == format('refs/heads/{0}', matrix.branch)
    
    steps:
      - name: Deploy to ${{ matrix.environment }}
        uses: Crokily/dokploy-deploy-action@v1.0.1
        with:
          dokploy_url: ${{ secrets.DOKPLOY_URL }}
          api_key: ${{ secrets.DOKPLOY_API_KEY }}
          project_name: ${{ matrix.project_name }}
          application_name: 'my-application'
```

## 错误处理

Action 会在以下情况下失败：

- 无法连接到 Dokploy 服务器
- API 密钥无效或权限不足
- 找不到指定的项目或应用程序
- 部署请求被 Dokploy 拒绝

### 常见错误及解决方案

| 错误信息 | 可能原因 | 解决方案 |
|----------|----------|----------|
| `Not found: project="xxx" application="yyy"` | 项目或应用程序名称错误 | 检查 Dokploy 面板中的确切名称 |
| `HTTP 401` | API 密钥无效 | 重新生成并更新 API 密钥 |
| `HTTP 403` | 权限不足 | 确保 API 密钥有部署权限 |
| `Deployment failed` | 部署过程中出错 | 检查 Dokploy 日志和应用程序配置 |

## 要求

- Dokploy 服务器支持 API 访问
- 有效的 Dokploy API 密钥
- 项目和应用程序必须已在 Dokploy 中创建

## 版本历史

- **v1.0.0**: 初始版本，支持基本的应用程序和 Compose 项目部署
- 使用新的 `x-api-key` 头部认证方式
- 自动查找应用程序 ID 功能

## 许可证

[MIT License](LICENSE)

## 贡献

欢迎提交 Issue 和 Pull Request！

## 相关链接

- [Dokploy 官方文档](https://dokploy.com/docs)
- [GitHub Actions 文档](https://docs.github.com/en/actions)

---

如果您觉得这个 Action 有用，请给个 ⭐ 支持一下！
