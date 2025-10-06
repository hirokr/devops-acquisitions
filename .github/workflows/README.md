# GitHub Actions CI/CD Workflows

This directory contains GitHub Actions workflows for automated testing, linting, and Docker image building/deployment.

## 📋 Available Workflows

### 1. Lint and Format Check (`lint-and-format.yml`)

**Purpose:** Ensures code quality by running ESLint and Prettier checks.

**Triggers:**
- Push to `main` or `staging` branches
- Pull requests to `main` or `staging` branches

**What it does:**
1. Sets up Node.js 20.x with npm caching
2. Installs dependencies with `npm ci`
3. Runs ESLint to check for code issues
4. Runs Prettier to check code formatting
5. Provides annotations with fix suggestions
6. Generates a detailed summary in GitHub Actions

**Fixing Issues:**
- **ESLint issues:** Run `npm run lint:fix`
- **Prettier issues:** Run `npm run format`

**Status Badge:**
```markdown
![Lint and Format](https://github.com/hirokr/devops-acquisitions/actions/workflows/lint-and-format.yml/badge.svg)
```

---

### 2. Tests (`tests.yml`)

**Purpose:** Runs the test suite and generates coverage reports.

**Triggers:**
- Push to `main` or `staging` branches
- Pull requests to `main` or `staging` branches

**What it does:**
1. Sets up Node.js 20.x with npm caching
2. Installs dependencies with `npm ci`
3. Runs Jest tests with environment variables:
   - `NODE_ENV=test`
   - `NODE_OPTIONS=--experimental-vm-modules`
   - `DATABASE_URL=postgres://test:test@localhost:5432/testdb`
4. Uploads coverage reports as artifacts (30-day retention)
5. Generates a summary with test results and coverage status
6. Annotates test failures with actionable suggestions

**Artifacts:**
- Coverage reports are available for download for 30 days
- HTML coverage report: `coverage/lcov-report/index.html`
- JSON coverage summary: `coverage/coverage-summary.json`

**Status Badge:**
```markdown
![Tests](https://github.com/hirokr/devops-acquisitions/actions/workflows/tests.yml/badge.svg)
```

---

### 3. Docker Build and Push (`docker-build-and-push.yml`)

**Purpose:** Builds multi-platform Docker images and pushes them to Docker Hub.

**Triggers:**
- Push to `main` branch
- Manual trigger via `workflow_dispatch`

**What it does:**
1. Sets up Docker Buildx for multi-platform builds
2. Logs in to Docker Hub using secrets
3. Extracts metadata for tags and labels
4. Builds Docker image for `linux/amd64` and `linux/arm64`
5. Pushes image to Docker Hub with multiple tags:
   - Branch name (e.g., `main`)
   - Commit SHA (e.g., `main-abc1234`)
   - `latest` (only for main branch)
   - Timestamp format `prod-YYYYMMDD-HHmmss` (only for main branch)
6. Uses layer caching for faster builds
7. Generates a comprehensive summary with pull/run commands

**Tags Generated:**
- `your-dockerhub-username/devops-acquisitions:main`
- `your-dockerhub-username/devops-acquisitions:main-abc1234`
- `your-dockerhub-username/devops-acquisitions:latest`
- `your-dockerhub-username/devops-acquisitions:prod-20251006-103000`

**Status Badge:**
```markdown
![Docker Build](https://github.com/hirokr/devops-acquisitions/actions/workflows/docker-build-and-push.yml/badge.svg)
```

---

## 🔐 Required Secrets

To use these workflows, you need to configure the following secrets in your GitHub repository:

### Docker Hub Secrets

1. **DOCKER_USERNAME**
   - Your Docker Hub username
   - Go to: Settings → Secrets and variables → Actions → New repository secret

