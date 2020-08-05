---
title: "Regex"
date: 2020-05-20
type : "docs"
---

Expressions régulières
===

Doc. php : https://www.php.net/manual/fr/pcre.pattern.php

Alternatives
---

- ```(abc|def)``` alternative avec capture : recherche `abc` ou `def` et fournira le résultat trouvé
- ```(?:abc|def)``` alternative sans capture : recherche `abc` ou `def`, mais ne fournira pas le résultat trouvé
- ```(?:(Lun|Mar)di)``` alternative avec capture interne : recherche `Lun` ou `Mar` et fournira le résultat trouvé respectivement dans les positions `1` et `2`
- ```(?|(Lun|Mar)di)``` alternative avec capture interne : recherche `Lun` ou `Mar` et fournira toujours le résultat trouvé dans la position `1`
- ```(<(?:span|div)>Lorem Ipsum</\\1>)``` alternative sans capture et référence arrière

Classes de caractères
---

### Plages de caractères

- ```.``` recherche tout caractère
- ```\d``` recherche un des caractères entre `0` et `9`
- ```\.``` recherche le caractère `.`
- ```[2-6]``` recherche un des caractères entre `2` et `6`
- ```[a-z]``` recherche un des caractères entre `a` et `z`
- ```[A-Z]``` recherche un des caractères entre `A` et `Z`
- ```[a-zA-Z]``` recherche un des caractères entre  `a` et `z` et entre `A` et `Z`
- ```[a-zA-Z0-9]``` recherche un des caractères entre  `a` et `z`, entre `A` et `Z` et entre `0` et `9`

### Répétitions

- ```[a-z0-9-]+``` recherche les caractères 1 fois ou plus
- ```[a-z0-9-]*``` recherche des caractères 0 fois ou plus
- ```[a-z0-9-]?``` crecherche des caractères 0 fois ou 1 fois

### Recherche inversée

- ```[^,]*``` recherche tout caractère différent de `,`
- ```[^a-z]*``` recherche tout caractère différent de la plage entre `a` et `z`

### Ancres

- ```^/catalog/``` (importance du `^`) recherche toute chaine qui commence par `/catalog/`
- ```\\.html$``` (importance du `$`) recherche toute chaine qui termine par `.html`
- ```^(?:/.*|)(/[a-z0-9-]+)\.html$``` recherche toute chaine qui termine par `.html` et ne capture que la dernière partie, ex :
  - `/lorem-ipsum.html` deviendra `/lorem-ipsum`
  - `/lorem-ipsum/dolor-sit-amet.html` deviendra `/dolor-sit-amet`