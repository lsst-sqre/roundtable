##########################################
Configuring custom Helm chart repositories
##########################################

To use a custom Helm chart repository in an application, you need to first define that repository in the ``helm.repositories`` field of the `argocd-cm.yaml`_ configuration patch.

Note that the default repository (which provides the ``stable/<chart>`` charts from https://github.com/helm/charts) is included by default.

.. seealso::

   `Helm Chart Repositories <https://argoproj.github.io/argo-cd/operator-manual/declarative-setup/#helm-chart-repositories>`_ in the Argo CD documentation.

.. _`argocd-cm.yaml`: https://github.com/lsst-sqre/roundtable/blob/master/deployments/argo-cd/patches/argocd-cm.yaml
