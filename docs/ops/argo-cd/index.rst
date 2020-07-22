############################
argo-cd app deployment guide
############################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |argo-cd-status|
   * - Edit on GitHub
     - `/deployments/argo-cd <https://github.com/lsst-sqre/roundtable/tree/master/deployments/argo-cd>`__
   * - Type
     - Kustomize_
   * - Parent app
     - :doc:`roundtable <../roundtable/index>`
   * - Dashboard
     - https://cd.roundtable.lsst.codes

.. rubric:: Overview

This Argo CD Application deploys `Argo CD`_ itself.
The web dashboard for Roundtable's Argo CD is https://cd.roundtable.lsst.codes.

.. rubric:: Guides

.. toctree::

   repositories
   helm-repositories
   argocd-github-sso
   argocd-rbac
   github-webhook
   port-forward
   grafana-dashboard
