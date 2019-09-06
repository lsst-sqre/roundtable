# cert-manager-crd

These are the CRDs for the [cert-manager](../cert-manager) application.
They are installed separately from cert-manager itself, and should be installed first.

This CRD is installed via the [cert-manager-crd application](../roundtable/resources/cert-manager-crd.yaml) defined in Roundtable.
The [crds-release-0.10.yaml](crds-release-0.10.yaml) file is downloaded from https://raw.githubusercontent.com/jetstack/cert-manager/release-0.10/deploy/manifests/00-crds.yaml.
If cert-manager is upgraded, be sure to manually update the CRDs in this Kustomization.

For background, see the [cert-manager Quick-start for Nginx Ingress](https://docs.cert-manager.io/en/latest/tutorials/acme/quick-start/index.html#step-5-deploy-cert-manager).
