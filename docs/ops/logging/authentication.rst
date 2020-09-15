############################
Elasticsearch authentication
############################

Elasticsearch has a blizzard of authentication options, and Open Distro for Elasticsearch replaces those with yet different ones.
They're also in general poorly documented.
This is an attempt to explain the basics of the authentication system and the choices we made when configuring it.

.. rubric:: Configuration

Open Distro for Elasticsearch does authentication through a custom plugin called ``opendistro_security``.
Kibana and Elasticsearch have separate authentication paths but use the same plugin.
Kibana also has to authenticate to Elasticsearch as itself (not as an authenticated front-end user) to get status information and to store internal data.

The ``opendistro_security`` plugin is primarily configured via a ``config.yml`` file that can be provided via a Kubernetes secret.
It is the ``logging-security-config`` secret in the Roundtable configuration.
This configuration file can stack multiple authentication methods using an ``order`` key to specify their priority.

For Kibana to work and for fluentd to talk to Elasticsearch, either certificate authentication or HTTP basic authentication has to be configured.
We're using HTTP basic authentication.
This means there must be a ``type: basic`` stanza in the ``opendistro_security`` config and defined internal accounts in the ``internal_users.yml`` file.
One can use only basic authentication and also define a shared ``admin`` account, but that requires looking up the password in 1Password and sharing a single password.
It's therefore better to add a different authentication method for users.

We use proxy authentication as the primary authentication mechanism to Kibana.
Kibana then makes requests on behalf of the user to Elasticsearch, so Elasticsearch has to also accept proxy authentication and trust the incoming headers.
The authentication is done by Gafaelfawr, which runs as an auth request handler in the NGINX ingress and sets appropriate headers.
Kibana and Elasticsearch are configured to take the authenticated user from the ``X-Auth-Request-User`` header.

The recommended way to configure proxy authentication is to specify the IP address of the ingress that will set the headers and the IP address of the Kibana server (for proxy authentication to Elasticsearch) as the trusted internal proxies.
However, this requires hard-coding internal IP addresses, which we don't want to do.
The proxy authentication module is therefore configured to accept any internal IP address, and a Kubernetes ``NetworkPolicy`` is used to prevent other pods from talking to Kibana or Elasticsearch and providing the proxy headers to impersonate a legitimate user.

.. rubric:: Authorization

Authorization in Elasticsearch is done via roles.
This is complex in Open Distro for Elasticsearch because there are two different concepts of roles in play:

- Roles asserted via authentication
- Elasticsearch roles (sometimes called "backend roles")

The first are mapped to the second via the ``role_mapping.yml`` file.

Roles can be assigned directly to accounts in the ``internal_users.yml`` file shown in the bootstrapping instructions.
Otherwise, they need to be asserted by the authentication configuration.

There are some default mappings and roles, such as ``admin`` and ``logstash``.
The documentation claims that there is a default role for the Kibana user as well.
In different pieces of documentation, it is named three different things (``kibanauser``, ``kibana_user``, and ``kibana_system``).
None of these work or appear to do anything.
The current configuration just assigns a role of ``admin`` to the Kibana user, which is excessive but works.

For the proxy authentication mechanism that we're using, the roles of a user are asserted by the ``X-Proxy-Roles`` header, which is a comma-separated list of roles.
These are the authentication roles, which are then mapped to backend roles via ``role_mapping.yml``.
We map all users who are allowed access to Kibana to the ``admin`` role, and limit access via Gafaelfawr to people in the ``square`` team of the ``lsst-sqre`` GitHub organization.

.. rubric:: Multi-tenancy

Kibana has some concept of multi-tenancy, which appears to be a way to create private and shared index spaces.
This didn't seem like something that was necessary for our use case and it was confusing the authentication configuration, so we have it turned off.

.. rubric:: Making configuration changes

The ``opendistro_security`` configuration is stored in a special Elasticsearch index.
That index is created or updated via configuration files stored in the Elasticsearch master pod.
Once the index exists in persistent storage, the configuration files are ignored.
An Open Distro script must be run to update the configuration.

Therefore, after making any change to the security configuration, take the following steps to update the live configuration:

#. Find an Elasticsearch master pod.
   They will be named something like ``logging-opendistro-es-master-0`` in the ``logging`` namespace.

#. Run the update command:

   .. code-block:: console

      $ kubectl -n logging exec -ti logging-opendistro-es-master-0 -- \
        /usr/share/elasticsearch/plugins/opendistro_security/tools/securityadmin.sh \
        -cacert /usr/share/elasticsearch/config/root-ca.pem \
        -cert /usr/share/elasticsearch/config/kirk.pem \
        -key /usr/share/elasticsearch/config/kirk-key.pem \
        -cd /usr/share/elasticsearch/plugins/opendistro_security/securityconfig/

   This authenticates with the internal certificates to the local host and updates the security configuration based on the YAML files present on disk.
   (Those YAML files are created by the Helm chart, including the overrides that come from secrets added by the ``logging`` application.)
