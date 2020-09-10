---
title: "Stack FAQ"
date: 2020-09-10
type : "docs"
---

Stack FAQ
===

Listes d'erreurs rencontrées lors de nos développements, avec une solution éventuelle pour remédier au problème.

#### Erreur configuration "snc_redic.session"


````
The child node "client" at path "snc_redic.session" must be configured
````

Pour éviter cette erreur, on peut commenter la configuration Redis dans `config_prod.yml` tel que :

````
#snc_redis:
#    session:
#        locking: false
```` 

#### Erreur workflow "doesn't have allowed transition"

````
Step "open" of workflow "b2c_flow_abandoned_shopping_cart" doesn't have allowed transition "__start__".
````

Cette erreur correspond à un probleme de workflow, possibilité de corriger avec la commande :
`bin/console oro:workflow:definitions:load`

#### Erreur "Compilation failed: invalid range"

````
An exception has been thrown during the rendering of a template ("Warning: preg_match(): Compilation failed: invalid range in character class at offset 72") in OroSidebarBundle::sidebar.html.twig at line 21.
````

Probleme lié à une mauvaise version de php

#### Erreur `write timed-out` lors d'un migration load

La commande a été lancé avant que tous les services soient démarrés

#### Erreur connexion logstash lors d'un platform update

````
Failed connecting to tcp://logstash:5044
````

Vérifier que les 2 services `logstash` et `kibana` sont bien lancés.


#### Erreur "data type json"

````
Undefined object: 7 ERROR:  data type json has no default operator class for access method “btree”
````

Vérifier que les patchs soient bien appliqués :
- `composer run kiboko-apply-patches`

Patch réglant le probleme `EntityExtendBundle_UpdateExtendIndicesMigration.patch`

## Erreurs ElasticSearch

#### Erreur "No alive nodes found in your cluster"

````
No alive nodes found in your cluster
````

Cette erreur correspond à un probleme d'ElasticSearch. Le service doit être cassé.


#### Erreur "index_not_found_exception"

````
{“error”:{“root_cause”:[{“type”:“index_not_found_exception”,“reason”:“no such index" .... 
````
Solution apportée :
- on créé des index vides avec cette commande `bin/console oro:elasticsearch:create-standard-indexes`
- puis on regénère les indexes `bin/console oro:website-search:reindex --website-id=3`
ou n'importe quel type de commande qui fait de la reindexation (oro:search:reindex, etc.)



#### Erreur "process_cluster_event_timeout_exception" lors d'un oro:website-search:reindex

````
{“error”:{“root_cause”:[{“type”:“process_cluster_event_timeout_exception”,“reason”:“failed to process cluster event (put-mapping) within 30s”}],“type”:“process_cluster_event_timeout_exception”,“reason”:“failed to process 
                cluster event (put-mapping) within 30s”},“status”:503} 
````

Probleme de configuration d'ElasticSearch, actions à réaliser :
- suppression du volume du service et du container elastic
- configuration du service et des données "environment" dans le fichier `docker-compose.yml` tel que :

````
- http.cors.allow-origin=http://localhost:${DEJAVU_PORT},http://127.0.0.1:${DEJAVU_PORT}
````


