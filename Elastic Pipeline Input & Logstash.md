# Elastic Pipeline

## Création du dossier input

Définir le dossier "/input" contenant le fichier "inlog.log" qui est utilisé comme source de données pour Logstash.

## Création du dossier logstash

Définir le dossier "/logstash" contenant le fichier "logstash.conf", il est utilisé pour collecter, traiter et transférer des données de différents formats à différents endroits.

Le fichier "logstash.conf" contient un code , ce code comporte deux parties : **la configuration d'entrée et la configuration de sortie.**

* La configuration d'entrée spécifie la source de données à partir de laquelle Logstash collecte les données. Dans ce cas, la source est un fichier appelé "/input/inlog.log". Le chemin d'accès absolu du fichier est spécifié en utilisant le chemin d'access "path".

```
input {
  file {
    path => "/input/inlog.log"
  }
}
```

* La configuration de sortie spécifie où les données traitées seront envoyées. Dans ce cas, les données sont envoyées à Elasticsearch, une base de données de recherche et d'analyse en temps réel. La propriété "hosts" spécifie les hôtes Elasticsearch auxquels les données seront envoyées. Dans ce cas, l'hôte est simplement appelé "Elasticsearch", ce qui signifie qu'il doit être résolu par un système DNS ou configuré dans le fichier "hosts" du système d'exploitation.

```
output {
   elasticsearch {
    hosts => ["Elasticsearch"]
    index => "my-index-%{+YYYY.MM.dd}"
  }
}
```

* La propriété "index" spécifie le nom de l'index Elasticsearch dans lequel les données seront stockées. Dans ce cas, le nom de l'index est "my-index-" suivi de la date actuelle dans le format "YYYY.MM.dd". Cette syntaxe utilise la fonctionnalité de formatage de chaîne de Logstash pour générer un nom d'index dynamique qui inclut la date actuelle.

**logstash.conf :**

```
input {
  file {
    path => "/input/inlog.log"
  }
}

output {
   elasticsearch {
    hosts => ["Elasticsearch"]
    index => "my-index-%{+YYYY.MM.dd}"
  }
}
```
