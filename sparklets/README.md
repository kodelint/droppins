# Apache Spark Helm Chart for AWS

Apache Spark is a fast and general-purpose cluster computing system including Apache Zeppelin, Livy and Airflow.

* [Spark](http://spark.apache.org/)
* [Zepplin](https://zeppelin.apache.org/)
* [Airflow](https://airflow.apache.org/)
* [Livy](https://livy.apache.org/)

## Chart Details
This chart will do the following:

* 1 x Spark Master with port `8080` exposed on an external `LoadBalancer`
* 2 x Spark Workers with `HorizontalPodAutoscaler` to scale to max 10 pods when CPU hits 50% of 100m. A `LoadBalancer` on these worker machines exposes ports 8080, 8888, 8889 and 4040 for purposes such as monitoring or serving spark-serving endpoints.
* 1 x Zeppelin pod (Optional) with `HorizontalPodAutoscaler` to scale to max 10 pods when CPU hits 50% of 100m on port `8080` exposed on an external `LoadBalancer`
* 1 x Livy pod (Optional) with port `8998` exposed on an external `LoadBalancer`
* 1 x Airflow Web with port `8080` exposed on an external `LoadBalancer`
* 1 x Airflow Scheduler internal
* 1 x Airflow Worker internal
* All using Kubernetes Deployments

### Docker Images
Airflow the componenets here are `docker` container. To see the code for container `images`
* [Airflow on Azure](./../k8s-airflow-with-spark-azure)
* [Airflow on Aws](./../k8s-airflow-with-spark-aws)

## Installing the Chart

To install the chart with the release name `my-release`:

```bash
$ helm install --name my-release spark-aws -n <<namespace>>
```



Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`.

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```bash
$ helm install --name my-release -f values.yaml stable/spark
```

> **Tip**: You can use the default [values.yaml](values.yaml)

## Running Spark Jobs

There are two ways to sumbit jobs to the spark cluster.
1. Using `Airflow` create your `dags` and make sure they are in the `docker image`

2. Using the zeppelin pod. The zeppelin pod exists in the cluster and already has its ports readily visible to the master and works pods and as such can run apps in both client and cluster mode. In addition, the pod contains a running instance of the zepplin notebook on port 8080  that can be used to run spark scripts in either scala, python, or as bash scripts.  To access the notebook look for the `spark-zeppelin` service under service on your dashboard.

3. Using Livy. See https://livy.incubator.apache.org/docs/latest/. The livy rest endpoint is exposed by the livy loadbalancer service. By default, livy uses the `spark-master` pod as the master and livy jobs can be monitored from either the `spark-webui` loadbalancer service on port `8080` or the livy ui endpoint.

## Upgrading

To upgrade to this chart from 0.2.2 you will have to update the docker images to point the images in `values.yaml`. The dockerfiles for these images are here https://github.com/Azure/mmlspark/tree/master/tools/helm/docker_images.  To enable livy, you will need to create a livy deployment with a load balancer and optional pod scaler as described in `spark-livy-deployment.yaml` and `spark-livy-hpa.yaml`. Ensure that your livy pods have the right `SPARK_MASTER` environment variables.
