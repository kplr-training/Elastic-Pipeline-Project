# Elastic Pipeline

## Creation du docker-compose.yaml

Définir un fichier Docker Compose qui est utilisé pour gérer plusieurs conteneurs dans une seule application. 
L'application comprend trois services: Elasticsearch, Logstash et Kibana, qui font tous partie de la pile ELK utilisée pour l'analyse et la visualisation des données.

### 1. Determiner la version

La version utilisée est 3.6, qui est la version du format de fichier Compose. Ce fichier est compatible avec Docker Engine 18.03.0 ou supérieur.

```
version: '3.6'
```

### 2. Determiner les services

Le premier service défini est Elasticsearch, qui utilise l'image Docker Elasticsearch officielle avec la version 8.7.0.

```
version: '3.6'
services:
  Elasticsearch:
    image: elasticsearch:8.7.0
    container_name: elasticsearch
    restart: always
    volumes:
    - elastic_data:/usr/share/elasticsearch/data/
    environment:
      XPACK_MONITORING_ENABLED: false
      xpack.security.enabled: false
      ES_JAVA_OPTS: "-Xmx256m -Xms256m"
      discovery.type: single-node
      network.host: "0.0.0.0" 
    ports:
    - '9200:9200'
    - '9300:9300'
    networks:
      - elk
```
* Le service est nommé "Elasticsearch" et a un nom de conteneur "elasticsearch". 
* L'option "restart" est définie sur "always" pour garantir que le conteneur redémarre automatiquement en cas de défaillance. 
* Le service a un volume défini, qui est "elastic_data".Ce volume est utilisé pour persister les données Elasticsearch. 
* Les options "XPACK_MONITORING_ENABLED" et "xpack.security.enabled" sont définies sur false pour désactiver les fonctionnalités de surveillance et de sécurité respectivement. 
* L'option "ES_JAVA_OPTS" définit la taille de la mémoire heap Java. 
* L'option "discovery.type" est définie sur "single-node" qui est utilisée lorsqu'il n'y a qu'un seul nœud Elasticsearch dans le cluster. 
* L'option "network.host" est définie sur "0.0.0.0" pour lier Elasticsearch à toutes les interfaces réseau. 
* Le service expose deux ports, 9200 et 9300, qui sont utilisés pour la communication avec Elasticsearch. 
* Le service est connecté au réseau "elk".

