# k8s-get-open-ports Helm Chart

k8s-get-open-ports project, is a monitoring system. It collects all [nodePorts](https://kubernetes.io/docs/concepts/services-networking/service/#type-nodeport) from the cluster where it resides at given intervals and show the result at JSON format in the logs of the job.

This chart bootstraps k8s-get-open-ports on a [Kubernetes](http://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

## Prerequisites

- Kubernetes 1.16+
- Helm 3+

## Get Repo Info

```console
helm repo add k8s-get-open-ports https://hecklawert.github.io/k8s-get-open-ports
helm repo update
```

_See [helm repo](https://helm.sh/docs/helm/helm_repo/) for command documentation._

## Installing the Chart

To install the chart with the release name `my-release`:

```console
helm install my-release k8s-get-open-ports/k8s-get-open-ports
```

## Uninstalling the Chart

To uninstall/delete the my-release deployment:

```console
helm delete my-release
```

## Configuration

The command removes all the Kubernetes components associated with the chart and deletes the release.

| Parameter                                | Description                                                                                                | Default         |
|------------------------------------------|------------------------------------------------------------------------------------------------------------|-----------------|
| cronjobs.name                            | Name of generated containers                                                                               | get-open-ports  |
| cronjobs.spec.successfulJobsHistoryLimit | Amount of successful jobs you want to keep in your cluster                                                 | 1               |
| cronjobs.spec.failedJobsHistoryLimit     | Amount of failed jobs you want to keep in your cluster                                                     | 2               |
| cronjobs.spec.schedule                   | Cron schedule expression You can generate your own expresions here                                         | " */1 * * * * " |
| cronjobs.ignorePorts                     | List of ports you want to ignore in the report. You can use single ports or ranges [See examples below](https://github.com/hecklawert/k8s-get-open-ports/blob/main/k8s-get-open-ports/README.md#example-with-custom-values)      | []              |
| serviceAccount.create                    | Define if you want create a Service Account with the release or if you want use a existing Service Account | true            |
| serviceAccount.name                      | Name of the Service Account                                                                                |                 |

### Example with custom values

Use a custom service connection:
```yaml
serviceAccount: 
  create: false
  name: my-service-account
```

Configure cronjobs:
```yaml
cronjobs:
  name: get-open-ports
  spec:
    successfulJobsHistoryLimit: 1
    failedJobsHistoryLimit: 2
    schedule: "*/1 * * * *"
  ignorePorts:
    - "30009"
    - "30010-30015"
```
