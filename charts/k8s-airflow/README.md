# Airflow / Celery

[Airflow](https://airflow.apache.org/) is a platform to programmatically author, schedule and
monitor workflows.

## Install Chart

To install the Airflow Chart into your Kubernetes cluster :

```bash
helm install --namespace "airflow" --name "airflow" ./k8s-airflow
```

After installation succeeds, you can get a status of Chart

```bash
helm status "airflow"
```

If you want to delete your Chart, use this command:

```bash
helm delete  --purge "airflow"
```

### Helm ingresses

The Chart provides ingress configuration to allow customization the installation by adapting
the `values.yaml` depending on your setup.
Please read the comments in the `values.yaml` file for more details on how to configure your reverse
proxy or load balancer.

### Chart Prefix

This Helm automatically prefixes all names using the release name to avoid collisions.

### Airflow connections

Connections define how your Airflow instance connects to environment and 3rd party service providers.
This helm chart allows you to define your own connections at the time of Airflow initialization.
For each connection the id and the type has to be defined. All other properties are optional.

Example:
```yaml
airflow:
  connections:
  - id: my_aws
    type: aws
    extra: '{"aws_access_key_id": "**********", "aws_secret_access_key": "***", "region_name":"eu-central-1"}'
```

Note: As connections may require to include sensitive data - the resulting script is stored encrypted in a kubernetes secret and mounted into the airflow scheduler container. It is probably wise not to put connection data in the default values.yaml and instead create an encrypted my-secret-values.yaml. this way it can be decrypted before the installation and passed to helm with -f <my-secret-values.yaml>
