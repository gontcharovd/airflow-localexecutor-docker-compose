# Using the DockerOperator with the LocalExecutor in docker-compose.yaml

This guide will allow you to run te DockerOperator with the LocalExecutor using
the Apache Airflow deployed on Docker Compose.

To deploy Airflow on Docker Compose, you should fetch the docker-compose.yaml:

```
curl -LfO 'https://airflow.apache.org/docs/apache-airflow/2.1.2/docker-compose.yaml'
```

For more information about running Airflow in Docker read the following
[documentation](https://airflow.apache.org/docs/apache-airflow/stable/start/docker.html)

## Preparing the docker-compose.yaml

First you need to adapt the standard docker-compose.yaml file to work with
the LocalExecutor and DockerOperator:

1. Line 50: replace `CeleryExecutor` with `LocalExecutor`.
2. Line 58: add the `apache-airflow-providers-docker` package to `_PIP_ADDITIONAL_REQUIREMENTS`.
3. Comment out or delete lines: 53, 65-66, 85-94, 118-128, 140-150.

You can now initialize the environment with `docker-compose up airflow-init`.

## Adding a new services in docker-compose.yaml

## Creating a DAG file with the DockerOperator that connects to the Docker API using this proxy

## Testing connection
