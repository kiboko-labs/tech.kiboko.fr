---
title: "Commandes Symfony, Oro et Doctrine"
date: 2022-04-29
type : "docs"
---

À partir de Oro 4.1, Oro a fourni sa propre stack Docker. La différence majeure par rapport à la stack Kiboko est l'utilisation de symfony CLI  à la place de services php.

On lance désormais les commandes Symfony en local.

## 1. Commandes Symfony

**Avant :**

`docker compose exec sh bin/console cache:clear`

**Après :**

`symfony console cache:clear`

On utilise désormais Symfony CLI :  
- https://github.com/symfony-cli/symfony-cli
- https://symfony.com/doc/current/components/console.html

Symfony CLI permet également de lancer les commandes `composer` et `php`.
> ⚠︎ Toutes les commandes `php`, `composer` et `bin/console` doivent être lancées en utilisant Symfony CLI

`symfony composer`

**Multiples versions de Php**

Quand il y a plusieurs versions de php installées sur votre poste, il est préférable de forcer la version utilisée sur le projet. Il faut pour cela ajouter un fichier `.php-version` à la racine du projet pour indiquer la version de php à utiliser.

https://symfony.com/doc/current/setup/symfony_server.html#different-php-settings-per-project

**Server local**

Pour lancer le serveur HTTPS local :

- Soit `symfony serve`
- Soit `symfony server:start`

Pour arrêter le serveur HTTPS local :
- Soit faire un `CTRL` + `C` dans la console qui a lancé le serveur
- Soit `symfony server:stop`
https://symfony.com/doc/current/setup/symfony_server.html#getting-started

**Activer TLS/HTTPS**

Pour que le HTTPS soit fonctionnel, il faut ajouter les certificats TLS sur le poste local avec la commande suivante :

`symfony server:ca:install`

https://symfony.com/doc/current/setup/symfony_server.html#enabling-tls

## 2. Commandes Oro

### 2.1. Commande "platform update"

La commande `oro:platform:update` d'Oro : https://github.com/oroinc/platform/blob/master/src/Oro/Bundle/InstallerBundle/Command/PlatformUpdateCommand.php

`symfony oro:platform:update --force --timeout=0`

Quand on lance la commande `oro:platform:update`, les commandes ci-dessous sont lancées dans cet ordre :

1. `oro:check-requirements`
2. `oro:migration:load` (comprend le rafraichissement de cache d'entités)
3. `security:permission:configuration:load`
4. `oro:cron:definitions:load`
5. `oro:workflow:definitions:load`
6. `oro:process:configuration:load`
7. `oro:migration:data:load`
8. `router:cache:clear`
9. `oro:message-queue:create-queues`
10. `assets:install`
11. `fos:js-routing:dump`
12. `oro:localization:dump`
13. `oro:translation:dump`
14. `oro:assets:build`

Si vous avez un grand nombre de migrations, ou que votre migration a un risque de dépasser 300 secondes d'exécution, il faut ajouter l'option `--timeout=0`

Si vous souhaitez reprendre rapidement la main sur votre console et laisser l'indexation se lancer en tâche de fond, il faut ajoueer l'option `--schedule-search-reindexation`
pour ne pas bloquer la console et remplir la MQ :

https://github.com/oroinc/platform/blob/master/src/Oro/Bundle/SearchBundle/EventListener/Command/ReindexationOptionsCommandListener.php

ou `--skip-search-reindexation` pour totalement passer l'indexation back et front

à utiliser uniquement dans certaines situations notament pour une multi-boutiques qui a de nombreux produits, il faudra ensuite lancer les commandes d'indexation telles que :

`symfony oro:search:reindex --scheduled`

`symfony oro:website-search:reindex --website-id=2 --scheduled`


### 2.2 Commandes de cache

`symfony cache:clear`

laisse le cache existant, en construit un à côté puis quand c'est fini il inverse les 2 (de_ et pro_)

- evite les problemes dans les sockets et messages queue
- ne met pas à jour le fichier bundles.php (quand on ajoute un bundle il faut supprimer ce fichier)

`symfony cache:warmup`

=> reconstruit seulement, se cumule avec le cache existant

==> systématiquement apres un rm -rf var/cache/* et être sur qu'il n'y a pas de processus qui tourne et pourrait le reconstruire en parallèle


### 2.3. Commandes d'assets

`symfony assets:install` => commande symfony
`symfony oro:assets:build` => uniquement le build webpack

`symfony oro:assets:install` => 4 commandes sont lancées :
1. fos:js-routing:dump
2. oro:localization:dump
3. assets:install
4. oro:assets:build


## 3. Commandes Doctrine

`symfony doctrine:schema:update --dump`

https://symfony.com/doc/6.0/doctrine.html

`symfony doctrine:migrations`

https://symfony.com/bundles/DoctrineMigrationsBundle/current/index.html#usage

=> déconseillé sur Oro

sur Oro :
`symfony oro:migration:dump`

=> générer un fichier de migration schema

https://doc.oroinc.com/backend/entities/migration/

ne fonctionne pas avec les extensions (ex: extendExtension)


## 4. Conclusion

Privilégier le lancement de commandes génériques :
- `symfony oro:platform:update`
- `symfony oro:assets:install`
