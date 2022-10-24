# Kafka Standard

This example folder will deploy a Kafka cluster with the following characteristics:

- 3 Zookeeper nodes
- 3 Kafka nodes
- 1 Entity operator pod
- 1 Kafka Exporter pod

Also, a namespace called `sighup` will be created.

## Deploy

To deploy the Kafka cluster, run the following command:

```bash
kustomize build . | kubectl apply -f -
```

You need to have the `strimzi-cluster-operator` and `monitoring-configs` packages installed.

## Example topics

In this cluster, we enabled the `entityOperator` to manage the topics and users. 

See the file `topics.yaml` to see an example of how to create topics.

## Monitoring

We are also adding monitoring to the Kafka Cluster, using pod-monitors and enabling KafkaExporter.

## Custom configurations

Additionally in this example we are customizing the listeners to make the Kafka Cluster reachable from outside the cluster
using NodePorts.

# Conclusions

This example shows how to deploy a Kafka cluster using the Strimzi Operator, with a comprehensive monitoring configuration.
Before using in production, check all the values and configurations for your use case.