# Tempo multitenant

This is an example about how to deploy a Tempo instance using the Tempo Operator. It receives traces from two tenants (`prod` and `dev`).

It also enables the creation of the Jaeger UI `.spec.template.queryFrontend.jaegerQuery.enabled` and the gateway (`.spec.template.gateway.enabled`).

The Tempo deployment accepts traces from two tenants: `dev` and `prod`. They way to authenticate the tenants is through the `X-Scope-OrgID` OTLP header. When the `X-Scope-OrgID` header is set to `dev` in the trace, the tenant is `dev`. When the `X-Scope-OrgID` header is set to `prod` in the trace, the tenant is `prod`.

## How to run
1. Deploy the `minio` instance:
    ```sh
        kubectl create -f minio.yaml
    ```
1. Deploy the Tempo instance in the `tempo-example` OpenShift Project:
    ```sh
        kubectl create -f tempo.yaml
    ```

> **Warning**
> `minio` is used here for demo purposes. For production purposes, you should use [OpenShift Data Foundation](https://www.redhat.com/en/technologies/cloud-computing/openshift-data-foundation), [AWS S3](https://aws.amazon.com/s3/), [Google Cloud Storage](https://cloud.google.com/storage) or [Azure Storage](https://learn.microsoft.com/azure/storage/common/storage-introduction).
