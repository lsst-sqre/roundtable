###################################
Using Vault for application secrets
###################################

You application likely needs sensitive information to run.
These secrets include API keys and tokens that let your application access secured services.
You can't include this information inside your application's code base or GitOps deployment manifests because it would be exposed in our open source GitHub repositories.
Instead, you want your application to have these secrets available only when needed: at runtime inside the Roundtable Kubernetes cluster.

Applications can use Roundtable's Vault_ service to store and access secrets within Kubernetes.
By using Vault, Roundtable applications can use a completely public GitOps approach to deployments while ensuring that secret information like passwords and API tokens never leave the Kubernetes cluster.

This page includes an overview of the Vault system along with procedures for using Vault in your application's deployment.

Background
==========

To use Vault, some concepts that you'll need to understand are:

- :ref:`vault-def` itself
- :ref:`vault-paths-def`
- :ref:`vault-kv-def`
- :ref:`vault-vso-def`

.. _vault-def:

Vault
-----

Vault_ is a database and API for managing sensitive data.
Roundtable provides a centralized Vault service that all applications can integrate with to store and access their sensitive data.

Read `DMTN-112: LSST DM Vault <https://dmtn-112.lsst.io>`__ for more information about our Vault service.

.. _vault-paths-def:

Paths for application secrets
-----------------------------

Every application stores its secrets at a specific **path** within the Vault service.
Paths keep secrets organized and also helps control access with tokens.

The root path for all Roundtable secrets is:

.. code-block:: text

   secret/k8s_operator/roundtable

If your application is named ``myapp``, its specific path is:

.. code-block:: text

   secret/k8s_operator/roundtable/myapp

.. _vault-kv-def:

Key-value storage
-----------------

At each path, one or more secrets can be stored as key-value pairs.
Keys must be strings, while values can be anything: strings, JSON objects, or even binary blobs.

.. _vault-vso-def:

Vault Secrets Operator
----------------------

The `Vault Secrets Operator`_ acts as a bridge between Vault and your application in Kubernetes.
To use it, you deploy a Kubernetes resource called a ``VaultSecret``.
That ``VaultSecret`` specifies a path in Vault.
The Vault Secrets Operator then accesses the secrets at that path and creates a regular Kubernetes `Secret resource <https://kubernetes.io/docs/concepts/configuration/secret/>`__ containing them.

Since the ``VaultSecret`` doesn't contain any sensitive information, you can include it in your Kubernetes deployment manifests.
The corresponding ``Secret`` resource only exists within Kubernetes.

Tasks
=====

This section includes procedures for accomplishing different tasks related to using Vault with your application deployment.

.. _vault-write-access:

Get write access to Vault
-------------------------

1. Install Vault on your computer.
   You can either `download Vault from Hashicorp <https://www.vaultproject.io/downloads>`__ or install it with a package manager like `Homebrew <https://brew.sh>`_ on macOS: ``brew install vault``.

2. Obtain a "write" token scoped for the applicable Vault secret path:

   - SQuaRE team members can use the write token for ``k8s_operator/roundtable`` found in the ``LSP Vault Tokens`` 1Password secret.
     This token is scoped for all Roundtable applications.
   - Contributors can request a token, `see DMTN-112 <https://dmtn-112.lsst.io/#token-acquisition-and-revocation>`__.

3. Set shell environment variables for convenience:

   .. code-block:: bash

      export VAULT_ADDR="https://vault.lsst.codes"
      export VAULT_TOKEN=<token id>

.. important::

   Vault tokens have both an ``id`` and an ``accessor``.
   As a Vault enduser, you will always use the ``id`` string.

.. _vault-create-a-secret:

Create a new Vault secret for an application
--------------------------------------------

*Prerequisites:*

- :ref:`vault-write-access`

If your application is named ``myapp``, you will create secrets for your application at the ``secret/k8s_operator/roundtable/myapp`` path.

.. note::

   *What's my applications name?*

   By convention, your application's name corresponds to the name of the Argo CD ``Application`` resource.
   This same name is also the name of your application's directory in the `deployments directory <https://github.com/lsst-sqre/roundtable/tree/master/deployments>`__ of the Roundtable repository on GitHub.

Suppose you have multiple secrets.
Each secret has a key (which is a string) and a value (which is often a string, but can also be binary blobs):

.. list-table:: Example secrets
   :widths: 50 50
   :header-rows: 1

   * - Key
     - Secret value

   * - ``key1``
     - ``value1``
   * - ``key2``
     - ``value2``

You can create a Vault secret with these two keys using a `vault kv put`_ command:

.. code-block:: bash

   vault kv put secret/k8s_operator/roundtable/myapp key1="value1" key2="value2"

.. note::

   You can also upload secrets from a JSON document using the ``@data.json`` argument or from a stdin using the ``-`` symbol.
   These are useful for large/complex secrets and binary objects.
   See the `Vault kv put documentation`_ for details.

.. _Vault kv put documentation:
.. _vault kv put: https://www.vaultproject.io/docs/commands/kv/put

.. _vault-update-a-secret:

Update a Vault secret for an application
----------------------------------------

*Prerequisites:*

- :ref:`vault-write-access`
- :ref:`vault-create-a-secret`

This command will allow you to update one or more key-value pairs at your application's Vault path without affecting key-value pairs that are not named:

.. code-block:: bash

   vault kv patch secret/k8s_operator/roundtable/myapp key1="new-value" key2="new-value"

For more information, see the `Vault kv patch documentation`_.

.. _Vault kv patch documentation: https://www.vaultproject.io/docs/commands/kv/patch

.. _vault-vaultsecret:

