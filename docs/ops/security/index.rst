#############################
security app deployment guide
#############################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |security-status|
   * - Edit on GitHub
     - `/deployments/security <https://github.com/lsst-sqre/roundtable/tree/master/deployments/security>`__
   * - Type
     - Kustomize_
   * - Parent app
     - None

.. rubric:: Overview

The ``security`` app is responsible for deploying security services for Roundtable, most notably Vault and all of its dependencies.
It follows the `app of apps <https://argoproj.github.io/argo-cd/operator-manual/cluster-bootstrapping/#app-of-apps-pattern>`__ pattern.
It deploys:

- :doc:`nginx-ingress <../nginx-ingress/index>` for shared ingress.
- :doc:`cert-manager <../cert-manager/index>` for Let's-Encrypt-provided TLS certificates.
- :doc:`vault <../vault/index>` for the Vault secret service.

.. rubric:: Bootstrapping the Application

Since ``security`` is a parent app, its ``Application`` resource was not created automatically and is not managed by GitOps.
We manually created the ``security`` application from the :command:`argocd` CLI:

.. code-block:: bash

   argocd app create security \
     --dest-namespace argocd \
     --dest-server https://kubernetes.default.svc \
     --repo https://github.com/lsst-sqre/roundtable.git \
     --path deployments/security \
     --sync-policy automated \
     --project default 

The ``security`` Application's properties (such as the sync policy) should be managed entirely through the Argo CD dashboard or CLI.
Of course, the ``security`` manifest in Git can be modified to manage the applications that are *created by* the ``security`` parent application.
