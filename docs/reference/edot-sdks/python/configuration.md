---
navigation_title: Configuration
description: Configure the Elastic Distribution of OpenTelemetry Python (EDOT Python) to send data to Elastic.
applies_to:
  stack:
  serverless:
    observability:
  product:
    edot_python: ga
products:
  - id: cloud-serverless
  - id: observability
  - id: edot-sdk
---

# Configure the EDOT Python agent

Configure the {{edot}} Python (EDOT Python) to send data to Elastic.

## Configuration method

Configure the OpenTelemetry SDK through the mechanisms [documented on the OpenTelemetry website](https://opentelemetry.io/docs/zero-code/python/configuration/). EDOT Python is typically configured with `OTEL_*` environment variables defined by the OpenTelemetry spec. For example:

```sh
export OTEL_RESOURCE_ATTRIBUTES=service.name=<app-name>
export OTEL_EXPORTER_OTLP_ENDPOINT=https://my-deployment.ingest.us-west1.gcp.cloud.es.io
export OTEL_EXPORTER_OTLP_HEADERS="Authorization=ApiKey P....l"
opentelemetry-instrument <command to start your service>
```

## Configuration options

Because the {{edot}} Python is an extension of OpenTelemetry Python, it supports both:

* [General OpenTelemetry configuration options](#opentelemetry-configuration-options)
* [Specific configuration options that are only available in EDOT Python](#configuration-options-only-available-in-edot-python)

### OpenTelemetry configuration options

EDOT Python supports all configuration options listed in the [OpenTelemetry General SDK Configuration documentation](https://opentelemetry.io/docs/languages/sdk-configuration/general/) and [OpenTelemetry Python](https://opentelemetry.io/docs/languages/python).

#### Logs

Instrument Python `logging` module to format and forward logs in OTLP format is turned off by default and gated under a configuration environment variable:

```sh
export OTEL_PYTHON_LOGGING_AUTO_INSTRUMENTATION_ENABLED=true
```

:::{note}
Turning this on will make any call to [logging.basicConfig](https://docs.python.org/3/library/logging.html#logging.basicConfig) from your application a no-op.
:::

#### Differences from OpenTelemetry Python

EDOT Python uses different defaults than OpenTelemetry Python for the following configuration options:

| Option | EDOT Python default | OpenTelemetry Python default |
|---|---|---|
| `OTEL_EXPERIMENTAL_RESOURCE_DETECTORS` | `process_runtime,os,otel,telemetry_distro,service_instance,_gcp,aws_ec2,aws_ecs,aws_elastic_beanstalk,azure_app_service,azure_vm` | `otel` |
| `OTEL_METRICS_EXEMPLAR_FILTER` | `always_off` | `trace_based` |
| `OTEL_EXPORTER_OTLP_METRICS_TEMPORALITY_PREFERENCE` | `DELTA` | `CUMULATIVE` |

:::{note}
`OTEL_EXPERIMENTAL_RESOURCE_DETECTORS` cloud resource detectors are dynamically set. When running in a Kubernetes Pod it will be set to `process_runtime,os,otel,telemetry_distro,service_instance,_gcp,aws_eks`.
:::


### Configuration options only available in EDOT Python

`ELASTIC_OTEL_` options are specific to Elastic and will always live in EDOT Python include the following.

| Option(s) | Default | Description |
|---|---|---|
| `ELASTIC_OTEL_SYSTEM_METRICS_ENABLED` | `false` | When sets to `true`, sends *system namespace* metrics. |

## LLM settings

LLM instrumentations implement the following configuration options:

| Option                                                | default | description               |
|-------------------------------------------------------|---------|:--------------------------|
| `OTEL_INSTRUMENTATION_GENAI_CAPTURE_MESSAGE_CONTENT`  | `false`| If set to `true`, enables the capturing of request and response content in the log events outputted by the agent.
