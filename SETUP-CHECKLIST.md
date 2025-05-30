# Setup Checklist for Using the Reusable OpenAPI Workflow

Follow these steps to implement the reusable OpenAPI validation and publishing workflow in your repository.

## Prerequisites
- [ ] Your repository has an OpenAPI specification file
- [ ] You have a ReadMe API key for publishing (if needed)

## Setup Steps

### 1. Repository Access
- [ ] Ensure your repository can access `flipdishbytes/publish-api`
- [ ] If this repo is private, verify your repository has the necessary permissions

### 2. Add ReadMe API Key Secret
- [ ] Go to your repository: Settings → Secrets and variables → Actions
- [ ] Click "New repository secret"
- [ ] Name: `README_API_KEY`
- [ ] Value: Your ReadMe API key
- [ ] Click "Add secret"

### 3. Create Workflow File
- [ ] In your repository, create `.github/workflows/validate-openapi.yml`
- [ ] Copy and customize this template:

```yaml
name: Validate and Publish OpenAPI

on:
  push:
    branches:
      - main       # Adjust branches as needed
      - release
  workflow_dispatch:

jobs:
  validate-and-publish:
    uses: flipdishbytes/publish-api/.github/workflows/validate-publish-openapi.yml@main
    with:
      openapi_file_path: 'path/to/your/openapi.json'  # UPDATE THIS PATH
      readme_version: 'v3.0'                          # Optional: customize version
      skip_readme_publish: false                      # Optional: set to true for validation only
    secrets:
      README_API_KEY: ${{ secrets.README_API_KEY }}
```

### 4. Customize Parameters
- [ ] Update `openapi_file_path` to point to your OpenAPI spec file
- [ ] Set appropriate `readme_version` if different from 'v3.0'
- [ ] Configure `skip_readme_publish: true` if you only want validation

### 5. Test the Workflow
- [ ] Commit and push the workflow file
- [ ] Navigate to Actions tab in your repository
- [ ] Manually trigger the workflow using "Run workflow" button
- [ ] Verify it runs successfully and validates your OpenAPI spec

## Optional Configurations

### Multiple OpenAPI Files
If you have multiple OpenAPI specs, create separate jobs:

```yaml
jobs:
  validate-public-api:
    uses: flipdishbytes/publish-api/.github/workflows/validate-publish-openapi.yml@main
    with:
      openapi_file_path: 'api/public/openapi.json'
      readme_version: 'v3.0-public'
    secrets:
      README_API_KEY: ${{ secrets.README_API_KEY }}
  
  validate-internal-api:
    uses: flipdishbytes/publish-api/.github/workflows/validate-publish-openapi.yml@main
    with:
      openapi_file_path: 'api/internal/openapi.json'
      skip_readme_publish: true  # Don't publish internal APIs
```

### Custom Spectral Rules
If you need different validation rules:

```yaml
with:
  openapi_file_path: 'api/openapi.json'
  spectral_rules_url: 'https://example.com/custom-rules.yaml'
```

### Validation Only (No Publishing)
For repositories that only need validation:

```yaml
with:
  openapi_file_path: 'api/openapi.json'
  skip_readme_publish: true
```

## Troubleshooting

### Common Issues
- [ ] **Workflow not found**: Ensure `flipdishbytes/publish-api` is accessible
- [ ] **File not found**: Verify `openapi_file_path` is correct
- [ ] **ReadMe upload fails**: Check `README_API_KEY` secret is set correctly
- [ ] **Spectral validation fails**: Review the validation errors and fix your OpenAPI spec

### Getting Help
- Check the [main README](README.md) for detailed documentation
- Review workflow run logs in the Actions tab
- Ensure your OpenAPI spec follows [Flipdish Spectral rules](https://raw.githubusercontent.com/flipdishbytes/spectral-rules/main/.spectral.yaml)

## Verification
- [ ] Workflow appears in your repository's Actions tab
- [ ] Manual trigger works without errors
- [ ] OpenAPI spec validation passes
- [ ] ReadMe publishing works (if enabled)
- [ ] Team members can see the workflow runs 