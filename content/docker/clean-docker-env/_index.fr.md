---
title: "Nettoyer son environnement docker"
date: 2020-03-06
type : "docs"
---

## Documentation

https://docs.docker.com/config/pruning/

## Processus

Pour libérer de l'espace sur VM, on peut lancer plusieurs commandes pour supprimer les images et les volumes qui sont non utilisés :

* `docker image prune`

* `docker volume prune`

_Attention à ne pas supprimer tous ses containers_
