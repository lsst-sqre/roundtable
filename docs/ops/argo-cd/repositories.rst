#####################################
Git repositories monitored by Argo CD
#####################################

Argo CD monitors remote Git repositories for changes, and to get manifests for applications.
To include a Git repository, add it to the ``repositories`` field in the `argocd-cm.yaml`_ configuration patch.

For example, https://github.com/lsst-sqre/roundtable is included in this list because all application manifests for Roundtable are defined in this GitHub repository.

We also include the Argo CD repository, https://github.com/argoproj/argo-cd, because Roundtable's `kustomization.yaml`_ references that Git repository directly.

.. seealso::

   `Repositories <https://argoproj.github.io/argo-cd/operator-manual/declarative-setup/#repositories>`_ in the Argo CD documentation.

.. _`argocd-cm.yaml`: https://github.com/lsst-sqre/roundtable/blob/master/deployments/argo-cd/patches/argocd-cm.yaml
.. _`kustomization.yaml`: https://github.com/lsst-sqre/roundtable/blob/master/deployments/argo-cd/kustomization.yaml
