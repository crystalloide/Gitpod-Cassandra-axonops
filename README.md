# Cassandra-axon-ops-example-with-gitpod

Pour information, AxonOps a une grande expérience dans la migration des clients à partir du support DataStax Enterprise et Luna. 

Nous allons le voir en service sur un cas de démonstration :

## Pour aller plus loin ; Présentation de la solution : https://axonops.com/

[# Pour voir vos environnements Gitpod éventuellement déjà provisionnés :](https://gitpod.io/workspaces)

[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/crystalloide/Gitpod-Cassandra-axonops)

Cela installera un cluster Cassandra à 3 nœuds avec AxonOps sur un bureau à des fins de développement et de formation. 

•	Si on était sur une VM sans installation préalable réalisée, il faudrait (pas nécessaire ici puisqu'on utilise Gitpod et le contexte est déjà préchargé)  :

    - Installez Docker avec Docker Compose V2 en suivant les instructions d’installation ici : https://docs.docker.com/compose/install
    - Téléchargez le fichier docker-compose.yml :
        curl -O https://raw.githubusercontent.com/axonops/axonops-cassandra-dev-cluster/main/docker-compose.yml
    - ou une autre possibilité : 
        curl -O https://raw.githubusercontent.com/crystalloide/Gitpod-Cassandra-axonops/main/docker-compose.yml
    -Démarrer ensuite le cluster avec la surveillance AxonOps :
        docker-compose up -d
    - Attendre quelques minutes que les conteneurs démarrent, puis ouvrir http://127.0.0.1:3000 dans votre navigateur pour voir le tableau de bord AxonOps


## Affichons les conteneurs en cours d'exécution : 

docker ps -a 

    CONTAINER ID   IMAGE                                                                   COMMAND                  CREATED         STATUS                   PORTS                                                                                    NAMES
    7e7da8c4ed35   registry.axonops.com/axonops-public/axonops-docker/cassandra:4.1        "/axonops-entrypoint…"   7 minutes ago   Up 5 minutes (healthy)   7000-7001/tcp, 7199/tcp, 9042/tcp, 9160/tcp, 0.0.0.0:9044->9044/tcp, :::9044->9044/tcp   gitpod-cassandra-axonops-cassandra-2-1
    2bd83e993f23   registry.axonops.com/axonops-public/axonops-docker/axon-dash:latest     "/bin/sh -c 'sed -i …"   7 minutes ago   Up 6 minutes (healthy)   0.0.0.0:3000->3000/tcp, :::3000->3000/tcp                                                gitpod-cassandra-axonops-axon-dash-1
    2d5047ef56c6   registry.axonops.com/axonops-public/axonops-docker/cassandra:4.1        "/axonops-entrypoint…"   7 minutes ago   Up 6 minutes (healthy)   7000-7001/tcp, 7199/tcp, 9042/tcp, 9160/tcp, 0.0.0.0:9043->9043/tcp, :::9043->9043/tcp   gitpod-cassandra-axonops-cassandra-1-1
    b7116ecce530   registry.axonops.com/axonops-public/axonops-docker/axon-server:latest   "/usr/share/axonops/…"   7 minutes ago   Up 7 minutes (healthy)                                                                                            gitpod-cassandra-axonops-axon-server-1
    4dc4d6728238   registry.axonops.com/axonops-public/axonops-docker/cassandra:4.1        "/axonops-entrypoint…"   7 minutes ago   Up 7 minutes (healthy)   7000-7001/tcp, 7199/tcp, 9160/tcp, 0.0.0.0:9042->9042/tcp, :::9042->9042/tcp             gitpod-cassandra-axonops-cassandra-0-1
    d42b768f74fb   docker.elastic.co/elasticsearch/elasticsearch:7.17.12                   "/bin/tini -- /usr/l…"   7 minutes ago   Up 7 minutes (healthy)   9200/tcp, 9300/tcp                                                                       gitpod-cassandra-axonops-elasticsearch-1


# Monitoring a Cassandra Cluster in Docker :

nodetool est un programme en ligne de commande qui offre un large éventail de façons d'examiner un cluster, de comprendre son activité et de le modifier.

    L'utilitaire nodetool permet notamment : 
    - d'obtenir des statistiques sur le cluster
    - de voir les plages de token maintenues par chaque nœud 
    - de réaliser une variété de tâches de gestion :
        - comme le déplacement de données d'un nœud à un autre
        - la mise hors service de nœuds, la réparation de nœuds
        - les réparations
        - les snapshots
        - etc.

## Utilisons donc nodetool :  ici la commande "nodetool status" lancée sur n'importe lequel des nœuds pour voir l'état des nœuds du cluster :

docker exec -it gitpod-cassandra-axonops-cassandra-2-1 nodetool status

### Affichage :
    Datacenter: dc1
    ===============
    Status=Up/Down
    |/ State=Normal/Leaving/Joining/Moving
    --  Address     Load        Tokens  Owns (effective)  Host ID                               Rack 
    UN  172.18.0.6  75.19 KiB   16      59.3%             3d8f189c-ad47-4b1e-8ea8-c004c0cd95b5  rack1
    UN  172.18.0.7  75.19 KiB   16      76.0%             f4539d5f-6e29-417a-b2c2-f59d72101120  rack2
    UN  172.18.0.2  109.38 KiB  16      64.7%             e415bf87-8f58-41c3-b3be-ee800006a71c  rack0


## The nodetool info command provides additional details :

docker exec -it gitpod-cassandra-axonops-cassandra-2-1 nodetool info

### Affichage :
    gitpod /workspace/Gitpod-Cassandra-axonops (main) $ docker exec -it gitpod-cassandra-axonops-cassandra-2-1 nodetool info
    ID                     : f4539d5f-6e29-417a-b2c2-f59d72101120
    Gossip active          : true
    Native Transport active: true
    Load                   : 75.19 KiB
    Generation No          : 1692971004
    Uptime (seconds)       : 702
    Heap Memory (MB)       : 87.96 / 251.00
    Off Heap Memory (MB)   : 0.00
    Data Center            : dc1
    Rack                   : rack2
    Exceptions             : 0
    Key Cache              : entries 11, size 984 bytes, capacity 12 MiB, 104 hits, 122 requests, 0.852 recent hit rate, 14400 save period in seconds
    Row Cache              : entries 0, size 0 bytes, capacity 0 bytes, 0 hits, 0 requests, NaN recent hit rate, 0 save period in seconds
    Counter Cache          : entries 0, size 0 bytes, capacity 6 MiB, 0 hits, 0 requests, NaN recent hit rate, 7200 save period in seconds
    Network Cache          : size 8 MiB, overflow size: 0 bytes, capacity 15 MiB
    Percent Repaired       : 100.0%
    Token                  : (invoke with -T/--tokens to see all 16 tokens)


## Pour voir les détails de quels nœuds possèdent quels ranges de token, on va utiliser "nodetool ring" :

docker exec -it gitpod-cassandra-axonops-cassandra-2-1 nodetool ring

### Affichage : 

    Datacenter: dc1
    ==========
    Address          Rack        Status State   Load            Owns                Token                                       
                                                                                    8972475839137452131                         
    172.18.0.2       rack0       Up     Normal  109.38 KiB      64.66%              -9055590551661409925                        
    172.18.0.7       rack2       Up     Normal  75.19 KiB       75.99%              -8608019211763529537                        
    ....                      
    172.18.0.7       rack2       Up     Normal  75.19 KiB       75.99%              8007190946676663489                         
    172.18.0.2       rack0       Up     Normal  109.38 KiB      64.66%              8311780714600540032                         
    172.18.0.7       rack2       Up     Normal  75.19 KiB       75.99%              8721486411074324482                         
    172.18.0.6       rack1       Up     Normal  75.19 KiB       59.34%              8972475839137452131                         

      Warning: "nodetool ring" is used to output all the tokens of a node.
      To view status related info of a node use "nodetool status" instead.
  

## Utilisons le client CQL en ligne de commande "cqlsh" :  

docker exec -it gitpod-cassandra-axonops-cassandra-0-1 cqlsh


### Affichage : 

    Connected to demo-cluster at 127.0.0.1:9042
    [cqlsh 6.1.0 | Cassandra 4.1.3 | CQL spec 3.4.6 | Native protocol v5]
    Use HELP for help.


    DESCRIBE KEYSPACES;

### Affichage : 

    cqlsh> DESCRIBE KEYSPACES;

    system       system_distributed  system_traces  system_virtual_schema
    system_auth  system_schema       system_views 
    
    EXIT;
    
### Affichage : 

    cqlsh> EXIT
    
    gitpod /workspace/Gitpod-Cassandra-axonops (main) $ 
    

# Have fun!
