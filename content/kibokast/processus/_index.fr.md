---
title: "Les processus dans Oro"
date: 2020-12-11
type : "docs"
---

## Qu'est ce qu'un processus dans Oro ?

Les Processes permettent d'effectuer des tâches automatique lorsqu'une entité du système est créé, modifiée ou supprimée.

Doc. officielle : https://doc.oroinc.com/backend/entities-data-management/processes/

> Les processus offrent la possibilité d'automatiser les tâches liées à la gestion des entités. Ils utilisent les principaux événements de doctrine pour exécuter les tâches décrites au bon moment. Chaque processus peut être exécuté immédiatement ou après un délai. Les processus utilisent le composant OroMessageQueue et le bundle pour offrir la possibilité d'une exécution retardée.
> _(traduit depuis Oro)_

Exemple d'utilisation : lorsqu'une commande est créé (l'entité \Order\Bundle\OrderBundle\Entity\Order) celle-ci est envoyé à un ERP.

## Oro Processess

- Bundle OroWorkflowBundle
- Fonctionnalité allégée par rapport aux Workflows
- Repose sur les entités de l'ORM de Doctrine et ses événements + Components d'OroPlatform
- Définit dans un fichier `<MyBundle>/Resources/config/oro/processes.yml`

### Avantages / inconvénients

Les plus :
- facile à mettre en place et à comprendre
- pour des tâches assez simple
- peu, voir pas de code nécessaire

Les moins :
- compliqué à débugger : création d'un job, puis placement de breakpoints dans les actions ou conditions correspondantes
 
### Format du fichier `Processes.yml`

Constituté de 2 sections principales :
- définition : label, actif ou non, préconditions, actions
- trigger (le déclencheur) : par le bias d'un cron, via doctrine persistance (create, update, delete), avec retard ou immédiatement, dans le messaque queue ou non

**Exemple**

Dans le bundle Order : https://github.com/oroinc/orocommerce/blob/master/src/Oro/Bundle/OrderBundle/Resources/config/oro/processes.yml

## Commandes

#### Pour charger la configuration des processus

Commande à éxécuter dès qu'on modifie un fichier `processess.yml`

`bin/console oro:process:configuration:load`

> This command loads processes configuration from .yml configuration files to the database. It is used during application installation and update. The command has two optional options:
>
>  –directories - this option specifies directories used to find configuration files (multiple values allowed)
>
>  –definitions - this option specifies names of the process definitions that should be loaded (multiple values allowed)

#### Pour débugger les actions et conditions

`bin/console oro:debug:action` : Commande pour débugger les différentes actions et les paramètres à utiliser, fournit la liste des actions disponibles

`bin/console oro:debug:condition` : Commande pour débugger les différentes conditions, fournit la liste des conditions disponibles

Doc. officielle : https://doc.oroinc.com/backend/entities-data-management/actions/commands/

- Actions : tag "name: oro_action.action"
- Conditions : tag "name: oro_condition.condition"

_Note : également présent dans les Workflows_

## Détails et débug

#### Différences entre `$` & `$.`

- `$`= Entité en cours

Ex: `$` = `\Order\Bundle\OrderBundle\Entity\Order` 

- `$.`= ProcessData = données contextuelles du process en cours

#### Comment débugger ?

- Activé xdebug
- Créer un job dans la table oro_process_job
- Chercher l'action ou la condition à débugger (cf commandes en fin de présentation)
- Lancer `bin/console oro:process:handle-trigger --id=<ID> --name=<PROCESS_NAME>`

**Source :** Présentation effectuée par Sylvain Rayé, [Diglin](https://www.diglin.com/fr/)
