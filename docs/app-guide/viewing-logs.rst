########################
Viewing logs with Kibana
########################

Roundtable includes its own logging infrastructure.
Your application's log messages to stdout and stderr are automatically captured by fluentd, stored in ElasticSearch, and made available with Kibana.
This page will help you use Kibana to view your app's log messages.

Step 1: Log into Kibana
=======================

1. Open https://roundtable.lsst.codes/logs in your browser.

2. Log in with GitHub if needed.

Step 2: Explore with the Discover tab
=====================================

The best place to start exploring log messages is in the Discover tab: https://roundtable.lsst.codes/logs/app/kibana#/discover.

By default you can see every log message from across the entire Roundtable cluster.
Here are a few tips to help filter the log stream down to just the messages you're interested in:

- **You can filter messages based on the values you see.**
  To do that, mouse over the log messages.
  A magnifying glass appears:

  - **If the message has a value you're interested in,** click the magnifying glass with a "+" symbol inside it.
    Now only messages containing that value are shown.

  - **If the message is one you're not interested in,** click the magnifying glass with a "-" symbol inside it.
    Now messages containing that value aren't shown.

- **Create filters based on Kubernetes labels.**
  For example, a partial pod resource might look like this:

  .. code-block:: yaml

     apiVersion: v1
     kind: Pod
     metadata:
       labels:
         name: myapp

  You can select log messages this pod, and ones like it in a Deployment, based on the ``name: myapp`` label:

  1. Click on the **+ Add filter** button, located below the search bar.

  2. For **field,** enter ``kubernetes.labels.name`` (change as appropriate for the label).

  3. For **operator,** select "``is``."
     Alternatively, "``is in``" is useful for combining multiple log sources together.

  4. For **value,** enter the value of the label (``myapp``, in this example).

- **Create filters based on Kubernetes namespaces** using the field ``kubernetes.namespace_name``, following the same technique as above.

- **Enter a plain text search.**
  If you're looking for something specific, type it into the search box.

Learning more
=============

The official `Kibana Guide <https://www.elastic.co/guide/en/kibana/current/index.html>`__ is a great resource for learning more about Kibana.
