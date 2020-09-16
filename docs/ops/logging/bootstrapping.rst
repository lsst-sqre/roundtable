#####################################
Bootstrapping the logging application
#####################################

Most of the configuration is done by the Helm chart referenced by the ``logging`` application, but one external Vault secret is required.

Authentication to Elasticsearch is done via either HTTP basic authentication with a password or via :doc:`Gafaelfawr <../gafaelfawr/index>` (which uses GitHub to authenticate a user).
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
