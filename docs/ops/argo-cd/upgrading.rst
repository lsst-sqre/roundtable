#################
Upgrading Argo CD
#################

Argo CD cannot upgrade itself, so upgrades must be done manually.
In Roundtable, Argo CD is configured to be aware of itself via the ``argo-cd`` app, but this is only for the UI display.
Do not try to sync the ``argo-cd`` app to perform an upgrade; it will fail and probably bring down the UI.
(It's fine to sync the ``argo-cd`` app to update resources that are added by the Roundtable configuration.)

To upgrade Argo CD:

#. Go to the `Argo CD GitHub page <https://github.com/argoproj/argo-cd>`__ and determine the latest release.
   You can also check the Roundtable repository with ``neophile analyze`` (see the `neophile documentation <https://neophile.lsst.io/>`__ for more information).
#. Update the `Argo CD configuration <https://github.com/lsst-sqre/roundtable/blob/master/deployments/argo-cd/kustomization.yaml>`__ to point to the latest version.
   This will not upgrade Argo CD, but it will allow you to see what will change via the UI.
#. Read the upgrade notes and then upgrade Argo CD following the `upstream instructions <https://argoproj.github.io/argo-cd/operator-manual/upgrading/overview/>`__.
   Roundtable uses the non-HA configuration.
