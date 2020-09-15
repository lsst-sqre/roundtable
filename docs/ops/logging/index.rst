############################
logging app deployment guide
############################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |logging-status|
   * - Edit on GitHub
     - `/deployments/logging <https://github.com/lsst-sqre/roundtable/tree/master/deployments/logging>`__
   * - Type
     - Helm_
   * - Parent app
     - :doc:`roundtable <../roundtable/index>`

.. rubric:: Overview

The logging app deploys Open Distro for Elasticsearch, a self-contained system for gathering, storing, and visualizing logs from the Roundtable Kubernetes cluster.
Kubernetes logs, as well as application logs emitted on stdout and stderr, are automatically gathered by the logging app.

Be aware that we are using Open Distro for Elasticsearch, not Elasticsearch directly.
Open Distro bundles a set of plugins that are quite different from the facilities available from the commercial Elasticsearch product.
Much of the on-line information about Elasticsearch is for the commercial product or for other plugins.
When making changes or debugging problems, be sure to start with the Open Distro documentation.

The logging app is built primarily around the `opendistro-es Helm chart <https://github.com/opendistro-for-elasticsearch/community/tree/master/open-distro-elasticsearch-kubernetes/helm>`__.
This chart is community-maintained, not kept up-to-date with new releases, and not uploaded to any Helm chart repository.
We maintain a light fork in the `charts repository <https://github.com/lsst-sqre/charts/>`__ that is updated for new Open Distro for Elasticsearch releases.
This chart provides Elasticsearch_ and Kibana_ services.

The logging app also includes the `fluentd-elasticsearch chart <https://github.com/kiwigrid/helm-charts>`__.
Fluentd gathers logs from each cluster node and forwards them to Elasticsearch.

.. rubric:: Upgrading

Since the ``opendistro-es`` chart is not uploaded to any Helm repository, we have to periodically check if there are upstream changes and import a new version into the charts repository.
We also have to check for new releases of Open Distro for Elasticsearch and bump the version number in our copy of the chart.
From there, WhiteSource Renovate will notice the new published version and create a PR for the Roundtable repository.

``fluentd-elasticsearch`` is a properly-maintained Helm chart that is monitored by WhiteSource Renovate.
It can be upgraded by merging the normal Renovate PRs.

A ``fluentd-elasticsearch`` upgrade is normally smooth provided that the Elasticsearch cluster is up.
It deploys agents on every node in the cluster and restarts them one at a time, so it can take some time to complete.

An ``opendistro-es`` upgrade can cause more problems.
Sometimes new versions of the Helm chart will want to make changes to deployments that cannot be patched into the existing deployment.
In this case, you will need to go to the Argo CD console, delete the deployments, and let Argo CD recreate them.

There are four deployments: ``logging-opendistro-es-master``, ``logging-opendistro-es-data``, ``logging-opendistro-es-client``, and ``logging-opendistro-es-kibana``.
The restart is timing-sensitive, since the cluster forms around the nodes seen in a short window of time.
Therefore, if you need to remake the deployments, recreate (via sync) the ``logging-opendistro-es-master`` and ``logging-opendistro-es-data`` services together or in close succession.
Once those services are up and stable, recreate ``logging-opendistro-es-client``, and then finally ``logging-opendistro-es-kibana``.
Be aware that it will take some time for Kibana to start, since it recompiles its JavaScript on each restart.

The health checks for these deployments are sadly worthless.
They will show green even if the service is not healthy.
Checking the logs is more reliable.
The logs for ``logging-opendistro-es-kibana`` in particular will include messges about red or yellow status if the cluster is not working properly.
You can then track down the problem by looking at the logs of the other pods.
``logging-opendistro-es-master`` is normally the most informative.

If you have to recreate the persistent storage used by Elasticsearch, you will need to recreate an index.
To do this, click on the gear icon in the Kibana sidebar and then select **Index Patterns**.
If there are no patterns shown in the resulting screen, select **Create index pattern**.
Use ``*`` as the index pattern and then, on the subsequent screen, use ``@timestamp`` as the timestamp field.
Then, go to **Discover** in the sidebar (the top icon below the thin grey line) and do a search and you should see some data.

.. rubric:: Bootstrapping the application

Most of the configuration is done by the Helm chart referenced by the ``logging`` application, but one external Vault secret is required.

Authentication to Elasticsearch is done via either HTTP basic authentication with a password or via :doc:`../gafaelfawr/index` Gafaelfawr (which uses GitHub to authenticate a user).
No configuration is required for GitHub authentication, but the internal accounts that will be using HTTP basic authentication must be set up.
This is done via a secret stored in Vault at ``secret/k8s_operator/roundtable/logging``.
It is referenced via a ``VaultSecret`` resource in the ``logging`` application.

The secret should contain the following keys:

cookie
    A random string used for cookie encryption.

username
    The username for the internal Kibana user to authenticate to Elasticsearch.
    We use ``kibana`` for this purpose.

password
    The password for the ``kibana`` account.

logstash-password
    The password for the ``logstash`` account, used by fluentd to write logs into Elasticsearch.

Use ``os.urandom(32).hex()`` to generate values for the ``cookie`` and ``*password`` keys.

Finally, there should be an ``internal_users.yml`` key whose value is the ``internal_users.yml`` configuration file for the opendistro_security plugin.
This value should look like this:

.. code-block:: yaml

   _meta:
     config_version: 2
     type: internalusers
   admin:
     backend_roles:
       - admin
     description: Admin user
     hash: <hash>
     reserved: true
   kibana:
     backend_roles:
       - admin
     description: Kibana user
     hash: <hash>
     reserved: true
   logstash:
     backend_roles:
       - logstash
     description: Logstash writing user
     hash: <hash>
     reserved: true

where the ``<hash>`` values should be replaced with bcrypt hashes of the corresponding passwords.
The ``kibana`` hash should be the hashed form of the ``password`` key, and the ``logstash`` hash should be the hashed form of the ``logstash-password`` key.
The password corresponding to the ``admin`` hash is kept only in 1Password.

To generate the hashes, you can use the Python bcrypt module:

.. code-block:: python

   import bcrypt
   bcrypt.hashpw(b"password", bcrypt.gensalt(rounds=12, prefix=b"2a"))

replacing ``password`` with the password to hash.

See the `Open Distro for Elasticsearch documentation <https://opendistro.github.io/for-elasticsearch-docs/docs/security/configuration/yaml/>`__ for more information.

.. rubric:: Guides

.. toctree::

   authentication
   troubleshooting

.. seealso::

   * :doc:`/app-guide/viewing-logs` explains how to log into the Kibana UI.
   * `Open Distro for Elasticsearch <https://opendistro.github.io/for-elasticsearch-docs/>`__
