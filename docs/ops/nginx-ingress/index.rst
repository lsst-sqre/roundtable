##################################
nginx-ingress app deployment guide
##################################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |nginx-ingress-status|
   * - Edit on GitHub
     - `/deployments/nginx-ingress <https://github.com/lsst-sqre/roundtable/tree/master/deployments/nginx-ingress>`__
   * - Type
     - Helm_
   * - Parent app
     - :doc:`roundtable <../roundtable/index>`

.. rubric:: Overview

This Argo CD Application deploys and configures `nginx-ingress <https://github.com/kubernetes/ingress-nginx>`__ from the `stable Helm chart repository <https://github.com/helm/charts/tree/master/stable/nginx-ingress>`__.

.. rubric:: Upgrading

While the nginx-ingress Helm chart tries to do a seamless upgrade, there is a possibility something could go wrong and bring down the ingress.
If this happens, the normal `Argo CD dashboard <https://cd.roundtable.lsst.codes/>`__ will become inaccessible.
Some care is therefore required when updating to a new version of this Helm chart.

Follow the instructions to :doc:`access Argo CD via port forwarding <../argo-cd/port-forward>` before beginning.
Bring up the Argo CD dashboard via port forwarding before merging the pull request to update the ingress.
You will then hopefully be able to use the normal Argo CD dashboard to fix any problems with the upgrade or roll back if needed.

.. rubric:: Ingress public IP

The nginx-ingress needs to bind to a stable public IP address which matches the DNS entries for Roundtable configured in AWS Route 53.
In GCE, this is done by reserving a static IP address for the correct region in the underlying Google Cloud account, and then configuring the ingress to request that IP address.
The latter step is done in `the values file for nginx-ingress <https://github.com/lsst-sqre/roundtable/blob/master/deployments/nginx-ingress/values.yaml>`__ by setting ``controller.service.loadBalacerIP``.

If there is ever a need to reserve a new static IP address in GCP, see `the Google documentation <https://cloud.google.com/compute/docs/ip-addresses/reserve-static-external-ip-address>`__.
If we have lost the external IP and need to allocate a new one, it is possible to promote whatever ephemeral IP address is currently bound to Roundtable to a reserved static IP.
Instructions are in that same Google documentation page.

If the IP address ever changes, at least the following DNS records in AWS Route 53 also need to be updated:

- roundtable.lsst.codes
- cd.roundtable.lsst.codes
- grpc.cd.roundtable.lsst.codes
- monitoring.roundtable.lsst.codes
- keeper.lsst.codes
- vault.lsst.codes
- vault-1.lsst.codes
- vault-2.lsst.codes

Searching for the old IP address on the Route 53 hosted domain page for lsst.codes is the best way to find any records.
The Route 53 console breaks the records up into multiple pages of results.
Be sure to check all pages!
