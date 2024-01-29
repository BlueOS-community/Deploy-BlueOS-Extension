# Deploy-BlueOS-Extension
An action for building a BlueOS Extension and deploying it to a Docker registry.

Automatically builds for multiple hardware architectures, and provides inputs for common BlueOS Extesnion metadata variables to pass to your Dockerfile when building.

## Input Variables

| Variable | Description | Required? | Example |
|---|---|---|---|
`docker-username` | Your username for your Docker registry of choice*.<br>Should be stored in a [GitHub Action Secret](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-a-repository).<br>*Currently only Docker Hub is supported.<br>MUST be lowercase. | YES | `secrets.DOCKER_USERNAME`
`docker-password` | The password for your Docker registry authentication.<br>Should be stored in a GitHub Action Secret.<br>Recommended to set the secret to a [Docker Hub Access Token](https://docs.docker.com/docker-hub/access-tokens/). | YES | `secrets.DOCKER_PASSWORD`
`github-token` | Your [authentification token for GitHub](https://docs.github.com/en/actions/security-guides/automatic-token-authentication), for uploading image artifacts to a release/tag. | YES (if building for releases) | Should be set to `secrets.GITHUB_TOKEN`
`build-platforms` | A comma-separated string of the architectures to build for.<br>Defaults to the ones BlueOS is automatically built for. | NO | `'linux/arm/v7,linux/arm64/v8,linux/amd64'`
`image-name` | The base name for the Docker Images and GitHub Artifacts. | YES | `'extension-name'`
`image-prefix` | An optional prefix for the Docker Image name. | NO | `'blueos-'`
`author` | | NO | `'Author Name'`
`author-email` | | NO | `'author.email@example.com'`
`maintainer` | The maintaining organisation or developer.<br> Defaults to the repository owner. | NO | `'Devs-R-US'`
`maintainer-email` | | NO | `'maintainer.email@example.com'`

## Example Usage

To start with, make a Docker repository in your registry accound with the name `{image-prefix}{image-name}`. Note that `image-prefix` defaults to `blueos-` if left unspecified.

```action.yml
name: Deploy BlueOS Extension Image

on:
  push:
  pull_request:
  # Run manually
  workflow_dispatch:
  # NOTE: caches may be removed if not run weekly
  #  -> may be worth scheduling for every 6 days

jobs:
  deploy-docker-image:
    # set the agent to run on
    runs-on: ubuntu-latest
    steps:
      - name: Deploy BlueOS Extension
        uses: BlueOS-community/Deploy-BlueOS-Extension@v1.0.1
        # specify the desired variables
        with:
          docker-username: ${{ secrets.DOCKER_USERNAME }}
          docker-password: ${{ secrets.DOCKER_PASSWORD }}
          github-token: ${{ secrets.GITHUB_TOKEN }}
          image-name: 'something-creative'
```

You may also wish to define some [GitHub Action Variables](https://docs.github.com/en/actions/learn-github-actions/variables), which can be modified later / by repository forkers without needing to change the action file. [ES-Alexander/QuickStart-Python-Extension](https://github.com/ES-Alexander/QuickStart-Python-Extension/blob/main/.github/workflows/deploy.yml) is an example of this.
