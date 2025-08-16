# GitHub Pages Configuration Guide

**Repository**: fiverings-photo  
**URL**: https://fiverings.photo  
**Deployment Method**: GitHub Actions (Workflow)  
**Last Updated**: 2025-08-16  

## 🚨 CRITICAL: Custom Domain with Workflow Deployment

### The Problem
When using GitHub Actions workflow deployment (not branch deployment):
- The CNAME file in your artifact does **NOT** automatically configure the custom domain
- GitHub Pages settings will show custom domain as `null`
- SSL certificates won't provision without the domain being set

### The Solution
You MUST manually set the custom domain using one of these methods:

#### Method 1: GitHub CLI API (Recommended)
```bash
# Set custom domain via API
gh api --method PUT repos/jur1st/fiverings-photo/pages \
  --field cname="fiverings.photo"

# Verify it was set
gh api repos/jur1st/fiverings-photo/pages --jq '.cname'
# Should output: fiverings.photo
```

#### Method 2: GitHub Web UI
1. Navigate to repository Settings
2. Scroll to Pages section
3. Enter `fiverings.photo` in Custom domain field
4. Click Save
5. Wait for DNS check to complete

## 📋 Complete Setup Process

### Step 1: Repository Configuration
```bash
# Clone repository
git clone https://github.com/jur1st/fiverings-photo.git
cd fiverings-photo

# Ensure CNAME file exists in deployment directory
echo "fiverings.photo" > public/CNAME
```

### Step 2: GitHub Actions Workflow
Location: `.github/workflows/deploy.yaml`

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        
      # Deploy static HTML from public/ directory
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

### Step 3: Enable GitHub Pages
```bash
# Via API
gh api --method POST repos/jur1st/fiverings-photo/pages \
  --field source='{"branch":"main","path":"/"}'

# Or in UI: Settings → Pages → Source: GitHub Actions
```

### Step 4: Set Custom Domain (CRITICAL!)
```bash
# This step is often missed but is REQUIRED for workflow deployment
gh api --method PUT repos/jur1st/fiverings-photo/pages \
  --field cname="fiverings.photo"
```

### Step 5: Verify Deployment
```bash
# Check Pages status
gh api repos/jur1st/fiverings-photo/pages

# Check latest deployment
gh api repos/jur1st/fiverings-photo/actions/runs \
  --jq '.workflow_runs[0] | {status: .status, conclusion: .conclusion}'

# Test the site
curl -I https://fiverings.photo
```

## 🔄 Deployment Commands

### Trigger Manual Deployment
```bash
# Via workflow dispatch
gh workflow run deploy.yaml

# Via empty commit
git commit --allow-empty -m "Trigger deployment"
git push origin main
```

### Monitor Deployment
```bash
# Watch workflow progress
gh run watch

# Check deployment status
gh api repos/jur1st/fiverings-photo/deployments \
  --jq '.[0] | {id: .id, state: .state, created: .created_at}'
```

## 🔧 Troubleshooting

### Custom Domain Not Working
```bash
# 1. Verify CNAME file exists in deployed content
curl https://jur1st.github.io/fiverings-photo/CNAME
# Should return: fiverings.photo

# 2. Set custom domain via API (often the fix!)
gh api --method PUT repos/jur1st/fiverings-photo/pages \
  --field cname="fiverings.photo"

# 3. Trigger rebuild
git commit --allow-empty -m "Rebuild after domain config"
git push
```

### SSL Certificate Errors
1. Ensure custom domain is set (see above)
2. Verify DNS A records point to GitHub
3. Wait 10-30 minutes for provisioning
4. Check HTTPS enforcement:
```bash
# Enable HTTPS enforcement
gh api --method PUT repos/jur1st/fiverings-photo/pages \
  --field https_enforced=true
```

### 404 Errors
- Verify workflow completed successfully
- Check artifact was uploaded
- Ensure public/ directory contains index.html
- Verify Pages is enabled

## 📊 Pages Configuration Status Check

### Complete Status Report
```bash
gh api repos/jur1st/fiverings-photo/pages | jq '{
  html_url: .html_url,
  status: .status,
  cname: .cname,
  custom_404: .custom_404,
  https_enforced: .https_enforced,
  public: .public,
  build_type: .build_type,
  source: .source
}'
```

### Expected Output
```json
{
  "html_url": "https://fiverings.photo",
  "status": "built",
  "cname": "fiverings.photo",
  "custom_404": false,
  "https_enforced": true,
  "public": true,
  "build_type": "workflow",
  "source": {
    "branch": "main",
    "path": "/"
  }
}
```

## 🎯 Best Practices

1. **Always verify custom domain is set** after enabling Pages
2. **Include CNAME file** in your deployment artifact
3. **Use API commands** for reliable configuration
4. **Test both HTTP and HTTPS** after deployment
5. **Document your deployment process** for team members

## 📚 Related Resources

- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Custom Domain Setup](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site)
- [GitHub Actions for Pages](https://github.com/actions/deploy-pages)
- [DNS Configuration](DNS-CONFIGURATION.md)

---

**Deployment Status**: ✅ Operational  
**Custom Domain**: ✅ fiverings.photo configured  
**SSL Certificate**: ✅ Active  
**Workflow**: ✅ GitHub Actions configured