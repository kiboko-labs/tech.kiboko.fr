---
title: "Conventions"
date: 2020-12-01
type : "docs"
---

## Conventions

Utilisez le mapping de formulaire au lieu de sélecteurs dans vos scénarios pour les garder clairs
et compréhensibles pour les personnes du monde technique et non technique.

### Exemples 

Ne pas utiliser des selecteurs CSS dans le test:

```
I fill in "oro_workflow_definition_form[label]" with "User Workflow Test"
I fill in "oro_workflow_definition_form[related_entity]" with "User"
```

Mais utilisez plutôt des raccourcis:

```
 And I fill "Workflow Edit Form" with:
   | Name                  | User Workflow Test |
   | Related Entity        | User               |
```

Que vous définnissez dans le fichier yml :

```
 Workflow Edit Form:
   selector: 'form[name="oro_workflow_definition_form"]'
   class: Oro\Bundle\TestFrameworkBundle\Behat\Element\Form
   options:
     mapping:
       Name: 'oro_workflow_definition_form[label]'
       Related Entity: 'oro_workflow_definition_form[related_entity]'
```

Utilisez des menu et des liens pour obtenir les bonnes pages au lieu de l'URL de la page directe

Faire :

```
And I open User Index page
```

Et non pas :

```
And I go to "/users"
```

Évitez la redondance des scénarios (par exemple, en répétant la même séquence d'étapes, comme la connexion, dans plusieurs scénarios).
Pour cela chainez vos tests dans un ordre logique.

- **Nommer les éléments dans le style camelCase sans espaces**

Vous pouvez toujours vous y référer en utilisant le style camelCase avec des espaces dans les scénarios behat.
Par exemple, un élément nommé OroProductForm peut être mentionné dans l'étape du scénario comme «Oro Product From»:
