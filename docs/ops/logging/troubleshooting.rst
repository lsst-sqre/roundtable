#############################
Troubleshooting Elasticsearch
#############################

Memory limits
=============

The most frequent problem with Elasticsearch is that some component will run out of memory.
The Helm chart for Open Distro for Elasticsearch allocates 512MB of memory.
I have no idea how this could work; we ran into constant problems with that little of memory.

Running out of memory will cause strange symptoms, such as large portions of the UI simply not working or producing cryptic JavaScript errors.
Looking at the logs of the underlying components may show you error messages talking about the "circuit breaker."
This indicates an attempted memory allocation is larger than the available memory.

There are two parameters to tweak for memory allocations: the ``javaOpts`` setting for a component and the ``resources`` setting.
``resources`` controls is the total memory that Kubernetes will request for the pod.
Generally the limit and the request should be set to the same thing.
The memory allocated by Java, controlled by the ``-Xms`` and ``-Xmx`` flags, cannot be as large as the requested memory because there is some overhead.
On-line estimates of that overhead vary, but it's probably about 500MB.
These settings therefore need to be updated in lockstep.
If you set the Java memory allocation too high for the pod memory limit, the component will fail to start and go into a crash loop.

JavaScript errors
=================

In Chrome, I have sometimes seen parts of the UI not load, often with no error and sometimes with cryptic empty JavaScript errors.
By using the JavaScript console, that turned out to be due to 413 HTTP (Payload Too Large) errors from Elasticsearch.
This apparently happens if one indexes a data source with too many fields.

There are some possible fixes on the web (Google for that error and Elasticsearch) involving tweaking configuration settings, but I am inclined to write it off as bad data.
I worked around it by selectively indexing data and leaving out older indices.
I think it may be due to old corrupt data, and therefore may be fixed by expiring old data.
