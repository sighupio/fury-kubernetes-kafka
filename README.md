<h1>
    <img src="https://github.com/sighupio/fury-distribution/blob/master/docs/assets/fury-epta-white.png?raw=true" align="left" width="90" style="margin-right: 15px"/>
    Kubernetes Fury Kafka
</h1>

![Release](https://img.shields.io/badge/Latest%20Release-v1.0.0-blue)
![License](https://img.shields.io/github/license/sighupio/fury-kubernetes-kafka?label=License)
![Slack](https://img.shields.io/badge/slack-@kubernetes/fury-yellow.svg?logo=slack&label=Slack)

<!-- <KFD-DOCS> -->

**Kubernetes Fury Kafka** provides the Kafka add-on for [Kubernetes Fury Distribution (KFD)][kfd-repo].

If you are new to KFD please refer to the [official documentation][kfd-docs] on how to get started with KFD.

## Overview

**Kubernetes Fury Kafka** uses the Strimzi operator to install and manage Kafka clusters in a Kubernetes environment.

All the components are deployed in the `XXX` namespace in the cluster.

## Packages

The following packages are included in the Fury Kubernetes Logging katalog:

| Package                                                | Version  | Description                                                                                             |
|--------------------------------------------------------|----------|---------------------------------------------------------------------------------------------------------|
| [x](katalog/x)                             | `x`  | x           |

Click on each package to see its full documentation.

## Compatibility

| Kubernetes Version |   Compatibility    | Notes                                               |
|--------------------|:------------------:|-----------------------------------------------------|
| `1.22.x`           | :white_check_mark: | No known issues                                     |
| `1.23.x`           | :white_check_mark: | No known issues                                     |
| `1.24.x`           | :white_check_mark: | No known issues                                     |

Check the [compatibility matrix][compatibility-matrix] for additional information about previous releases of the modules.

## Usage

### Prerequisites

| Tool                        | Version   | Description                                                                                                                                                    |
|-----------------------------|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [furyctl][furyctl-repo]     | `>=0.6.0` | The recommended tool to download and manage KFD modules and their packages. To learn more about `furyctl` read the [official documentation][furyctl-repo].     |
| [kustomize][kustomize-repo] | `>=3.5.3` | Packages are customized using `kustomize`. To learn how to create your customization layer with `kustomize`, please refer to the [repository][kustomize-repo]. |

### Deployment

1. List the packages you want to deploy and their version in a `Furyfile.yml`

```yaml
bases:
  - name: kafka/x
    version: "v1.0.0"
```

> See `furyctl` [documentation][furyctl-repo] for additional details about `Furyfile.yml` format.

2. Execute `furyctl vendor -H` to download the packages

3. Inspect the download packages under `./vendor/katalog/logging`.

4. Define a `kustomization.yaml` that includes the `./vendor/katalog/kafka` directory as resource.

```yaml
resources:
- ./vendor/katalog/kafka/x
```

5. To deploy the packages to your cluster, execute:

```bash
kustomize build . | kubectl apply -f -
```

### Common Customisations

#### Setup a high-availability three-node opensearch

Logging module offers an out of the box, highly-available setup for `opensearch` instead of a single node version. To set this up, in the `Furyfile` and `kustomization`, you can replace `opensearch-single` with `opensearch-triple`.

<!-- Links -->

[strimzi-page]: https://strimzi.io
[kfd-repo]: https://github.com/sighupio/fury-distribution
[furyctl-repo]: https://github.com/sighupio/furyctl
[kustomize-repo]: https://github.com/kubernetes-sigs/kustomize
[kfd-docs]: https://docs.kubernetesfury.com/docs/distribution/
[compatibility-matrix]: https://github.com/sighupio/fury-kubernetes-kafka/blob/main/docs/COMPATIBILITY_MATRIX.md

<!-- </KFD-DOCS> -->

<!-- <FOOTER> -->

## Contributing

Before contributing, please read first the [Contributing Guidelines](docs/CONTRIBUTING.md).

### Reporting Issues

In case you experience any problem with the module, please [open a new issue](https://github.com/sighupio/fury-kubernetes-kafka/issues/new/choose).

## License

This module is open-source and it's released under the following [LICENSE](LICENSE)

<!-- </FOOTER> -->