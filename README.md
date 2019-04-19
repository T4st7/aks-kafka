# aks-kafka

Three node kafka and three node zookeeper cluster. Kafka and Zookeeper can be scaled up at any time by modifying and re-applying configurations.

## login to aks
```bash
# login to aks cluster
$ az aks get-credentials --resource-group <aks-resource-group> --name <aks-name>
```

## mount clouddrive (if necessary)
You can mount a file share from a adls storage account (should the yml configs be stored there)

```bash
clouddrive mount -s <adls-subscription-id> -g <adls-resource-group> -f <file-share>
```

## apply configurations

### create namespace

```bash
# Create namespace
$ kubectl create -f ./000-namespace.yml
```

### create persistent volume and volumeClaim

```bash
# create persistent volume
$ kubectl -n <namespace> create -f ./000-pv-kafka.yml
# create persistent volumeClaim
$ kubectl -n <namespace> create -f ./000-pvc-kafka.yml
```

### create zookeeper pods

```bash
# create zookeeper service
$ kubectl -n <namespace> create -f ./110-zookeeper-service.yml

# create zookeeper headless service
$ kubectl -n <namespace> create -f ./110-zookeeper-service-headless.yml

# create zookeeper statefulset
$ kubectl -n <namespace> create -f ./140-zookeeper-statefulset.yml

# create zookeeper disruptionbudget
$ kubectl -n <namespace> create -f ./150-zookeeper-disruptionbudget.yml
```

### create kafka pods

```bash
# create kafka service
$ kubectl -n <namespace> create -f ./210-kafka-service.yml

# create kafka headless service
$ kubectl -n <namespace> create -f ./210-kafka-service-headless.yml

# create kafka-statefulset
$ kubectl -n <namespace> create -f ./240-kafka-statefulset.yml
```

### create a test pod

```bash
# create test pods
$ kubectl -n <namespace> create -f ./400-pod-test.yml
```

## sanity checks

```bash
# check pods
$ kubectl -n <namespace> get pods

# access dashboard
$ az aks browse --resource-group <aks-resource-group> --name <aks-name>

# list created topics on test pod
$ kubectl -n <aks-namespace> exec kafka-test-client -- \
/usr/bin/kafka-topics --zookeeper kafka-zookeeper:2181 --list

# create a topic
$ kubectl -n <aks-namespace> exec kafka-test-client -- \
/usr/bin/kafka-topics --zookeeper kafka-zookeeper:2181 \
--topic test --create --partitions 1 --replication-factor 1
```
