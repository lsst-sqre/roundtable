##############
OpenID Connect
##############

Open Distro for Elasticsearch also supports OpenID Connect authentication, and we have tested it with Gafaelfawr.
As documented in :doc:`Elasticsearch authentication <authentication>`, we are using proxy authentication instead.
The primary reason is that this makes role management easier given that we want to assign the same role to every user.
We add a request header at the NGINX ingress, and don't need to manage per-user roles.

However, in case OpenID Connect becomes appealing for other reasons, here is a summary of how to reconfigure Open Distro for Elasticsearch to use it instead.

Elasticsearch configuration
===========================

The Elasticsearch security configuration is defined by the ``logging-security-config`` secret.
To use OpenID Connect instead, replace the ``proxy_auth_domain`` stanza in that configuration with the following:

.. code-block:: yaml

   openid_auth_domain:
     http_enabled: true
     transport_enabled: true
     order: 1
     http_authenticator:
       type: openid
       challenge: false
       config:
         enable_ssl: true
         subject_key: sub
         openid_connect_url: https://roundtable.lsst.codes/.well-known/openid-configuration

This does not provide any source of role information, which means that users will not have access to any indices.
To add roles, either manually map users to roles using the ``roles_mapping.yml`` file (see the `Open Distro for Elasticsearch security configuration <https://opendistro.github.io/for-elasticsearch-docs/docs/security/configuration/yaml/>`__), or add support to Gafaelfawr for populating a token attribute with a comma-separated list of Open Distro for Elasticsearch roles.

Kibana configuration
====================

First, ensure that the ``kibana`` OpenID Connect client has an associated secret set in the ``oidc-server-secrets`` key of the Gafaelfawr secrets.
See the `Gafaelfawr documentation <https://gafaelfawr.lsst.io/applications.html#using-openid-connect>`__ for more information.

Then, add a matching ``openid-secret`` key to the ``logging`` Vault secret for Roundtable.

Finally, add the following to ``values.yaml`` for the ``logging`` application to configure Kibana to use OpenID Connect:

.. code-block:: yaml

   kibana:
     config:
       opendistro_security.auth.type: "openid"
       opendistro_security.openid.client_id: "kibana"
       opendistro_security.openid.client_secret: "${OPENID_SECRET}"
       opendistro_security.openid.scope: "openid"
       opendistro_security.openid.connect_url: "https://roundtable.lsst.codes/.well-known/openid-configuration"
       opendistro_security.openid.logout_url: "https://roundtable.lsst.codes/logout"
       opendistro_security.openid.base_redirect_url: "https://roundtable.lsst.codes/"
     extraEnvs:
       - name: OPENID_SECRET
         valueFrom:
            secretKeyRef:
              name: "logging-accounts"
              key: openid-secret

Also remove the old setting of ``opendistro_security.auth.type`` to ``proxy``, and remove the NGINX ingress annotations on the Kibana ingress.

After syncing all the relevant applications and applying the Open Distro for Elasticsearch security configuration changes, Kibana should start using OpenID Connect authentication via Gafaelfawr.
