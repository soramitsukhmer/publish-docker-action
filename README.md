# soramitsukhmer/publish-docker-action

This repository is considered **EXPERIMENTAL** and under active development until further notice. It is subject to non-backward compatible changes or removal in any future version so you should [pin to a specific tag/commit](https://docs.github.com/en/actions/creating-actions/about-actions#using-tags-for-release-management) of this action in your workflow (i.e `soramitsukhmer/publish-docker-action@v1`).

## About

Build and Publish Docker Container via action composition that use Docker Buildx Bake as a high-level build command.

## Usage

```yml
name: ci

on:
  schedule:
    # https://crontab.guru/#40_10_*_*_*
    - cron: "40 10 * * *"
  push:
    branches: [main]
    # Publish semver tags as releases.
    tags: ["v*.*.*"]
  pull_request:
    branches: [main]

env:
  # Use docker.io for Docker Hub if empty
  REGISTRY: ghcr.io
  # github.repository as <account>/<repo>
  IMAGE_NAME: ${{ github.repository }}

jobs:
  bake:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      # Login against a Docker registry
      # https://github.com/docker/login-action
      - name: Log into registry ${{ env.REGISTRY }}
        uses: docker/login-action@v1
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      # Build and push Docker image with Buildx Bake
      # https://github.com/soramitsukhmer/publish-docker-action
      - name: Build and push
        uses: soramitsukhmer/publish-docker-action@v1
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
          targets: build
          push: true
```

## Customizing

- Docker Login: https://github.com/docker/login-action#readme
- Buildx Bake options: https://github.com/docker/bake-action#customizing

| Name    | Type     |                                                    |
| ------- | -------- | -------------------------------------------------- |
| images  | List/CSV | List of Docker images to use as base name for tags |
| targets | List/CSV | List of bake targets                               |
| push    | Bool     | Push to registry (default false)                   |

## Built-in actions

This action composition uses the following actions.

- [docker/setup-qemu-action](https://github.com/docker/setup-qemu-action)
- [docker/setup-buildx-action](https://github.com/docker/setup-buildx-action)
- [docker/metadata-action](https://github.com/docker/metadata-action)
- [docker/bake-action](https://github.com/docker/bake-action)

## Keep up-to-date with GitHub Dependabot

Since [Dependabot](https://docs.github.com/en/github/administering-a-repository/keeping-your-actions-up-to-date-with-github-dependabot) has [native GitHub Actions support](https://docs.github.com/en/github/administering-a-repository/configuration-options-for-dependency-updates#package-ecosystem), to enable it on your GitHub repo all you need to do is add the `.github/dependabot.yml` file:

```yml
version: 2
updates:
  # Maintain dependencies for GitHub Actions
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "daily"
```
