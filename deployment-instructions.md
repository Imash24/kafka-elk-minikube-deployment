# Minikube and Kafka-ELK Deployment Instructions

## Deploy Minikube

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube start --memory 8096 --cpus 4

Deploy Apache Kafka with Strimzi
Create a namespace called kafka:
kubectl create namespace kafka

Apply the Strimzi install files to the kafka namespace:

kubectl create -f 'https://strimzi.io/install/latest?namespace=kafka' -n kafka

Follow the deployment of the Strimzi cluster operator:

kubectl get pod -n kafka --watch

Apply the Kafka Cluster Custom Resource (CR) file:

kubectl apply -f https://strimzi.io/examples/latest/kafka/kafka-persistent-single.yaml -n kafka

Wait for the Kafka cluster to become ready:

kubectl wait kafka/my-cluster --for=condition=Ready --timeout=300s -n kafka

Create a Kafka producer:

kubectl -n kafka run kafka-producer -ti --image=quay.io/strimzi/kafka:0.36.1-kafka-3.5.1 --rm=true --restart=Never -- bin/kafka-console-producer.sh --bootstrap-server my-cluster-kafka-bootstrap:9092 --topic my-topic

Create a Kafka consumer:

kubectl -n kafka run kafka-consumer -ti --image=quay.io/strimzi/kafka:0.36.1-kafka-3.5.1 --rm=true --restart=Never -- bin/kafka-console-consumer.sh --bootstrap-server my-cluster-kafka-bootstrap:9092 --topic my-topic --from-beginning

Deploy ELK Stack with Helm

# Install Helm
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh

# Add Elastic Helm repository
helm repo add elastic https://helm.elastic.co

# Install Elasticsearch, Logstash, Kibana, and Filebeat
helm install my-elasticsearch elastic/elasticsearch --version 7.17.3 --set replicas=1
helm install my-logstash elastic/logstash --version 7.17.3
helm install my-kibana elastic/kibana --version 7.17.3
helm install my-filebeat elastic/filebeat --version 7.17.3

Verify Deployments
Check the status of your pods:

kubectl get pods
kubectl get pods -n kafka

Access Kibana via port forwarding:

kubectl port-forward service/my-kibana-kibana 5601:5601

That's it! You've successfully deployed Minikube, Apache Kafka using Strimzi, and the ELK Stack with Helm.

------------------------------------------------------------------------------------------------------------








