# Strimzi Operator - Maintenance

This document describes how to maintain the Strimzi Operator.

Search for the latest version of the Kafka Operator here : [Strimzi Releases](https://github.com/strimzi/strimzi-kafka-operator/releases)

Get the manifests for the selected version, for example the version `0.31.1`:

- strimzi-cluster-operator-0.31.1.yaml

Overwrite the namespace with `kafka-operator` using (on MacOs) this command:

```bash
sed -i '' 's/namespace: .*/namespace: kafka-operator/' strimzi-cluster-operator-0.31.1.yaml
```

Then, check the differences with the current files:

- cluster-operator.yaml

Check all the updated images and sync/patch them with the https://github.com/sighupio/fury-distribution-container-image-sync
repository.