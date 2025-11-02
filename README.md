# OCI Image and Helm Chart GitHub Action

A reusable GitHub Action for building and deploying OCI container images and Helm charts with semantic versioning using [svu](https://github.com/caarlos0/svu).

## Features

- **Semantic Versioning**: Automatically calculate next version using svu based on conventional commits
- **Docker Image Build**: Build multi-platform Docker images and push to OCI registries
- **Helm Chart Packaging**: Package and push Helm charts to OCI registries
- **GitHub Releases**: Automatically create GitHub releases with assets
- **Flexible Configuration**: Extensive inputs for customization
- **Cache Support**: Built-in Docker layer caching for faster builds

## Usage

### Basic Example

```yaml
name: Build and Deploy

on:
  push:
    branches: [ main ]

permissions:
  contents: write
  packages: write

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Build and Deploy
        uses: hauke-cloud/oci-helm-githubaction@v1
        with:
          image-name: ghcr.io/${{ github.repository_owner }}/${{ github.event.repository.name }}
          oci-namespace: ${{ github.repository_owner }}/charts
          registry-password: ${{ secrets.GITHUB_TOKEN }}
```

### Advanced Example

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

permissions:
  contents: write
  packages: write

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    # Only deploy on main branch
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    
    steps:
      - name: Build and Deploy OCI Image and Helm Chart
        uses: hauke-cloud/oci-helm-githubaction@v1
        with:
          # Version configuration
          calculate-version: 'true'
          push-tag: 'true'
          
          # Docker configuration
          build-docker: 'true'
          dockerfile-path: './Dockerfile'
          docker-context: '.'
          image-name: ghcr.io/${{ github.repository_owner }}/${{ github.event.repository.name }}
          docker-platforms: 'linux/amd64,linux/arm64'
          docker-build-args: |
            BUILD_DATE=${{ github.event.head_commit.timestamp }}
            VCS_REF=${{ github.sha }}
          tag-latest: 'true'
          
          # Helm configuration
          build-helm: 'true'
          chart-dir: './helm'
          chart-name: ${{ github.event.repository.name }}
          oci-registry: 'ghcr.io'
          oci-namespace: ${{ github.repository_owner }}/charts
          
          # Registry authentication
          registry-username: ${{ github.actor }}
          registry-password: ${{ secrets.GITHUB_TOKEN }}
          
          # Release configuration
          create-github-release: 'true'
          release-notes: |
            Automated release from CI/CD pipeline
            
            See CHANGELOG.md for details
```

### Docker Only

```yaml
- name: Build Docker Image Only
  uses: hauke-cloud/oci-helm-githubaction@v1
  with:
    build-docker: 'true'
    build-helm: 'false'
    image-name: ghcr.io/${{ github.repository }}
    oci-namespace: ${{ github.repository_owner }}/charts
    registry-password: ${{ secrets.GITHUB_TOKEN }}
```

### Helm Only

```yaml
- name: Deploy Helm Chart Only
  uses: hauke-cloud/oci-helm-githubaction@v1
  with:
    build-docker: 'false'
    build-helm: 'true'
    chart-dir: './charts/my-app'
    image-name: ghcr.io/${{ github.repository }}
    oci-namespace: ${{ github.repository_owner }}/charts
    registry-password: ${{ secrets.GITHUB_TOKEN }}
```

### Manual Version Override

```yaml
- name: Build with Manual Version
  uses: hauke-cloud/oci-helm-githubaction@v1
  with:
    manual-version: 'v1.2.3'
    image-name: ghcr.io/${{ github.repository }}
    oci-namespace: ${{ github.repository_owner }}/charts
    registry-password: ${{ secrets.GITHUB_TOKEN }}
```

## Inputs

### Version Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `calculate-version` | Calculate next semantic version using svu | No | `true` |
| `push-tag` | Push git tag when calculating version | No | `true` |
| `manual-version` | Manual version override (skips svu calculation) | No | `''` |

### Docker Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `build-docker` | Build and push Docker image | No | `true` |
| `dockerfile-path` | Path to Dockerfile | No | `./Dockerfile` |
| `docker-context` | Docker build context | No | `.` |
| `image-name` | Docker image name (e.g., ghcr.io/owner/repo) | **Yes** | - |
| `docker-platforms` | Target platforms for Docker build | No | `linux/amd64` |
| `docker-build-args` | Docker build arguments (multiline) | No | `''` |
| `tag-latest` | Also tag image as latest | No | `true` |

### Helm Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `build-helm` | Build and push Helm chart | No | `true` |
| `chart-dir` | Path to Helm chart directory | No | `./helm` |
| `chart-name` | Helm chart name (defaults to repository name) | No | `''` |
| `oci-registry` | OCI registry URL | No | `ghcr.io` |
| `oci-namespace` | OCI registry namespace (e.g., owner/charts) | **Yes** | - |

### Registry Auth Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `registry-username` | Registry username | No | `${{ github.actor }}` |
| `registry-password` | Registry password/token | **Yes** | - |

### Release Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `create-github-release` | Create GitHub release | No | `true` |
| `release-notes` | Custom release notes | No | `''` |

## Outputs

| Output | Description |
|--------|-------------|
| `version` | Version that was built/deployed |
| `docker-image` | Full Docker image reference with tag |
| `helm-chart` | Full Helm chart OCI reference |

## Semantic Versioning with svu

This action uses [svu](https://github.com/caarlos0/svu) to automatically calculate semantic versions based on git tags and conventional commits.

### Commit Message Format

svu follows conventional commit format to determine version bumps:

- **Major version** (1.x.x → 2.0.0): `feat!:` or `fix!:` or `BREAKING CHANGE:` in footer
- **Minor version** (1.0.x → 1.1.0): `feat:` prefix
- **Patch version** (1.0.0 → 1.0.1): `fix:` prefix

### Example Commit Messages

```bash
# Patch version bump
git commit -m "fix: resolve authentication bug"

# Minor version bump
git commit -m "feat: add new API endpoint"

# Major version bump
git commit -m "feat!: redesign authentication system"
# or
git commit -m "feat: new auth system

BREAKING CHANGE: old authentication methods no longer supported"
```

## Requirements

### Repository Structure

For Helm chart deployment, your repository should have:

```
.
├── Dockerfile              # Required for Docker builds
├── helm/                   # Default Helm chart location
│   ├── Chart.yaml
│   ├── values.yaml
│   └── templates/
│       └── ...
```

You can customize paths using `dockerfile-path` and `chart-dir` inputs.

### Permissions

The workflow needs these permissions:

```yaml
permissions:
  contents: write    # For creating releases and pushing tags
  packages: write    # For pushing to container registry
```

### Git Tags

The action requires at least one git tag for svu to work. If starting fresh:

```bash
git tag v0.0.0
git push origin v0.0.0
```

## Examples from Real Projects

This action was extracted from the [bookmark-generator](https://github.com/hauke-cloud/bookmark-generator) project. See its [ci-cd.yaml](.github/workflows/ci-cd.yaml) for a complete working example.

## License

MIT License - see [LICENSE](LICENSE) for details.

## Contributing

Contributions are welcome! Please open an issue or submit a pull request.

## Author

Created by [hauke-cloud](https://github.com/hauke-cloud)
