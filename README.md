# kafka-kubernetes-cluster
This is a sample project of 3-node fully functioning Kafka cluster on a local workstation, which is using Strimzi Kafka distribution.

# Introduction

Configuration of Kafka Brokers listen to both listeners:

● PLAINTEXT (unauthenticated)

● SSL certificates (authenticated)

The goal is to start the Kafka clients directly from within the cluster to produce and consume some test messages.


# Instructions

# Install Docker Desktop

1. With MacOS it is enough to download the Docker Desktop from:

   ```
   https://www.docker.com/products/docker-desktop/
   ```

If you are on Windows, you might want to use WSL2 and the docker desktop extension.

Docker desktop will run small, light-weight Linux VM in the backgroud.

2. Enable Kubernetes in Docker Destop settings

Docker Dashboard -> Setting icon -> Kubernetes from the left sidebar -> Select Checkbox -> Apply & Restart

This instantiates the images required to run the Kubernetes server as containers, and installs the /usr/local/bin/kubectl command on your environment.

Then verify with command that your kubernetes service is running

   ```
   kubectl get services
   ```

With another command, you can check which images docker created for that Kubernetes server:

   ```
   docker images
   ```

# Kubernetes Cluster

1. Create namespace:

Let's create a namespace in which our kafka cluster will operate:

   ```
   kubectl create namespace kafka
   ```
2. Setting up the context:

   ```
   kubectl config set-context --current --namespace=kafka
   ```

Tip:
Instead of typing kubectl, you can use "k" by alias with this command:

   ```
   alias k='kubectl' 
   ```

3. Apply the Strimzi install files


   ```
   kubectl create -f 'https://strimzi.io/install/latest?namespace=kafka’
   ```

4. Monitoring cluster deployment

Follow the deployment of the Strimzi cluster operator:


   ```
   kubectl get pod -n kafka --watch
   ```

You can also follow the operator’s log:


   ```
   kubectl logs deployment/strimzi-cluster-operator -n kafka -f
   ```

# Deploy Kafka cluster with config file

1. From now on we would need to use custom files from this repo. 


   ```
   git clone https://github.com/sztrafi/kafka-kubernetes-cluster.git
   ```

2. Run the command to deploy Kafka 

   ```
   kubectl apply -f /your_choosen_destination/kafka-kubernetes-cluster/nodes/kafka_3_node.yaml
   ```

3. Monitoring:

   ```
   kubectl get pod --watch
   ```

# Create a Kafka Topic using the operator

1. Let's create a topic for our Kafka workflow. The yaml file for creation it consist of the topic name: "samples":

   ```
   kubectl apply -f /your_choosen_destination/kafka-kubernetes-cluster/topic/topic.yaml 
   ```
2. Here are useful comands for verification:

   ```
   kubectl get kafkatopics
   kubectl describe kafkatopic samples
   ```

# Create a Kafka User with Authentication TLS & Simple Authorization

1. As we have repo downloaded, we can use another yaml files, this time for user creation:

   ```
   kubectl apply -f /your_choosen_destination/kafka-kubernetes-cluster/user/kafka_user.yaml
   ```

2. Let's verify creation of secrets:

   ```
   kubectl get secrets
   ```

   Or because in the kafka_user.yaml the name for this object is "kafka-tls-client-credentials" we can use:

   ```
   kubectl get secret kafka-tls-client-credentials -o yaml
   ```

# Send and receive messages

1. Again, as in the previous steps, we need to deploy a component of Kafka workflow, this time producer and consumer:

   ```
   kubectl apply -f /your_choosen_destination/kafka-kubernetes-cluster/producer_consumer/producer_consumer.yaml
   ```

2. Let's verify with simple kubectl command if our pods are running.
   We should see both "hello_world_producer_xxx" and "hello_world_consumer_xxx" as defined in our yaml config file.

   ```
   kubectl get pods
   ```

3. To get more specific logs of the messeges send/received we can use:

   ```
   kubectl logs hello-world-producer-xxx
   kubectl logs hello-world-consumer-xxx
   ```

# Clean up 

When you are finished with your Apache Kafka cluster, you can delete it by the command:

   ```
   kubectl -n kafka delete $(kubectl get strimzi -o name -n kafka)
   ```

and in case you want to fully remove the Strimzi cluster operator and everything associated with it, run:

   ```
   kubectl -n kafka delete -f 'https://strimzi.io/install/latest?namespace=kafka'
   ```

