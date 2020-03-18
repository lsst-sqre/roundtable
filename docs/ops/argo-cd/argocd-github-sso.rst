##################################
Configuring GitHub SSO for Argo CD
##################################

The Argo CD instance (https://cd.roundtable.lsst.codes) is accessible with GitHub OAuth single sign on (SSO).
This page is an overview of how the GitHub SSO is configured and is intended for Roundtable operators.

For background documentation, see `SSO Configuration`_ from the Argo CD documentation.

GitHub OAuth app and configuration
==================================

The GitHub OAuth app is called `LSST Roundtable Argo CD <https://github.com/organizations/lsst-sqre/settings/applications/1132574>`_ and is owned by the `lsst-sqre GitHub organization`_.

GitHub SSO is primarily configured through the `argocd-cm.yaml patch`_.
See the ``url`` and ``dex.config`` fields.

The client secret is configured in the ``dex.github.clientSecret`` key of the ``argocd`` namespace.

Organization-based access
=========================

Access can be limited to members of organizations listed in the GitHub SSO of the `argocd-cm.yaml patch`_.
Currently only members of the `lsst-sqre GitHub organization`_ can log in.

Dex, the OIDC component used by Argo CD, also supports limited access by GitHub team.
See the `Authenticating with GitHub page <https://github.com/dexidp/dex/blob/master/Documentation/connectors/github.md>`__ in the Dex documentation.

.. _`SSO Configuration`: https://argoproj.github.io/argo-cd/operator-manual/user-management/#sso
.. _`argocd-cm.yaml patch`: https://github.com/lsst-sqre/roundtable/blob/master/deployments/argo-cd/patches/argocd-cm.yaml
