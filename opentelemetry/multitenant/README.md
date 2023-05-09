# Multitenant

Overall, this configuration sets up an OpenTelemetry collector deployed as a `Deployment` that receives Jaeger, OTLP, OpenCensus and Zipkin traces, adds Kubernetes attributes, and exports the traces to Tempo via OTLP managing multiple tenants.

The OpenTelemetry Collector receives traces and, depending on the value of the `X-Scope-OrgID` header, the traces are routed to the desired exporter. For this example, we just set the resources to create traces for the `dev` tenant.
