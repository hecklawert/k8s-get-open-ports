# k8s-get-open-ports Helm Chart

* TODO

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
