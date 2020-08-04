---
title: "Utilisation Tmetric"
date: 2020-03-06
type : "docs"
---

Support officiel : https://tmetric.com/support/

## 1. Outils

- web app : https://app.tmetric.com/

- extension navigateur  (indispensable)

Google Chrome : https://chrome.google.com/webstore/detail/tmetric-%E2%80%93-time-tracker-pr/ffijoclmniipjbhecddgkfpdafpbdnen

Mozilla Firefox : https://addons.mozilla.org/fr/firefox/addon/tmetric-extension/

=> permet d'ajouter un bouton "start timer" sur le ticket (Jira/Github)

- desktop app : https://tmetric.com/download


## 2. Saisie de temps

#### 2.1. Depuis un ticket

Dès que lon commence une tache, on déclenche l'enregistrement du temps via le bouton "Start timer" présent sur le ticket (ou la PR), une modal s'ouvre :

- **task** : correspond au nom du ticket (avec son numéro)
- **notes** : surcharge le nom du ticket si modifié
- **project** : :warning: bien vérifier que le projet dans la liste déourlante est bien le bon :warning: 
- **tags** : non utilisé dans Tmetric, il est préférable de les retirer à ce jour

(changement à prévoir lors de l'utilisation de la version )

#### 2.2. Tache interne

Si la tache effectuée n'a pas de ticket, c'est une tache interne (sinon on créé le ticket manquant sur le projet)

On peut donc ajouter un "Time entry" depuis l'application web https://app.tmetric.com/ ou desktop.

Dans la description, utiliser l'auto-complete si la mission existe déjà et ajouter dans Project "Kiboko interne".

Sinon on peut renseigner l'information.
 

## 3. Autres vues

### 3.1. Vue "My time" 

=> https://app.tmetric.com/

Sur cette vue, on peut suivre et vérifier sa saisie de temps et la modifier simplement si il y a des erreurs.


### 3.2. Vue "Projects" 

Liste les différents projets disponibles, si il en manque merci de prévenir Xavier (admin) ou Dimitri (owner).


### 3.3. Vue "Reports"

Permet d'analyser le temps saisie par projet, tache ou équipe.
Cette fonctionnalité permet d'exporter les données de l'équipe au format .csv afin de créer les CRA par client.