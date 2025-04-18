# Deploy-BlueOS-Extension
An action for building a BlueOS Extension and deploying it to a Docker registry.

Automatically builds for multiple hardware architectures, and provides inputs for common BlueOS Extension metadata variables to pass to your Dockerfile when building.

## Input Variables

| Variable | Description | Required? | Example |
|---|---|---|---|
`docker-username` | Your username for your Docker registry of choice*.<br>Should be stored in a [GitHub Action Secret](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-a-repository).<br>*Currently only Docker Hub is supported.<br>MUST be lowercase. | YES | `secrets.DOCKER_USERNAME`
`docker-password` | The password for your Docker registry authentication.<br>Should be stored in a GitHub Action Secret.<br>Recommended to set the secret to a [Docker Hub Access Token](https://docs.docker.com/docker-hub/access-tokens/). | YES | `secrets.DOCKER_PASSWORD`
`github-token` | Your [authentification token for GitHub](https://docs.github.com/en/actions/security-guides/automatic-token-authentication), for uploading image artifacts to a release/tag. | YES (if building for releases) | Should be set to `secrets.GITHUB_TOKEN`
`build-platforms` | A comma-separated string of the architectures to build for.<br>Defaults to the ones BlueOS is automatically built for. | NO | `'linux/arm/v7,linux/arm64/v8,linux/amd64'`
`image-name` | The base name for the Docker Images and GitHub Artifacts. | YES | `'extension-name'`
`image-prefix` | An optional prefix for the Docker Image name. Defaults to `'blueos-'`. | NO | `'blueos-'`
`image-tag` | An optional override for the Docker Image tag pushed to the registry.<br>Defaults to the source repository branch or tag name. | NO | `'custom-tag'`
`author` | | NO | `'Author Name'`
`author-email` | | NO | `'author.email@example.com'`
`maintainer` | The maintaining organisation or developer.<br> Defaults to the repository owner. | NO | `'Devs-R-US'`
`maintainer-email` | | NO | `'maintainer.email@example.com'`
`dockerfile-location` | The location of the Dockerfile to be used for the build.<br>Defaults to the repository's root directory. | NO | `'/.container'`
`skip-checkout` | Skip the checkout step (e.g. if a previous Action has already checked out the repository). | NO | `'false'`

## Example Usage

To start with, make a Docker repository in your registry accound with the name `{image-prefix}{image-name}`. Note that `image-prefix` defaults to `blueos-` if left unspecified.

```action.yml
name: Deploy BlueOS Extension Image

on:
  push:
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
        uses: BlueOS-community/Deploy-BlueOS-Extension@v1
        # specify the desired variables
        with:
          docker-username: ${{ secrets.DOCKER_USERNAME }}
          docker-password: ${{ secrets.DOCKER_PASSWORD }}
          github-token: ${{ secrets.GITHUB_TOKEN }}
          # image-name should not start with blueos- (see image-prefix)
          image-name: 'something-creative'
```

You may also wish to define some [GitHub Action Variables](https://docs.github.com/en/actions/learn-github-actions/variables), which can be modified later / by repository forkers without needing to change the action file. The [QuickStart-Python-Extension](https://github.com/BlueOS-Community/QuickStart-Python-Extension/blob/main/.github/workflows/deploy.yml) is an example of this.

## Outputs

Built images are pushed to Docker Hub, tagged with:
- the `image-tag` value (if one is specified), or
- the code repository branch name (when triggered by a code push), or
- the name of the GitHub tag (when triggered by the creation of a GitHub release), **and**
- `latest`, if the initial image tag is [SemVer](https://semver.org/)-compliant (to reflect that it is the most recently released version)
