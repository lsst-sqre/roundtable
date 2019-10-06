###################################
sealed-secrets app deployment guide
###################################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |sealed-secrets-status|
   * - Edit on GitHub
     - `/deployments/sealed-secrets <https://github.com/lsst-sqre/roundtable/tree/master/deployments/sealed-secrets>`__
   * - Type
     - Kustomize_
   * - Parent app
     - :doc:`roundtable <../roundtable/index>`

.. rubric:: Overview

`Sealed Secrets`_ is a Kubernetes controller from Bitnami that uses asymmetric crypto to encrypt secrets so that an encrypted secret can be safely committed to a public Git repository, and be decrypted only by the controller inside the Roundtable Kubernetes cluster.
