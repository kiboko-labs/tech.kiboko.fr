---
title: "Stack FAQ"
date: 2020-09-10
type : "docs"
---

Stack FAQ
===


#### Erreur `The child node "client" at path "snc_redic.sessin" must be configured`

Pour éviter cette erreur, on peut commenter la configuration Redis dans `config_prod.yml` tel que :

````
#snc_redis:
#    session:
#        locking: false
```` 

#### Erreur `No alive nodes found in your cluster`

Cette erreur correspond à un probleme d'ElasticSearch qui doit être cassé.


#### Erreur `Step "open" of workflow "b2c_flow_abandoned_shopping_cart" doesn't have allowed transition "__start__".`

Cette erreur correspond à un probleme de workflow, possibilité de corriger avec la commande :
`bin/console oro:workflow:definitions:load`

#### Erreur `An exception has been thrown during the rendering of a template ("Warning: preg_match(): Compilation failed: invalid range in character class at offset 72") in OroSidebarBundle::sidebar.html.twig at line 21.`

Probleme lié à une mauvaise version de php

#### Erreur `write timed-out` lors d'un migration load

La commande a été lancé avant que tous les services soient démarrés


