######################################
1password-connect app deployment guide
######################################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |1password-connect-status|
   * - Edit on GitHub
     - `/deployments/1password-connect <https://github.com/lsst-sqre/roundtable/tree/master/deployments/1password-connect>`__
   * - Type
     - Helm_
   * - Parent app
     - :doc:`roundtable <../roundtable/index>`

.. rubric:: Overview

The ``1password-connect`` application is an installation of `1Password Connect Server <https://developer.1password.com/docs/connect>`__.
This server provides a REST API to a 1Password vault, which is used by `Phalanx <https://phalanx.lsst.io/>`__ for secrets management for the deployments run by SQuaRE.

.. rubric:: Bootstrapping the Application

1Password Connect requires a secret to talk to 1Password.
This is managed via a ``VaultSecret`` resource installed by the Roundtable deployment.
The ultimate source of this secret is the 1Password item ``SQuaRE Integration Credentials File`` in the SQuaRE vault.

Clients of the 1Password Connect server will need to present a token for authentication.
The initial bootstrap token is in the 1Password item ``SQuaRE Integration Access Token: Argo`` in the SQuaRE vault.
