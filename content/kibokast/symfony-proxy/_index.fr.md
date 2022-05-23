---
title: "Proxy Symfony"
date: 2022-05-23
type : "docs"
---

## Pourquoi utiliser le Proxy de Symfony ?

Le Proxy de symfony permet d'utiliser des noms de domaines HTTPS en local, à la manière du fichier `etc/hosts`.

L'avantage est que le proxy fournit un certificat et permet de tester en local les fonctionnalités reservées au HTTPS (localisation, différente gestion des cookies, etc...)

Autre avantage: on évite de modifier le fichier `etc/host` pour ajouter ou enlever un domaine, et symfony fournit une commande `symfony proxy:status` pour lister les domaines actifs et leur répertoire.

## Mise en place

#### 1. Activer TLS

`symfony server:ca:install`

Crée et installe le certificat, pour faire croire au navigateur que les domaines sont en HTTPS.

#### 2. Paramètrer le proxy

Dans les paramètres réseau de l'OS ([linux](https://help.ubuntu.com/stable/ubuntu-help/net-proxy.html.en), [mac](https://support.apple.com/guide/mac-help/enter-proxy-server-settings-on-mac-mchlp2591/mac)), il faut trouver l'option "Configuration automatique du proxy" et lui assigner l'URL:

`http://127.0.0.1:7080/proxy.pac`

#### 3. Lancer le proxy

`symfony proxy:start`

## Utilisation

#### Ajouter / enlever un domaine

<code>symfony proxy:domain:attach <b>domaine</b></code>

<code>symfony proxy:domain:detach <b>domaine</b></code>

#### Voir la liste des domaines

[Accedez à 127.0.0.1:7080](http://127.0.0.1:7080)

ou lancez

`symfony proxy:status`

---

Plus de détails dans [la documentation de symfony server](https://symfony.com/doc/current/setup/symfony_server.html#setting-up-the-local-proxy)
