##################################
GKE cluster configuration overview
##################################

Roundtable is deployed on a Google Kubernetes Engine (GKE) cluster called ``roundtable`` (`view the GKE console`_).
The Roundtable cluster is not configuration managed — we configure the Roundtable cluster through the :command:`glcloud` command-line tool or the `GKE console`_.
The purpose of this page is to summarize key cluster configurations and decisions so that current operators can understand the cluster, and the cluster could be roughly redeployed elsewhere in the future.

.. _view the GKE console:
.. _GKE console: https://console.cloud.google.com/kubernetes/clusters/details/us-central1-a/roundtable?project=plasma-geode-127520

Region
======

Both the master and nodes are deployed in the ``us-central1-a`` `zone <https://cloud.google.com/compute/docs/regions-zones/>`_ (Iowa).
See also the `Google Cloud Platform Pricing <https://cloud.google.com/compute/all-pricing>`_ page for current pricing in this region.

VPC-native
==========

The Roundtable cluster is `VPC-native <https://cloud.google.com/kubernetes-engine/docs/how-to/alias-ips>`_ (alias IP) **enabled**.
VPC-native networking is necessary for connecting to Google Cloud Platform's managed datastores such as Cloud Memory Store (`connection docs <https://cloud.google.com/memorystore/docs/redis/connect-redis-instance#connecting-cluster>`__) and Cloud SQL (`connection docs <https://cloud.google.com/sql/docs/postgres/connect-kubernetes-engine#private-ip>`__).

Stackdriver monitoring
======================

Stackdriver Kubernetes monitoring is **enabled** and Legacy Stackdriver Logging and Legacy Stackdriver Monitoring are **disabled**.

We use Stackdriver for logs.
See the `Stackdriver Kubernetes Engine Monitoring <https://cloud.google.com/stackdriver/docs/solutions/gke>`_ documentation for more information.

Istio
=====

At this time the `Istio on GKE <https://cloud.google.com/istio/docs/istio-on-gke>`_ is **disabled**.
We trialled Istio early in the Roundtable deployment, and decided to focus on more traditional ingress and continuous deployment in the initial phase.
As Roundtable matures, we may revisit using a service mesh on Roundtable.

Nodes
=====

See :doc:`node-pools` for more information.

Generally nodes are configured with both auto-upgrade and auto-repair **enabled**.

Making the default storage class expandable
===========================================

Out-of-the-box, the ``standard`` ``StorageClass`` (default, with spinning hard drives) does not allow volume expansion.
We manually enabled that feature:

.. code-block:: bash

   kubectl patch sc standard -p '{"allowVolumeExpansion": true}'

See the blog post `Resizing Persistent Volumes using Kubernetes <https://kubernetes.io/blog/2018/07/12/resizing-persistent-volumes-using-kubernetes/>`_.
