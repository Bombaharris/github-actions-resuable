# GitHub Actions Reusable Workflows

This repository contains a collection of reusable workflows designed to streamline CI/CD processes in different projects. These workflows cover continuous integration, release management, Docker image creation, and deployment.

## Workflows usages

1. [CI Workflow](.github/workflows/ci.yml) — automates testing and builds on code pushes or pull requests.

    ```yaml
    name: Call a reusable CI

    on: workflow_dispatch

    jobs:
    build:
        uses: Bombaharris/github-actions-reusable/.github/workflows/ci.yml@master
        permissions:
        contents: read
        packages: write
        secrets: inherit
    ```

2. [Release Workflow](.github/workflows/release.yml) — manages automated tagging and releases.

    ```yaml
    name: Call a reusable Release

    on:
    workflow_dispatch:
        inputs:
        version:
            description: 'Release version to start'
            required: true

    jobs:
    build:
        uses: Bombaharris/github-actions-reusable/.github/workflows/release.yml@master
        permissions:
        contents: write
        packages: write
        pull-requests: write
        with:
            VERSION: ${{ github.event.inputs.version }}
        secrets: inherit
    ```

3. [Dockerize Workflow](.github/workflows/dockerize.yml) — builds and publishes Docker images.

    ```yaml
    name: Call a reusable Dockerize

    on:
        release:
        types: [created]
        workflow_dispatch:

    jobs:
    build:
        uses: Bombaharris/github-actions-reusable/.github/workflows/dockerize.yml@master
        permissions:
        contents: read
        packages: write
        with:
            TAG: ${{ github.event.release.tag_name || github.sha }}
            IMAGE_NAME: ${{ github.repository }}
        secrets: inherit
    ```

4. [Deploy Workflow](.github/workflows/deploy.yml) — automates deployment to various environments.

    ```yaml
    name: Call a reusable Deploy
    on:
    release:
        types: [created]
    push:
        branches:
            - develop
    workflow_dispatch:

    jobs:
    deploy:
        strategy:
        matrix:
            env: [DEV, DEVAS]
        uses: Bombaharris/github-actions-reusable/.github/workflows/deploy.yml@master
        permissions:
        contents: read
        packages: write
        with:
        ENV: ${{ matrix.env }}
        GH_REF: ${{ github.event.release.tag_name || github.sha }}
        secrets: inherit
    ```

### Specifing version

```yaml
uses: Bombaharris/github-actions-reusable/.github/workflows/<workflow_name>.yaml@v1.0.0
```

Replace `<workflow_name>` with `ci`, `release`, `dockerize`, or `deploy`, and `<version>` with the desired version or branch.

## Prerequisites

Every repository implementing reusable workflows should have this.

## Repository variables

| Variable Name                   | Value                                                     |
|---------------------------------|-----------------------------------------------------------|
| APP_NAME                        | prefix-name-app                                           |
| NODE_VERSION                    | 18                                                        |

### Environments

#### Environments variables

| Variable Name                   | Value                                                     |
|---------------------------------|-----------------------------------------------------------|
| AZURE_ASSETS_BLOB_STORAGE_NAME  | $web                                                      |
| AZURE_BLOB_STORAGE_BASE         | $web                                                      |
| AZURE_BLOB_STORAGE_NAME         | ps-portal                                                 |
| DEPLOY_URL                      | https://example.com/                                      |
| FRAGMENT_URL                    | ps-portal                                                 |

#### Environments secrets

| Secret Name                                  |
|----------------------------------------------|
| AZURE_ASSETS_BLOB_STORAGE_CONNECTION_STRING  |
| AZURE_BLOB_STORAGE_CONNECTION_STRING         |
