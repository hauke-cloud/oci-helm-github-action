# Contributing to OCI Helm GitHub Action

Thank you for your interest in contributing! This document provides guidelines for contributing to this project.

## Getting Started

1. Fork the repository
2. Clone your fork: `git clone https://github.com/YOUR_USERNAME/oci-helm-github-action.git`
3. Create a feature branch: `git checkout -b feature/my-new-feature`
4. Make your changes
5. Test your changes
6. Commit with conventional commits (see below)
7. Push to your fork: `git push origin feature/my-new-feature`
8. Create a Pull Request

## Conventional Commits

This project uses semantic versioning based on conventional commits. Please follow this format:

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

- `feat`: A new feature (minor version bump)
- `fix`: A bug fix (patch version bump)
- `docs`: Documentation only changes
- `style`: Code style changes (formatting, missing semi-colons, etc)
- `refactor`: Code refactoring without changing functionality
- `perf`: Performance improvements
- `test`: Adding or updating tests
- `chore`: Maintenance tasks, dependency updates, etc
- `ci`: Changes to CI/CD configuration

### Breaking Changes

For breaking changes (major version bump), add `!` after the type or add `BREAKING CHANGE:` in the footer:

```
feat!: redesign authentication system
```

or

```
feat: new authentication system

BREAKING CHANGE: old authentication methods no longer supported
```

## Examples

```bash
# New feature (minor version bump)
git commit -m "feat: add support for custom registry endpoints"

# Bug fix (patch version bump)
git commit -m "fix: resolve Docker build context issue"

# Documentation update (no version bump)
git commit -m "docs: update README with new examples"

# Breaking change (major version bump)
git commit -m "feat!: change input parameter names for clarity"
```

## Testing

Before submitting a PR:

1. Test the action in a real repository
2. Verify all examples in `.github/workflows/example-*.yml` work
3. Update documentation if you change inputs/outputs
4. Add examples for new features

### Testing Your Changes

Create a test repository with:
- A Dockerfile
- A Helm chart in `./helm/`
- An initial git tag (e.g., `v0.0.0`)

Then reference your branch in a workflow:

```yaml
- uses: YOUR_USERNAME/oci-helm-github-action@your-branch
  with:
    image-name: ghcr.io/YOUR_USERNAME/test-repo
    oci-namespace: YOUR_USERNAME/charts
    registry-password: ${{ secrets.GITHUB_TOKEN }}
```

## Pull Request Guidelines

- **Title**: Use conventional commit format
- **Description**: Explain what changes you made and why
- **Testing**: Describe how you tested your changes
- **Documentation**: Update README.md if you add/change features
- **Examples**: Add workflow examples for new features

## Code Style

- Use clear, descriptive variable names
- Add comments for complex logic
- Keep steps focused and single-purpose
- Use shell best practices (quote variables, check exit codes, etc.)

## Questions?

Open an issue for questions or discussions about contributing.

## License

By contributing, you agree that your contributions will be licensed under the MIT License.
