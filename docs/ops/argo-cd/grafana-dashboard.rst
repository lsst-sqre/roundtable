#################
Grafana dashboard
#################

A `Grafana dashboard for Argo CD <https://monitoring.roundtable.lsst.codes/d/BjWwX3jik/argo-cd>`__ is available on monitoring.roundtable.lsst.codes.
This is a copy of the `example Grafana dashboard <https://github.com/argoproj/argo-cd/blob/master/examples/dashboard.json>`__, installed via a ``ConfigMap`` added as an additional resource to the Argo CD configuration.

This dashboard may require updates for newer versions of Prometheus.
To update the dashboard, download its JSON definition from the above link.
Then, edit `argocd-grafana-dashboard-cm.yaml <https://github.com/lsst-sqre/roundtable/blob/master/deployments/argo-cd/resources/argocd-grafana-dashboard-cm.yaml>`__ and replace the contents of the ``argocd-dashboard.json`` key with the contents of that JSON file, indented with four spaces.
Finally, add the following two settings to the end of the JSON to retain the same dashboard URL:

.. code-block:: json

   {
     "uid": "BjWwX3jik",
     "version": 5
   }

without the braces, incrementing the version to one more than the current version.
