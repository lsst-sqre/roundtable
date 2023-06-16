#################
Upgrading Argo CD
#################

To upgrade Argo CD:

#. Go to the `Argo CD GitHub page <https://github.com/argoproj/argo-cd>`__ and determine the latest release.
#. Read the `upgrade notes <https://argo-cd.readthedocs.io/en/stable/operator-manual/upgrading/overview/>`__ to see if there are any changes to adjust for.
#. Update the `Argo CD configuration <https://github.com/lsst-sqre/roundtable/blob/master/deployments/argo-cd/kustomization.yaml>`__ to point to the latest version.
   Argo CD will then sync and upgrade itself as soon as it notices the change to the repository.

If the upgrade fails (upgrading oneself is a tricky operation!), you may need to manually apply the Argo CD configuration.
You can do this with:

.. code-block:: console

   $ kubectl apply -k deployments/argo-cd/kustomization.yaml

from the top level of the Roundtable repository with the update to change the Argo CD version applied.

For major upgrades, consider taking a backup before upgrading with:

.. code-block:: console

   $ chmod 644 ~/.kube/config
   $ docker run -v ~/.kube:/home/argocd/.kube --rm \
       argoproj/argocd:$VERSION argocd-util export -n argocd > backup.yaml
   $ chmod 600 ~/.kube/config

You have to temporarily make your ``kubectl`` configuration file world-readable so that the Argo CD Docker image can use your credentials.
Replace ``$VERSION`` with the current Argo CD version as seen in the top left hand sidebar in the UI, with the leading ``v`` but without the ``+`` and trailing hash.
This is taken from the `Argo CD disaster recovery documentation <https://argo-cd.readthedocs.io/en/stable/operator-manual/disaster_recovery/>`__ with the addition of the namespace flag.

If anything goes horribly wrong, you can then restore the configuration with:

.. code-block:: console

   $ chmod 644 ~/.kube/config
   $ docker run -i -v ~/.kube:/home/argocd/.kube --rm \
       argoproj/argocd:$VERSION argocd-util import -n argocd - < backup.yaml
   $ chmod 600 ~/.kube/config
