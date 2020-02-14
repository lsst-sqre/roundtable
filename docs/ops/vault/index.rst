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

.. rubric:: Changing Vault configuration

When making configuration changes, be aware that Argo CD will not detect a change to the configuration in ``values.yaml`` and automatically relaunch the ``vault-*`` pods.
You will need to delete the pods and let Kubernetes recreate them in order to pick up changes to the configuration.

If you change the number of HA replicas, Argo CD will fail to fully synchronize the configuration because the ``poddisruptionbudget`` resource cannot be updated.
Argo CD will show an error saying that a resource failed to synchronize.
To fix this problem, delete the ``poddisruptionbudget`` resource in Argo CD and then resynchronize the ``vault`` app, and then Argo CD will be happy.

.. rubric:: Seal configuration

A Vault database is "sealed" by encrypting the stored data with an encryption key, which in turn is encrypted with a master key.
In a default Vault installation, the master key is then split with Shamir secret sharing and a quorum of key fragments is required to manually unseal the Vault each time the Vault server is restarted.
This is a poor design for high availability and for Kubernetes management, so we instead use an "auto-unseal" configuration.

Auto-unsealing works by using a Google KMS key as the master key.
That KMS key is stored internally by Google and cannot be retrieved or downloaded, but an application can request that data be encrypted or decrypted with that key.
Vault has KMS decrypt the encryption key on startup and then uses that to unseal the Vault database.
The Vault server uses a Google service account with permission on the relevant key ring to authenticate to KMS to perform this operation.

In auto-unseal mode, there is still a manual key, but this key is called a "recovery key" and cannot be used to unseal the database.
It is, however, still needed for certain operations, such as seal key migration.

The recovery key for the Vault is kept in 1Password.

.. rubric:: Changing seal keys

It is possible to change the key used to seal Vault (if, for instance, Vault needs to be migrated to another GCP project), but it's not well-documented and is moderately complicated.
Here are the steps:

#. Add ``disabled = "true"`` to the ``seal`` configuration stanza in ``values.yaml``.
#. Change ``vault.server.ha.relicas`` to 1 in ``values.yaml``.
#. Push the changes and wait for Argo CD to sync and remove the other running Vault containers.
   Argo CD will complain about synchronizing the affinity configuration; this is harmless and can be ignored.
#. Relaunch the ``vault-0`` pod by deleting it and letting Kubernetes recreate it.
#. Get the recovery key from 1Password.
#. ``kubectl exec --namespace=vault -ti vault-0 -- vault operator unseal -migrate`` and enter the recovery key.
   This will disable auto-unseal and convert the unseal recovery key to be a regular unseal key using Shamir.
   Vault is no longer using the KMS key at this point.
#. Change the KMS ``seal`` configuration stanza in ``values.yaml`` to point to the new KMS keyring and key that you want to use.
   Remove ``disabled = "true"`` from the ``seal`` configuration.
   Push this change and wait for Argo CD to synchronize it.
#. Relaunch the ``vault-0`` pod by deleting it and letting Kubernetes recreate it.
#. ``kubectl exec --namespace=vault -ti vault-0 -- vault operator unseal -migrate`` and enter the recovery key.
   This will reseal Vault using the KMS key and convert the unseal key you have been using back to being a recovery key.
#. Change ``vault.server.ha.replicas`` back to 3 in ``values.yaml``, push, and let Argo CD start the remaining Vault pods.

.. _external:

.. rubric:: External configuration

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
#. Create a DNS entry for the new cluster pointing to the external IP of the nginx ingress for the Kubernetes cluster in which this application is deployed.
   We have ``vault.lsst.codes`` for the primary Vault installation and ``vault-1.lsst.codes`` and ``vault-2.lsst.codes`` for test installations.
   Update the list of hostnames configured in the Vault ``values.yaml`` file for the ingress to match the DNS entries that you want to use.
   The DNS entry has to match the hostname for cert-manager to be able to get TLS certificates for Vault.

When deploying Vault elsewhere, at least the storage bucket name will have to change because bucket names are globally unique in GCP.
Note that the GCP project and region are also encoded in ``values.yaml`` if deploying elsewhere.

.. rubric:: Migrating Vault

If you want to migrate a Vault deployment from one GCP project and Kubernetes cluster to another, do the following:

#. Create the `external configuration <External configuration_>`_ required for the new Vault server in the new GCP project.
#. Grant the new service account access to the KMS keyring and key used for unsealing in the old GCP project.
   This is necessary to be able to do a seal migration later.
   See `this StackOverflow answer <https://stackoverflow.com/questions/49214127/can-you-share-google-cloud-kms-keys-across-projects-with-service-roles>`__ for how to grant access.
#. Copy the data from the old GCS bucket to the new GCS bucket using a GCS transfer.
#. Configure the new vault to point to the KMS keyring and key in the old project.
#. Perform a `seal migration <Changing seal keys_>`_ to switch from the old seal key in KMS in the old GCP project to the new seal key in the new GCP project.
#. Change DNS to point the Vault server name (generally ``vault.lsst.codes``) to point to the new installation.
