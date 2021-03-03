###############################
roundtable app deployment guide
###############################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |roundtable-status|
   * - Edit on GitHub
     - `/deployments/roundtable <https://github.com/lsst-sqre/roundtable/tree/master/deployments/roundtable>`__
   * - Type
     - Kustomize_
   * - Parent app
     - None

.. rubric:: Overview

The ``roundtable`` app is responsible for deploying the core infrastructure of Roundtable.
It follows the `app of apps <https://argoproj.github.io/argo-cd/operator-manual/cluster-bootstrapping/#app-of-apps-pattern>`__ pattern.
It deploys:

- Namespaces for core infrastructure apps.
- The ``faster`` (SSD) StorageClass.
- The core infrastructure apps:

  - :doc:`argo-cd <../argo-cd/index>` for continuous delivery of Roundtable apps with Argo CD.
  - :doc:`kube-prometheus-stack <../kube-prometheus-stack/index>` for the Kubernetes-native monitoring stack (Prometheus Operator, Prometheus, and Grafana).
  - :doc:`vault-secrets-operator <../vault-secrets-operator/index>` to retrieve secrets from Vault and store them as Kubernetes secrets.

This app depends on the :doc:`security <../security/index>` app, which provides secret management facilities and ingress.

.. rubric:: Bootstrapping the application

Since ``roundtable`` is a parent app, its ``Application`` resource was not created automatically and is not managed by GitOps.

Before bootstrapping the ``roundtable`` app, the ``security`` app needs to be bootstrapped so that Vault is running.
See :doc:`its documentation <../security/index>` for more information.
Then, a Vault access token for the Vault Secrets Operator must be created in Vault and stored as the ``vault-secrets-operator`` Kubernetes secret.
Here is the template for that secret:

.. code-block:: yaml

   apiVersion: v1
   kind: Secret
   metadata:
     name: vault-secrets-operator
     namespace: vault-secrets-operator
   type: Opaque
   stringData:
     VAULT_TOKEN: <token>
     VAULT_TOKEN_LEASE_DURATION: 86400

Replace ``<token>`` with the ``read`` Vault token for the path ``secret/k8s_operator/roundtable`` in Vault (see `DMTN-112 <https://dmtn-112.lsst.io>`__ for more information):

Finally, create the ``roundtable`` ``Application`` from the :command:`argocd` CLI:

.. code-block:: bash

   argocd app create roundtable \
     --dest-namespace argocd \
     --dest-server https://kubernetes.default.svc \
     --repo https://github.com/lsst-sqre/roundtable.git \
     --path deployments/roundtable \
     --sync-policy automated \
     --project default 

Consequently, the ``roundtable`` Application's properties (such as the sync policy) should be managed entirely through the Argo CD dashboard or CLI.
Of course, the ``roundtable`` manifest in Git can be modified to manage the applications that are *created by* the ``roundtable`` parent application.
