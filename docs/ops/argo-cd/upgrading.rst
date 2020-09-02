#################
Upgrading Argo CD
#################

To upgrade Argo CD:

#. Go to the `Argo CD GitHub page <https://github.com/argoproj/argo-cd>`__ and determine the latest release.
   You can also check the Roundtable repository with ``neophile analyze`` (see the `neophile documentation <https://neophile.lsst.io/>`__ for more information).
#. Update the `Argo CD configuration <https://github.com/lsst-sqre/roundtable/blob/master/deployments/argo-cd/kustomization.yaml>`__ to point to the latest version.
   This will not upgrade Argo CD, but it will allow you to see what will change via the UI.
#. Read the `upgrade notes <https://argoproj.github.io/argo-cd/operator-manual/upgrading/overview/>`__ to see if there are any changes to adjust for.
#. Tell Argo CD to sync the update.
