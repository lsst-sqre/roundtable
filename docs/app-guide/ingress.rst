###################
Defining an Ingress
###################

If your Roundtable application has to accept HTTP requests from the outside world, you need to define an ingress for it.
There are two options: Give your application its own subdomain of ``roundtable.lsst.codes``, or add a route to ``roundtable.lsst.codes``.

Using a Subdomain
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
     annotations:
       kubernetes.io/ingress.class: nginx
       certmanager.k8s.io/issuer: roundtable-letsencrypt
   spec:
     tls:
       - hosts:
           - <fqdn>
         secretName: <app>-ingress-tls
     rules:
       - host: <fqdn>
         http:
           paths:
             - path: /
               backend:
                 serviceName: <app-name>
                 servicePort: <app-port>

Replace ``<app-name>`` with the name of your application, ``<app-port>`` with the port on which it's running, and ``<fqdn>`` with the domain name to use (which should be a subdomain of ``lsst.codes`` and normally of ``roundtable.lsst.codes``).
For Kustomize, don't forget to include this ingress definition in ``kustomization.yaml``.

You do not need to define your own issuer.
You can use the shared ``roundtable-letsencrypt`` issuer, which will use the ACME HTTP validator.

Using a Route
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
