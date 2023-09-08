# Hancom Document Viewer

## Introduction

This chart bootstraps a Hancom Document Viewer deployment on a Kubernetes cluster using the Helm package manager.

It also packages the [Bitnami Redis chart](https://github.com/bitnami/charts/tree/main/bitnami/redis) which is required for bootstrapping a Redis deployment for the database requirements of the Hancom Document Viewer applications.

## Prerequisites

- Kubernetes 1.16+
- Helm 3.2.0+
- PV provisioner support in the underlying infrastructure
- ReadWriteMany volumes for deployment scaling

## Installing the Chart

1. If you have a license file, you need to create a ConfigMap using the file

```console
kubectl create configmap license --from-file=LicenseFile.dat
```
2. To install the chart with the release name `my-release`:

```console
helm repo add hancomio https://hancom-io.github.io/charts-release
helm repo update
helm install my-release hancomio/hancom-document-viewer --set documentviewerFilter.licenseConfigMap=license
```

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```console
helm delete my-release
```

## Parameters

### Common parameters
| Name                     | Description                                                           | Value           |
| ------------------------ | --------------------------------------------------------------------- | --------------- |
| `clusterDomain`          | Kubernetes Cluster Domain                                             | `cluster.local` |

### Ingress parameters

| Name                                 | Description                                               |  Value                      |
| ------------------------------------ | --------------------------------------------------------- | --------------------------- |
|`ingress.enabled`                     | If true, an Ingress is created	                           | `false`                     |
|`ingress.className`                   | The name of the Ingress Class associated with the ingress | `""`                        |
|`ingress.annotations`                 | Ingress annotations	                                   | `{}`                        |
|`ingress.hosts`                       | Hostnames to be covered with this ingress record          | See below                   |
|`ingress.hosts[0].host`               | Host for the Ingress rule                                 | `documentviewer.example.com`|
|`ingress.hosts[0].paths`              | Paths for the Ingress rule                                | See below                   |
|`ingress.hosts[0].paths[0].path`      | Path for the Ingress rule	                           | `/`                         |
|`ingress.hosts[0].paths[0].pathType`  | Path Type for the Ingress rule	                           | `Prefix`                    |
|`ingress.tls`                         | TLS configuration	                                   | []                          |

### DataProvider Job parameters

| Name                                                                  | Description                                                                                | Value              |
| --------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ | ------------------ |
| `dataProvider.podAnnotations`                                         | Annotations for the DataProvider pods	                                                     | `{}`               |
| `dataProvider.podSecurityContext`                                     | The DataProvider pods' Security Context                                                    | `{}`               |
| `dataProvider.dataProvider.env`                                       | The DataProvider container environment variables                                           | See below          |
| `dataProvider.dataProvider.env.sharedMountDirPath`                    | Mount path to pile shared files                                                            | `/mnt/shared`      |
| `dataProvider.dataProvider.env.templateMountDirPath`                  | Mount path to pile template files                                                          | `/mnt/template`    |
| `dataProvider.dataProvider.image.repository`                          | The DataProvider container image registry                                                  | `""`               |
| `dataProvider.dataProvider.image.tag`                                 | The DataProvider container image tag                                                       | `""`               |
| `dataProvider.dataProvider.securityContext`                           | The DataProvider containers' Security Context                                              | `{}`               |
| `dataProvider.dataProvider.imagePullSecrets`                          | The DataProvider image pull secrets                                                        | `[]`               |
| `dataProvider.dataProvider.imagePullPolicy`                           | The DataProvider image pull policy                                                         | `IfNotPresent`     |
| `dataProvider.dataProvider.resources.limits`                          | Pod resource limits                                                                        | `{}`               |
| `dataProvider.dataProvider.resources.requests`                        | Pod resource requests                                                                      | `{}`               |
| `dataProvider.nodeSelector`                                           | Node labels for pod assignment	                                                     | `{}`               |
| `dataProvider.tolerations`                                            | Tolerations for pod assignment	                                                     | `[]`               |
| `dataprovider.affinity`                                               | Affinity for pod assignment	                                                             | `{}`               |

### DocumentViewer Deployment parameters

| Name                                                                  | Description                                                                                | Value              |
| --------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ | ------------------ |
| `documentviewerFilter.replicas`                                       | Number of the DocumentViewer replicas to deploy	                                     | `3`                |
| `documentviewerFilter.licenseConfigMap`                               | Name of existing ConfigMap containing license data. The key value must be "LicenseFile.dat"| `""`               |
| `documentviewerFilter.podAnnotations`                                 | Annotations for the DocumentViewer pods	                                             | `{}`               |
| `documentviewerFilter.podSecurityContext`                             | The DocumentViewer pods' Security Context                                                  | `{}`               |
| `documentviewerFilter.documentviewerFilter.env`                       | The DocumentViewer container environment variables                                         | See below          |
| `documentviewerFilter.documentviewerFilter.env.hdvRedisDb`            | Redis DB name                                                                              | `"0"`              |
| `documentviewerFilter.documentviewerFilter.env.hdvRedisPassword`      | Redis passowrd ( **Need to set same value as `redis.auth.password`** )                     | `"redispass"`      |
| `documentviewerFilter.documentviewerFilter.env.hdvRedisPort`          | Redis port                                                                                 | `6379`             |
| `documentviewerFilter.documentviewerFilter.env.hdvRedisRetryCount`    | Connection retry count with Redis                                                          | `20`               |
| `documentviewerFilter.documentviewerFilter.env.hdvRedisRetryInterval` | Connection retry interval duration seconds                                                 | `3`                |
| `documentviewerFilter.documentviewerFilter.image.repository`          | The DocumentViewer container image registry                                                | `""`               |
| `documentviewerFilter.documentviewerFilter.image.tag`                 | The DocumentViewer container image tag                                                     | `""`               |
| `documentviewerFilter.documentviewerFilter.securityContext`           | The DocumentViewer containers' Security Context                                            | `{}`               |
| `documentviewerFilter.documentviewerFilter.imagePullSecrets`          | The DocumentViewer image pull secrets	                                                     | `[]`               |
| `documentviewerFilter.documentviewerFilter.imagePullPolicy`           | The DocumentViewer image pull policy	                                                     | `IfNotPresent`     |
| `documentviewerFilter.documentviewerFilter.resources.limits`          | Pod resource limits	                                                                     | `{}`               |
| `documentviewerFilter.documentviewerFilter.resources.requests`        | Pod resource requests                                                                      | `{}`               |
| `documentviewerFilter.service.port`                                   | Service port                                                                               | `8101`             |
| `documentviewerFilter.service.type`                                   | Service type	                                                                             | `ClusterIP`        |
| `documentviewerFilter.autoscaling.enabled`                            | Enable Horizontal POD autoscaling for the DocumentViewer                                   | `false`            |
| `documentviewerFilter.autoscaling.minReplicas`                        | Minimum number of DocumentViewer replicas	                                             | `""`               |
| `documentviewerFilter.autoscaling.maxReplicas`                        | Maximum number of DocumentViewer replicas	                                             | `""`               |
| `documentviewerFilter.autoscaling.targetCPUUtilizationPercentage`     | Target CPU utilization percentage	                                                     | `""`               |
| `documentviewerFilter.autoscaling.targetMemoryUtilizationPercentage`  | Target Memory utilization percentage	                                                     | `""`               |
| `documentviewerFilter.nodeSelector`                                   | Node labels for pod assignment	                                                     | `{}`               |
| `documentviewerFilter.tolerations`                                    | Tolerations for pod assignment	                                                     | `[]`               |
| `documentviewerFilter.affinity`                                       | Affinity for pod assignment	                                                             | `{}`               |

### Persistence Parameters

| Name                                         | Description                                           | Value              |
| -------------------------------------------- | ----------------------------------------------------- | ------------------ |
| `persistence.filterConf.storageClass`        | Persistent Volume storage class for configure files   | `""`               |
| `persistence.filterConf.size`                | Persistent Volume size	for configure files            | `300Mi`            |
| `persistence.filterConf.accessModes`         | Persistent Volume access modes	for configure files    | `["ReadWriteMany"]`|
| `persistence.filterDocsTemplate.storageClass`| Persistent Volume storage class for template files    | `""`               |
| `persistence.filterDocsTemplate.size`        | Persistent Volume size for template files             | `300Mi`            |
| `persistence.filterDocsTemplate.accessModes` | Persistent Volume access modes for template files     | `["ReadWriteMany"]`|
| `persistence.filterDocsUpload.storageClass`  | Persistent Volume storage class for uploaded files    | `""`               |
| `persistence.filterDocsUpload.size`          | Persistent Volume size for uploaded files             | `1Gi`              |
| `persistence.filterDocsUpload.accessModes`   | Persistent Volume access modes for uploaded files     | `["ReadWriteMany"]`|
| `persistence.filterResources.storageClass`   | Persistent Volume storage class for resources files   | `""`               |
| `persistence.filterResources.size`           | Persistent Volume size for resources files            | `1Gi`              |
| `persistence.filterResources.accessModes`    | Persistent Volume access modes for resources files    | `["ReadWriteMany"]`|
| `persistence.filterShared.storageClass`      | Persistent Volume storage class for shared files      | `""`               |
| `persistence.filterShared.size`              | Persistent Volume size for shared files               | `2Gi`              |
| `persistence.filterShared.accessModes`       | Persistent Volume access modes for shared files       | `["ReadWriteMany"]`|

### Database Parameters

| Name                                     | Description                                                                       | Value               |
| ---------------------------------------- | --------------------------------------------------------------------------------- | ------------------- |
| `redis.enabled`                          | Deploy a Redis server to satisfy the applications database requirements           | `true`              |
| `redis.architecture`                     | Redis architecture. Allowed values: `standalone` or `replication`                 | `replication`       |
| `redis.auth.enabled`                     | Enable password authentication	                                               | `true`              |
| `redis.auth.password`                    | Redis password ( **Need to set with your own password** )                         | `"redispass"`       |
| `redis.master.persistence.storageClass`  | Persistent Volume storage class for master	                                       | `""`                |
| `redis.master.persistence.size`          | Persistent Volume size for master	                                               | `1Gi`               |
| `redis.master.persistence.accessModes`   | Persistent Volume access modes for master	                                       | `["ReadWriteOnce"]` |
| `redis.replica.persistence.storageClass` | Persistent Volume storage class for replica                                       | `""`                |
| `redis.replica.persistence.size`         | Persistent Volume size for replica                                                | `1Gi`               |
| `redis.replica.persistence.accessModes`  | Persistent Volume access modes for replica                                        | `["ReadWriteOnce"]` |


## License

Licensed under the [Apache License, Version 2.0](LICENSE.txt)
