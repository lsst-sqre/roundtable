##########################
Argo CD RBAC configuration
##########################

This page describes the role-based access control (RBAC) configuration for the Roundtable Argo CD instance and is intended for Roundtable operators.
For background, see `Argo CD's RBAC documentation <https://argoproj.github.io/argo-cd/operator-manual/rbac/>`_.

The RBAC is configured through the `argocd-rbac-cm.yaml`_ patch.

Note how roles can be assigned based on GitHub organization and team membership.

.. _`argocd-rbac-cm.yaml`: https://github.com/lsst-sqre/roundtable/blob/master/deployments/argo-cd/patches/argocd-rbac-cm.yaml
