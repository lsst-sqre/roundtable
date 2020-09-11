###############################
gafaelfawr app deployment guide
###############################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |gafaelfawr-status|
   * - Edit on GitHub
     - `/deployments/gafaelfawr <https://github.com/lsst-sqre/roundtable/tree/master/deployments/gafaelfawr>`__
   * - Type
     - Helm_
   * - Parent app
     - :doc:`roundtable <../roundtable/index>`

.. rubric:: Overview

This Argo CD Application deploys and configures `Gafaelfawr <https://gafaelfawr.lsst.io/>`__, an authentication proxy and token management system.
Gafaelfawr is used to authenticate access to :doc:`Kibana <../logging/index>`.

It is configured to use GitHub for authentication.
Only members of the ``square`` team in the ``lsst-sqre`` organization currently have access to protected resources.
