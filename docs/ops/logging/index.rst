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

The logging app deploys Open Distro for Elasticsearch, a self-contained system for gathering, storing, and visualizing logs from the Roundtable Kubernetes cluster.
Kubernetes logs, as well as application logs emitted on stdout and stderr, are automatically gathered by the logging app.

Be aware that we are using Open Distro for Elasticsearch, not Elasticsearch directly.
Open Distro bundles a set of plugins that are quite different from the facilities available from the commercial Elasticsearch product.
Much of the on-line information about Elasticsearch is for the commercial product or for other plugins.
When making changes or debugging problems, be sure to start with the Open Distro documentation.

The logging app is built primarily around the `opendistro-es Helm chart <https://github.com/opendistro-for-elasticsearch/community/tree/master/open-distro-elasticsearch-kubernetes/helm>`__.
This chart is community-maintained, not kept up-to-date with new releases, and not uploaded to any Helm chart repository.
We maintain a light fork in the `charts repository <https://github.com/lsst-sqre/charts/>`__ that is updated for new Open Distro for Elasticsearch releases.
This chart provides Elasticsearch_ and Kibana_ services.

The logging app also includes the `fluentd-elasticsearch chart <https://github.com/kiwigrid/helm-charts>`__.
Fluentd gathers logs from each cluster node and forwards them to Elasticsearch.

.. rubric:: Guides

.. toctree::

   upgrading
   bootstrapping
   authentication
   troubleshooting

.. seealso::

   * :doc:`/app-guide/viewing-logs` explains how to log into the Kibana UI.
   * `Open Distro for Elasticsearch <https://opendistro.github.io/for-elasticsearch-docs/>`__
