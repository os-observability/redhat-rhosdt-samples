# Sidecar

Overall, this configuration sets up an OpenTelemetry collector deployed as a sidecar that receives Jaeger, OTLP, OpenCensus and Zipkin traces, adds Kubernetes attributes, and exports the traces to Tempo via OTLP.

The collector is configured to run as a sidecar in the cluster. This means that each pod with the `sidecar.opentelemetry.io/inject: "true"` annotation will be injected with a sidecar container to treat the traces according to the receivers, processors and exporters.

## How to run
1. Deploy the resources from the `otelcol.yaml` manifest file:
    ```sh
    oc apply -f otelcol.yaml
    ```
2. Deploy your application using a service account
3. Add permissions for the service account (replace `SERVICE ACCOUNT NAME` with the name of the service account used for your application).
    ```sh
    kubectl apply -f - <<EOF
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
      name: otel-collector-deployment
      namespace: otel-collector-example
    roleRef:
      kind: ClusterRole
      name: otel-collector
      apiGroup: rbac.authorization.k8s.io
    EOF
    ```
    These resources are needed to make the `resourcedetection` work properly on OpenShift.
