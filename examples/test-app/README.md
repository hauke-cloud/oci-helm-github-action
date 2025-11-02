# Test Application Example

This directory contains a minimal test application to verify the OCI Helm GitHub Action works correctly.

## Structure

```
test-app/
├── Dockerfile          # Minimal Docker image
├── helm/              # Minimal Helm chart
│   ├── Chart.yaml
│   ├── values.yaml
│   └── templates/
│       └── deployment.yaml
└── .github/
    └── workflows/
        └── test.yml    # Workflow to test the action
```

## Usage

1. Copy this directory to a new repository
2. Initialize git and create an initial tag:
   ```bash
   git init
   git add .
   git commit -m "feat: initial commit"
   git tag v0.0.0
   git push origin main --tags
   ```
3. Enable GitHub Actions in your repository
4. Push changes to trigger the workflow

## Testing the Action

The workflow will:
1. Calculate the next version using svu
2. Build a Docker image
3. Package a Helm chart
4. Push both to GitHub Container Registry
5. Create a GitHub release

## Expected Results

After a successful run:
- New git tag created (e.g., `v0.1.0`)
- Docker image pushed to `ghcr.io/YOUR_USERNAME/test-app`
- Helm chart pushed to `oci://ghcr.io/YOUR_USERNAME/charts/test-app`
- GitHub release created with the packaged chart