2. **DOCKER_PASSWORD**
   - Your Docker Hub password or access token (recommended)
   - [Generate a Docker Hub access token](https://hub.docker.com/settings/security)
   - Go to: Settings → Secrets and variables → Actions → New repository secret

**Setting up secrets:**
```bash
# Navigate to your GitHub repository
# Go to: Settings → Secrets and variables → Actions → New repository secret
# Add DOCKER_USERNAME and DOCKER_PASSWORD
```

---

## 🚀 Usage

### Running Workflows Locally (Testing)

**Test linting:**
```bash
npm run lint
npm run format:check
```

**Test with fixes:**
```bash
npm run lint:fix
npm run format
```

**Run tests:**
```bash
npm test
```

**Build Docker image locally:**
```bash
docker build -t devops-acquisitions:local --target production .
```

### Manual Workflow Trigger

You can manually trigger the Docker build workflow:

1. Go to **Actions** tab in your GitHub repository
2. Select **Docker Build and Push** workflow
3. Click **Run workflow**
4. Select the branch
5. Click **Run workflow** button

---

## 📊 Workflow Status

You can view the status of all workflows in the **Actions** tab of your repository.

### Adding Status Badges to README

Add these badges to your main `README.md`:

```markdown
# DevOps Acquisitions

![Lint and Format](https://github.com/hirokr/devops-acquisitions/actions/workflows/lint-and-format.yml/badge.svg)
![Tests](https://github.com/hirokr/devops-acquisitions/actions/workflows/tests.yml/badge.svg)
![Docker Build](https://github.com/hirokr/devops-acquisitions/actions/workflows/docker-build-and-push.yml/badge.svg)
```

---

## 🔧 Troubleshooting

### Common Issues

**1. Lint/Format workflow fails**
- Run `npm run lint:fix` and `npm run format` locally
- Commit and push the fixes

**2. Tests workflow fails**
- Run `npm test` locally to reproduce
- Check test logs in GitHub Actions for specific failures
- Fix failing tests and push changes

**3. Docker build fails**
- Verify `DOCKER_USERNAME` and `DOCKER_PASSWORD` secrets are set correctly
- Check Dockerfile syntax
- Ensure all required files are present in the repository
- Test build locally: `docker build -t test --target production .`

**4. Docker login fails**
- Verify Docker Hub credentials
- Consider using an access token instead of password
- Check if Docker Hub account is active

### Viewing Workflow Logs

1. Go to **Actions** tab
2. Click on the workflow run
3. Click on the job name
4. Expand steps to view detailed logs

### Downloading Coverage Reports

1. Go to **Actions** tab
2. Click on a test workflow run
3. Scroll to **Artifacts** section
4. Download **coverage-reports** artifact
5. Extract and open `lcov-report/index.html`

---

## 🎯 Best Practices

1. **Always run tests locally before pushing**
   ```bash
   npm run lint
   npm test
   ```

2. **Create feature branches for new development**
   ```bash
   git checkout -b feature/my-new-feature
   ```

3. **Use pull requests for code review**
   - All workflows will run automatically on PR creation
   - Merge only after all checks pass

4. **Monitor workflow status**
   - Check Actions tab regularly
   - Fix failures promptly

5. **Keep dependencies updated**
   ```bash
   npm outdated
   npm update
   ```

6. **Review Docker image sizes**
   - Use multi-stage builds (already configured)
   - Keep images lean

---

## 📝 Workflow Customization

### Adding More Branches

Edit the workflow files to add more branches:

```yaml
on:
  push:
    branches:
      - main
      - staging
      - develop  # Add new branch
```

### Changing Node.js Version

Update the `node-version` in workflow files:

```yaml
- name: Setup Node.js
  uses: actions/setup-node@v4
  with:
    node-version: '20.x'  # Change version here
```

### Adding Environment Variables

Add more environment variables in the test workflow:

```yaml
env:
  NODE_ENV: test
  DATABASE_URL: postgres://test:test@localhost:5432/testdb
  MY_CUSTOM_VAR: value  # Add custom variables
```

---

## 📚 Additional Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Docker Build Push Action](https://github.com/docker/build-push-action)
- [Node.js Setup Action](https://github.com/actions/setup-node)
- [Jest Documentation](https://jestjs.io/)
- [ESLint Documentation](https://eslint.org/)
- [Prettier Documentation](https://prettier.io/)

---

**Need Help?** Open an issue in the repository or contact the DevOps team.
