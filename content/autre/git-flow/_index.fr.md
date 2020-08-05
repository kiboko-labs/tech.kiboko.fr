---
title: "Git flow"
date: 2020-05-20
type : "docs"
---

Spécificités Kiboko pour la mise en place de git flow

## Environnements et branches

| Environnement | Branche |
| ------|-----|
| Production | `master` |
| Staging | `staging` |
| . | `develop` |

![40% center](gitflow.jpg)

## Branches

### Branche `master`

- nombre : unique
- durée de vie : permanente
- responsabilité : Lead dev et DT
- création : démarrage du projet

Code stable, testé et validé. Ce code doit être validé en staging par le client.

⚠️ Sera déployé sur l’environnement de production

Les PR à destination de la branche Master sont validées par les personnes ayant la main sur les déploiements sur l’environnement de production

### Branche `staging`

- nombre : unique
- durée de vie : permanente
- responsabilité : Lead dev et DT
- création : démarrage du projet

Code stable, testé et validé en environnement de dev/test. Permet de valider le fonctionnement d’une nouvelle fonctionnalité sur une environnement identique à la production. Permet au client d’effectuer sa recette.

Les PR à destination de la branche `staging` sont validées par les personnes ayant la main sur les déploiements sur l’environnement de staging après le code review validé.

⚠️ Sera déployé sur l’environnement de staging

### Branches « feature »

- nombre : au moins une par fonctionnalité à développer
- durée de vie : quelques jours
- responsabilité : Développeur
- création : démarrage du développement d’un ticket, par fork de la branche `develop`
- nommage : feature/<numéro de ticket>-<titre de la feature>
ex : feature/103-news-bundle
- Code review : Lead dev et DT

Code en cours de développement qui implémente une fonctionnalité à embarquer dans la prochaine version de l’application.

Une fois le développement sur cette branche terminé :
- création d’une PR depuis cette branche vers `develop`
- assignation de la responsabilité au Lead dev ou DT
- validation de la PR par le reviewer ou le lead-dev

### Branches « hotfix » 

- nombre : au moins une par bug à corriger en production
- durée de vie : quelques heures, le temps de passer le correctif en production
- responsabilité : Développeur
- création : par fork de la branche master
- nommage : hotfix/<numéro de ticket>-<titre du bug>
ex : hotfix/103-category-import
- Code review : Lead dev et DT
