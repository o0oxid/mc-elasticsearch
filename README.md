# Elasticsearch Local Cluster Setup

This section briefly describes how to run Elasticserach cluster on a local machine. Here is the official Elasticsearch reference https://www.elastic.co/guide/en/elasticsearch/reference/6.6/docker.html that is worth reading.
### Prerequsites
This manuall asusmes macOS with [Docker for Mac](https://docs.docker.com/engine/installation/mac/#/docker-for-mac).
#### Tune System Configuration
Elasticsearch uses a `mmapfs` directory by default to store its indices. The default operating system limits on mmap counts is likely to be too low, which may result in out of memory exceptions. To increase the limmits run following command:
```
$ screen ~/Library/Containers/com.docker.docker/Data/vms/0/tty
```
  Just press enter and configure the sysctl setting:
```
sysctl -w vm.max_map_count=262144
```
**Persistence**: in some cases, this change does not persist across restarts of the VM. So, while screen'd into, edit the file /etc/sysctl.d/00-alpine.conf and add the parameter vm.max_map_count=262144 to the end of file.
> Be aware that `sysctl -w vm.max_map_count=262144` change is on the host OS and NOT inside the container. Containers share the same kernel as the host OS and for that reason it's working. On OSX the "docker host OS" is a VM which is wrapped by the docker-machine cli.

#### Increase Resources Availble to Docker.
By default Docker is configured with 2Gb of RAM. It might be insufficient to run Elasticsearch nodes and Kibana. To increase go to Docker menu `Prefferences -> Advanced` and increase Memory at least to 4G. Hit `Apply & Restart` button. See [https://docs.docker.com/docker-for-mac/](https://docs.docker.com/docker-for-mac/) for other settings.

### Run Elasticsearch
Starting Elasticsearch in development mode on your local machine is as simple as checkking out this repository and runing docker-compose command:
```
> git clone https://github.com/bazaarvoice/mc-elasticsearch.git
> docker-compose up
```
It will bring up a docker container with Elasticsearch **cluster of one node** with default settings. To get the **cluster of three nodes** run:
```
docker-compose -f docker-compose-three-nodes.yml
```
A log messages go to the console. The Elasticsearch node listens on 'localhost:9200'.

The Elasticsearch cluster comes with [Kibana](https://www.elastic.co/guide/en/kibana/current/getting-started.html), which lets you visualize your Elasticsearch data. Service is available at http://localhost:5601/app/kibana.
### Inspect Status of Cluster:
> curl http://127.0.0.1:9200/_cat/health
1549584195 00:03:15 local-es-cluster green 1 1 0 0 0 0 0 0 - 100.0%
### Destroy Elasticsearch Cluster
To stop elasticsearch cluster run:
```
docker-compose down
```
After container is deleted indices will remain on a docker data volumes. To destroy the cluster and data volumes, just run:
```
docker-compose -v down
```
### Configure Elasticsearch with Docker
Elasticsearch configuration as well as JVM settings can be passed via Docker environment variables. Parameters should be added to `environment` section in [docker-compose.yml](docker-compose.yml)
```
environment:
  - cluster.name=local-es-cluster
  - bootstrap.memory_lock=true
  - discovery.type=single-node
  - "ES_JAVA_OPTS=-Xms1024m -Xmx1024m"
```
If for development or testing pursposes two Elasticsearch nodes are necessary the [docker-compose.yml](docker-compose.yml) can be easily extended. The elasticsearch nodes will talk to each other over a Docker network. A full example can be found at https://www.elastic.co/guide/en/elasticsearch/reference/6.6/docker.html Elasticsearch refference page.
