[Helm chart]&[Bitnami for Azure Kubernetes Service] Wavefront Adapter for Istio is an adapter for Istio to expose Istio metrics to Wavefront     
https://artifacthub.io/packages/helm/bitnami-aks/wavefront-adapter-for-istio

Оригинальный репозиторий.   
https://github.com/vmware-archive/wavefront-adapter-for-istio     


# Wavefront by VMware Adapter for Istio

[![CircleCI](https://img.shields.io/circleci/project/github/vmware/wavefront-adapter-for-istio/master.svg?logo=circleci)](https://circleci.com/gh/vmware/wavefront-adapter-for-istio)
[![Docker Pulls](https://img.shields.io/docker/pulls/vmware/wavefront-adapter-for-istio.svg?logo=docker)](https://hub.docker.com/r/vmware/wavefront-adapter-for-istio/)
[![Slack](https://img.shields.io/badge/slack-join%20chat-e01563.svg?logo=slack)](https://code.vmware.com/web/code/join)
[![License](https://img.shields.io/badge/license-Apache--2.0-blue.svg)](LICENSE)

<img alt="Wavefront by VMware" src="docs/images/logo.png">

Wavefront by VMware Adapter for Istio is an adapter for [Istio](https://istio.io)
to publish metrics to [Wavefront by VMware](https://www.wavefront.com/).


**Note:** The `master` branch is used for active development and can become
unstable. Please refer to the [Quick Start](https://github.com/vmware/wavefront-adapter-for-istio/tree/0.1.5#quick-start)
from version [0.1.5](https://github.com/vmware/wavefront-adapter-for-istio/releases/tag/0.1.5)
to install a stable version of the adapter.

## Quick Start

This adapter could be installed either via [Helm](#helm-installation) or via the
[standard](#standard-installation) method.

### Helm Installation

[Helm](https://helm.sh/) is the preferred way of installing this adapter. Please
see the [Helm Hub](https://hub.helm.sh/charts/wavefront/wavefront-adapter-for-istio) to learn to install
this adapter using Helm.

### Standard Installation

#### Prerequisites

To deploy this adapter, you will need a cluster with the following setup.

* Kubernetes v1.15+
* Istio v1.4 or v1.5 or v1.6 or v1.7

**Note:** From Istio v1.5.x onwards `Mixer` is disabled by default. Enable `Mixer` with the following step:

##### Istio v1.5.x
```console
istioctl manifest apply --set values.telemetry.v1.enabled=true --set values.telemetry.v2.enabled=false --set components.telemetry.enabled=true --set components.citadel.enabled=true
```

##### Istio v1.6.x or v1.7.x
```console
istioctl install --set values.telemetry.v1.enabled=true --set values.telemetry.v2.enabled=false --set components.telemetry.enabled=true

# Use below command if Istio is deployed in non default namespace, replace <NAMESPACE> with namespace name into which Istio is deployed.
istioctl install --set values.telemetry.v1.enabled=true --set values.telemetry.v2.enabled=false --set components.telemetry.enabled=true --set values.global.istioNamespace=<NAMESPACE>
```

#### Configuration

1\. Download the configuration.

```console
$ curl -LO https://raw.githubusercontent.com/vmware/wavefront-adapter-for-istio/0.1.5/install/config.yaml
```

2\. If you want the metrics to be published to the Wavefront instance directly,
supply the `direct` params for the `wavefront-handler` like so:

```yaml
params:
  direct:
    server: https://YOUR-INSTANCE.wavefront.com
    token: YOUR-API-TOKEN
```

Instructions for generating an API token can be found in the Wavefront by VMware
[docs](https://docs.wavefront.com/wavefront_api.html#generating-an-api-token).

If you want the metrics to be published to the Wavefront Proxy instead, supply
the `proxy` params like below:

```yaml
params:
  proxy:
    address: YOUR-PROXY-IP:YOUR-PROXY-PORT
```

3\. It is recommended that you update the `source` attribute to a reasonable
value, for example, to your cluster name.

```yaml
params:
  ...
  source: my-cluster
```

4(Optional)\. If Istio is deployed in non default namespace replace `istio-system` with namespace name into which Istio is deployed.

**Example:** Change `namespace: istio-system` to `namespace: istio-demo`, `handler: wavefront-handler.istio-system` to `handler: wavefront-handler.istio-demo`

See the [reference docs](https://istio.io/docs/reference/config/policy-and-telemetry/adapters/wavefront/)
for the available configuration parameters.

#### Deployment

##### Installation

Execute the following command to configure the Istio Mixer to publish metrics to
Wavefront using this adapter. This step must be performed after deploying
[Istio](https://istio.io/docs/setup/kubernetes/quick-start/).

```console
$ kubectl apply -f config.yaml
```

You should now be able to see Istio metrics on Wavefront under your configured
source (or `istio` by default).

##### Uninstallation

To uninstall this adapter, use the following command.

```console
$ kubectl delete -f config.yaml
```

## Contributing

Please see [CONTRIBUTING.md](CONTRIBUTING.md) if you'd like to contribute.


## Troubleshooting

- Check Istio adapter logs for errors `kubectl logs wavefront-xxxxxxx-xxxx -n wavefront-istio`.
- Check if `Mixer` is running `kubectl -n istio-system get service istio-telemetry`. If the pod `istio-telemetry` is not running then enable the `Mixer`.
- If Wavefront proxy is configured with the adapter then check proxy logs for errors `kubectl logs wavefront-adapter-for-istio-proxy-xxxxxxx-xxxx -n wavefront-istio`.

## License

Wavefront by VMware Adapter for Istio is licensed under the Apache License,
Version 2.0. See [LICENSE](LICENSE) for the full license text. Also, see the
[open_source_licenses](open_source_licenses) file for the full license text from
the packages used in this project.
