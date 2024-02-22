# jenkins CI/CD configuration
#### steps:  build server installation
1. install git
2. install docker
3. install eks-client
4. install helm

#### pugins need to install
1. docker plugin
2. aws credentials plugin
3. aws steps

#### CI/CD - DEV environamet steps
1. clone the code
2. run unit tests
3. run the scans 

     *  static source code analysis -  sonarqube
     *  static aplication security testing - fortyfy
     *  dynamic application security testing - webinspect
     *  open source scanning - nexus Iq /blackduck
     *  scan the images and containers - twistlock

4. docker build
5. push the images to private repos
6. deployement 
7.functional testing - seleinum
8.integration testing - cucumber




#### ELK set-up using helm 
1.Create the Logging Namespace: If you haven't already created a namespace for logging, you can create one using the following command

```
kubectl create namespace logging
```
2. Add Elasticsearch Helm Repository: If you haven't already added the Elasticsearch Helm repository, you can do so using the following command:

```
helm repo add elastic https://helm.elastic.co
helm repo update
```
3. Install Elasticsearch:  You can install Elasticsearch using the following Helm command, specifying the logging namespace

```
helm install elasticsearch elastic/elasticsearch --namespace logging 
```
4.Install Kibana: Install Kibana in the same logging namespace and configure it to connect to Elasticsearch. Here's the Helm command
```
helm install kibana elastic/kibana --namespace logging --set service.type=LoadBalancer --set elasticsearchHosts=http://elasticsearch-master:9200
```

