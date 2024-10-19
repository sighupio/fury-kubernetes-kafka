# Strimzi Operator - Maintenance

This document describes how to maintain the Strimzi Operator.

Search for the latest version of the Kafka Operator here : [Strimzi Releases](https://github.com/strimzi/strimzi-kafka-operator/releases)

Get the manifests for the selected version, for example the version `0.43.0`:

- strimzi-cluster-operator-0.43.0.yaml

Overwrite the namespace with `kafka-operator` using (on MacOs) this command:

```bash
sed -i '' 's/namespace: .*/namespace: kafka-operator/' strimzi-cluster-operator-0.43.0.yaml
```

Then, check the differences with the current files:

- cluster-operator.yaml

Check all the updated images and sync/patch them with the https://github.com/sighupio/fury-distribution-container-image-sync
repository. The patching is done via `kustomization.yaml` and `patch-images.yaml` files.

What was changed:

- STRIMZI_NAMESPACE environment variable set to `*` (to watch all namespaces).
- Rename ClusterRoleBinding `strimzi-cluster-operator` (with roleRef to the `strimzi-cluster-operator-global` ClusterRole) to `strimzi-cluster-operator-global`.
- Rename `strimzi-cluster-operator` RoleBinding (with roleRef to the `strimzi-cluster-operator-namespaced`) to `strimzi-cluster-operator-namespaced` and transform it to ClustRoleBinding (to watch all namespaces).
- Create `strimzi-cluster-operator-entity-operator-delegation` with roleRef to `strimzi-entity-operator` (to watch all namespaces).
- Transform `strimzi-cluster-operator-watched` to ClusterRoleBinding (to watch all namespaces).
- Increase `strimzi-cluster-operator` memory limit and request.
- Increase `strimzi-cluster-operator` replicas to `2`.

* * *

## Multi-version Upgrade steps of Strimzi Operator from v0.31.0 to v0.43.0

### Steps to perform a multi-version upgrade:

1. Ensure that the ```strimzi-cluster-operator``` has at least ```2``` replicas. 
   > Note that only one at a time will be elected as a **leader** and the relevant logs will be found there.

2. Put Strimzi ```cluster-operator-0.43.0.yaml``` and ```patch-images-0.43.0.yaml``` in the ```strimzi-cluster-operator``` directory.

   Run from the ```strimzi-cluster-operator``` directory:

   ```mv cluster-operator.yaml cluster-operator-0.31.0.yaml && mv cluster-operator-0.43.0.yaml cluster-operator.yaml```

   ```mv patch-images.yaml patch-images-0.31.0.yaml && mv patch-images-0.43.0.yaml patch-images.yaml```

   ```kustomize build | kubectl apply -f -```

3. After the `strinzi-cluster-operator` pods have been updated, an error will be shown in their logs about the Kafka unsupported version:    ```Exception: Unsupported Kafka.spec.kafka.version: 3.2.3. Supported versions are: [3.7.0, 3.7.1, 3.8.0]```.

   This error appears because the Strimzi operator `v0.43.0` does not support Kafka `v3.2.3`, which is the version used by the current `Kafka` resource.

   ```kubectl logs strimzi-cluster-operator-* -n kafka-operator | grep 'Exception: Unsupported'```

4. Check the image used by ```strimzi-cluster-operator```.

   ```kubectl get pod strimzi-cluster-operator-* -n kafka-operator -o jsonpath='{.spec.containers[0].image}'```

   It should use the image ```registry.sighup.io/fury/strimzi/operator:0.43.0```.

5. Change the ```spec.kafka.version``` of the Kafka resource to ```3.8.0```.
   
   > Example: update ```spec.kafka.version``` of ```kafka-cluster.yaml``` in ```examples/kafka-standard```.

   Apply the changes:

   ```kustomize build | kubectl apply -f -```

   The previous error ```Exception: Unsupported Kafka.spec.kafka.version: 3.2.3. Supported versions are: [3.7.0, 3.7.1, 3.8.0]``` at this point will disappear and the ```strimzi-cluster-operator``` will start the reconciliation with the Kafka resource.

   The update of ```zookeeper```, ```kafka```, ```exporter``` and ```entity-operator``` pods will be automatically triggered after the version update.

6. Wait for *two* rollouts of the Kafka resources to be completed, and check for their status.

   ```bash
   ~ % kubectl rollout status deploy sighup-kafka-cluster-kafka-exporter -n sighup
   deployment "sighup-kafka-cluster-kafka-exporter" successfully rolled out
   ```

   ```bash
   ~ % kubectl rollout status deploy sighup-kafka-cluster-entity-operator -n sighup
   deployment "sighup-kafka-cluster-entity-operator" successfully rolled out
   ```

   ```bash
   ~ % kubectl get strimzipodsets -n sighup
   NAME                             PODS   READY PODS   CURRENT PODS   AGE
   sighup-kafka-cluster-kafka       3      3            3              1h
   sighup-kafka-cluster-zookeeper   3      3            3              1h
   ```

7. Check the image used by ```zookeeper```, ```kafka```, ```exporter``` and ```entity-operator``` pods.  


   ```zookeeper```, ```kafka```, and ```exporter```pods should now use the image ```registry.sighup.io/fury/strimzi/kafka:0.43.0-kafka-3.8.0```. 

   ```shell
   kubectl get pod sighup-kafka-cluster-kafka-* -n sighup -o jsonpath='{.spec.containers[0].image}'

   kubectl get pod sighup-kafka-cluster-kafka-exporter-* -n sighup -o jsonpath='{.spec.containers[0].image}'

   kubectl get pod sighup-kafka-cluster-zookeeper-* -n sighup -o jsonpath='{.spec.containers[0].image}'
   ``` 

    The ```entity-operator``` pod should use the image ```registry.sighup.io/fury/strimzi/operator:0.43.0```.

    ```shell
    kubectl get pod sighup-kafka-cluster-entity-operator-* -n sighup -o jsonpath='{.spec.containers[0].image}'
    ```

8. Change the ```log.message.format.version``` of the Kafka resource to ```3.8.0```.

   > Example: update ```log.message.format.version``` of ```kafka-cluster.yaml``` in ```examples/kafka-standard```.

   Apply the changes:

   ```kustomize build | kubectl apply -f -```

9. Wait for the rollout of the Kafka resources, and check if the change has been correctly applied.

   > Example command for ```kafka-cluster.yaml``` in ```examples/kafka-standard```.
 
   ```kubectl get k sighup-kafka-cluster -n sighup -o jsonpath='{.spec.kafka.config.log\.message\.format\.version}'```

   The version should be ```3.8.0```.