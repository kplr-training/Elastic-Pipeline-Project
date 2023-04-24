# Elastic Pipeline

## Création du dossier input

Définir le dossier "/input" contenant le fichier "inlog.log" qui est utilisé comme source de données pour Logstash.

Contenue du fichier "inlog.log" : 
```
bonjour tout le monde
tout le monde
hey
salut
```

## Création du dossier logstash

Définir le dossier "/logstash" contenant le fichier "logstash.conf", il est utilisé pour collecter, traiter et transférer des données de différents formats à différents endroits.

Le fichier "logstash.conf" contient un code , ce code comporte deux parties : la configuration d'entrée et la configuration de sortie.
