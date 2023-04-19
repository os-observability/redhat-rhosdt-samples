# Tempo multitenant.

This is an example about how to deploy a Tempo instance using the Tempo Operator. It receives traces in OTLP format and stores them in a `minio` instance. It receives traces from two tenants (`prod` and `dev`).

## Explanation
This Tempo configuration defines an instance that stores the traces in a `minio` instance. The `minio` instance is created and deployed in the `tempo.yaml` file. The `minio-test` secret contains the values needed to connect to the `minio` instance, and it is provided to the Tempo instance throug the `.spec.storage.secret.name` field.

It also enables the creation of the Jaeger UI `.spec.template.queryFrontend.jaegerQuery.enabled` and the gateway (`.spec.template.gateway.enabled`).

The application accepts traces from two tenants: `dev` and `prod`. They way to authenticate the tenants is through the `X-Scope-OrgID` OTLP header. When the `X-Scope-OrgID` header is set to `1610b0c3-c509-4592-a256-a1871353dbfa` in the trace, the tenant is `dev`. When the `X-Scope-OrgID` header is set to `1610b0c3-c509-4592-a256-a1871353dbf` in the trace, the tenant is `prod`.
