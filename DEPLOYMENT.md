# 🚀 Public CI Repository Deployment Guide

This guide walks you through deploying the public CI repository for unlimited GitHub Actions.

## 📋 Quick Deployment Checklist

### ✅ Step 1: Create Public Repository
```bash
# 1. Go to GitHub and create new repository
Repository Name: remote-ai-coder-ci
Visibility: Public ⚠️ (Required for unlimited Actions)
Initialize: With README
```

### ✅ Step 2: Upload Workflow Files
```bash
# 2. Copy these files to your new public repository:
.github/workflows/dispatch-receiver.yml
.github/workflows/scheduled-sync.yml  
.github/workflows/manual-sync.yml
README.md
CI-SETUP-GUIDE.md
DEPLOYMENT.md (this file)
```

### ✅ Step 3: Configure Repository Settings
```bash
# 3. Repository Settings → Actions → General
Actions permissions: Allow all actions and reusable workflows
Workflow permissions: Read and write permissions
Allow GitHub Actions to create and approve pull requests: ✅
```

### ✅ Step 4: Set Up Secrets
```bash
# 4. Repository Settings → Secrets and variables → Actions
PRIVATE_REPO_TOKEN = <Your GitHub Personal Access Token>
# Token scopes needed: repo, workflow, write:packages
```

### ✅ Step 5: Test Workflows
```bash
# 5. Go to Actions tab and manually trigger "Manual Sync"
# This will verify your setup is working correctly
```

## 🔐 Personal Access Token Setup

### Create Token
1. **Go to GitHub Settings** → Developer settings → Personal access tokens → Tokens (classic)
2. **Generate new token** with these scopes:
   - ✅ `repo` (Full control of private repositories)
   - ✅ `workflow` (Update GitHub Action workflows)  
   - ✅ `write:packages` (Upload packages to GitHub Package Registry)
3. **Copy the token** - you'll need it for secrets configuration

### Configure Secrets

#### In Public CI Repository:
```bash
Name: PRIVATE_REPO_TOKEN
Value: <Your Personal Access Token>
```

#### In Private Development Repository:
```bash
Name: PUBLIC_REPO_DISPATCH_TOKEN  
Value: <Your Personal Access Token>
```

## 🔧 Repository Configuration

### Actions Settings
```bash
Repository Settings → Actions → General:

Actions permissions:
☑️ Allow all actions and reusable workflows

Workflow permissions:  
☑️ Read and write permissions
☑️ Allow GitHub Actions to create and approve pull requests

Fork pull request workflows:
☑️ Run workflows from fork pull requests
```

### Branch Protection (Optional)
```bash
Repository Settings → Branches → Add rule:

Branch name pattern: main
☑️ Require status checks to pass before merging
☑️ Require branches to be up to date before merging
```

## 📁 File Structure

Your public repository should have this structure:
```
remote-ai-coder-ci/
├── .github/
│   └── workflows/
│       ├── dispatch-receiver.yml      # Option 1: Fast CI
│       ├── scheduled-sync.yml         # Option 2: Scheduled
│       └── manual-sync.yml           # Option 3: Manual
├── README.md                         # Repository overview
├── CI-SETUP-GUIDE.md                # Detailed setup guide
├── DEPLOYMENT.md                     # This file
└── .gitignore                       # Standard GitHub ignore
```

## 🧪 Testing Your Setup

### Test 1: Manual Sync Workflow
```bash
1. Go to Actions tab in public repository
2. Click "Manual Sync with Docker Options"
3. Click "Run workflow"
4. Configure options:
   - Source: main
   - Docker Build: false (for first test)
   - Tests: true
   - Test Scope: smoke-tests
5. Click "Run workflow"
6. Verify it completes successfully
```

### Test 2: Repository Dispatch
```bash
1. Push a commit to your private repository
2. Check if dispatch-trigger.yml runs in private repo
3. Check if dispatch-receiver.yml runs in public repo
4. Verify source code is transferred and tested
```

### Test 3: Scheduled Sync
```bash
1. Go to Actions tab in public repository
2. Click "Scheduled Sync with Docker Build"  
3. Click "Run workflow"
4. Choose "main" as sync source
5. Verify it syncs code and builds Docker image
```

## 🐳 Docker Registry Setup

### GitHub Container Registry
Your Docker images will be automatically pushed to:
```bash
ghcr.io/YOUR_USERNAME/remote-ai-coder:latest
ghcr.io/YOUR_USERNAME/remote-ai-coder:COMMIT_SHA
```

### Registry Permissions
```bash
Repository Settings → Actions → General:
☑️ Read and write permissions

This allows workflows to push to GitHub Container Registry
```

## 🔍 Monitoring & Troubleshooting

### Check Workflow Status
```bash
# Actions tab shows:
✅ Green checkmark = Success
❌ Red X = Failed  
🟡 Yellow circle = In progress
⚪ Gray circle = Queued
```

### Common Issues & Solutions

#### Issue: "Resource not accessible by integration"
```bash
Solution: Check workflow permissions
Repository Settings → Actions → General → Workflow permissions
Select: "Read and write permissions"
```

#### Issue: "Bad credentials" or "Not Found"
```bash
Solution: Check PRIVATE_REPO_TOKEN secret
1. Verify token has correct scopes (repo, workflow, write:packages)
2. Check token hasn't expired
3. Verify repository name is correct in workflows
```

#### Issue: "No such file or directory"
```bash
Solution: Check private repository structure
1. Verify frontend/ and backend/ directories exist
2. Check package.json and requirements.txt are present
3. Ensure file paths in workflows match your structure
```

#### Issue: Docker build fails
```bash
Solution: Check Dockerfile and dependencies
1. Verify all COPY paths exist in source
2. Check requirements.txt and package.json are valid
3. Test Docker build locally first
```

## 📊 Performance Optimization

### Workflow Optimization
```yaml
# Use caching for dependencies
- uses: actions/cache@v4
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}

# Use matrix builds for parallel execution
strategy:
  matrix:
    test-type: [frontend, backend, integration]
```

### Docker Optimization
```dockerfile
# Multi-stage builds for smaller images
FROM node:18-alpine AS build
# ... build steps ...

FROM nginx:alpine AS production
COPY --from=build /app/dist /usr/share/nginx/html
```

## 🔄 Maintenance

### Regular Tasks
- **Update workflow dependencies** (actions versions)
- **Rotate Personal Access Tokens** (every 6-12 months)
- **Clean up old artifacts** (automatic after 7 days)
- **Monitor workflow performance** and optimize as needed

### Updates
```bash
# To update workflows:
1. Edit workflow files in public repository
2. Commit changes
3. Test with manual trigger
4. Monitor for any issues
```

## 🎯 Success Criteria

Your deployment is successful when:

✅ **All three workflows** can be triggered manually  
✅ **Repository dispatch** works from private repo  
✅ **Docker images** build and push successfully  
✅ **Tests execute** and results are reported  
✅ **Artifacts** are collected and stored  
✅ **No billing issues** with unlimited Actions  

## 📞 Getting Help

If you encounter issues:

1. **Check workflow logs** in Actions tab for detailed errors
2. **Verify all secrets** are configured correctly
3. **Test token permissions** by accessing private repo manually
4. **Review file paths** and repository structure
5. **Start with manual sync** (easiest to debug)

## 🎉 Next Steps

Once deployed successfully:

1. **Customize workflows** for your specific needs
2. **Set up branch protection** rules if desired
3. **Configure notifications** for workflow results
4. **Add status badges** to your private repository README
5. **Document your CI/CD process** for team members

**Congratulations! You now have unlimited GitHub Actions with complete code privacy!** 🚀✨
