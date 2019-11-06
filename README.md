# helm-shield


[Shield](https://github.com/shieldproject/shield),

A standalone system that can perform backup and restore functions for a wide variety of pluggable data systems

## TL;DR;

```console
helm repo add  orange-cloudfoundry-shield  https://orange-cloudfoundry.github.io/helm-shield/
helm install orange-cloudfoundry-shield/shield-server  -n shield
```

## Introduction

This chart bootstraps a [Shield](https://github.com/shieldproject/shield) deployment on a [Kubernetes](http://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

## Prerequisites

- Kubernetes 1.3+ with Beta APIs enabled

## Installing the Chart

To install the chart with the release name `my-release`:

```console
git clone https://github.com/orange-cloudfoundry/helm-shield.git
cd core
helm dependency build
$  helm install shield-server-0.1.0.tgz  -n shield
```

The command deploys Shield core on the Kubernetes cluster in the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```console
$ helm delete my-release
```
pvc is need to be deleted manually

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Shield core

Shield core use Vault.

## Configuration

The following table lists the configurable parameters of the Prometheus chart and their default values.

Parameter | Description | Default
--------- | ----------- | -------
`replicaCount`|| `1`
`core.image.repository`|| `shieldproject/shield`
`core.image.tag`|| `8`
`cor.image.pullPolicy`|| `IfNotPresent`
`core.failsafe.username`|| `admin`
`core.failsafe.password`|| `adminp`
`vault.install`|If true, install vault | `true`
`vault.vault.address`|| `http://shield-vault:8200`
`vault.vault.token`|| `mytoken`
`service.type`|| `ClusterIP`
`service.port`|| `80`
`persistence.enabled`|If true, Shield core will create a Persistent Volume Claim| `true`
`persistence.accessMode`|Shield core Persistent Volume access modes| `ReadWriteOnce`
`persistence.size`|Shield core Persistent Volume size| `8Gi`
`persistence.labels`|Shield core Persistent Volume label| 
`persistence.storageClass`|Shield core Persistent Volume storage class| `standard`
`ingress.enabled`|If true, Shield core Ingress will be created| `false`
`ingress.annotations`|Shield core ingress annotation | 
`ingress.hosts`| Shield core Ingress hostnames |`[]`
`ingress.tls`|Shield core Ingress tls |`[]`
`resources`| Shield core pod resource requests & limits |`{}`
`nodeSelector`|| `{}`
`tolerations`|| `[]`
`affinity`|| `{}`

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```console
$ helm install shield-server-0.1.0.tgz --name my-release \
    --set server.terminationGracePeriodSeconds=360
```

Alternatively, a YAML file that specifies the values for the above parameters can be provided while installing the chart. For example,

```console
$ helm install shield-server-0.1.0.tgz --name my-release -f values.yaml
```

> **Tip**: You can use the default [values.yaml](values.yaml)

### Ingress TLS
If your cluster allows automatic creation/retrieval of TLS certificates (e.g. [kube-lego](https://github.com/jetstack/kube-lego)), please refer to the documentation for that mechanism.

To manually configure TLS, first create/retrieve a key & certificate pair for the address(es) you wish to protect. Then create a TLS secret in the namespace:

```console
kubectl create secret tls pshield-core-tls --cert=path/to/tls.cert --key=path/to/tls.key
```

Include the secret's name, along with the desired hostnames, in the alertmanager/server Ingress TLS section of your custom `values.yaml` file:

```yaml
server:
  ingress:
    ## If true, Shield core Ingress will be created
    ##
    enabled: true

    ## Prometheus server Ingress hostnames
    ## Must be provided if Ingress is enabled
    ##
    hosts:
      - shield-core.domain.com

    ## Prometheus server Ingress TLS configuration
    ## Secrets must be manually created in the namespace
    ##
    tls:
      - secretName: shield-core-tls
        hosts:
          - shield-core.domain.com
```
