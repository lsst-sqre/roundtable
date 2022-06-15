###############################
monitoring app deployment guide
###############################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |monitoring-status|
   * - Edit on GitHub
     - `/deployments/monitoring <https://github.com/lsst-sqre/roundtable/tree/master/deployments/monitoring>`__
   * - Type
     - Helm_
   * - Parent app
     - :doc:`roundtable <../roundtable/index>`

.. rubric:: Overview

Monitoring is a Helm-managed Argo CD application that deploys an
InfluxDB v2 server and a Chronograf server connected to the InfluxDB v2
server.  The InfluxDB v2 implementation receives telemetry from RSP
applications across all clusters.

Since Chronograf can only use InfluxDB v2 via a compatibility API, every
Influx DB v2 bucket must have a corresponding database retention policy; the
application also includes a CronJob that watches InfluxDB v2 and creates
any missing Database Retention Policy mappings for new buckets it finds.

.. rubric:: Adding a bucket for application monitoring

Each time a new RSP application namespace is added (we usually have a
1:1 mapping of ArgoCD application to namespace), we want to create a
bucket for telemetry data from that application.  This must be done
through the InfluxDB v2 UI, and currently it must be done as ``admin``
(we see no point in adding local users; Chronograf allows us a similar
UI with GitHub authentication, and we expect that eventually InfluxDB v2
will have pluggable authentication).

Log in to https://monitoring.lsst.codes as ``admin`` (the password is in
1Password), click on ``Data`` in the upper left, then the ``Buckets``
tab on the top.  Click ``Create Bucket`` and add a bucket whose name is
the namespace of the new RSP application, with any dashes replaced by
underscores (for instance, the application ``production-tools`` would
get the bucket name ``production_tools``).  Give it a retention policy
of 7 days.

Data will automatically begin flowing to the bucket, and within 15
minutes, the bucket will have a DBRP created for it, as well as a task
to monitor pod restarts within the application.  You can explore
its telemetry via the Chronograf interface at
https://monitoring.lsst.codes/chronograf (use GitHub to authenticate).

