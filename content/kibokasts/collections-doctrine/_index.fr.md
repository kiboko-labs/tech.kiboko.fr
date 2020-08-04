---
title: "Collections Doctrine"
date: 2020-04-01
type : "docs"
---

* [Documentation](https://www.doctrine-project.org/projects/doctrine-collections/en/1.6/index.html)
* [Github](https://github.com/doctrine/collections/)

La collection sert à adresser le besoin de fournir des listes d'objets. Doctrine fournit une implémentation utilisée dans son ORM et ses ODM.

La collection permet de gérer une liste d'objets du même type, pour :

* filtrer
* trier
* appliquer un traitement (map)
* calculer un résultat d'après le contenu de la liste (reduce)

L'interface `Collection`
---

Il s'agit du contrat "Collection" de Doctrine, toute collection devra implémenter cette interface.

Cette interface implémente elle-même les contrats suivants :

* [`\Countable`](https://www.php.net/manual/fr/class.countable.php) : la collection pourra être utilisée avec la fonction `count()`, ce qui permettra de connaitre le nombre d'éléments contenus par la collection.
* [`\IteratorAggregate`](https://www.php.net/manual/fr/class.iteratoraggregate.php) : la collection pourra être parcourue dans une structure `foreach`
* [`\ArrayAccess`](https://www.php.net/manual/fr/class.arrayaccess.php) : la collection pourra être utilisée avec les opérateurs `[]`, de la même manière qu'on le fait avec un tableau

Les méthodes
---

### `map`

La méthode `map( callable $callback )` va appliquer la fonction de rappel passée en argument sur chaque
élément qu'elle contient et retournera une copie de la collection. Dans cette copie 
chaque élément aura été remplacé avec le résultat de cette fonction.

```php
<?php
$collection = new ArrayCollection(['SKU001', 'SKU002', 'SKU003']);

$mappedCollection = $collection->map(function(string $value): Product {
    return new Product($value);
}); // Retournera : [object (Product) { $sku = 'SKU001' }, object (Product) { $sku = 'SKU002' }, object (Product) { $sku = 'SKU003' }]
```

### `filter`

La méthode `filter( callable $callback )` va appliquer la fonction de rappel passée en argument sur chaque
élément qu'elle contient et retournera une copie de la collection. Dans cette copie, ne
restera que les éléments pour lesquels la fonction de rappel a renvoyé `true`

```php
<?php
$collection = new ArrayCollection([new Product('SKU001'), new Product('SKU002'), new Product('SKU003')]);

$mappedCollection = $collection->filter(function(Product $value): bool {
    return in_array($value->sku, ['SKU001', 'SKU003', 'SKU005']);
}); // Retournera : [object (Product) { $sku = 'SKU001' }, object (Product) { $sku = 'SKU003' }]
```

### `slice`

La méthode `slice( int $start, null|int $length )` va retourner une nouvelle copie de la collection contenant
uniquement les éléments commençant à la position `$start`, avec au maximum `$length` éléments à partir
de ce point si ce paramètre est fourni.

```php
<?php
$collection = new ArrayCollection([new Product('SKU001'), new Product('SKU002'), new Product('SKU003'), new Product('SKU004'), new Product('SKU005')]);

$mappedCollection = $collection->slice(2, 3);
// Retournera : [object (Product) { $sku = 'SKU002' }, object (Product) { $sku = 'SKU003' }, object (Product) { $sku = 'SKU004' }]
```