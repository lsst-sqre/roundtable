#########################################
Node pools for the Roundtable GKE cluster
#########################################

.. list-table::
   :header-rows: 1

   * - Name
     - Count
     - Machine type
     - vCPU / node
     - Memory (GB) / node
   * - :ref:`default-pool`
     - 4
     - n1-standard-2
     - 2
     - 7.5

.. _default-pool:

default-pool
============

`Console link <https://console.cloud.google.com/kubernetes/nodepool/us-central1-a/roundtable/default-pool?project=plasma-geode-127520>`__

The ``default-pool`` is a general purpose node pool for tenant applications and most of the core Roundtable infrastructure.

.. todo::

   Add guidelines on how to configure applications for affinity to the default-pool.
