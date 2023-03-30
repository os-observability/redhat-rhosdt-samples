# DaemonSet

Overall, this configuration sets up an OpenTelemetry collector deployed as daemonset that receives Jaeger traces, adds Kubernetes attributes, and exports the traces to Tempo over OTLP.

## How to run
1. Create the `SecurityContextConstraints`: `oc apply -f scc.yaml`
1. Create the `otel-collector-daemonset` `ServiceAccount`: `oc apply -f sa.yaml`
1. Add the `SecurityContextConstraints` to the `ServiceAccount`: `oc adm policy add-scc-to-user -z otel-collector-daemonset daemonset-with-hostport`
1. Create the `ClusterRole` from `clusterrole.yaml`: `oc apply -f clusterrole.yaml`
1. Modify the namespace from the `ClusterRoleBinding` object from `clusterrolebinding.yaml` and deploy it: `oc apply -f clusterrolebinding.yaml`
1. Deploy the `OpenTelemetryCollector` instance: `oc apply -f otelcol.yaml`

## Explanation
This example configuration defines an OpenTelemetry Collector in an OpenShift environment. The collector is configured to run as a daemonset in the cluster, which means that one instance of the collector will be deployed to each node in the cluster.

The collector is configured with a receiver for Jaeger traces over the Thrift HTTP protocol.

It also has a processor that adds Kubernetes attributes to the spans based on the node they originated from. This filter is done to ensure your colector only retrieves pods from the node where the collector is installed. You allows you to not keep track of a long list of pods if you have a big cluster.

Finally, the collector is configured with an exporter that sends traces to the tempo-simplest-distributor endpoint over the OTLP protocol. TLS is enabled for this exporter, and it uses the server.crt, client.crt, and client.key files for authentication.

The `SecurityContextConstrains` object is required by OpenShift to allow the deployment of the daemonset. The `ClusterRole` and `ClusterRoleBinding` are [needed by the `k8sattributesprocessor`](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor/k8sattributesprocessor#role-based-access-control).
