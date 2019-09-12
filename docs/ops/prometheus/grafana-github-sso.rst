#########################################################
Configuring GitHub SSO for the Grafana monitoring service
#########################################################

The Grafana instance (https://monitoring.roundtable.lsst.codes) is accessible through a GitHub OAuth single sign on (SSO).
This page is an overview of how the GitHub SSO is configured and is intended for Roundtable operators.

GitHub OAuth app and grafana.ini configuration
==============================================

The GitHub OAuth app is called `LSST Roundtable Monitor <https://github.com/organizations/lsst-sqre/settings/applications/1133398>`_ and is owned by the `lsst-sqre GitHub organization`_ organization.

GitHub SSO is primarily configured through the `values.yaml file`_.
The Grafana Helm chart translates YAML into the standard `grafana.ini configuration file <https://grafana.com/docs/installation/configuration/>`_.
See the Grafana documentation on `GitHub OAuth2 Authentication <https://grafana.com/docs/auth/github/>`_, specifically.

The only part of the configuration not included in the `values.yaml file`_ is the GitHub OAuth client secret.
This value is obtained from an environment variable override mounted from the ``grafana-env`` secret resource (see the ``envFromSecret`` field in the `values.yaml file`_).
The secret configuration:

.. code-block:: yaml

   apiVersion: v1
   kind: Secret
   metadata:
     name: grafana-env
   type: Opaque
   stringData:
     GF_AUTH_GITHUB_CLIENT_SECRET: "<client-secret>"

Deploy this secret into the ``prometheus`` namespace.

.. _`values.yaml file`: https://github.com/lsst-sqre/roundtable/blob/master/deployments/prometheus/values.yaml

Organization and team-based access
==================================

Only GitHub users belonging to certain GitHub organizations, and teams within organizations, can authenticate.

.. important::

   GitHub organization and team membership does not automatically translate into specific roles within Grafana.
   See :ref:`grafana-assigning-user-orgs-roles`, below.

Initially, only members of the `lsst-sqre GitHub organization`_ and its `roundtable-ops team`_ can authenticate.
This will be expanded later to allow application developers who are not part of the ops team to monitor their applications.

To identify the team ID for the ``team_ids`` configuration field, use this HTTP call (using httpie_):

.. code-block:: bash

   http get https://api.github.com/orgs/<org>/teams Authorization:"token <token>"

Replace ``<token>`` with a GitHub personal access token.
Replace ``<org>`` with the slug of a GitHub organization (for example ``lsst-sqre``).

.. _grafana-assigning-user-orgs-roles:

Assigning users to Organizations and Roles
==========================================

By default, authenticated users get "Viewer" privileges.
At the moment, members need to be manually by an admin user to have admin or editor privileges.

The credentials for the default admin user are in SQuaRE's 1Password account, see ``Roundtable Grafana``.
