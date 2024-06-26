##################################
ingress-nginx app deployment guide
##################################

.. list-table::
   :widths: 10,40

   * - Deployment
     - |ingress-nginx-status|
   * - Edit on GitHub
     - `/deployments/ingress-nginx <https://github.com/lsst-sqre/roundtable/tree/master/deployments/ingress-nginx>`__
   * - Type
     - Helm_
   * - Parent app
     - :doc:`roundtable <../roundtable/index>`

.. rubric:: Overview

This Argo CD Application deploys and configures `ingress-nginx <https://github.com/kubernetes/ingress-nginx>`__ from its `Helm chart repository <https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx>`__.

.. rubric:: Upgrading

While the ingress-nginx Helm chart tries to do a seamless upgrade, there is a possibility something could go wrong and bring down the ingress.
If this happens, the normal `Argo CD dashboard <https://cd.roundtable.lsst.codes/>`__ will become inaccessible.
Some care is therefore required when updating to a new version of this Helm chart.

Follow the instructions to :doc:`access Argo CD via port forwarding <../argo-cd/port-forward>` before beginning.
Bring up the Argo CD dashboard via port forwarding before merging the pull request to update the ingress.
You will then hopefully be able to use the normal Argo CD dashboard to fix any problems with the upgrade or roll back if needed.

.. rubric:: Ingress public IP

The ingress-nginx needs to bind to a stable public IP address which matches the DNS entries for Roundtable configured in AWS Route 53.
In GCE, this is done by reserving a static IP address for the correct region in the underlying Google Cloud account, and then configuring the ingress to request that IP address.
The latter step is done in `the values file for ingress-nginx <https://github.com/lsst-sqre/roundtable/blob/master/deployments/ingress-nginx/values.yaml>`__ by setting ``controller.service.loadBalacerIP``.

If there is ever a need to reserve a new static IP address in GCP, see `the Google documentation <https://cloud.google.com/compute/docs/ip-addresses/reserve-static-external-ip-address>`__.
If we have lost the external IP and need to allocate a new one, it is possible to promote whatever ephemeral IP address is currently bound to Roundtable to a reserved static IP.
Instructions are in that same Google documentation page.

If the IP address ever changes, at least the following DNS records in AWS Route 53 also need to be updated:

- roundtable.lsst.codes
- cd.roundtable.lsst.codes
- grpc.cd.roundtable.lsst.codes
- keeper.lsst.codes

Searching for the old IP address on the Route 53 hosted domain page for lsst.codes is the best way to find any records.
The Route 53 console breaks the records up into multiple pages of results.
Be sure to check all pages!
