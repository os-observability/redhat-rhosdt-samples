# Tempo deployment

This is an example about how to deploy a Tempo instance using the Tempo Operator. It receives traces in OTLP format and stores them in a `minio` instance.

## Explanation
This Tempo configuration defines an instance that stores the traces in a `minio` instance. The `minio` instance is created and deployed in the `tempo.yaml` file. The `minio-test` secret contains the values needed to connect to the `minio` instance, and it is provided to the Tempo instance throug the `.spec.storage.secret.name` field.

It also enables the creation of the Jaeger UI `.spec.template.queryFrontend.jaegerQuery.enabled`.
