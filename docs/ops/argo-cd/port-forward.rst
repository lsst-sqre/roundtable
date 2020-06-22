###################
Port-forward access
###################

When performing upgrades that may break external access to the Argo CD dashboard (such as an ngninx-ingress upgrade), you may want to connect via port-forwarding instead.

To create the port-forwarded connection, run:

.. code-block:: bash

   kubectl port-forward service/argocd-server -n argocd 8080:443

using a Kubernetes context with admin access to the Roundtable cluster.
You can then access the Argo CD UI via http://localhost:8080/.

Argo CD will not be able to use GitHub authentication or set cookies correctly with password authentication when used via port-forwarding.
To gain access, you will need to manually set a localhost ``argocd.token`` cookie matching the cookie you obtain for cd.roundtable.lsst.codes after successful authentication.

.. seealso::

   `Adding and modifying cookies in Chrome Developer Tools <https://thisinterestsme.com/modifying-cookies-developer-tools/>`__
