# Deployment

Overall, this configuration sets up an OpenTelemetry collector deployed as a sidecar that receives Jaeger, OTLP, OpenCensus and Zipkin traces, adds Kubernetes attributes, and exports the traces to Tempo via OTLP.

## How to run
1. Deploy the resources from the `otelcol.yaml` manifest file:
    ```sh
    oc apply -f otelcol.yaml
    ```
2. Deploy your application using a service account
3. Add permissions for the service account (replace `SERVICE ACCOUNT NAME` with the name of the service account used for your application).
    ```yaml
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
    metadata:
    name: otel-collector
    namespace: otel-collector-example
    rules:
    - apiGroups: ["", "config.openshift.io"]
    resources: ["pods", "namespaces", "infrastructures"]
    verbs: ["get", "watch", "list"]
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRoleBinding
    metadata:
    name: otel-collector
    subjects:
    - kind: ServiceAccount
    name: <SERVICE ACCOUNT NAME>
    namespace: otel-collector-example
    roleRef:
    kind: ClusterRole
    name: otel-collector
    apiGroup: rbac.authorization.k8s.io
    ```


## Explanation
This example configuration defines an OpenTelemetry Collector in an OpenShift environment. The collector is configured to run as a sidecar in the cluster. This means that each pod with the `sidecar.opentelemetry.io/inject: "true"` annotation will be injected with a sidecar container to treat the traces according to the receivers, processors and exporters.

The collector is configured with a receiver for Jaeger traces, OpenCensus traces over the OpenCensus protocol, Zipkin traces over the Zipkin protocol and OTLP traces over the GRPC protocol


It also has different processors:
* [`batch`](https://github.com/open-telemetry/opentelemetry-collector/blob/main/processor/batchprocessor): batching helps better compress the data and reduce the number of outgoing connections required to transmit the data.
* [`memory_limiter`](https://github.com/open-telemetry/opentelemetry-collector/tree/main/processor/memorylimiterprocessor): prevents out of memory situations on the collector.
* [`resourcedetectionr`](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor/resourcedetectionprocessor): adds information detected from the host to the traces.

Keep in mind that the sequence of the processors determines how the data is processed.
