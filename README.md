# condition-bitbucket-pipelines

[![Latest Release](https://img.shields.io/github/v/release/SemRels/condition-bitbucket-pipelines?label=version&color=blue)](https://github.com/SemRels/condition-bitbucket-pipelines/releases/latest)

Allows releases only when semrel is running inside Bitbucket Pipelines.

This plugin is distributed as the standalone Go binary `semrel-plugin-condition-bitbucket-pipelines`. Semrel executes the binary as a subprocess, provides plugin configuration through `SEMREL_PLUGIN_*` environment variables, provides release context through `SEMREL_*` environment variables, reads standard output, and treats exit code `0` as success and any non-zero exit code as failure. Install the binary in `~/.semrel/plugins/` or anywhere on your `$PATH`.

## Installation

### Binary

```bash
go install github.com/SemRels/condition-bitbucket-pipelines/cmd/plugin@latest
```

### Docker

Pre-built, multi-platform images (linux/amd64, linux/arm64) are published to the GitHub Container Registry on every release:

```bash
docker pull ghcr.io/semrels/condition-bitbucket-pipelines:latest
```

Images are signed with [cosign](https://github.com/sigstore/cosign) and include a full SBOM attestation. Verify the signature:

```bash
cosign verify ghcr.io/semrels/condition-bitbucket-pipelines:latest \
  --certificate-identity-regexp 'https://github.com/SemRels/condition-bitbucket-pipelines/.github/workflows/release.yml.*' \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com
```

## Configuration

```yaml
plugins:
  - name: condition-bitbucket-pipelines
    path: ~/.semrel/plugins/semrel-plugin-condition-bitbucket-pipelines
    env:
      {}
```

## `SEMREL_PLUGIN_*` variables

| Name | Required | Description | Default |
| --- | --- | --- | --- |
| `SEMREL_PLUGIN_BRANCH` | no | Optional branch name override. When set, require `BITBUCKET_BRANCH` to match this value. | unset |

## `SEMREL_*` release context used

This plugin does not consume any `SEMREL_*` release context variables directly.

## Example behavior

The plugin checks the CI environment and succeeds when `BITBUCKET_PIPELINE_UUID` is set. If `SEMREL_PLUGIN_BRANCH` is set, it also requires `BITBUCKET_BRANCH` to match. Outside Bitbucket Pipelines it exits non-zero to stop the release.

## License

Apache-2.0
