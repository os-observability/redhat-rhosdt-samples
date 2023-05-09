# Tempo multitenant

This is an example about how to deploy a Tempo instance using the Tempo Operator. It receives traces from two tenants (`prod` and `dev`).

It also enables the creation of the Jaeger UI `.spec.template.queryFrontend.jaegerQuery.enabled` and the gateway (`.spec.template.gateway.enabled`).

The Tempo deployment accepts traces from two tenants: `dev` and `prod`. They way to authenticate the tenants is through the `X-Scope-OrgID` OTLP header. When the `X-Scope-OrgID` header is set to `dev` in the trace, the tenant is `dev`. When the `X-Scope-OrgID` header is set to `prod` in the trace, the tenant is `prod`.

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
