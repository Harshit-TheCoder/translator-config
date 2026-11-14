# Translator GitOps configuration

This repository holds environment-specific Helm values and the Argo CD
Application for the translator service.

Argo CD reads `apps/translator-dev.yml`. It uses the Helm chart from the
`translator` repository and `env/dev/values.yaml` from this repository.

The translator CI workflow updates the image tag after it builds and pushes a
new image. Set a `CONFIG_REPO_TOKEN` secret in the `translator` repository with
permission to write to this repository so that step can commit the new tag.

## Pulling a private image

If the GHCR package is private, create a token with `read:packages` access and
make the secret in the `translator` namespace:

```sh
kubectl -n translator create secret docker-registry ghcr-pull \
  --docker-server=ghcr.io \
  --docker-username=YOUR_GITHUB_USER \
  --docker-password=YOUR_GITHUB_TOKEN
```

Do not put the token in this repository. Then set this in `env/dev/values.yaml`:

```yaml
image:
  pullSecrets:
    - name: ghcr-pull
```

Alternatively, make the GHCR package public in GitHub package settings and leave
`pullSecrets` empty.
