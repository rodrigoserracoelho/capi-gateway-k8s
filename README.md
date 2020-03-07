# CAPI Gateway Kubernetes Installation

### Prerequisites
  - Kubernetes 1.9.2+
  - Helm 2.8.2
  - Tiller
  
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

##### The goal is to have the following endpoints available:
  - https://gateway.<your domain>
  - https://manager.<your domain>
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

We will be installing Kafka and Zookeeper using Confluent's Helm Chart: https://github.com/confluentinc/cp-helm-charts/tree/master/charts/cp-kafka

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



