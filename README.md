# CAPI Gateway Kubernetes Installation

### Prerequisites
  - Kubernetes 1.9.2+
  
##### Tested on:
   - Minikube with Docker driver (--vm-driver=none)
   - AWS AKS

##### This project contains all the necessary files to install CAPI Gateway on a K8s service.
  - CAPI Manifest / Service (CAMEL Route server)
  - CAPI Rest Manifest / Service (Manager server)
  - Mongo Manifest / Service
  - Zipkin Manifest / Service
  - Prometheus Manifest / Service
  - Grafana Manifest / Service
  - Config maps (Mongo, Prometheus, Grafana, Certificates)
  - Kafka / Zookeeper Manifest / Service

##### The goal is to have the following endpoints available:
  - https://gateway.<your domain>
  - https://manager.<your domain>

We are still integrating the following endpoints on K8s:  
  - https://zipkin.<your domain>
  - https://grafana.<your domain>

# Get your certificates
If you have already your strategy to generate certificates you can skip this part, we used Lets Encrypt

#### Prerequisites:
   * The domain configured
   * Running apache on port 80
   * Docker Installed (create local directories certbot and html)
#### Steps:
   - Run the certbot
```sh
$ sudo docker run -v $HOME/html:/html -v $HOME/certbot:/etc/letsencrypt certbot/certbot certonly --webroot -w /html -d manager.<your domain> -m <your e-mail> -t -n --agree-tos
```
   - Go the directory where the pem files were generated, you should have the certificate, the private key and the full chain. Generate a P12 file for your server. (this file will be installed as a secret in K8s)
```sh
$ openssl pkcs12 -export -out capi-rest.p12 -name capi-rest -inkey privkey1.pem -in cert1.pem -certfile fullchain1.pem
```
   - Repeat the previous steps for all the certificates. Ex.: manager, gateway, grafana.

# Installation
  - Start by installing the certificates as configmaps:
```sh
$ kubectl create configmap capi-rest-certificate --from-file=$HOME/capi-rest.p12
```
   - Repeat the same for all the certificates, and then check if they were installed.
```sh
$ kubectl get configmap
```

##### Start by running Kafka and Zookeeper

```sh
$ kubectl apply -f capi-kafka-yaml
```
- Check if Kafka and Zookeeper are running
```sh
$ kubectl get pods
```
   - Install all the configmaps in this repository:
```sh
$ kubectl apply -f configmap-truststore.yaml
$ kubectl apply -f configmap-grafana.yaml
$ kubectl apply -f configmap-prometheus.yaml
$ kubectl apply -f configmap-mongo.yaml
```
   - You can again check if they are all installed:
```sh
$ kubectl get configmap
```
