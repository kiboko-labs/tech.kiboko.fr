---
title: "Utilisation stack Docker Kiboko"
date: 2020-01-24
type : "docs"
---

## 1. Initialisation d’un projet sur dev0 avec cette stack

### 1.1. Initialisation du projet

- cloner le projet application en local `git clone git@github.com:kiboko-labs/{nom-du-projet}.git`
- connexion ssh sur dev0 
> `ssh kiboko@dev0.kiboko.io`
- se rendre dans le répertoire `www/`
> cd www/
- cloner le projet dans un répertoire nominatif tel que : 
> `git clone git@github.com:kiboko-labs/{nom-du-projet}.git {nom-du-projet}-{NOM-DU-DEV}`
- se rendre dans le répertoire créé 
> cd {nom-du-projet}-{NOM-DU-DEV}
- créer ma configuration phpstorm pour le déploiement sur dev0 (remote host)
    - dans l'onglet `Mapping`, dans le champs `Deployment path`, mettre un `/` 
    - définir le remote en sftp et `par défaut` 
> si vous aviez déjà au moins 1 remote paramétré et `par defaut`, il faut forcer le remote dev0 `par defaut` car c'est le remote `par defaut` qui sera automatiquement synchronisé depuis votre local
> DANGER : si `par defaut` est une prod

- activer le déploiement automatique : `Tools/Déployment/Automatic upload` (les modif. locales à l’enregistrement seront deployées automatiquement)

- creation du fichier .env à partir du fichier .env.dist 
	- avec des ports uniques (published port)
	- ajout du token Github
	- ajout des tokens Blackfire ou supprimer les valeurs

#### 1.1.1. Installation vierge

- `make start` (lance tous les services sauf WS et MQ)
- `make install` (ne pas remplir le parameters.yml à la main attendre que l’installeur le demande)

#### 1.1.2. Installation avec un dump

- `make start`
- importer son dump dans le service sql
- `make vendor platform-update`

### 1.2. Lancement du MQ et WS avant exploitation

- `make start-{mq,ws}`


### 1.3.Exploitation

- voir la consommation des messages : docker-compose logs -f amqp`
- accès à RabbitMQManagement ex : http://dev0.kiboko.io:10006/
- accès au mailcatcher ex :  http://dev0.kiboko.io:10004/
- accès à ElasticSearch via DejaVu ex : http://dev0.kiboko.io:10009/

Pratique : quand vous n’utilisez plus l’environnement, make stop

### 1.4. Vider le cache

- `make cache-clear` pour le cache de l'envirnonnement `prod`
- `make cache-clear-dev` pour le cache de l'environnement `dev`

### 1.5 Changer de branche

Il est impératif de faire changer de branche sur l'environnement local *et* l'environnement distant. L'environnement distant réclamera d'être committé ou remis à l'état original.

**⚠️ Aucun commit ne doit être fait depuis l'environnement distant**

* Pour remettre les fichiers à leur état original : `git reset --hard`
* Pour checkout la nouvelle version de travail : `git checkout <branch>`

## 2. Usages particuliers

- utilisation xdebug via le service DBGp (à venir)


## 3. Ressources tiers

- extension navigateur qui permet de switcher en mode dev, prod et debug: 
https://github.com/kiboko-labs/kiboko-symfony-env

> sur Chrome : 
> - ajout extensions [chrome://extensions/](chrome://extensions/)
> - activer le mode développeur
> - charger l'extension non empaquetée

- images docker :
https://github.com/kiboko-labs/docker-images