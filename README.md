# Using the DockerOperator with the LocalExecutor in docker-compose.yaml

This guide will allow you to run the DockerOperator using the LocalExecutor with
Apache Airflow deployed on Docker Compose.

To deploy Airflow on Docker  with Docker Compose you should fetch the docker-compose.yaml:

```
curl -LfO 'https://airflow.apache.org/docs/apache-airflow/2.1.2/docker-compose.yaml'
```

For more information about running Airflow in Docker read the following
[documentation](https://airflow.apache.org/docs/apache-airflow/stable/start/docker.html).

## Preparing the docker-compose.yaml

First you need to adapt the standard docker-compose.yaml file to work with
the LocalExecutor and DockerOperator:

1. line 50: replace `CeleryExecutor` with `LocalExecutor`
2. line 58: add the `apache-airflow-providers-docker` package to `_PIP_ADDITIONAL_REQUIREMENTS`
3. comment out or delete lines: 53, 65-66, 85-94, 118-128, 140-150

You can now initialize the environment with `docker-compose up airflow-init`.

## Adding a new services in docker-compose.yaml

You have to add one additional service to the `docker-compose.yaml` to use the
DockerOperator.

```
# Required because of DockerOperator. For secure access and handling permissions.
docker-socket-proxy:
  image: tecnativa/docker-socket-proxy:0.1.1
  environment:
    CONTAINERS: 1
    IMAGES: 1
    AUTH: 1
    POST: 1
  privileged: true
  volumes:
    - /var/run/docker.sock:/var/run/docker.sock:ro
  restart: always
```

This service allows to mount the Docker socket more securely.
You can find more information about this service [here](https://github.com/Tecnativa/docker-socket-proxy).

## Creating a DAG file with the DockerOperator that connects to the Docker API using this proxy

Below is an example DAG that uses the DockerOperator:

```
curl -LfO 'https://raw.githubusercontent.com/apache/airflow/main/airflow/providers/docker/example_dags/example_docker.py'
```

Place this file inside the dags/ folder.

You have to modify the task `t3` that uses the DockerOperator for it to work with the
docker-socker-proxy:

* line 30: you have to replace `localhost` with `docker-socket-proxy`
* line 45: change the `api_version` to `1.30`
* line 47: change the command to `"echo TEST DOCKER SUCCESSFUL"`

## Testing connection
