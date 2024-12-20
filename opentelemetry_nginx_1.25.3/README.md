# Nginx: OpenTelemetry Setup for Tracing

This project sets up the nginx with openTelemetry, work with  Opentelemetry Collector to receive, process, and export trace data using the OTLP protocol. The configuration is designed to work with Jaeger as the trace backend.

## Prerequisites

- Docker
- Docker Compose

## Nginx OpenTelemetry Module Setup

### Building Nginx with OpenTelemetry webserver

The Nginx OpenTelemetry module needs to be compiled with Nginx. This is reference site <https://opentelemetry.io/blog/2022/instrument-nginx/>

You must to use nginx and openTelemetry that compatibility

List of nginx version: <https://github.com/open-telemetry/opentelemetry-cpp-contrib/releases?q=webserver&expanded=true>

For example `Dockerfile`:

``` sh
FROM nginx:1.25.3
RUN apt-get update ; apt-get install unzip
ADD https://github.com/open-telemetry/opentelemetry-cpp-contrib/releases/download/webserver%2Fv1.0.4/opentelemetry-webserver-sdk-x64-linux.tgz /opt
RUN cd /opt ; unzip -o opentelemetry-webserver-sdk-x64-linux.tgz; tar xvfz opentelemetry-webserver-sdk-x64-linux.tgz
RUN cd /opt/opentelemetry-webserver-sdk; ./install.sh
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/opentelemetry-webserver-sdk/sdk_lib/lib   
```

`nginx:1.25.3` that support `webserver/v1.0.4`

### Otel webserver module configuration

File `nginx/opentelemetry_module.conf`

```sh
NginxModuleEnabled ON;
NginxModuleOtelSpanExporter otlp;
NginxModuleOtelExporterEndpoint collector:4317;
NginxModuleOtelSpanProcessor simple;
NginxModuleServiceName DemoService_1.25.3;
NginxModuleServiceNamespace DemoServiceNamespace;
NginxModuleServiceInstanceId DemoInstanceId;
NginxModuleResolveBackends ON;
NginxModuleTraceAsError ON;
```

More config: <https://github.com/open-telemetry/opentelemetry-cpp-contrib/tree/main/instrumentation/otel-webserver-module#configuration-1>

## Running

Running nignx, opentelemetry and jaeger

```sh
docker compose up
```

- nginx: <http://localhost:8080>
- jaeger: <http://localhost:16686>

trace name module reference <https://nginx.org/en/docs/>
