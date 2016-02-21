# Update features of Kubernetes v1.1

## New features
### Daemon Set
A `Daemon Set` ensures that all nodes run a copy of a pod. As nodes are added to the cluster, pods are added to them. As nodes are removed from the cluster, those pods are garbage collected. Deleting a Daemon Set will clean up the pods it created.

Typical use cases:
- Running a cluster storage daemon, such as *glusterd*, *ceph*, etc on each node.
- Running a logs collection daemon on every node, such as *fluentd* or *logstash*
- Running a node monitoring daemon on every node, such as *collectd*, New Relic agent or Ganglia.

### Deployment
A `Deployment` provides declarative update for Pods and ReplicationControllers. Users describe the desired state in deployment object and deployment controller changes the actual state to that at a controlled rate. Users can define deployments to create new resources or replace existing ones by new ones.

Typical use cases:
- Create a deployment to bring up a replication controller and pods.
- Later, update that deployment to recreate the pods. (ex: to use a new image)

### Ingress Resource
Typically, service and pods have IPs only routable by the cluster network. All traffic that ends up at an edge router is either dropped or forwarded elsewhere. Conceptually, this might look like:

 Internet
     |
----------
[Services]

An ingress is a collection of rules that allow inbound connections to reach the cluster services.

 Internet
     |
[ Ingress ]
--|----|--
[ Services ]

It can be configured to give services externally-reachable urls, load balance traffic, terminate SSL, offer name based virtual hosting etc. Users request ingress by POSTing the Ingress resource to the API server. An `Ingress Controller` is responsible for fulfilling the Ingress, usually with a loadbalancer, though it may also configure your edge router or additional frontends to help handle the traffic in an HA manner.

A minimal Ingress might look like that:
```
01. apiVersion: extensions/v1beta1
02. kind: Ingress
03. metadata:
04.  name: test-ingress
05. spec:
06.  rules:
07.  - http:
08.      paths:
09.      - path: /testpath
10.        backend:
11.          serviceName: test
12.          servicePort: 80
```
Lines 01-04: As with all other Kubenetes config, an Ingress needs `apiVersion`, `kind`, and `metadata` fields.
Lines 05-07: Ingress spec has all the information needed to configure a loadbalancer or proxy server. Most importantly, it contains a list of rules matched against all incoming requests. Currently the Ingress resource only supports http rules.
Lines 08-09: Each http rule contains the following information: A host, a list of paths (eg: /testpath), each of which has an associated backend (test:80). Both the host and path must match the content of an incoming request before the loadbalancer directs traffic to the backend.
Lines 10-12: A backend is a service:port combination as described in the services doc. Ingress traffic is typically sent directly to the endpoints matching a backend.

### Horizontal Pod Autoscaler
It allows the number of pods in a replication controller or deployment to scale automatically based on observed CPU utilization.

### Job
A `job` creates one or more pods and ensures that a specified number of them successfully terminate. As pods successfully complete, the `job` tracks the successful completions. When a specified number of successful completions is reached, the job itself is complete. Deleting a job will cleanup the pods it created.

A simple case is to create one job object in order to reliably run on pod to completion. A job of course can be used to run multiple pods in parallel.

### Want more ?
https://github.com/kubernetes/kubernetes/tree/release-1.1/docs/design
