---
title: "Tips"
date: 2020-12-01
type : "docs"
---

## Health Checkers

Behat a une possibilité native d'invoquer des formateurs sans exécuter les tests et les hooks.

Vous pouvez essayer:

````yaml
 bin/behat --dry-run
````

Cela peut être utile au cas où vous n'êtes pas sûr d'avoir déclaré tout le contexte nécessaire à votre fonctionnalité.
OroBehatExtension améliore cette fonctionnalité et ajoute des fonctionnalités supplémentaires.

## Erreurs

**Erreur courante : Couldn’t generate random unique value for OroBundleUserBundleEntityUser: username in 128 tries** 

hot fix: 

Vérifiez votre fixtures. Supprimez le suffixe (unique) dans la propriété d'entité dans le luminaire d'entité, comme dans l'exemple suivant:

### Comment trouver le nom d'une étape

Lorsque vous concevez des scénarios d'automatisation de test pour la nouvelle fonctionnalité, vous pouvez avoir du mal à trouver des étapes à réutiliser parmi les centaines d'étapes déjà automatisées.
Utilisez les conseils ci-dessous pour trouver l'étape nécessaire.

Suggestion automatique dans PhpStorm

Lors de la conception d'un scénario dans le fichier de fonctionnalités, PhpStorm vous propose des conseils sur les étapes implémentées qui correspondent aux mots clés. Par exemple, lorsque vous tapez grid ou form, les étapes qui impliquent ces éléments apparaissent dans le bloc de suggestions.

![Step Suggestion](images/phpstorm_step_suggestion.png)

Si PhpStorm ne vous propose aucun conseil lors de la frappe, veuillez vérifier les points suivants:

1. Vous avez installé les vendors
2. Vous avez installé le plugin behat pour PhpStorm

Trouvez le contexte nécessaire

Chaque classe Context doit implémenter l'interface `Behat\Behat\Context\Context`. Obtenez la liste des contextes implémentés et trouvez celui nécessaire par nom.

![Get Context by name](images/context_implements_interface.png)

![Get Context by file](images/find_context.png)

Habituellement, le nom du contexte est explicite, par exemple, GridContext, FormContext, ACLContext, etc.

Utiliser Grep dans la console

Si, pour une raison quelconque, vous n'utilisez pas PhpStorm ou le plugin Behat, vous pouvez toujours trouver l'étape nécessaire en filtrant la sortie de la commande qui prévisualise toutes les étapes de la fonctionnalité (utilisez Grep).

Tapez la commande suivante dans votre console:

`bin/behat -dl -s AcmeDemoBundle | grep "flash message"`

![Grep console](images/grep_flash_messages.png)

`bin/behat -dl -s AcmeDemoBundle | grep "grid"`

![Grep console 2](images/grep_grid.png)

PS :  Vous ne pouvez utiliser l'interface de ligne de commande behat qu'après avoir installé l'application.
