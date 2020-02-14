###########################################
vault-secrets-operator app deployment guide
###########################################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |vault-secrets-operator-status|
   * - Edit on GitHub
     - `/deployments/vault-secrets-operator <https://github.com/lsst-sqre/roundtable/tree/master/deployments/vault-secrets-operator>`__
   * - Type
     - Helm_
   * - Parent app
     - :doc:`roundtable <../roundtable/index>`

.. rubric:: Overview

The ``vault-secrets-operator`` application is an installation of `Vault Secrets Operator`_ to retrieve necessary secrets from Vault and materialize them as Kubernetes secrets for the use of other applications.
It processes ``VaultSecret`` resources defined in the `Roundtable repository <https://github.com/lsst-sqre/roundtable>`__ and creates corresponding Kubernetes ``Secret`` resources.

See `DMTN-112 <https://dmtn-112.lsst.io>`__ for the LSST Vault design.

.. rubric:: Bootstrapping the Application

The Vault Secrets Operator requires Kubernetes secrets to talk to Vault.
This bootstrapping is done as part of bootstrapping the :doc:`roundtable <../roundtable/index>` app.
See its documentation for more information.
