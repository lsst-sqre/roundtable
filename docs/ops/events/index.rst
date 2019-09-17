###########################
events app deployment guide
###########################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |events-status|
   * - Edit on GitHub
     - `/deployments/events <https://github.com/lsst-sqre/roundtable/tree/master/deployments/events>`__
   * - Type
     - Kustomize_
   * - Parent app
     - :doc:`roundtable <../roundtable/index>`

.. rubric:: Overview

Roundtable Events enables real-time, events-based, applications in the Roundtable platform.
For example, SQuaRE Bot uses Events to pass Slack messages from a central Slack client to domain-specific message handlers.

Events is built on Apache Kafka and deployed with the Strimzi Cluster Operator (see the :doc:`strimzi <../strimzi/index>` application deployment guide).
