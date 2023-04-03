# DaemonSet

Overall, this configuration sets up an OpenTelemetry collector deployed as a DaemonSet that receives Jaeger traces, adds Kubernetes attributes, and exports the traces to Tempo via OTLP.

## How to run
1. Modify the namespace from the `otel-collector` `ClusterRoleBinding` from `daemonset-otelcol.yaml` to your namespace. Replace the `<OPENTELEMETRY_NAMESPACE>` string with the name of the desired namespace.
1. Deploy the resources from the `daemonset-otelcol.yaml` manifest file:
    ```sh
    oc apply -f daemonset-otelcol.yaml
    ```
1. Add the `SecurityContextConstraints` to the `ServiceAccount`:
    ```sh
    oc adm policy add-scc-to-user -z otel-collector-daemonset daemonset-with-hostport
    ```

## Explanation
This example configuration defines an OpenTelemetry Collector in an OpenShift environment. The collector is configured to run as a daemonset in the cluster, which means that one instance of the collector will be deployed to each node in the cluster.

The collector is configured with a receiver for Jaeger traces over the Thrift HTTP protocol, OpenCensus traces over the OpenCensus protocol, Zipkin traces over the Zipkin protocol and OTLP traces over the GRPC protocol.

It also has a processor that adds Kubernetes attributes to the spans based on the node they originated from. This filtering is done to ensure that your collector only retrieves pods from the node where the collector is installed. It allows you to avoid keeping track of a long list of pods if you have a large cluster.

Finally, the collector is configured with an exporter that sends traces to the tempo-simplest-distributor endpoint over the OTLP protocol. This exporter has TLS enabled and uses the `server.crt`, `client.crt`, and `client.key` files for authentication.

The `SecurityContextConstrains` object is required by OpenShift to allow the deployment of the daemonset. The `ClusterRole` and `ClusterRoleBinding` are [needed by the `k8sattributesprocessor`](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor/k8sattributesprocessor#role-based-access-control).

The `trusted-ca` `ConfigMap` is created [to contain a trusted CA](https://docs.openshift.com/container-platform/4.12/networking/configuring-a-custom-pki.html#certificate-injection-using-operators_configuring-a-custom-pki). This CA is mounted in the `OpenTelemetryCollector` instance using `Volumes`.
