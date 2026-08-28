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

* Before caching: **13 seconds**
* After caching: **7 seconds**

https://github.com/Tomsonne/holbertonschool-continuous_integrations/actions/runs/33170837986/job/98847319830

https://github.com/Tomsonne/holbertonschool-continuous_integrations/actions/runs/33170936369/job/98847646591

## Task 4: Scan Before You Ship

The Docker image is scanned with Trivy before being published to GHCR.

The pipeline blocks publication when Trivy detects a vulnerability with the `CRITICAL` severity. Trivy returns exit code `1`, causing the workflow to fail and skipping the image push step.

https://github.com/Tomsonne/holbertonschool-continuous_integrations/actions/runs/33172814138

https://github.com/Tomsonne/holbertonschool-continuous_integrations/actions/runs/33173289773

The failed run detected `CVE-2026-59873` in the `tar` package and correctly prevented the image from being published.

A temporary exception is documented in `.trivyignore`. This exception allows the successful demonstration run, but it does not fix the vulnerability and should be removed when the dependency is updated.