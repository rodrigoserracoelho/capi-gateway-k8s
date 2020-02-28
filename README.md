# CAPI Gateway Kubernetes Installation

This project contains all the necessary files to install CAPI Gateway on a K8S service.
  - CAPI Manifest / Service (CAMEL Route server)
  - CAPI Rest Manifest / Service (Manager server)
  - Mongo Manifest / Service
  - Zipkin Manifest / Service
  - Prometheus Manifest / Service
  - Grafana Manifest / Service
  - Config maps (Mongo, Prometheus, Grafana, Certificates)

We will be installing Kafka and Zookeeper using Confluent's Helm Chart: https://github.com/confluentinc/cp-helm-charts/tree/master/charts/cp-kafka

# Prerequisites
  - Kubernetes 1.9.2+
  - Helm 2.8.2
  - Tiller
  
Tested with Minikube with Docker driver (--vm-driver=none)
# Installation
  - Start installing Kafka and Zookeper:
```sh
$ git clone https://github.com/confluentinc/cp-helm-charts.git
```
  - Edit cp-helm-chars/values.yaml
  - Make sure you enable only Zookeeper and Kafka:
```sh
cp-zookeeper:
  enabled: true
  servers: 1
.......
cp-kafka:
  enabled: true
  servers: 1
```
- All the other components should be disabled.
```sh
$ helm tiller start
$ helm install --name kafkaf -f ./cp-helm-charts/values.yaml
$ helm tiller stop
```
- Check if Kafka and Zookeeper are running
```sh
$ kubectl get pods
```


