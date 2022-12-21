# Kubernetes

We provide a [OpenTelemetry Demo Helm
chart](https://github.com/open-telemetry/opentelemetry-helm-charts/tree/main/charts/opentelemetry-demo)
to help deploy the demo to an existing Kubernetes cluster.

[Helm](https://helm.sh) must be installed to use the charts.
Please refer to Helm's [documentation](https://helm.sh/docs/) to get started.

## Prerequisites

- Pre-existing Kubernetes Cluster
- Helm 3.0+

## Install the Chart

Add OpenTelemetry Helm repository:

```shell
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
```

**Replace variables in the `helm-values.yml` file at the root of this repository with the actual values, For more information about the variables, see [Variables](https://www.alibabacloud.com/help/en/log-service/latest/import-trace-data-from-opentelemetry-to-log-service#table-wzj-90p-dvw)**

To install the chart with the release name my-otel-demo, run the following command:

```shell
helm install my-otel-demo open-telemetry/opentelemetry-demo --values helm-values.yml
```

> **Note**
> The OpenTelemetry Demo Helm chart version 0.11.0 or greater is required to
> perform all usage methods mentioned below.

## Use the Demo

The demo application will need the services exposed outside of the Kubernetes
cluster in order to use them. You can expose the services to your local system
using the `kubectl port-forward` command or by configuring service types
(ie: LoadBalancer) with optionally deployed ingress resources.

### Expose services using kubectl port-forward

To expose the frontendproxy service use the following command (replace
`my-otel-demo` with your Helm chart release name accordingly):

```shell
kubectl port-forward svc/my-otel-demo-frontendproxy 8080:8080
```

In order for spans from the browser to be properly collected, you will also
need to expose the OpenTelemetry Collector's OTLP/HTTP port (replace
`my-otel-demo` with your Helm chart release name accordingly):

```shell
kubectl port-forward svc/my-otel-demo-otelcol 4318:4318
```

> **Note** > `kubectl port-forward` will proxy the port until the process terminates. You
> may need to create separate terminal sessions for each use of
> `kubectl port-forward`, and use CTRL-C to terminate the process when done.

With the frontendproxy and Collector port-forward set up, you can access:

- Webstore: <http://localhost:8080/>
- Grafana: <http://localhost:8080/grafana/>
- Feature Flags UI: <http://localhost:8080/feature/>
- Load Generator UI: <http://localhost:8080/loadgen/>
- Jaeger UI: <http://localhost:8080/jaeger/ui/>
- AlibabaCloud LogService UI: <https://sls.console.aliyun.com/lognext/trace>

### Expose services using service type configurations

> **Note**
> Kubernetes clusters may not have the proper infrastructure components to
> enable LoadBalancer service types or ingress resources. Verify your cluster
> has the proper support before using these configuration options.

Each demo service (ie: frontendproxy) offers a way to have its Kubernetes
service type configured. By default these will be `ClusterIP` but you can change
each one using the `serviceType` property for each service.

To configure the frontendproxy service to use a LoadBalancer service type you
would specify the following in your values file:

```yaml
components:
  frontendProxy:
    serviceType: LoadBalancer
```

> **Note**
> It is recommended to use a values file when installing the Helm chart in order
> to specify additional configuration options.

The Helm chart does not provide facilities to create ingress resources. If
required these would need to be created manually after installing the Helm chart.
Some Kubernetes providers require specific service types in order to be used by
ingress resources (ie: EKS ALB ingress, requires a NodePort service type).

In order for spans from the browser to be properly collected, you will also
need to expose the OpenTelemetry Collector's OTLP/HTTP port to be accessible to
user web browsers. The location where the OpenTelemetry Collector is exposed
must also be passed into the frontend service using the
`PUBLIC_OTEL_EXPORTER_OTLP_TRACES_ENDPOINT` environment variable. You can do
this using the following in your values file:

```yaml
components:
  frontend:
    env:
      - name: PUBLIC_OTEL_EXPORTER_OTLP_TRACES_ENDPOINT
        value: "http://otel-demo-collector.mydomain.com:4318/v1/traces"
```

To install the Helm chart with a custom `my-values-file.yaml` values file use:

```shell
helm install my-otel-demo open-telemetry/opentelemetry-demo --values my-values-file.yaml
```

With the frontendproxy and Collector exposed, you can access the demo UI at the
base path for the frontendproxy. Other demo components can be accessed at the
following sub-paths:

- Webstore: `/` (base)
- Grafana: `/grafana`
- Feature Flags UI: `/feature`
- Load Generator UI: `/loadgen/` (must include trailing slash)
- Jaeger UI: `/jaeger/ui`
