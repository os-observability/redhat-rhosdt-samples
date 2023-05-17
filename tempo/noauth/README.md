# Tempo deployment

This is an example to deploy a Tempo instance using the Tempo Operator. It doesn't have authorization nor multitenancy enabled.

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
