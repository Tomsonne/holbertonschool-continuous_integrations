# Building and Publishing Docker Images in CI

This project uses GitHub Actions to build and publish a hardened Node.js Docker image automatically.

## Application

The image contains a small Express server running on port `3000`.

Available routes:

* `/` returns the application message.
* `/health` returns the container health status.

## Task 0: Build the Image in CI

The workflow builds the Docker image automatically on a clean GitHub Actions runner.

https://github.com/Tomsonne/holbertonschool-continuous_integrations/actions/runs/33077380583

## Task 1: Publish to GHCR

On a push to `main`, the workflow authenticates to GitHub Container Registry with `GITHUB_TOKEN` and publishes the image.

https://github.com/Tomsonne/holbertonschool-continuous_integrations/actions/runs/33088318903

https://github.com/Tomsonne/holbertonschool-continuous_integrations/pkgs/container/holbertonschool-continuous_integrations

## Task 2: Automatic Docker Tags

Docker tags are generated automatically from the Git context using `docker/metadata-action`.

A push to `main` generates:

* `latest`
* `main`
* a short commit SHA

https://github.com/Tomsonne/holbertonschool-continuous_integrations/actions/runs/33160620281

Pushing the Git tag `v1.0.0` generates the Docker image tag `1.0.0`.

https://github.com/Tomsonne/holbertonschool-continuous_integrations/actions/runs/33160833549

## Task 3: Docker Layer Cache

Docker layer caching is configured with the GitHub Actions cache backend.

Unchanged layers such as the system packages and NPM dependencies are reused during later builds.

* Before caching: **9 seconds**
* After caching: **14 seconds**

https://github.com/Tomsonne/holbertonschool-continuous_integrations/actions/runs/33160620281

https://github.com/Tomsonne/holbertonschool-continuous_integrations/actions/runs/33162261688

The cached run was slower because this is a very small image and importing, extracting and exporting the cache added more overhead than rebuilding the layers. The logs still confirm that the unchanged layers were marked as `CACHED`.

