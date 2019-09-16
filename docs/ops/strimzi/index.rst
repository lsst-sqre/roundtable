############################
strimzi app deployment guide
############################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |strimzi-status|
   * - Edit on GitHub
     - `/deployments/strimzi <https://github.com/lsst-sqre/roundtable/tree/master/deployments/strimzi>`__
   * - Type
     - Helm_
   * - Parent app
     - :doc:`roundtable <../roundtable/index>`

.. rubric:: Overview

Strimzi_ is a Kubernetes Operators that manages the deployment of Kafka, specifically for the Roundtable Events service.
This application deploys the Strimzi Custom Resource Definitions and the Strimzi Cluster Operator.
Individual Kafka clusters are managed with other Argo CD Applications.
