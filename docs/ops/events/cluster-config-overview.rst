###########################################
Events Kafka cluster configuration overview
###########################################

This page summarizes the configuration of the Kafka cluster for Roundtable's :doc:`Events <index>` service, which is deployed through the Strimzi_ Cluster Operator.
Refer to the `kafka.yaml`_ resource as the most definition reference for the Kafka cluster deployment; this page adds additional context.

Pod scheduling
==============

Both the Kafka Brokers and Zookeeper pods have dedicated access to the :ref:`events-pool`.
Since the ``events`` pool currently only have three nodes, Kafka Brokers and Zookeeper pods are co-located:

- Using a toleration, both the Kafka Brokers and Zookeeper pods tolerate the ``dedicated: events`` taint.
- Using a node affinity, Kafka Brokers and Zookeeper pods are required to be scheduled on the ``events`` pool's nodes.
- Use a pod anti-affinity, Kafka Brokers will not be scheduled on the same node as other brokers.
  Likewise, Zookeeper pods will not be scheduled on the node as other Zookeeper pods.

Thus Brokers and Zookeepers are typically seen in a pair on each node of the ``events`` node pool.
The pod resource requests and limits are based on the assumption that the Kafka Broker and Zookeeper instance are the only tenants on each node.

Storage
=======

Both the Kafka Brokers and Zookeeper pods use persistent volumes with the ``faster`` StorageClass (SSD-based, defined by the :doc:`roundtable <../roundtable/index>` application).
This storage class supports volume expansion: increase the storage capacity by increasing the sizes in `kafka.yaml`_.

Listeners and authentication
============================

The cluster has an internal listener on port ``9093``, which supports mutual TLS authentication between the client and brokers.
See `Creating a Kafka user with mutual TLS authentication <https://strimzi.io/docs/operators/latest/configuring.html#tls_client_authentication>`_ for details on how create a ``KafkaUser`` resource and use the corresponding Kubernetes ``Secret`` resource with client TLS certificates.

Authorization
=============

Authorization is enabled for this cluster.
This means that that Kafka clients need a corresponding ``KafkaUser`` resource with corresponding access permissions.
See the `Strimzi Authorization documentation <https://strimzi.io/docs/operators/latest/configuring.html#assembly-securing-kafka-clients-str>`_ for more information.

Kafka configuration
===================

The Kafka brokers are configured to disallow automatic topic creation (``auto.create.topics.enable: "false"``).
Doing so prevents clients from accidentally creating new topics just by subscribing to a typo'd topic name.

See the ``spec.kafka.config`` field of `kafka.yaml`_ for the complete list of configuration overrides.

.. _kafka.yaml: https://github.com/lsst-sqre/roundtable/blob/master/deployments/events/resources/kafka.yaml