Add a VaultSecret Kubernetes resource to your application
---------------------------------------------------------

The ``VaultSecret`` Kubernetes resource bridges Vault secrets to Kubernetes ``Secret`` resources.
With a ``Secret``, you can integrate secret information with your application's Kubernetes deployment as usual.

*Prerequisites:*

- Have an application deployment in Roundtable
- :ref:`vault-create-a-secret`

To integrate Vault secrets into your application's deployment:

1. **Create a VaultSecret.**
   If your application's name is ``myapp``, and therefore its Vault path is ``secret/k8s_operator/roundtable/myapp``, create a YAML-formatted ``VaultSecret``:

   .. code-block:: yaml
      :emphasize-lines: 4,6

      apiVersion: ricoberger.de/v1alpha1
      kind: VaultSecret
      metadata:
        name: myapp
      spec:
        path: secret/k8s_operator/roundtable/myapp
        type: Opaque

   The ``metadata.name`` field determines the resource name of both the ``VaultSecret`` resource, and the regular ``Secret``.

2. **Add the VaultSecret to your application's Roundtable deployment**

   You need to add the ``VaultSecret`` YAML file created in the first step to your application's deployment in the roundtable repository on GitHub.

   .. note::

      An alternative would be add the ``VaultSecret`` as part of your application's base manifests (using Kustomize) or chart (using Helm) and then provide a way to template the Vault secrets path.
      Since Vault is a facility in Roundtable, though, it might make more sense to define the ``VaultSecret`` directly within the deployment manifest (Kustomize) or chart (Helm) within the roundtable repository.

   How the secret is added depends on the packaging:

   - **For Helm:** save the ``VaultSecret`` YAML to a file located at ``deployments/myapp/templates/vaultsecret.yaml`` (replace "myapp").

   - **For Kustomize:**

     1. Save the ``VaultSecret`` YAML to a file located at ``deployments/myapp/resources/vaultsecret.yaml`` (replace "myapp").
     2. Reference that resource from your application's ``kustomization.yaml`` file (located at ``deployments/myapp/kustomization.yaml``).
        For example:

        .. code-block:: yaml
           :emphasize-lines: 7

           apiVersion: kustomize.config.k8s.io/v1beta1
           kind: Kustomization

           namespace: events

           resources:
             - resources/vaultsecret.yaml
             - github.com/lsst-sqre/myapp.git//manifests/base?ref=1.0.0


.. _vault-envvar:

Mount secrets as environment variables
--------------------------------------

The final step is to actually allow your application to access the secrets.
One method, described here, is to mount secrets as environment variables in your application's containers.
This method works well for secrets that are needed to configure your application when it starts up.
(Another method is to :ref:`mount secrets in a file <vault-file-mount>`, discussed next.)

**Prerequisites:**

- :ref:`vault-vaultsecret`

At this point, you should have a ``VaultSecret`` resource that's part of your application's deployment.
In the Argo CD dashboard, you'll notice a corresponding ``Secret`` resource:

.. code-block:: text

   myapp (VaultSecret) => myapp (Secret)

The ``Secret`` named ``myapp`` is created for you automatically by the Vault Secrets Operator.
Inside that ``Secret``, the keys correspond to the key-value pairs you set in Vault at your application's Vault path:

.. code-block:: yaml
   :emphasize-lines: 11-12

   apiVersion: v1
   kind: Secret
   type: Opaque
   metadata:
     labels:
       app.kubernetes.io/instance: myapp
       created-by: vault-secrets-operator
     name: myapp
     namespace: events
   data:
     key1: "**********"
     key2: "**********"

To make the value of ``key1`` available as an environment variable called ``KEY1`` from your application's container, add an item to the ``env`` field of your container's ``spec`` in the ``Deployment`` resource:

.. code-block:: yaml
   :emphasize-lines: 19-24

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: myapp
     labels:
       app: myapp
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: myapp
     template:
       metadata:
         labels:
           app: myapp
       spec:
         containers:
           - name: app
             env:
               - name: KEY1
                 valueFrom:
                   secretKeyRef:
                     name: myapp
                     key: key1

.. _vault-file-mount:

Mount secrets in a file
-----------------------

Instead of accessing :ref:`secrets as an environment variable <vault-envvar>`, you can instead make the secret available as a file in the container.
This approach is well suited to large or complex secret values.
It's also an excellent choice if your application is able to monitor the secret file.
When a ``Secret`` changes, Kubernetes updates the file inside the running container.
This way, your application can receive updated secrets without needing a rolling restart (which is the case for secrets mounted as environment variables).

As an example, to make the value of ``key1`` available as a file available from your container, first specify a volume in your application's Deployment resource under the ``spec.template.spec.volumes`` key:

.. code-block:: yaml
   :emphasize-lines: 17-20

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: myapp
     labels:
       app: myapp
   spec:
     replicas: 1
     selector:
       matchLabels:
         name: myapp
     template:
       metadata:
         labels:
           name: myapp
       spec:
         volumes:
           - name: secrets
             secret:
               secretName: myapp

Next, add the volume mount to the container at the ``spec.template.spec.containers[].volumeMounts`` key:

.. code-block:: yaml
   :emphasize-lines: 19-22

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: myapp
     labels:
       app: myapp
   spec:
     replicas: 1
     selector:
       matchLabels:
         name: myapp
     template:
       metadata:
         labels:
           name: myapp
       spec:
         containers:
           - name: app
             volumeMounts:
               - name: secret
                 mountPath: "/etc/myapp/secret.txt"
                 readOnly: true
         volumes:
           - name: secret
             secret:
               secretName: myapp
