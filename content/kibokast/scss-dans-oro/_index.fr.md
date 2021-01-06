---
title: "SCSS dans Oro"
date: 2020-12-18
type : "docs"
---

## Qu'est ce que le SCSS (et le SASS) ?

Ce sont des préprocesseurs CSS

- **SASS** = Syntactically Awesome Style Sheets : https://sass-lang.com/documentation/
- **SCSS** = Sassy CSS c'est-à-dire du CSS dans le style de SASS.

Avec la version 3 de SASS, SCSS a été défini comme **la syntaxe officielle**.

La plus grande différence réside dans l’utilisation des crochets et des points-virgules.
- SASS plus proche du Yaml niveau visuel
- SCSS plus proche du CSS niveau visuel

**Outil similaire :** Less http://lesscss.org/ (basé sur du JS contrairement au Sass basé sur du Ruby)

### 1. Avantages/Inconvénients

| Avantages | Inconvénients |
|---|:---:| 
| Plus de possibilités	| Le code doit être compilé | 
| Code plus clair |	Résolution des erreurs difficiles | 
| Frameworks étendus	| Effort d’apprentissage | 
| Travail avec des mixins et des variables | | 

### 2. Différences entre CSS et SCSS

Liste de fonctionnalités SCSS trés pratiques : https://sass-lang.com/documentation/at-rules

En voici quelqu'unes en détails.

#### 2.1. Utilisation de variables

On peut déclarer des variables avec cette syntaxe : 

`$variable: value;`

Exemples : 

**CSS**

```
body{
    color: #ffffff;
    font: 'Ubuntu', 'Arial', 'Helvetica', sans-serif;
    font-size: 18px;
}
```

**SCSS**
```
$white: #ffffff;
$ubuntu-font: 'Ubuntu', 'Arial', 'Helvetica', sans-serif;
$md-font: 18px;

body{
    color: $white;
    font: $ubuntu-font;
    font-size: $md-font;
}
```

Trés pratique pour les couleurs, taille de typo, les fonts mais aussi pour les points de ruptures pour le responsive :

```
$sm-font: 14px;
$md-font: 18px;
$xl-font: 22px;

$grid--breakpoint-sm: 480px;
$grid--breakpoint-md: 768px;
$grid--breakpoint-lg: 1024px;

@media (max-width: $grid--breakpoint-sm) {
    body {
        font-size: $sm-font;
    }
}

```

Attention on peut utiliser uniquement les variables déclarés dans le fichiers courants (sauf si on utilise des imports).

#### 2.2. Utilisation des imports

Possibilité d'importer des fichiers SASS :

```
@import 'components/page-container';
@import 'components/page-header';
@import 'components/page-content';
@import 'components/page-footer';
@import 'components/page-title';
```

Exemple concret :

__variables.scss_
```
$sm-font: 14px;
$md-font: 18px;
$xl-font: 22px;
```

_style.scss_
```
@import 'variables';
   
body {
    font-size: $sm-font;
}
```

avec ces 2 fichiers au même niveau.

#### 2.3. Utilisation de mixins

Pour simplifier et éviter de répéter tout le temps les mêmes lignes, on peut créer des mixins :

**Mixin simple**
```
@mixin reset-list {
  margin: 0;
  padding: 0;
  list-style: none;
}

nav ul {
  @include reset-list;
}
```

**Mixin avec variables**
```
@mixin box-shadow($left, $top, $radius, $color) {
    box-shadow: $left $top $radius $color;
    -webkit-box-shadow: $left $top $radius $color;
    -moz-box-shadow: $left $top $radius $color;
}

input {
    @include box-shadow(10px 5px 5px red);
}
```

#### 2.4. Méthodologie BEM

BEM = une convention CSS qui va nous aider à rendre nos classes plus compréhensibles, et ce grâce au nommage.

- **B**loc
- **E**lément
- **M**odificateur

**Exemple :**

SCSS
```
.block{
    background: #000;

    .block__element {
      color: #fff;
    }
}
```

CSS généré
```
.block {
    background: #000;
}
.block .block__element {
    color: #fff;
}
```

SCSS avec méthodologie BEM
```
.block {
    background-color: #000;

    &__element {
        color: #fff;
    }
}
```

CSS généré
```
.block {
    background: #000;
}
.block__element {
    color: #fff;
}
```

Ressources externes pour avoir plus d'informations sur la méthodologie BEM : 
- https://openclassrooms.com/fr/courses/6106181-simplifiez-vous-le-css-avec-sass/6596494-utilisez-des-selecteurs-bem-avec-sass
- https://medium.com/@andrew_barnes/bem-and-sass-a-perfect-match-5e48d9bc3894
- https://www.alsacreations.com/article/lire/1641-Bonnes-pratiques-en-CSS--BEM-et-OOCSS.html#bem

## 2. Dans Oro

Documentation officiele pour l'usage front : https://doc.oroinc.com/frontend/storefront/css/

## 2.1. Utilisation et déclaration

Exemple d'un usage simple pour ajouter des assets .Scss pour le front :
 
Dans le fichier `src/AppBundle/Resources/views/layouts/montheme/config/assets.yml`

```
styles:
    inputs:
        - 'bundles/app/montheme/scss/settings/global-settings.scss'
        - 'bundles/app/montheme/scss/variables/base-config.scss'
        - 'bundles/app/montheme/scss/variables/page-header-config.scss'
        - 'bundles/app/montheme/scss/variables/page-content-config.scss'
        - 'bundles/app/montheme/scss/variables/page-footer-config.scss'
        - 'bundles/app/montheme/scss/styles.scss'
    filters: ['cssrewrite', 'cssmin']
    output: 'css/styles.css'
```

L'option `filters` est optionnel.

**NOTE**
Dans la partie `inputs`, il faut renseigner l'emplacement du fichier dans le répertoire `/public/`
Visible une fois avoir lancer la commande `bin/console assets:install`.

## 2.2. Commandes SF et Oro

Source : https://doc.oroinc.com/backend/bundles/platform/AssetBundle/

`bin/console oro:assets:build` : cette command lance webpack pour construire les assets. (et donc compiler le SCSS en CSS)

- En mode dév., la commande créé les ressources sans minification et avec les fichiers sources.
- En mode production, les ressources sont minifiées et n'incluent pas les fichiers sources.

> **Use Commands**
>
> `oro:assets:build` [-w|--watch] [-i|--npm-install] [--] [<theme>]
>
> `oro:assets:build admin.oro` to build assets only for the default management-console theme called admin.oro
>
> `oro:assets:build default --watch` to build assets only for the blank theme with enabled watch mode
>
> `oro:assets:build default -skip-js` to build assets without JS

**NOTE**
Lorsque vous utilisez le mode "Watch" après avoir modifié la configuration dans le fichier assets.yml, il est nécessaire de redémarrer la commande ; sinon, il ne détectera pas les changements.
