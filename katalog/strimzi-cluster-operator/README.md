# Strimzi Cluster Operator

<!-- <KFD-DOCS> -->

Strimzi provides a way to run an Apache Kafka® cluster on Kubernetes or OpenShift in various deployment configurations.
See our [Strimzi website][strimzi-website] for more details about the project.

## Requirements

- Kubernetes >= `1.16.0`
- Kustomize = `v3.5.4`
- [prometheus-operator][prometheus-operator]

> Prometheus Operator is necessary since we configure a `PodMonitor` to make
> some metrics available from `Strimzi` on prometheus

## Image repository and tag

* Strimzi Operator image: `registry.sighup.io/fury/strimzi/operator:0.31.1`
* Strimzi Kafka images: `registry.sighup.io/fury/strimzi/kafka:0.31.1-kafka-{3.1.0,3.1.1,3.1.2,3.2.0,3.2.1,3.2.3}`
* Strimzi Kafka bridge: `registry.sighup.io/fury/strimzi/kafka-bridge:0.22.1`
* Strimzi JMXTrans: `registry.sighup.io/fury/strimzi/jmxtrans:0.31.1`
* Strimzi Kaniko executor: `registry.sighup.io/fury/strimzi/kaniko-executor:0.31.1`
* Strimzi Maven builder: `registry.sighup.io/fury/strimzi/maven-builder:0.31.1`

## Configuration

The Strimzi operator is deployed to manage Kafka clusters in all namespaces.

## Deployment

You can deploy the Strimzi operator with the following command:

```bash
kustomize build . | kubectl apply -f -
```

<!-- Links -->

[strimzi-website]: https://strimzi.io/
[prometheus-operator]: https://github.com/sighupio/fury-kubernetes-monitoring/tree/main/katalog/prometheus-operator

<!-- </KFD-DOCS> -->