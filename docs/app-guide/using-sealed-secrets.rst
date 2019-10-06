##################################################################
Using Sealed Secrets to make encrypted secrets for your deployment
##################################################################

Roundtable provides a feature called `Sealed Secrets`_, developed by Bitnami, that allows you to securely encrypt secrets so that you can include them alongside the rest of your app's deployment in the Roundtable Git repository (quite unlike regular Kubernetes ``Secret`` resources!).
This page provides a quick primer on how Sealed Secrets work and gives you directions on how to create Sealed Secrets for your application.

Sealed Secrets primer
=====================

This is the basic workflow for Sealed Secrets:

1. You create a Kubernetes ``Secret`` resource manifest on your private computer.
2. Using the ``kubeseal`` command-line tool, you encrypt the secrets and create a new Kubernetes manifest for a resource called a ``SealedSecret``.
   You can commit this ``SealedSecret`` as part of your deployment's kustomization or Helm chart.
   The secret can only be decrypted within the Roundtable Kubernetes cluster.
3. When you deploy your application, the Sealed Secrets controller instantly detects the ``SealedSecret`` resource and creates a decrypted ``Secret`` resource with the same name in the same namespace.
4. Your application uses the ``Secret`` resource as usual.

In other words ``SealedSecrets`` are versions of ``Secrets`` that you can package with your application's deployment.

The main principle behind Sealed Secrets is that only the Sealed Secrets controller knows the private key.
As long as the Roundtable Kubernetes cluster is properly secured, ``SealedSecret`` resources cannot be decrypted, not even by the original author.

.. note::

   If you're familiar Travis CI, Sealed Secrets work just like the "secure" environment variables that you create with the ``travis encrypt`` command --- since only Travis itself knows the private keys, those secure encrypted environment variables can be safely committed in a ``.travis.yml`` file.

For more information about Sealed Secrets, see their documentation at https://github.com/bitnami-labs/sealed-secrets/blob/master/README.md.

How to create a SealedSecret for your application deployment
============================================================

This section describes the steps for creating Sealed Secrets for your Roundtable-based application deployment.

.. _sealedsecrets-prereq:

Before you start
----------------

Before you can create sealed secrets, ensure you have met these prerequisites first:

- The namespace for your application must exist.

  Roundtable applications are deployed in individual Kubernetes namespaces.
  To get started, ask the Roundtable operations team to create a namespace for your application.

- You must have ``kubectl`` access to your application's namespace.

- You must have the ``kubeseal`` command-line tool installed on your local machine.
  See the `Sealed Secrets installation documentation <https://github.com/bitnami-labs/sealed-secrets/blob/master/README.md#homebrew>`_.

.. _sealedsecrets-create-secret:

Step 1. Create a Secret resource manifest file locally
------------------------------------------------------

On your local machine, create a YAML file with a Kubernetes ``Secret`` resource.
**Do not commit this YAML file into a Git repo.**

This is a sample ``Secret``:

.. code-block:: yaml

   apiVersion: v1
   kind: Secret
   metadata:
     name: example  # Replace the name
   type: Opaque
   stringData:
     key1: "value1"
     key2: "value2"
     config.yaml: |-
       # Contents of a secret file
       a: "Secret"

.. tip::

   By using the ``stringData`` field instead of ``data``, you can avoid having to base64-encode the values.

.. seealso::

   See the Kubernetes `Secret <https://kubernetes.io/docs/concepts/configuration/secret/>`_ documentation for more information about secrets.

.. _sealedsecrets-create-sealedsecret:

Step 2. Create the SealedSecret resource manifest file
------------------------------------------------------

Run the kubeseal command:

.. code-block:: sh

   kubeseal --controller-namespace sealed-secrets -o yaml \
     --namespace my-namespace <my-secret.yaml >my-sealedsecret.yaml

Replace the example information:

- Replace "my-namespace" with the name of your application's namespace in Roundtable.
- Replace "my-secret.yaml" with the file name of the secret you created in :ref:`Step 1 <sealedsecrets-create-secret>`.
- Replace "my-sealedsecret.yaml" with the file name you would like to give to the new sealed secret.
  **This is the file that kubeseal will create.**

Step 3. Add the SealedSecret to your applicaton's deployment
------------------------------------------------------------

Finally, move the sealed secret file you created in the previous step into your application's deployment and commit it into the Roundtable Git repository.
Depending on whether your application is deployed with Helm, Kustomize, or a different tool, this step will differ.

Once you redeploy you application with the ``SealedSecret`` resource, the Sealed Secrets controller will instantly and automatically create an equivalent Secret resource with the same name in your application's namespace.

Updating the SealedSecret
-------------------------

Since you cannot decrypt a ``SealedSecret`` outside the Kubernetes cluster, if you need to update the secret (to add a field or to modify a value, for instance), the best course of action is to recreate the ``SealedSecret`` from scratch, starting at :ref:`Step 1 <sealedsecrets-create-secret>`, above.
