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
     - 6
     - n1-standard-2
     - 2
     - 7.5
   * - :ref:`events <events-pool>`
     - 4
     - n1-standard-2
     - 2
     - 7.5
   * - :ref:`monitoring <monitoring-pool>`
     - 2
     - e2-standard-8
     - 8
     - 32

.. _default-pool:

default-pool
============

`Console link <https://console.cloud.google.com/kubernetes/nodepool/us-central1-a/roundtable/default-pool?project=plasma-geode-127520>`__

The ``default-pool`` is a general purpose node pool for tenant applications and most of the core Roundtable infrastructure.

.. todo::

   Add guidelines on how to configure applications for affinity to the default-pool.

.. _events-pool:

events node pool
================

`Console link <https://console.cloud.google.com/kubernetes/nodepool/us-central1-a/roundtable/events?project=plasma-geode-127520>`__

The ``events`` node pool is dedicated to Kafka brokers and Zookeeper databases for the :doc:`events <../events/index>` application.
Nodes in this pool are labeled:

.. list-table::
   :header-rows: 1

   * - Key
     - Value
   * - ``dedicated``
     - ``events``

Nodes in this pool are also tainted:

.. list-table::
   :header-rows: 1

   * - Effect
     - Key
     - Value
   * - ``NoExecute``
     - ``dedicated``
     - ``events``

.. _monitoring-pool:

monitoring node pool
====================

`Console link <https://console.cloud.google.com/kubernetes/nodepool/us-central1-a/roundtable/monitoring?project=plasma-geode-127520>`__

The ``monitoring`` node pool is dedicated to the components of our
:doc:`monitoring <../monitoring/index>` application, chiefly
InfluxDBv2 and Chronograf.
Nodes in this pool are labeled:

.. list-table::
   :header-rows: 1

   * - Key
     - Value
   * - ``dedicated``
     - ``monitoring``

Nodes in this pool are also tainted:

.. list-table::
   :header-rows: 1

   * - Effect
     - Key
     - Value
   * - ``NoSchedule``
     - ``dedicated``
     - ``monitoring``
