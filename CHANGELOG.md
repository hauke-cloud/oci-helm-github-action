# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2024-11-02

### Added
- Initial release of OCI Image and Helm Chart GitHub Action
- Automatic semantic versioning using svu
- Docker image building and pushing to OCI registries
- Helm chart packaging and pushing to OCI registries
- Multi-platform Docker builds support
- Docker layer caching for faster builds
- GitHub release creation with assets
- Flexible configuration with extensive inputs
- Support for manual version override
- Separate control for Docker and Helm builds
- Custom release notes support
- Example workflows for common use cases

### Features
- **Version Management**: Automatic semantic versioning via svu with conventional commits
- **Docker Support**: Build and push multi-platform images with build args
- **Helm Support**: Package and push charts with automatic version alignment
- **OCI Registry**: Push both images and charts to any OCI-compatible registry
- **Caching**: Built-in GitHub Actions cache for Docker layers
- **Releases**: Automatic GitHub release creation with changelogs
- **Flexibility**: Enable/disable Docker or Helm builds independently

## [Unreleased]

### Planned
- Support for additional OCI registries (Docker Hub, AWS ECR, etc.)
- Chart testing and validation
- Security scanning integration
- Artifact attestation and signing
- Custom Helm repository support
