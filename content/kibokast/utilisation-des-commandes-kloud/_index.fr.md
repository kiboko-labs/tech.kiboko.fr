---
title: "Utilisation des commandes Kloud"
date: 2020-03-24
type : "docs"
---

https://github.com/kiboko-labs/docker-images

Commandes de gestion des images Docker
---

- `kloud images:test` 

Pour vérifier que les images soient conformes aux exigences (vérifie les pré-requis)

- `kloud images:pull`

Pour mettre à jour les images docker 

- `kloud images:tree`

Pour afficher l'arbre de dépendances des images Docker


Commandes de manipulation des stacks Docker
---

> Initialize the Docker stack ion a project without Docker Stack

- `kloud stack:init` 

Identifie le meilleur choix de stack en posant jusqu'à une dizaine de questions interactivement. Initialise cette stack d'après les réponses et crée un fichier `.kloud.yaml` à la racine du projet pour les prochaines utilisations de la commande. 

- `kloud stack:upgrade`

Met à jour la stack Docker d'après les contraintes listées dans le fichier `.kloud.yaml`

Utilisation en local
---

### Utilisation à travers Docker (préférable sous Mac)

Créer un fichier `/usr/local/bin/kloud`, avec le contenu suivant :

```
#!/usr/bin/env sh
docker run --rm -ti -v '/var/run/docker.sock:/var/run/docker.sock' -v $PWD:/app kiboko/kloud $@
```

Puis rendre le fichier exécutable avec `chmod +x /usr/local/bin/kloud`

### Utilisation du fichier Phar (préférable sous Linux)

Télécharger la dernière version publiée de l'utilitaire au format Phar : https://github.com/kiboko-labs/docker-images/releases