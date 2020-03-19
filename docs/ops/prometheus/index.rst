###############################
prometheus app deployment guide
###############################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |prometheus-status|
   * - Edit on GitHub
     - `/deployments/prometheus <https://github.com/lsst-sqre/roundtable/tree/master/deployments/prometheus>`__
   * - Type
     - Helm_
   * - Parent app
     - :doc:`roundtable <../roundtable/index>`
   * - Grafana
     - https://monitoring.roundtable.lsst.codes

.. rubric:: Overview

This Argo CD Application deploys and configures a Prometheus_ monitoring stack based on the `prometheus-operator Helm chart <https://github.com/helm/charts/tree/master/stable/prometheus-operator>`__.
The Grafana dashboard is online at https://monitoring.roundtable.lsst.codes.

.. rubric:: Guides

.. toctree::

   grafana-github-sso
