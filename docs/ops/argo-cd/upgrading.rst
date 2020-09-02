#################
Upgrading Argo CD
#################

To upgrade Argo CD:

#. Go to the `Argo CD GitHub page <https://github.com/argoproj/argo-cd>`__ and determine the latest release.
   You can also check the Roundtable repository with ``neophile analyze`` (see the `neophile documentation <https://neophile.lsst.io/>`__ for more information).
#. Read the `upgrade notes <https://argoproj.github.io/argo-cd/operator-manual/upgrading/overview/>`__ to see if there are any changes to adjust for.
#. Update the `Argo CD configuration <https://github.com/lsst-sqre/roundtable/blob/master/deployments/argo-cd/kustomization.yaml>`__ to point to the latest version.
   Argo CD will then sync and upgrade itself as soon as it notices the change to the repository.

If the upgrade fails (upgrading oneself is a tricky operation!), you may need to manually apply the Argo CD configuration.
You can do this with:

.. code-block:: console

   $ kubectl apply -k deployments/argo-cd/kustomization.yaml

from the top level of the Roundtable repository with the update to change the Argo CD version applied.
