###################
Defining an ingress
###################

If your Roundtable application has to accept HTTP requests from the outside world, you need to define an ingress for it.
There are two options: Give your application its own subdomain of ``roundtable.lsst.codes``, or add a route to ``roundtable.lsst.codes``.

Using a subdomain
=================

Giving your application its own subdomain is the best option for applications that serve an entire web site.
Examples of applications like this are Argo CD and Grafana.
In this case, the application definition should manage its own ingress.

.. _Roundtable repository: https://github.com/lsst-sqre/roundtable

To do this, add an ingress definition to the ``resources`` subdirectory (for Kustomize) or the ``templates`` subdirectory (for Helm) in the deployment directory for your applicdation in the `Roundtable repository`_.
That ingress definition should look something like this:

.. code-block:: yaml

   apiVersion: extensions/v1beta1
   kind: Ingress
   metadata:
     name: <app-name>
     namespace: <app-namespace>
     annotations:
       kubernetes.io/ingress.class: nginx
       certmanager.k8s.io/issuer: <app-name>-letsencrypt
   spec:
     tls:
       - hosts:
           - <fqdn>
         secretName: <app-name>-ingress-tls
     rules:
       - host: <fqdn>
         http:
           paths:
             - path: /
               backend:
                 serviceName: <app-name>
                 servicePort: <app-port>

Replace ``<app-name>`` with the name of your application, ``<app-namespace>`` with the namespace the app is running in, ``<app-port>`` with the port on which it's running, and ``<fqdn>`` with the domain name to use (which should be a subdomain of ``lsst.codes`` and normally of ``roundtable.lsst.codes``).
If the app is running in the ``events`` namespace, set the ``certmanager.k8s.io/issuer`` annotation to ``roundtable-letsencrypt``.
Otherwise, you will also need to define an issuer in the same namespace as the ingress, as follows:

.. code-block:: yaml

   apiVersion: certmanager.k8s.io/v1alpha1
   kind: Issuer
   metadata:
     name: <app-name>-letsencrypt
   spec:
     acme:
       server: https://acme-v02.api.letsencrypt.org/directory
       email: sqre-admin@lists.lsst.org
       privateKeySecretRef:
         name: <app-name>-letsencrypt
       http01: {}

As above, replace ``<app-name>`` with the name of your application.

For Kustomize, don't forget to include the ingress definition, and the issuer definition if you needed one, in ``kustomization.yaml``.

Using a route
=============

Giving your application a route on ``roundtable.lsst.codes`` is most appropriate for small :abbr:`SOA (Service-Oriented Architecture)` services that provide an API rather than a web site.
Examples of applications like this are ghslacker and sqrbot-jr.
In this case, add the route for the application to the shared ingress defined in ``deployments/app-land/resources/roundtable-ingress.yaml``.

This ingress defines the ``roundtable.lsst.codes`` host and its routes.
Under ``spec.rules``, you will find an ``http.paths`` block.
Add the route definition for your service as an additional item in that list:

.. code-block:: yaml

   - path: /<app-name>
     backend:
       serviceName: <app-name>
       servicePort: <app-port>

Replace ``<app-name>`` with the name of your application and ``<app-port>`` with the port on which it's running.
The requirement that the path match the application name is not strictly required, but it is strongly preferred.
Using a path with a different name will require justification.

Your application **must** be deployed in the ``events`` namespace if it is using a route on ``roundtable.lsst.codes``.
(The reason for this is complex and will be documented in more detail later.
The root problem is that the ingress has to be in the same namespace as the service to which it is routing.)
