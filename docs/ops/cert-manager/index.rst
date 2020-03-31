#################################
cert-manager app deployment guide
#################################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |cert-manager-status|
   * - Edit on GitHub
     - `/deployments/cert-manager <https://github.com/lsst-sqre/roundtable/tree/master/deployments/cert-manager>`__
   * - Type
     - Helm_
   * - Parent app
     - :doc:`security <../security/index>`

.. rubric:: Overview

This Argo CD Application deploys and configures `cert-manager <https://cert-manager.io>`__ from its `Helm chart repository <https://hub.helm.sh/charts/jetstack/cert-manager>`__.

.. rubric:: CRDs

cert-manager upstream does not include their CRDs in their Helm chart.
They must be downloaded and applied separately.
Upstream recommends using ``kubectl`` directly.
We want everything to be managed by Argo CD, so we use a separate Argo CD project (``cert-manager-crd``) to install the CRDs, and tell Argo CD to sync it before the cert-manager app.
Rather than applying the CRDs directly with ``kubectl``, we download the CRD file, rename it to ``certmanager-crd-<version>.yaml``, save it in ``deployments/cert-manager-crd``, and update ``kustomziation.yaml`` for that deployment.

See the `cert-manager Helm chart documentation <https://hub.helm.sh/charts/jetstack/cert-manager>`__ for information on how to download new CRDs when upgrading.
The URL for the CRDs is given in the ``kubectl`` command line.
