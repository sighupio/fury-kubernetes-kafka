# Monitoring Configs

<!-- <KFD-DOCS> -->

This package provides the monitoring configurations for the Strimzi Kafka Operator.

## Requirements

- Kubernetes >= `1.16.0`
- Kustomize = `v3.5.4`
- [Kubernetes Fury Monitoring][kubernetes-fury-monitoring]

## Deployment

You can deploy the package with the following command:

```bash
kustomize build . | kubectl apply -f -
```

<!-- Links -->

[Kubernetes Fury Monitoring]: https://github.com/sighupio/fury-kubernetes-monitoring

<!-- </KFD-DOCS> -->