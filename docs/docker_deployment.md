# Docker

## Prerequisites

- Docker
- [Docker Compose](https://docs.docker.com/compose/install/#install-compose) v2.0.0+
- 5 GB of RAM

## Clone Repo

- Clone this repository:

```shell
git clone https://github.com/aliyun-sls/opentelemetry-demo.git
```

## Open Folder

- Navigate to the cloned folder:

```shell
cd opentelemetry-demo/
```

## Add AlibabaCloud LogService Configuration

Replace the variables in `otelcol-config-extras.yml` file with the actual values. For more information about the variables, see [Variables](https://www.alibabacloud.com/help/en/log-service/latest/import-trace-data-from-opentelemetry-to-log-service#table-wzj-90p-dvw).

## Run Docker Compose

- Start the demo:

```shell
docker compose up --no-build
```

> **Note:** If you're running on Apple Silicon, please run `docker compose
build` in order to create local images vs. pulling them from the repository.

**Note:** The `--no-build` flag is used to fetch released docker images from
[ghcr](http://ghcr.io/open-telemetry/demo) instead of building from source.
Removing the `--no-build` command line option will rebuild all images from
source. It may take more than 20 minutes to build if the flag is omitted.

## Verify the Webstore & the Telemetry

Once the images are built and containers are started you can access:

- Webstore: <http://localhost:8080/>
- Grafana: <http://localhost:8080/grafana/>
- Feature Flags UI: <http://localhost:8080/feature/>
- Load Generator UI: <http://localhost:8080/loadgen/>
- AlibabaCloud LogService UI: <https://sls.console.aliyun.com/lognext/trace>
