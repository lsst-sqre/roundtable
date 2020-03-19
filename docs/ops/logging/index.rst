############################
logging app deployment guide
############################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |logging-status|
   * - Edit on GitHub
     - `/deployments/logging <https://github.com/lsst-sqre/roundtable/tree/master/deployments/logging>`__
   * - Type
     - Helm_
   * - Parent app
     - :doc:`roundtable <../roundtable/index>`

.. rubric:: Overview

The logging app is a self-contained system for gathering, storing, and visualizing logs from the Roundtable Kubernetes cluster.
Kubernetes logs, as well as application logs emitted on stdout and stderr, are automatically gathered by the logging app.

The logging app is built primarily around the ``opendistro-es`` chart maintained in https://github.com/lsst-sqre/charts.
This chart provides Elasticsearch_ and Kibana_ services.
The logging app also includes a ``fluentd-elasticsearch`` chart maintained in https://github.com/kiwigrid/helm-charts.
Fluentd gathers logs.

.. seealso::

   :doc:`/app-guide/viewing-logs` explains how to log into the Kibana UI.
