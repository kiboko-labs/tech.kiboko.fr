---
title: "Commandes Debug"
date: 2020-04-07
type : "docs"
---

# Commandes Symfony

Articles officiels présentants certaines fonctionnalités :

- https://symfony.com/blog/new-in-symfony-3-4-debug-autowiring-command
- https://symfony.com/blog/new-in-symfony-3-4-debug-form-command

Pour avoir la liste complete :
`bin/console list debug`

### Debug Router

Doc officiel : https://symfony.com/doc/3.4/routing/debug.html

Pour avoir l'ensemble des routes avec leur nom et les URLs qui correspondent

`bin/console debug:router`

Exemple pour rechercher une route "produit" dans des controllers :

`bin/console debug:router --show-controllers | grep /product`

### Debug Container

Doc officiel : https://symfony.com/doc/3.4/service_container/debug.html

Pour avoir l'ensemble des services publiques

`bin/console debug:container`

- Pour rechercher un service par son nom : `bin/console debug:container validator`
- Pour rechercher un service par tag : `bin/console debug:container --tag=form.type`
- Pour rechercher un service par parametre : `bin/console debug:container --parameter=kernel.debug`

### Debug Config

Doc officiel : https://symfony.com/doc/3.4/reference/configuration/debug.html

Pour avoir l'ensemble des configuration

`bin/console debug:config`

- Pour rechercher pour un bundle : `bin/console debug:config AppBundle`

### Debug Twig

Pour avoir l'ensemble des fonctions des filtres, des tests et des globales disponibles ainsi que les chemins chargés par l'application

`bin/console debug:twig`

### Debug Swiftmailer

Doc officiel : https://symfony.com/doc/3.4/reference/configuration/swiftmailer.html

Pour avoir les mailers disponibles

`bin/console debug:swiftmailer`

### Debug Autowiring

Doc officiel : https://symfony.com/doc/3.4/service_container/debug.html

Pour avoir l'ensemble des services déclarés en auto-wiring :

`bin/console debug:autowiring`

### Debug Forms

Pour avoir l'ensemble des formulaires (Type, Extension, Type Guessers) disponibles : 

`bin/console debug:form`

### Debug Translations

Pour avoir l'ensemble des traductions par locale ou bundle :

`bin/console debug:translation`

### Debug routes JS

`bin/console fos:js-routing:debug`


# Commandes Oro

### Entity config Oro

Permet de récupérer la configuration d'une entité  :

`bin/console oro:entity-config:debug AppBundle\\Entity\\Norm`

Permet de récupérer la configuration d'un champ d'une entité :

`bin/console oro:entity-config:debug AppBundle\\Entity\\Norm code`

### Layout

_à compléter_

`bin/console oro:debug:layout`

### Workflow

_à compléter_

`bin/console oro:debug:workflow:definitions`