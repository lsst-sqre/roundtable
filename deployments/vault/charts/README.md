# Overview

This directory contains the official [Vault Helm chart](https://github.com/hashicorp/vault-helm).
The upstream maintainers do not provide a Helm repository, so instead we vendor the version we're using.

# Updating

To update the version of the Helm chart:

#. Go to the [Vault Helm chart releases page](https://github.com/hashicorp/vault-helm/releases)
#. Find the new release that you want to use (generally the latest).
#. Select `Source code (tar.gz)` to download.
#. Copy the resulting `.tar.gz` file into this directory.
#. Update the version in `../requirements.yaml` to point to the new file.
#. Optionally delete the old `.tar.gz` file.
