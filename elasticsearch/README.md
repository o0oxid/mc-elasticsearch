# Elasticsearch cluster setup

## Elasticsearch on local machine
This section briefly describes how to run Elasticserach cluster on a local machine. Here is the official Elasticsearch reference https://www.elastic.co/guide/en/elasticsearch/reference/6.6/docker.html if you need more details.
### Prerequsites
It might be necessary to update `vm.max_map_count` setting. Asusming macOS with [Docker for Mac](https://docs.docker.com/engine/installation/mac/#/docker-for-mac)
```
$ screen ~/Library/Containers/com.docker.docker/Data/vms/0/tty
```
Just press enter and configure the sysctl setting:
```
sysctl -w vm.max_map_count=262144
```
Be aware that `sysctl -w vm.max_map_count=262144' change is on the host OS and NOT inside the container. Containers share the same kernel as the host OS and for that reason it's working. On OSX the "docker host OS" is a VM which is wrapped by the docker-machine cli.

### Running Elasticsearch on local machine
Starting Elasticsearch in development mode on your local machine is as simple as checkking out this repository and runnning following command:
```
docker-compose up
```
It will bring up a docker container with Elasticsearch **cluster of single node** with default settings and Kibana. A log messages go to the console. The Elasticsearch node listens on 'localhost:9200'. Kibana is available at http://localhost:5601/app/kibana.
###### Inspect status of cluster:
> curl http://127.0.0.1:9200/_cat/health
1549584195 00:03:15 local-es-cluster green 1 1 0 0 0 0 0 0 - 100.0%
###### Destroy elasticsearch cluster
To stop elasticsearch cluster run:
```
docker-compose down
```
Cool feature of this method is after container is deleted indeces will remain on a docker data volume. To destroy the cluster and data volumes, just run:
```
docker-compose -v down
```
###### Configuring Elasticsearch with Docker
Elasticsearch configuration as well as JVM settings can be passed via Docker environment variables. Parameters should be added to `environment` section in [docker-compose.yml](docker-compose.yml)
```
environment:
  - cluster.name=local-es-cluster
  - bootstrap.memory_lock=true
  - discovery.type=single-node
  - "ES_JAVA_OPTS=-Xms1024m -Xmx1024m"
```
If for development or testing pursposes two Elasticsearch nodes are necessary the [docker-compose.yml](docker-compose.yml) can be easily extended. The elasticsearch nodes will talk to each other over a Docker network. A full example can be found at https://www.elastic.co/guide/en/elasticsearch/reference/6.6/docker.html Elasticsearch refference page.
