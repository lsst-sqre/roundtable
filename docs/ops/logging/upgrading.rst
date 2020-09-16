#######################
Upgrading Elasticsearch
#######################

Since the ``opendistro-es`` chart is not uploaded to any Helm repository, we have to periodically check if there are upstream changes and import a new version into the charts repository.
We also have to check for new releases of Open Distro for Elasticsearch and bump the version number in our copy of the chart.
From there, WhiteSource Renovate will notice the new published version and create a PR for the Roundtable repository.

``fluentd-elasticsearch`` is a properly-maintained Helm chart that is monitored by WhiteSource Renovate.
It can be upgraded by merging the normal Renovate PRs.

Upgrading fluentd
=================

A ``fluentd-elasticsearch`` upgrade is normally smooth provided that the Elasticsearch cluster is up.
It deploys agents on every node in the cluster and restarts them one at a time, so it can take some time to complete.

Upgrading opendistro-es
=======================

An ``opendistro-es`` upgrade can cause more problems.
Sometimes new versions of the Helm chart will want to make changes to deployments that cannot be patched into the existing deployment.
In this case, you will need to go to the Argo CD console, delete the deployments, and let Argo CD recreate them.

There are four deployments: ``logging-opendistro-es-master``, ``logging-opendistro-es-data``, ``logging-opendistro-es-client``, and ``logging-opendistro-es-kibana``.
The restart is timing-sensitive, since the cluster forms around the nodes seen in a short window of time.
Therefore, if you need to remake the deployments, recreate (via sync) the ``logging-opendistro-es-master`` and ``logging-opendistro-es-data`` services together or in close succession.
Once those services are up and stable, recreate ``logging-opendistro-es-client``, and then finally ``logging-opendistro-es-kibana``.
Be aware that it will take some time for Kibana to start, since it recompiles its JavaScript on each restart.

The health checks for these deployments are sadly worthless.
They will show green even if the service is not healthy.
Checking the logs is more reliable.
The logs for ``logging-opendistro-es-kibana`` in particular will include messges about red or yellow status if the cluster is not working properly.
You can then track down the problem by looking at the logs of the other pods.
``logging-opendistro-es-master`` is normally the most informative.

If you have to recreate the persistent storage used by Elasticsearch, you will need to recreate an index.
To do this, click on the gear icon in the Kibana sidebar and then select **Index Patterns**.
If there are no patterns shown in the resulting screen, select **Create index pattern**.
Use ``*`` as the index pattern and then, on the subsequent screen, use ``@timestamp`` as the timestamp field.
Then, go to **Discover** in the sidebar (the top icon below the thin grey line) and do a search and you should see some data.
