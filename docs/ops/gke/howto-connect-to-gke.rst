####################################################################
How to connect to the Roundtable GKE cluster from the web or kubectl
####################################################################

IAM membership
==============

Before you can directly access Roundtable's GKE cluster, you need to be a member of the ``sqre`` Google Cloud Platform project.
SQuaRE team members generally have the ``Owner`` role.
Existing team members can add and edit project members from the IAM dashboard: https://console.cloud.google.com/iam-admin/iam?project=plasma-geode-127520

The GKE console
===============

The URL for the GKE dashboard is https://console.cloud.google.com/kubernetes/clusters/details/us-central1-a/roundtable?project=plasma-geode-127520.

From the console you can view and edit the cluster configuration.
Generally you will use Argo CD, at https://cd.roundtable.lsst.codes, to deploy and inspect applications.

From kubectl
============

To connect to the Roundtable cluster
You can use the ``kubectl`` command-line tool to access the Roundtable cluster.
Follow Google's `Configuring cluster access for kubectl <https://cloud.google.com/kubernetes-engine/docs/how-to/cluster-access-for-kubectl>`_ page.
Briefly, the steps are:

1. Install the `Cloud SDK <https://cloud.google.com/sdk/downloads>`_.

2. Set the project:

   .. code-block:: bash

       gcloud config set project plasma-geode-127520

3. Get credentials for the Roundtable cluster, creating a context in ``kubectl``:

   .. code-block:: bash

      gcloud container clusters get-credentials roundtable

4. Check you access:

   .. code-block:: bash

      kubectl -n argocd get pods

   You should see a listing of pods.
