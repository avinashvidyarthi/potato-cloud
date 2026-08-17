# Potato Cloud

Monorepo for Potato Cloud services.

## Projects

| Folder | Description |
| --- | --- |
| [`home/`](home/) | Static website served by nginx, packaged as a Docker image |

## CI/CD

GitHub Actions workflows live in [`.github/workflows/`](.github/workflows/). The home site workflow builds and pushes to GCP Artifact Registry on merges to `main`.
