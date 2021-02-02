##########################################
kube-prometheus-stack app deployment guide
##########################################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |kube-prometheus-stack-status|
   * - Edit on GitHub
     - `/deployments/kube-prometheus-stack <https://github.com/lsst-sqre/roundtable/tree/master/deployments/kube-prometheus-stack>`__
   * - Type
     - Helm_
   * - Parent app
     - :doc:`roundtable <../roundtable/index>`
   * - Grafana
     - https://monitoring.roundtable.lsst.codes

.. rubric:: Overview

This Argo CD Application deploys and configures a Prometheus_ monitoring stack based on the `kube-prometheus-stack Helm chart <https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack>`__.
The Grafana dashboard is online at https://monitoring.roundtable.lsst.codes.

.. rubric:: Upgrading

The prometheus chart is large and complex, so some hand-holding may be required when upgrading it to a new upstream release.

You may have to delete the existing prometheus-grafana replica set to release its underlying storage, and then delete the new prometheus-grafana pod to force it to re-attempt to attach its storage.

Argo CD will often not complete all updates in the first, automatically triggered pass.
A subsequent manual sync will normally clear up the remaining out-of-date resources.
Syncing the chart will take several minutes, so check the `Argo CD dashboard <https://cd.roundtable.lsst.codes/>`__ a half-hour after merging a pull request to update this chart to see if it needs another manual sync to pick up straggling resource updates.

There have been some issues in the past with updates dropping the stock dashboards.
After merging an update and ensuring everything syncs properly, go to the `Grafana dashboard <https://monitoring.roundtable.lsst.codes/>`__, log on with GitHub, select search from the left tool bar, and verify that the default dashboards appear.
There should be multiple Kubernetes dashboards, an Argo CD dashboard, and a few other miscellaneous dashboards.

.. rubric:: Guides

.. toctree::

   grafana-github-sso
