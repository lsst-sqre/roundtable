########################################
GitHub webhook configuration for Argo CD
########################################

By configuring webhooks, Argo CD can be triggered to resync as soon as a new configuration is pushed to GitHub.

The webhook is configured directly on the lsst-sqre/roundtable application: https://github.com/lsst-sqre/roundtable/settings/hooks/139148573

The secret for the webhook is configured in the ``argocd-secret`` Kubernetes resource in the ``argocd`` namespace.
