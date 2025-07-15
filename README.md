# Dokploy Deploy Action

[中文文档](README_CN.md) | English

A GitHub Action for automatically deploying applications to Dokploy platform. Supports the latest Dokploy API version with `x-api-key` header authentication.

## Features

- 🔍 Automatically find application ID from project and application names
- 🚀 Support for both applications and Compose project deployments
- 🔑 Uses the latest `x-api-key` header authentication method
- ✅ Detailed error handling and status feedback
- 🛡️ Automatic masking of sensitive data in outputs

## Usage

### Basic Usage

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

### Input Parameters

| Parameter | Description | Required | Example |
|-----------|-------------|----------|---------|
| `dokploy_url` | Dokploy server base URL | ✅ | `https://dokploy.example.com` |
| `api_key` | Dokploy API key | ✅ | `${{ secrets.DOKPLOY_API_KEY }}` |
| `project_name` | Dokploy project name | ✅ | `my-project` |
| `application_name` | Dokploy application name or compose name | ✅ | `my-app` |

## Setup Instructions

### 1. Get API Key

1. Log in to your Dokploy panel
2. Go to **Settings** → **My Profile** page
3. Create a new API key
4. Copy the generated key

### 2. Configure GitHub Secrets

1. In your GitHub repository, go to **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Name: `DOKPLOY_API_KEY`
4. Value: Paste your API key from Dokploy
5. Click **Add secret**

### 3. Determine Project and Application Names

- **Project Name**: The project name displayed in the Dokploy panel
- **Application Name**: The application name or Compose service name under the project

## Workflow

1. **Environment Setup**: Ensure the runtime environment has `jq` tool installed
2. **Get Application ID**:
   - Call Dokploy API to get all project lists
   - Find the corresponding application ID based on project name and application name
   - Support both regular applications and Compose projects
3. **Trigger Deployment**:
   - Use the found application ID to call the deployment API
   - Return deployment status and results

## Example Configurations

### Deploy Single Application

```yaml
- name: Deploy Application
  uses: Crokily/dokploy-deploy-action@v1.0.1
  with:
    dokploy_url: 'https://dokploy.mycompany.com'
    api_key: ${{ secrets.DOKPLOY_API_KEY }}
    project_name: 'frontend'
    application_name: 'web-app'
```

### Deploy Compose Project

```yaml
- name: Deploy Compose Project
  uses: Crokily/dokploy-deploy-action@v1.0.1
  with:
    dokploy_url: 'https://dokploy.mycompany.com'
    api_key: ${{ secrets.DOKPLOY_API_KEY }}
    project_name: 'backend'
    application_name: 'api-stack'
```

### Multi-Environment Deployment

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

## Error Handling

The Action will fail in the following situations:

- Unable to connect to Dokploy server
- Invalid API key or insufficient permissions
- Cannot find the specified project or application
- Deployment request rejected by Dokploy

### Common Errors and Solutions

| Error Message | Possible Cause | Solution |
|---------------|----------------|----------|
| `Not found: project="xxx" application="yyy"` | Incorrect project or application name | Check the exact names in Dokploy panel |
| `HTTP 401` | Invalid API key | Regenerate and update API key |
| `HTTP 403` | Insufficient permissions | Ensure API key has deployment permissions |
| `Deployment failed` | Error during deployment process | Check Dokploy logs and application configuration |

## Requirements

- Dokploy server with API access support
- Valid Dokploy API key
- Project and application must be already created in Dokploy

## Version History

- **v1.0.0**: Initial version, support for basic application and Compose project deployment
- Uses new `x-api-key` header authentication method
- Automatic application ID lookup feature

## License

[MIT License](LICENSE)

## Contributing

Issues and Pull Requests are welcome!

## Related Links

- [Dokploy Official Documentation](https://dokploy.com/docs)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)

---

If you find this Action useful, please give it a ⭐ to show your support!
