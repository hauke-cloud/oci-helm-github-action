# Quick Start Guide

Get started with the OCI Helm GitHub Action in minutes!

## Prerequisites

1. A GitHub repository with:
   - A `Dockerfile` (for Docker builds)
   - A Helm chart in `./helm/` directory (for Helm deployments)
   - At least one git tag (e.g., `v0.0.0`)

2. Required permissions in your workflow:
   ```yaml
   permissions:
     contents: write
     packages: write
   ```

## Step 1: Create Initial Git Tag

If your repository doesn't have any tags yet:

```bash
git tag v0.0.0
git push origin v0.0.0
```

## Step 2: Create Workflow File

Create `.github/workflows/deploy.yml`:

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
      - uses: hauke-cloud/oci-helm-githubaction@v1
        with:
          image-name: ghcr.io/${{ github.repository }}
          oci-namespace: ${{ github.repository_owner }}/charts
          registry-password: ${{ secrets.GITHUB_TOKEN }}
```

## Step 3: Make Changes and Commit

Use conventional commit messages to control versioning:

```bash
# For a patch version bump (0.0.0 → 0.0.1)
git commit -m "fix: resolve authentication bug"

# For a minor version bump (0.0.0 → 0.1.0)
git commit -m "feat: add new API endpoint"

# For a major version bump (0.0.0 → 1.0.0)
git commit -m "feat!: redesign authentication"
```

## Step 4: Push and Watch

```bash
git push origin main
```

The action will:
1. Calculate the next version based on your commits
2. Build your Docker image
3. Package your Helm chart
4. Push both to GitHub Container Registry
5. Create a GitHub release

## Step 5: Use Your Artifacts

### Pull the Docker image:
```bash
docker pull ghcr.io/YOUR_USERNAME/YOUR_REPO:VERSION
```

### Install the Helm chart:
```bash
helm install my-app oci://ghcr.io/YOUR_USERNAME/charts/YOUR_REPO --version VERSION
```

## Common Customizations

### Build Docker only:
```yaml
- uses: hauke-cloud/oci-helm-githubaction@v1
  with:
    build-helm: 'false'
    image-name: ghcr.io/${{ github.repository }}
    oci-namespace: ${{ github.repository_owner }}/charts
    registry-password: ${{ secrets.GITHUB_TOKEN }}
```

### Custom Helm chart location:
```yaml
- uses: hauke-cloud/oci-helm-githubaction@v1
  with:
    chart-dir: './charts/my-app'
    image-name: ghcr.io/${{ github.repository }}
    oci-namespace: ${{ github.repository_owner }}/charts
    registry-password: ${{ secrets.GITHUB_TOKEN }}
```

### Multi-platform Docker builds:
```yaml
- uses: hauke-cloud/oci-helm-githubaction@v1
  with:
    docker-platforms: 'linux/amd64,linux/arm64'
    image-name: ghcr.io/${{ github.repository }}
    oci-namespace: ${{ github.repository_owner }}/charts
    registry-password: ${{ secrets.GITHUB_TOKEN }}
```

## Next Steps

- Read the full [README.md](README.md) for all configuration options
- Check [example workflows](.github/workflows/) for more use cases
- See [CONTRIBUTING.md](CONTRIBUTING.md) for development guidelines
- Review [CHANGELOG.md](CHANGELOG.md) for version history

## Troubleshooting

### "No tags found" error
Create an initial tag: `git tag v0.0.0 && git push origin v0.0.0`

### Permission denied errors
Ensure your workflow has `contents: write` and `packages: write` permissions.

### Chart not found
Verify your Helm chart exists at the `chart-dir` location (default: `./helm/`).

### Build failures
Check your Dockerfile and ensure it builds locally first: `docker build -t test .`

## Support

For issues or questions:
- Open an issue on [GitHub](https://github.com/hauke-cloud/oci-helm-githubaction/issues)
- Check existing [discussions](https://github.com/hauke-cloud/oci-helm-githubaction/discussions)
