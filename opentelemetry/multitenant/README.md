# Multitenant

Overall, this configuration sets up an OpenTelemetry collector deployed as a `Deployment` that receives Jaeger, OTLP, OpenCensus and Zipkin traces, adds Kubernetes attributes, and exports the traces to Tempo via OTLP managing multiple tenants.

The OpenTelemetry Collector accepts traces from two tenants: `dev` and `prod`. The way to identify the tenants is through the `X-Scope-OrgID` OTLP header. When the `X-Scope-OrgID` header is set to `dev` in the trace, the tenant is `dev`. When the `X-Scope-OrgID` header is set to `prod` in the trace, the tenant is `prod`.

This example can be deployed with the [`tempo/auth`](../../tempo/auth/) example to use it as backend for tracing storage. The tenant needs permissions to the `tempo.grafana.com` API Group to write traces. You can follow this example to know how to provide write permissions to the `tenant-sa` `ServiceAccount` to write traces for the `dev` tenant:
```yaml
# Create the Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tenant-sa
  namespace: other-namespace
---
# ClusterRole needed to grant permissions to the service account to write traces
# for the given tenant
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: tempostack-traces-write
rules:
  - apiGroups:
      - 'tempo.grafana.com'
    # Tenant name set in X-Scope-OrgID
    resources:
      - dev
    resourceNames:
      - traces
    verbs:
      - 'create'
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tempostack-traces
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: tempostack-traces-write
subjects:
  - kind: ServiceAccount
    name: tenant-sa
    namespace: otel-collector-example
```
Now, you can use the `tenant-sa` `ServiceAccount` in your`OpenTelemetry Collector` instance to write traces for the `dev` tenant.

