---
title: "Commandes Symfony, Oro et Doctrine"
date: 2022-04-29
type : "docs"
---

À partir de Oro 4.1, php est sorti de la stack Docker.

On lance désormais les commandes Symfony en local.

## 1. Commandes Symfony

**Avant :**

`bin/console cache:clear`

**Après :**

`symfony console cache:clear`

On utilise désormais l'outil client Symfony :  
- https://github.com/symfony-cli/symfony-cli
- https://symfony.com/doc/current/components/console.html

Pour lancer les commandes composer on peut également utiliser le client Symfony :

`symfony composer`

**Multiples versions de Php**

Quand il y a plusieurs versions de php, on peut ajouter un fichier `.php-version` dans le projet pour indiquer la version de php à utiliser.

https://symfony.com/doc/current/setup/symfony_server.html#different-php-settings-per-project

**Server local**

Lancement local d'un serveur :

- `symfony serve`
- `symfony server:start`

https://symfony.com/doc/current/setup/symfony_server.html#getting-started

**Activer TLS/HTTPS**

Activer certificat local

`symfony server:ca:install`

https://symfony.com/doc/current/setup/symfony_server.html#enabling-tls

## 2. Commandes Oro

### 2.1. Commande "platform update"

La commande platform:update d'Oro : https://github.com/oroinc/platform/blob/master/src/Oro/Bundle/InstallerBundle/Command/PlatformUpdateCommand.php

`symfony oro:platform:update --force --timeout=0`

Quand on lance la commande `platforma:update`, les commandes ci-dessus sont lancées dans cette ordre :

1. `oro:check-requirements`
2. `oro:migration:load` (comprend le rafraichissement de cache)
3. "load permissions"
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

Ajouter l'option `--timeout=0` (en local)

Ajouter l'option `--schedule-search-reindexation`
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

==> systématiquement apres un rm- var/cache/* et être sur qu'il n'y a pas de processus qui tourne et pourrait le reconstruit en parallèle


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

https://symfony.com/doc/6.1/doctrine.html

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