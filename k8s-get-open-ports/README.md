# k8s-get-open-ports Helm Chart
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

k8s-get-open-ports project, is a monitoring system. It collects all [nodePorts](https://kubernetes.io/docs/concepts/services-networking/service/#type-nodeport) from the cluster where it resides at given intervals and show the result in JSON format in the logs of the job.

This chart bootstraps k8s-get-open-ports on a [Kubernetes](http://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

## Features
(or rather: limitations)

- Reports all [nodePorts](https://kubernetes.io/docs/concepts/services-networking/service/#type-nodeport) from the cluster in Json format
- You could change the interval of the report as you desire
- Ability of setup a whitelist of ports to ignore them on the report
- Easy to export to metrics system as [ElasticSearch](https://www.elastic.co/es/what-is/elasticsearch)
- No support of reports persistance. If you need this feature you could use third-party data collector tools like [FileBeat](https://www.elastic.co/es/beats/filebeat) or [Fluentd](https://www.fluentd.org/). See [Enhance k8s-get-open-ports with data collectors](#enhance-k8s-get-open-ports-with-data-collectors) 

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

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

| Parameter                                | Description                                                                                                | Default         |
|------------------------------------------|------------------------------------------------------------------------------------------------------------|-----------------|
| cronjobs.name                            | Name of generated containers                                                                               | get-open-ports  |
| cronjobs.spec.successfulJobsHistoryLimit | Amount of successful jobs you want to keep in your cluster                                                 | 1               |
| cronjobs.spec.failedJobsHistoryLimit     | Amount of failed jobs you want to keep in your cluster                                                     | 2               |
| cronjobs.spec.schedule                   | Interval in Cron schedule expression. You can generate your own expresions [here](https://crontab.guru/)   | " */1 * * * * " |
| cronjobs.ignorePorts                     | List of ports you want to ignore in the report. You can use single ports or ranges. [See examples below](#example-with-custom-values)      | []              |
| serviceAccount.create                    | Define if you want to create a Service Account with the release or if you want to use an existing Service Account | true            |
| serviceAccount.name                      | Name of the Service Account                                                                                |                 |

> Please, be aware that if you set `serviceAccount.create` to `false` you must provide a [Service Account](https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/) with enough permissions to get and list all services in the cluster. Also, the [Service Account](https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/) MUST be on the same Namespace since CronJobs can only use [Service Accounts](https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/) in the same namespace.

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

## Enhance k8s-get-open-ports with data collectors

You could implement persistance of the reports or metrics using data collectors as [FileBeat](https://www.elastic.co/es/beats/filebeat) or [Fluentd](https://www.fluentd.org/). These tools allows you to setup persistance or even decode the Json at the reports to send it to an [ElasticSearch](https://www.elastic.co/es/what-is/elasticsearch) as indexes, ready to be displayed with Dashboarding tools as [Grafana](https://grafana.com/) or [Kibana](https://www.elastic.co/es/kibana/).

#### Example of filebeat configuration:
```yaml
filebeat.autodiscover:
  providers:
    - type: kubernetes
      node: ${NODE_NAME}
      templates:
        - condition:
            contains:
              kubernetes.container.name: "get-open-ports"
          config:
            - type: container
              paths:
                - "/var/log/containers/*-${data.kubernetes.container.id}.log"
              json.keys_under_root: true
              json.add_error_key: true
output.elasticsearch:
  host: '${NODE_NAME}'
  hosts: '${ELASTICSEARCH_HOSTS:elasticsearch-master:9200}'
```
This configuration decodes reports Json as indexes and send it to an [ElasticSearch](https://www.elastic.co/es/what-is/elasticsearch).

## License

<!-- Keep full URL links to repo files because this README syncs from main to gh-pages.  -->
[Apache 2.0 License](https://github.com/hecklawert/k8s-get-open-ports/blob/main/LICENSE).

## Author

Héctor Gutiérrez Fernández
