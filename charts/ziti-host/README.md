# Helm Chart ziti-host

## Why?

You may use this chart to publish cluster services to your Ziti network. For example, if you create a Ziti service with a server address of `tcp:kubernetes.default.svc:443` and write a Bind Service Policy assigning the service to the Ziti identity used with this chart, then your Ziti network's authorized clients will be able access this cluster's apiserver. You could do the same thing for any cluster service's domain name.

## How?

This chart deploys a pod running `ziti-edge-tunnel`, [the OpenZiti Linux tunneler](https://docs.openziti.io/docs/reference/tunnelers/linux/). The chart uses container image `docker.io/openziti/ziti-host` which runs `ziti-edge-tunnel run-host`. This puts the Linux tunneler in "hosting" mode which is useful for binding Ziti services without any need for elevated permissions and without any Ziti nameserver or intercepting proxy. You'll be able to publish any server that is known by an IP address or domain name that is reachable from the pod deployed by this chart.

## Installation

After adding the charts repo to Helm then you may install the chart. You must supply a Ziti identity JSON file when you install the chart.

```bash
helm install ziti-release03 openziti/ziti-host --set-file zitiIdentity=/tmp/k8s-tunneler-03.json
```

### Installation using a existing / pre-created secret

Alternatively when you want to use a existing / pre-created secret (i.e. you have sealed-secrets enabled in your setup), you could refer to an existing secret with the ziti identity to use.

This sample shows you how to create the secret:

```bash
kubectl create secret generic k8s-tunneler-03-identity --from-file=persisted-identity=k8s-tunneler-03.json
```

When you deploy the helm chart refer to the existing secret:

```bash
helm install ziti-release03 openziti/ziti-host --set secret.existingSecretName=k8s-tunneler-03-identity
```

When you don't want to use the default key name `persisted-identity` you can define your own name by adding `--set secret.keyName=myKeyName`.

## Namespaces

The `helm install` command accepts param `--namespace` and so you may install this chart separately for each namespace for purposes such as achieving network isolation of your Ziti-hosted cluster services.

## Developing this Chart

If you have downloaded this source code, then you may install the chart from a local source directory.

```bash
helm install {release} {source dir} --set-file zitiIdentity=/tmp/k8s-tunneler-03.json
```

If you change the chart's source files and wish to deploy with the same identity you need only bump the chart version in Chart.yaml and run:

```bash
helm upgrade {release} {source dir}
```