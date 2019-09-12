##################################
nginx-ingress app deployment guide
##################################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |nginx-ingress-status|
   * - Edit on GitHub
     - `/deployments/nginx-ingress <https://github.com/lsst-sqre/roundtable/tree/master/deployments/nginx-ingress>`__
   * - Type
     - Helm_
   * - Parent app
     - :doc:`roundtable <../roundtable/index>`

.. rubric:: Overview

This Argo CD Application deploys and configures `nginx-ingress <https://github.com/kubernetes/ingress-nginx>`__ from the `stable Helm chart repository <https://github.com/helm/charts/tree/master/stable/nginx-ingress>`__.
