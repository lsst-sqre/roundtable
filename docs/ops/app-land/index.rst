#############################
app-land app deployment guide
#############################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |app-land-status|
   * - Edit on GitHub
     - `/deployments/app-land <https://github.com/lsst-sqre/roundtable/tree/master/deployments/app-land>`__
   * - Type
     - Kustomize_
   * - Parent app
     - None

.. rubric:: Overview

The ``app-land`` app is responsible for deploying most, if not all, of Roundtable's tenant applications.
It follows the `app of apps <https://argoproj.github.io/argo-cd/operator-manual/cluster-bootstrapping/#app-of-apps-pattern>`_ pattern, equivalent to how the :doc:`roundtable <../roundtable/index>` application deploys core infrastructure.

.. rubric:: Bootstrapping the Application

Since ``app-land`` is a parent app, its ``Application`` resource was not created automatically and is not managed by GitOps.

We manually created the ``app-land`` ``Application`` from the :command:`argocd` CLI:

.. code-block:: bash

   argocd app create app-land \
     --dest-namespace argocd \
     --dest-server https://kubernetes.default.svc \
     --repo https://github.com/lsst-sqre/roundtable.git \
     --path deployments/app-land \
     --sync-policy automated \
     --project default 

Consequently, the ``app-land`` Application's properties (such as the sync policy) should be managed entirely through the Argo CD dashboard or CLI.
Of course, the ``app-land`` manifest in Git can be modified to manage the applications that are *created by* the ``app-land`` parent application.
