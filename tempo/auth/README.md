# Tempo authentication

This is an example about how to deploy a Tempo instance using the Tempo Operator. It receives traces from two tenants (`prod` and `dev`).


The Tempo deployment accepts traces from two tenants: `dev` and `prod`. They way to identify the tenants is through the `X-Scope-OrgID` OTLP header. When the `X-Scope-OrgID` header is set to `dev` in the trace, the tenant is `dev`. When the `X-Scope-OrgID` header is set to `prod` in the trace, the tenant is `prod`.

It also enables the creation of the Jaeger UI `.spec.template.queryFrontend.jaegerQuery.enabled` and the gateway (`.spec.template.gateway.enabled`). You can get the URL to the UI for a given tenant with the following command:

```sh
echo https://$(oc get routes tempo-authentication-example-gateway -o jsonpath='{.spec.host}')/<tenant name>
```

For instance, for the `dev` tenant, it would be:
```sh
echo https://$(oc get routes tempo-authentication-example-gateway -o jsonpath='{.spec.host}')/dev
```

The tenant needs permissions to the `tempo.grafana.com` API Group to write traces. You can follow this example to know
how to provide write permissions to the `tenant-sa` `ServiceAccount` to write traces for the `dev` tenant:
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
    namespace: other-namespace
```

## How to run
1. Create an Object storage instance using [OpenShift Data Foundation](https://access.redhat.com/documentation/en-us/red_hat_openshift_data_foundation/).
1. Create an Object Storage secret with keys as follows:
    ```console
    kubectl create secret generic tempostack-odf \
      --from-literal=bucket="<BUCKET_NAME>" \
      --from-literal=endpoint="https://s3.openshift-storage.svc" \
      --from-literal=access_key_id="<ACCESS_KEY_ID>" \
      --from-literal=access_key_secret="<ACCESS_KEY_SECRET>"
    ```
1. Deploy the Tempo instance in the `tempo-example` OpenShift Project:
    ```sh
    kubectl create -f tempo.yaml
    ```
