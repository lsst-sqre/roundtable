# Vault

- Status: ![](https://cd.roundtable.lsst.codes/api/badge?name=vault)
- Dashboard: https://cd.roundtable.lsst.codes/applications/vault
- Docs: https://roundtable.lsst.io/ops/vault

# Vendored Charts

The `charts` subdirectory contains the official [Vault Helm chart](https://github.com/hashicorp/vault-helm).
The upstream maintainers do not provide a Helm repository, so instead we vendor the version we're using.

## Updating

To update the version of the Helm chart:

#. Go to the [Vault Helm chart releases page](https://github.com/hashicorp/vault-helm/releases)
#. Find the new release that you want to use (generally the latest).
#. Select `Source code (tar.gz)` to download.
#. Unpack the `.tar.gz` file into a subdirectory of the `charts` directory
#. From the `charts` directory, run `helm package vault-helm-*/`.
#. Add the resulting `.tgz` file to Git.
#. Update the version in `../requirements.yaml` to point to the new file.
#. Optionally delete the old `.tgz` file.
