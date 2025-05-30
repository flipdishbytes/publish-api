# Reusable OpenAPI Validation and Publishing Workflow

This repository contains a reusable GitHub Actions workflow for validating and publishing OpenAPI specifications using Spectral linting and ReadMe.

## Features

- **Spectral Linting**: Validates OpenAPI specs against configurable Spectral rules
- **ReadMe Integration**: Validates and publishes specs to ReadMe documentation platform
- **Configurable**: Supports multiple parameters for different use cases
- **Reusable**: Can be called from any repository in your organization

## Usage

### From Another Repository

To use this reusable workflow in another repository, create a workflow file (e.g., `.github/workflows/validate-openapi.yml`) in your target repository:

```yaml
name: Validate and Publish OpenAPI

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:
  validate-and-publish:
    uses: flipdishbytes/publish-api/.github/workflows/validate-publish-openapi.yml@main
    with:
      openapi_file_path: 'path/to/your/openapi.json'
      readme_version: 'v3.0'
      skip_readme_publish: false
    secrets:
      README_API_KEY: ${{ secrets.README_API_KEY }}
```

### From This Repository

See `.github/workflows/example-usage.yml` for a working example.

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `openapi_file_path` | Path to the OpenAPI specification file | ✅ | - |
| `spectral_rules_url` | URL to the Spectral rules file | ❌ | `https://raw.githubusercontent.com/flipdishbytes/spectral-rules/main/.spectral.yaml` |
| `readme_version` | Version to use when uploading to ReadMe | ❌ | `v3.0` |
| `skip_readme_publish` | Skip publishing to ReadMe (validation only) | ❌ | `false` |
| `runs_on` | Runner to use for the job | ❌ | `ubuntu-latest` |

## Secrets

| Secret | Description | Required |
|--------|-------------|----------|
| `README_API_KEY` | API key for ReadMe | Only if publishing |

## Workflow Steps

1. **Checkout**: Checks out the repository code
2. **Download Spectral Rules**: Downloads the specified Spectral ruleset
3. **Lint with Spectral**: Validates the OpenAPI spec against the rules
4. **Validate with ReadMe**: Uses ReadMe's validation tool
5. **Publish to ReadMe**: Uploads the spec to ReadMe (if enabled)

## Examples

### Basic Usage
```yaml
uses: flipdishbytes/publish-api/.github/workflows/validate-publish-openapi.yml@main
with:
  openapi_file_path: 'api/openapi.json'
secrets:
  README_API_KEY: ${{ secrets.README_API_KEY }}
```

### Validation Only (No Publishing)
```yaml
uses: flipdishbytes/publish-api/.github/workflows/validate-publish-openapi.yml@main
with:
  openapi_file_path: 'api/openapi.json'
  skip_readme_publish: true
```

### Custom Spectral Rules
```yaml
uses: flipdishbytes/publish-api/.github/workflows/validate-publish-openapi.yml@main
with:
  openapi_file_path: 'api/openapi.json'
  spectral_rules_url: 'https://example.com/custom-rules.yaml'
  readme_version: 'v2.0'
secrets:
  README_API_KEY: ${{ secrets.README_API_KEY }}
```

### Multiple OpenAPI Files
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

## Setup Requirements

### In the Target Repository

1. **OpenAPI File**: Ensure your OpenAPI specification file exists at the specified path
2. **ReadMe API Key**: Add your ReadMe API key as a repository secret named `README_API_KEY`
3. **Permissions**: Ensure the calling workflow has appropriate permissions

### Repository Secrets

To publish to ReadMe, you need to set up the `README_API_KEY` secret:

1. Go to your repository Settings → Secrets and variables → Actions
2. Click "New repository secret"
3. Name: `README_API_KEY`
4. Value: Your ReadMe API key

## Troubleshooting

- **Spectral Validation Fails**: Check that your OpenAPI spec follows the rules defined in the Spectral ruleset
- **ReadMe Upload Fails**: Verify your API key is correct and has the necessary permissions
- **File Not Found**: Ensure the `openapi_file_path` is correct relative to your repository root

## Contributing

To modify this reusable workflow:

1. Edit `.github/workflows/validate-publish-openapi.yml`
2. Test changes using the example workflow
3. Update this README if inputs or behavior changes