#################################
cert-manager app deployment guide
#################################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |cert-manager-status|
   * - Edit on GitHub
     - `/deployments/cert-manager <https://github.com/lsst-sqre/roundtable/tree/master/deployments/cert-manager>`__
   * - Type
     - Helm_
   * - Parent app
     - :doc:`roundtable <../roundtable/index>`

.. rubric:: Overview

This Argo CD Application deploys and configures `cert-manager <https://cert-manager.readthedocs.io/en/latest/>`__ from the `stable Helm chart repository <https://github.com/helm/charts/tree/master/stable/cert-manager>`__.
