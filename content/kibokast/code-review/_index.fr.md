---
title: "Code review"
date: 2020-05-05
type : "docs"
---

## 1. Cycle de vie des PRs
- Pour la création des PRs, se reporter à la [procédure décrite dans les usages Github](usage.md#3-pull-request-pr)
- **Pas plus de 2 PRs ouvertes par personne** : tant que j'ai encore une PR non mergée, je n'entame pas de nouvelle tâche.
- **Tout le monde relit les PRs** de tout le monde
- Afin de fluidifier les développements, **il est important de relire les PRs plusieurs fois par jour** (ex : le matin en arrivant, puis après la pause déjeuner. Possibilité de relire à 2 si besoin)
- **Tout le monde peut merger** les PRs en respectant les règles de validité ci-dessous

## 2. Règles de validité d'une PR
### Prérequis
- Chaque branche doit contenir un numéro de ticket : `feature/XXX-new-feature`
- **Chaque commentaire de commit doit contenir un numéro de ticket** : `#XXX : Add new feature`
- Les tests unitaires doivent passer
- La PR doit avoir le libellé "GTM"
- La PR doit avoir une taille raisonnable
- Les commits doivent être bien découpés (pas de "fix, re-re-fix, wip")

### Règles de merge
Je merge une PR si elle respecte les critères suivants :
- Respect des prérequis décrits ci-dessus
- Déjà **2 autres personnes** ont approuvé la PR **dont un dev expérimenté**
- **Toutes les discussions sont marquées comme résolues**
- ⚠️ **Elle pointe sur la branche develop** ⚠️
- Je vérifie que le milestone est le bon pour faciliter la rédaction des notes de version

### Approbation et correction
#### En tant que relecteur
- Lorsque j'ai une question ou un retour à faire sur une PR, **je mets un commentaire** sur la ligne concernée (en restant courtois dans mes insultes)
- Lorsque je n'ai plus de retours à faire, **j'approuve la PR**. Si elle respecte les règles de validité, **je la merge sur develop**.

#### En tant que développeur
- Lorsque je corrige ma PR, **je réponds** au commentaire (en restant courtois dans mes insultes) et **je marque la discussion comme résolue**