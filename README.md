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
      readme_slug: 'your-api-slug'
      readme_api_key: ${{ secrets.README_API_KEY }}
      readme_version: 'v3.0'
      skip_readme_publish: false
```

### From This Repository

See `.github/workflows/example-usage.yml` for a working example.

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `openapi_file_path` | Path to the OpenAPI specification file | ✅ | - |
| `readme_slug` | Slug to use for the API definition in ReadMe | ✅ | - |
| `readme_api_key` | API key for ReadMe | ✅ | - |
| `spectral_rules_url` | URL to the Spectral rules file | ❌ | `https://raw.githubusercontent.com/flipdishbytes/spectral-rules/main/.spectral.yaml` |
| `readme_version` | Version to use when uploading to ReadMe | ❌ | `v3.0` |
| `skip_readme_publish` | Skip publishing to ReadMe (useful for validation-only runs) | ❌ | `false` |
| `skip_spectral` | Skip Spectral linting (useful for validation-only runs) | ❌ | `false` |
| `skip_validate_readme` | Skip ReadMe OpenAPI validation (useful for linting-only runs) | ❌ | `false` |

## Secrets

| Secret | Description | Required |
|--------|-------------|----------|
| `README_API_KEY` | API key for ReadMe | Only if publishing |

## Workflow Steps

1. **Download Spectral Rules**: Downloads the specified Spectral ruleset (skipped if `skip_spectral` is true)
2. **Show Spectral Ruleset**: Displays the downloaded rules for verification (skipped if `skip_spectral` is true)
3. **Node.js Setup**: Sets up Node.js environment for Spectral CLI
4. **Install Spectral**: Installs the Spectral CLI tool (skipped if `skip_spectral` is true)
5. **Lint with Spectral**: Validates the OpenAPI spec against the rules (skipped if `skip_spectral` is true)
6. **Validate with ReadMe**: Uses ReadMe's validation tool (skipped if `skip_validate_readme` is true)
7. **Publish to ReadMe**: Uploads the spec to ReadMe (skipped if `skip_readme_publish` is true)

## Examples

### Basic Usage
```yaml
uses: flipdishbytes/publish-api/.github/workflows/validate-publish-openapi.yml@main
with:
  openapi_file_path: 'api/openapi.json'
  readme_slug: 'my-api'
  readme_api_key: ${{ secrets.README_API_KEY }}
```

### Validation Only (No Publishing)
```yaml
uses: flipdishbytes/publish-api/.github/workflows/validate-publish-openapi.yml@main
with:
  openapi_file_path: 'api/openapi.json'
  readme_slug: 'my-api'
  readme_api_key: ${{ secrets.README_API_KEY }}
  skip_readme_publish: true
```

### Spectral Linting Only (Skip ReadMe Validation and Publishing)
```yaml
uses: flipdishbytes/publish-api/.github/workflows/validate-publish-openapi.yml@main
with:
  openapi_file_path: 'api/openapi.json'
  readme_slug: 'my-api'
  readme_api_key: ${{ secrets.README_API_KEY }}
  skip_validate_readme: true
  skip_readme_publish: true
```

### ReadMe Validation Only (Skip Spectral Linting and Publishing)
```yaml
uses: flipdishbytes/publish-api/.github/workflows/validate-publish-openapi.yml@main
with:
  openapi_file_path: 'api/openapi.json'
  readme_slug: 'my-api'
  readme_api_key: ${{ secrets.README_API_KEY }}
  skip_spectral: true
  skip_readme_publish: true
```

### Custom Spectral Rules
```yaml
uses: flipdishbytes/publish-api/.github/workflows/validate-publish-openapi.yml@main
with:
  openapi_file_path: 'api/openapi.json'
  readme_slug: 'my-api'
  readme_api_key: ${{ secrets.README_API_KEY }}
  spectral_rules_url: 'https://example.com/custom-rules.yaml'
  readme_version: 'v2.0'
```

### Multiple OpenAPI Files
```yaml
jobs:
  validate-public-api:
    uses: flipdishbytes/publish-api/.github/workflows/validate-publish-openapi.yml@main
    with:
      openapi_file_path: 'api/public/openapi.json'
      readme_slug: 'public-api'
      readme_api_key: ${{ secrets.README_API_KEY }}
      readme_version: 'v3.0-public'
  
  validate-internal-api:
    uses: flipdishbytes/publish-api/.github/workflows/validate-publish-openapi.yml@main
    with:
      openapi_file_path: 'api/internal/openapi.json'
      readme_slug: 'internal-api'
      readme_api_key: ${{ secrets.README_API_KEY }}
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