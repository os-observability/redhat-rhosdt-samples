# Deployment

Overall, this configuration sets up an OpenTelemetry collector deployed as a Deployment that receives Jaeger, OTLP, OpenCensus and Zipkin traces, adds Kubernetes attributes, and exports the traces to Tempo via OTLP.

## Explanation
This example configuration defines an OpenTelemetry Collector in an OpenShift environment. The collector is configured to run as a deployment in the cluster. The number of replicas will be determined by the `.spec.replicas` parameter or the parameters set in `.spec.autoscaler`.

The collector is configured with a receiver for Jaeger traces, OpenCensus traces over the OpenCensus protocol, Zipkin traces over the Zipkin protocol and OTLP traces over the GRPC protocol.

It also has different processors:
* [`batch`](https://github.com/open-telemetry/opentelemetry-collector/blob/main/processor/batchprocessor): batching helps better compress the data and reduce the number of outgoing connections required to transmit the data.
* [`k8sattributes`](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor/k8sattributesprocessor): adds Kubernetes attributes to the spans based on the node they originated from.
* [`memory_limiter`](https://github.com/open-telemetry/opentelemetry-collector/tree/main/processor/memorylimiterprocessor): prevents out of memory situations on the collector.
* [`resourcedetectionr`](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor/resourcedetectionprocessor): adds information detected from the host to the traces. This example enables two detectors: `env` (reads resource information from the `OTEL_RESOURCE_ATTRIBUTES` env variable) and `openshift` (reads from the OpenShift API to retrieve information).

Keep in mind that the sequence of the processors determines how the data is processed.
