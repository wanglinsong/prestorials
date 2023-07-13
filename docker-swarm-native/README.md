# Presto in Docker Swarm
This is a sample setup to run a Presto cluster with 1 coordinator and 2 workers. The cluster is running on an one-manager,
2-worker Docker Swarm cluster, each node has 2 CPUs and 8 GiB of memory.

## Requirements
* Mac OS X or Linux, with minimum 10G memory and 50G free disk space
* Docker Desktop 4.10.1 (Engine: 24.0.2, Compose: v2.18.1) and above
* Docker Swarm setup depending on your cluster configuration (https://docs.docker.com/engine/swarm/)

## Update catalog configurations
* Update `mysql.properties`, `postgresql.properties`, `hive.properties` and/or `glue.properties` if you need to.
* Add catalogs of other connectors if you need to.
* Update `volumes` sections in `docker-compose.yaml` to mount catalog files accordingly.
* Update `environment` sections in `docker-compose.yaml` to inject required environment variables into Presto proceses.

## Make sure all needed files are available on cooresponding swarm nodes

## Start a Presto cluster
Open a terminal, and change into this directory, on the Swarm manager node.
    docker stack deploy presto -c docker-compose.yaml
    
This will start coordinator on the Swarm manager node.

    docker service scale presto_worker_1=1
    docker service scale presto_worker_2=1
    
This will start Presto workers on the Swarm worker nodes. Check cluster with the following commands

    docker stack ls
    docker service ls
    docker container ls

The deployment resource `reservations` and `limits` are configured, so that each Presto worker can only run on a new Swarm
worker node.
You can monitor the logs of different containers in the terminal.

## Access Presto Web Console on Swarm manager node
    http://127.0.0.1:8080

## Run Presto CLI on Swarm manager node
    /opt/presto-cli --server http://127.0.0.1:8080

## Stop and delete the cluster
    docker stack rm presto

## Notes
* The cluster perfroms better on Linux because the docker image is built to run on linux/amd64.
* A container may hang when running on Mac OS X. You can restart it in either terminal or Docker Desktop.
