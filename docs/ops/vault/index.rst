##########################
vault app deployment guide
##########################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |vault-status|
   * - Edit on GitHub
     - `/deployments/vault <https://github.com/lsst-sqre/roundtable/tree/master/deployments/vault>`__
   * - Type
     - Helm_
   * - Parent app
     - :doc:`security <../security/index>`

.. rubric:: Overview

The ``vault`` application is an installation of Vault_ used as the primary LSST secrets management tool.
This installation currently provides secrets for all LSST environments and clusters.
Eventually, we may install a separate production Vault at the LDF and use the Roundtable Vault only for non-LDF services.

See `DMTN-112 <https://dmtn-112.lsst.io>`__ for the LSST Vault design.

This application deploys both the Vault server and the Vault Agent Injector.

Vault is configured in HA mode with a public API endpoint accessible at vault.lsst.codes.
TLS termination is done at the nginx ingress layer using a Let's Encrypt server certificate.
The UI is not available outside of the pod, but can be accessed via port forwarding.

To manipulate the secrets stored in this Vault instance, use lsstvaultutils_.
