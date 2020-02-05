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

.. rubric:: External Configuration

This deployment is currently only tested on GCP.
Vault requires some external resources and a Kubernetes secret to be created and configured before deployment.

#. Create a GCP service account named ``vault-server`` and download credentials for that service account.
#. Store those credentials as ``credentials.json`` in a Kubernetes secret in the ``vault`` namespace named ``vault-kms-creds``.
   You may have to manually create the ``vault`` namespace first.
#. Create a GCP KMS keyring in the ``global`` region named ``vault`` and a symmetric key named ``vault-seal``.
#. Grant the ``vault-server`` service account the ``Cloud KMS CryptoKey Encrypter/Decrypter`` role in IAM.
   This should be constrained to only the ``vault`` keyring, but I was unable to work out how to do that correctly in GCP.
#. Create a GCS bucket named ``storage.vault.lsst.codes``.
   (This will require domain valiation in Google Webmaster Tools.)
   Configure this bucket in the permissions tab to use uniform bucket-level access (there is no meaningful reason to use per-object access for this application and this configuration is simpler).
   Grant the ``vault-server`` service account the ``Storage Object Admin`` role on this bucket in the bucket permissions tab.
#. Create DNS entries for ``vault-1.lsst.codes`` and ``vault-2.lsst.codes`` pointing to the external IP of the nginx ingress for the Kubernetes cluster in which this application is deployed (or update the list of hosts in ``values.yaml``).

When deploying Vault elsewhere, at least the storage bucket name will have to change because bucket names are globally unique in GCP.
Note that the GCP project and region are also encoded in ``values.yaml`` if deploying elsewhere.
