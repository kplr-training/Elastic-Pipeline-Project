# Elastic Pipeline


Avant de créer le fichier docker-compose.yml vous devriez créer 3 dossiers:
  1. `input` : qui contient un fichier `inlog.log` vide.
  2. `logstash` : qui contient un fichier `logstash.conf` vide.
  3.  `output` : qui contient aucun fichier.

   ![image](https://user-images.githubusercontent.com/123748165/234107655-c5300edf-00c0-4b3e-8583-b5b05cdb325e.png)


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

* Le service est nommé "Elasticsearch" et a un nom de conteneur "elasticsearch". 
* L'option "restart" est définie sur "always" pour garantir que le conteneur redémarre automatiquement en cas de défaillance. 
* Le service a un volume défini, qui est "elastic_data".Ce volume est utilisé pour persister les données Elasticsearch. 
* Les options "XPACK_MONITORING_ENABLED" et "xpack.security.enabled" sont définies sur false pour désactiver les fonctionnalités de surveillance et de sécurité respectivement. 
* L'option "ES_JAVA_OPTS" définit la taille de la mémoire heap Java. 
* L'option "discovery.type" est définie sur "single-node" qui est utilisée lorsqu'il n'y a qu'un seul nœud Elasticsearch dans le cluster. 
* L'option "network.host" est définie sur "0.0.0.0" pour lier Elasticsearch à toutes les interfaces réseau. 
* Le service expose deux ports, 9200 et 9300, qui sont utilisés pour la communication avec Elasticsearch. 
* Le service est connecté au réseau "elk".

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

Le deuxième service défini est Logstash, qui utilise l'image Docker Logstash officielle avec la version 8.7.0.

* Le service est nommé "Logstash" et a un nom de conteneur "logstash".
* L'option "restart" est définie sur "always".
* Le service a trois volumes définis, qui sont "./logstash/", "./input" et "./output". Ces volumes sont utilisés pour monter les répertoires de configuration, d'entrée et de sortie de Logstash.
* L'option "command" est utilisée pour spécifier le fichier de configuration Logstash à utiliser.
* L'option "depends_on" spécifie que le service Logstash dépend du service Elasticsearch, ce qui signifie que le service Elasticsearch doit être démarré avant le service Logstash.
* Le service expose le port 9600, qui est utilisé pour la communication avec Logstash.
* L'option "LS_JAVA_OPTS" définit la taille de la mémoire heap Java. 
* Le service est connecté au réseau "elk".

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

  Logstash:
    image: logstash:8.7.0
    container_name: logstash
    restart: always
    volumes:
    - ./logstash/:/logstash_dir
    - ./input:/input
    - ./output:/output
    command: logstash -f /logstash_dir/logstash.conf 
    depends_on:
      - Elasticsearch
    ports:
    - '9600:9600'
    environment:
      LS_JAVA_OPTS: "-Xmx256m -Xms256m"
    networks:
      - elk
```

Le troisième service défini est Kibana, qui utilise l'image Docker Kibana officielle avec la version 8.7.0. 
* Le service est nommé "Kibana" et a un nom de conteneur "kibana". 
* L'option "restart" est définie sur "always". 
* Le service expose le port 5601, qui est utilisé pour la communication avec Kibana. 
* L'option "ELASTICSEARCH_URL" spécifie l'URL Elasticsearch que Kibana doit utiliser. Pour récupérer le port utiliser la commande : 
  ```
    gp url 9200
  ```
  
  ![image](https://user-images.githubusercontent.com/123748165/234105783-189d9242-fa5b-48c1-a116-1daf2a68f507.png)
  

* L'option "depends_on" spécifie que le service Kibana dépend du service Elasticsearch, ce qui signifie que le service Elasticsearch doit être démarré avant le service Kibana. 
* Le service est connecté au réseau "elk".

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

  Logstash:
    image: logstash:8.7.0
    container_name: logstash
    restart: always
    volumes:
    - ./logstash/:/logstash_dir
    - ./input:/input
    - ./output:/output
    command: logstash -f /logstash_dir/logstash.conf 
    depends_on:
      - Elasticsearch
    ports:
    - '9600:9600'
    environment:
      LS_JAVA_OPTS: "-Xmx256m -Xms256m"
    networks:
      - elk

  Kibana:
    image: kibana:8.7.0
    container_name: kibana
    restart: always       
    ports:
    - '5601:5601'
    environment:
      - ELASTICSEARCH_URL=https://9200-xxxxxxxx.gitpod.io
    depends_on:
      - Elasticsearch  
    networks:
      - elk
```

### 3. Determiner les sections volumes et networks

Enfin, la section "volumes" définit le volume "elastic_data" utilisé par Elasticsearch, et la section "networks" définit le réseau "elk" utilisé par tous les services.

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

  Logstash:
    image: logstash:8.7.0
    container_name: logstash
    restart: always
    volumes:
    - ./logstash/:/logstash_dir
    - ./input:/input
    - ./output:/output
    command: logstash -f /logstash_dir/logstash.conf 
    depends_on:
      - Elasticsearch
    ports:
    - '9600:9600'
    environment:
      LS_JAVA_OPTS: "-Xmx256m -Xms256m"
    networks:
      - elk

  Kibana:
    image: kibana:8.7.0
    container_name: kibana
    restart: always       
    ports:
    - '5601:5601'
    environment:
      - ELASTICSEARCH_URL=https://9200-xxxxxxxx.gitpod.io
    depends_on:
      - Elasticsearch  
    networks:
      - elk
volumes:
  elastic_data: {}

networks:
  elk:
```
