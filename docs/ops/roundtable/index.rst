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
It follows the `app of apps <https://argoproj.github.io/argo-cd/operator-manual/cluster-bootstrapping/#app-of-apps-pattern>`_ pattern.
It deploys:

- Namespaces for core infrastructure apps.
- The ``faster`` (SSD) StorageClass.
- The core infrastructure apps:

  - :doc:`argo-cd <../argo-cd/index>` for continuous delivery of Roundtable apps with Argo CD.
  - :doc:`nginx-ingress <../nginx-ingress/index>` for shared ingress.
  - :doc:`cert-manager <../cert-manager/index>` for Let's Encrypt-provided TLS certificates.
  - :doc:`prometheus <../prometheus/index>` for the Kubernetes-native monitoring stack (Prometheus Operator, Prometheus, and Grafana).

.. rubric:: Bootstrapping the Application

Since ``roundtable`` is a parent app, its ``Application`` resource was not created automatically and is not managed by GitOps.

We manually created the ``roundtable`` ``Application`` from the :command:`argocd` CLI:

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
