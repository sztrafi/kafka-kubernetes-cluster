# kafka-kubernetes-cluster
This is a sample project of 3-node fully functioning Kafka cluster on a local workstation, which is using Strimzi Kafka distribution.



# Introduction
This is a sample project of 3-node fully functioning Kafka cluster on a local workstation, which is using Strimzi Kafka distribution.

Configuration of Kafka Brokers listen to both listeners:

● PLAINTEXT (unauthenticated)

● SSL certificates (authenticated)

The goal is to start the Kafka clients directly from within the cluster to produce and consume some test messages.


# Instructions

1. Install Docker Desktop

With MacOS it is enough to download the Docker Desktop from:

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

3. Create namespace:

Let's create a namespace in which our kafka cluster will operate:

   ```
   kubectl create namespace kafka
   ```
Setting up the context:

   ```
   kubectl config set-context --current --namespace=kafka
   ```

Tip:
Instead of typing kubectl, you can use "k" by alias with this command:

   ```
   alias k='kubectl' 
   ```

4. Apply the Strimzi install files


   ```
   kubectl create -f 'https://strimzi.io/install/latest?namespace=kafka’
   ```

Follow the deployment of the Strimzi cluster operator:


   ```
   kubectl get pod -n kafka --watch
   ```

You can also follow the operator’s log:


   ```
   kubectl logs deployment/strimzi-cluster-operator -n kafka -f
   ```
