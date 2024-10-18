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

## Test the cluster

To test the cluster producing and consuming messages, you need to connect to the bootstrap address with a kafka client.

Check the address of one of your nodes:

```bash
kubectl get nodes -o wide

NAME       SNAME                 STATUS   ROLES           AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION    CONTAINER-RUNTIME
kafka-control-plane   Ready    control-plane   5m14s   v1.27.3   172.18.0.2    <none>        Debian GNU/Linux 11 (bullseye)   6.5.11-linuxkit   containerd://1.7.1
```

Create a ```producer``` and a ```consumer``` inside the cluster:

```bash
kubectl run -it kafka-producer --image=apache/kafka:latest -- sh

kubectl run -it kafka-consumer --image=apache/kafka:latest -- sh
```

Run the `kafka-producer-perf-test` from inside the `kafka-producer` pod for testing messages production:

```bash
/opt/kafka/bin/kafka-producer-perf-test.sh --throughput -1 --num-records 300000 --record-size 1024 --producer-props bootstrap.servers=<internal-ip>:32110 --topic example-topic-1

76396 records sent, 15279.2 records/sec (14.92 MB/sec), 1311.8 ms avg latency, 1931.0 ms max latency.
102060 records sent, 20412.0 records/sec (19.93 MB/sec), 1534.0 ms avg latency, 1883.0 ms max latency.
```

Run the `kafka-consumer-perf-test` from inside `kafka-consumer` pod for testing messages consumption:

```bash
/opt/kafka/bin/kafka-consumer-perf-test.sh --messages 30000 --broker-list <internal-ip>:32110 --topic example-topic-1

start.time: 2022-10-24 16:23:57:344
 end.time:  2022-10-24 16:24:01:054
 data.consumed.in.MB:  29.4385
 MB.sec:  7.9349
 data.consumed.in.nMsg:  30145
 nMsg.sec:  8125.3369
 rebalance.time.ms:  3435
 fetch.time.ms:  275
 fetch.MB.sec:  107.0490
 fetch.nMsg.sec:  109618.1818
```

# Conclusions

This example shows how to deploy a Kafka cluster using the Strimzi Operator, with a comprehensive monitoring configuration.
Before using in production, check all the values and configurations for your use case.