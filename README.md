# aks-kafka

Three node kafka and three node zookeeper cluster. Kafka and Zookeeper can be scaled up at any time by modifying and re-applying configurations.

## login to aks
```
# login to aks cluster
$ az aks get-credentials --resource-group <aks-resource-group> --name <aks-name>
