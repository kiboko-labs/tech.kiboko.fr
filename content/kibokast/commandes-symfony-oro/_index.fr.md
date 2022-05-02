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

Si vous souhaitez reprendre rapidement la main sur votre console et laisser l'indexation se lancer en tâche de fond, il faut ajouter l'option `--schedule-search-reindexation`
pour ne pas bloquer la console et remplir la MQ :

https://github.com/oroinc/platform/blob/master/src/Oro/Bundle/SearchBundle/EventListener/Command/ReindexationOptionsCommandListener.php

ou `--skip-search-reindexation` pour totalement passer l'indexation

Cette dernière option est à utiliser uniquement dans des situations où vous avez de grandes volumétries, comme un site multi-boutiques ou un site avec de nombreux produits. Vous devrez vous charger vous-même du lancement des commandes d'indexation.

Pour lancer la réindexation back-office :

`symfony oro:search:reindex --scheduled`

Pour lancer la réindexation d'un site web :

`symfony oro:website-search:reindex --website-id=2 --scheduled`

### 2.2 Commandes de cache

`symfony cache:clear`

Cette commande reconstruit un nouveau cache en conservant le cache existant vivre le temps de cette reconstruction. Une fois le cache reconstruit, l'ancien cache est remplacé par le nouveau. C'est cette commande qui crée les dossiers `var/cache/de_` et `var/cache/pro_` dans son processus de régénération de cache.

Cette commande ne met pas à jour le fichier `bundles.php` généré par Oro. Quand on ajoute un bundle Oro il faut absolument supprimer ce fichier.

`symfony cache:warmup`

Cette commande reconstruit un nouveau cache, Si un dossier de cache existe, le nouveau cache se cumulera avec le cache existant. Il es impératif de lancer cette commande après un `rm -rf var/cache/*` et de s'assurer que les processus de *Worker de MQ* ou se *serveur Websocket* ou qu'aucune requête HTTP ne soit en cours d'exécution. Si ce dernier critère n'est pas suivi, le cache aura une chance d'être corrompu.

### 2.3. Commandes d'assets

`symfony assets:install --symlink`

Cette commande permet d'effectuer une copie des assets  de l'application et des bundles dans le dossier `public/bundles`. La seule limite à cette commande est l'ajout d'un nouveau fichier d'assets. À chaque création de nouveau fichier il faudra relancer cette commande.

`symfony oro:assets:build`

Cette commande lance le build webpack. Elle permet avec l'option `--watch` de lancer le watcher Webpack, qui permet d'avoir des mises à jour en temps réel dans le navigateur sans avoir à rafraichir la page.

On peut la restreindre sur un seul theme pour la partie front en précisant le nom du thème en paramètre comme ceci :

`symfony oro:asset:build --watch mon_theme`

La commande `symfony oro:assets:install --symlink` permet de générer tous les assets, dans le cas où on ne ferait pas de développements front-end. En fond, 4 commandes sont lancées :
1. `fos:js-routing:dump`
2. `oro:localization:dump`
3. `assets:install --symlink`
4. `oro:assets:build`


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
- `symfony oro:platform:update --force --timeout=0`
- `symfony oro:assets:install`
