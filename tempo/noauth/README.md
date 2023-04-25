# Tempo deployment

This is an example about how to deploy a Tempo instance using the Tempo Operator. It doesn't have authorization nor multitenancy enabled.

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
