## QuickStart in Kubernetes

## Prerequisites

- [Helm](https://helm.sh/) 3.1.0+
- [Kubernetes](https://kubernetes.io/) 1.12+
- PV provisioner support in the underlying infrastructure

## Installing the Chart

To install the chart with the release name `dolphinscheduler`:

```bash
# download source code package by wget
$ wget https://apache.website-solution.net/incubator/dolphinscheduler/1.3.5/apache-dolphinscheduler-incubating-1.3.5-src.zip
# download source code package by curl
$ curl -O https://apache.website-solution.net/incubator/dolphinscheduler/1.3.5/apache-dolphinscheduler-incubating-1.3.5-src.zip
$ unzip apache-dolphinscheduler-incubating-1.3.5-src.zip
$ mv apache-dolphinscheduler-incubating-1.3.5-src-release dolphinscheduler-src
$ cd dolphinscheduler-src/docker/kubernetes/dolphinscheduler
$ helm repo add bitnami https://charts.bitnami.com/bitnami
$ helm dependency update .
$ helm install dolphinscheduler .
```

To install the chart with a namespace named `test`:

```bash
$ helm install dolphinscheduler . -n test
```

> **Tip**: If a namespace named `test` is used, the option `-n test` needs to be added to the `helm` and `kubectl` command

These commands deploy DolphinScheduler on the Kubernetes cluster in the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

## Access DolphinScheduler UI

If `ingress.enabled` in `values.yaml` is set to `true`, you just access `http://${ingress.host}/dolphinscheduler` in browser.

> **Tip**: If there is a problem with ingress access, please contact the Kubernetes administrator and refer to the [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)

Otherwise, you need to execute port-forward command like:

```bash
$ kubectl port-forward --address 0.0.0.0 svc/dolphinscheduler-api 12345:12345
$ kubectl port-forward --address 0.0.0.0 -n test svc/dolphinscheduler-api 12345:12345 # with test namespace
```

> **Tip**: If the error of `unable to do port forwarding: socat not found` appears, you need to install `socat` at first

And then access the web: http://192.168.xx.xx:12345/dolphinscheduler

The default username is `admin` and the default password is `dolphinscheduler123`

## Uninstalling the Chart

To uninstall/delete the `dolphinscheduler` deployment:

```bash
$ helm uninstall dolphinscheduler
```

The command removes all the Kubernetes components but PVC's associated with the chart and deletes the release.

To delete the PVC's associated with `dolphinscheduler`:

```bash
$ kubectl delete pvc -l app.kubernetes.io/instance=dolphinscheduler
```

> **Note**: Deleting the PVC's will delete all data as well. Please be cautious before doing it.

## Configuration

The Configuration file is `values.yaml`, and the [DolphinScheduler Kubernetes Configuration](https://github.com/apache/incubator-dolphinscheduler/blob/1.3.5-release/docker/kubernetes/dolphinscheduler/README.md) lists the configurable parameters of the DolphinScheduler chart and their default values.

## FAQ

### How to use MySQL as the DolphinScheduler's database instead of PostgreSQL?

Not yet supported, the version 1.3.6 will support

### How to support MySQL datasource in `Datasource manage`?

> Because of the commercial license, we cannot directly use the driver of MySQL.
>
> If you want to add MySQL datasource, you can build a new image based on the `apache/dolphinscheduler` image as follows.

1. Download the MySQL driver [mysql-connector-java-5.1.49.jar](https://repo1.maven.org/maven2/mysql/mysql-connector-java/5.1.49/mysql-connector-java-5.1.49.jar) (require `>=5.1.47`)

2. Create a new `Dockerfile` to add MySQL driver:

```
FROM apache/dolphinscheduler:latest
COPY mysql-connector-java-5.1.49.jar /opt/dolphinscheduler/lib
```

3. Build a new docker image including MySQL driver:

```
docker build -t apache/dolphinscheduler:mysql-driver .
```

4. Push the docker image `apache/dolphinscheduler:mysql-driver` to a docker registry

5. Modify image `registry` and `repository`, and update `tag` to `mysql-driver` in `values.yaml`

6. Run a DolphinScheduler release in Kubernetes (See **Installing the Chart**)

7. Add a MySQL datasource in `Datasource manage`

### How to support Oracle datasource in `Datasource manage`?

> Because of the commercial license, we cannot directly use the driver of Oracle.
>
> If you want to add Oracle datasource, you can build a new image based on the `apache/dolphinscheduler` image as follows.

1. Download the Oracle driver [ojdbc8.jar](https://repo1.maven.org/maven2/com/oracle/database/jdbc/ojdbc8/) (such as `ojdbc8-19.9.0.0.jar`)

2. Create a new `Dockerfile` to add Oracle driver:

```
FROM apache/dolphinscheduler:latest
COPY ojdbc8-19.9.0.0.jar /opt/dolphinscheduler/lib
```

3. Build a new docker image including Oracle driver:

```
docker build -t apache/dolphinscheduler:oracle-driver .
```

4. Push the docker image `apache/dolphinscheduler:oracle-driver` to a docker registry

5. Modify image `registry` and `repository`, and update `tag` to `oracle-driver` in `values.yaml`

6. Run a DolphinScheduler release in Kubernetes (See **Installing the Chart**)

7. Add a Oracle datasource in `Datasource manage`

For more information please refer to the [incubator-dolphinscheduler](https://github.com/apache/incubator-dolphinscheduler.git) documentation.
