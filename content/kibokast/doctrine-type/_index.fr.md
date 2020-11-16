---
title: "Doctrine type"
date: 2020-11-13
type : "docs"
---

# Les types Doctrine

## Les numériques

- _integer_ **smallint** : pas plus de 65 535
- _integer_ **integer** : jusqu'à 4 millions, ou alors +2 millions/-2 millions
- _string_ **bigint** : sur 64 bits. c'est beaucoup
- _string_ **decimal** : si on connaît le nombre de chiffres après la virgule
- _float_ **float** : précision pas très importante, ça peut être arrondi

## Les chaînes

- _string_ **string** : taille limitée par la DB, Doctrine peut le passer en texte
- _string_ **text** : no limit
- _string_ **guid** : global unique identifier. ex: filename iPhone, registre Windows
- _resource_ **binary** : images, médias, taille limitée par la DB, Doctrine peut le passer en blob
- _resource_ **blob** : no limit

## Les bits

- _boolean_ **boolean** : true/false (en réalité c'est juste un int)

## Les dates

- _\DateTime_ **date**		date
- _\DateTime_ **datetime**	date et heure
- _\DateTime_ **datetimetz**	date, heure et décalage horaire (par rapport à UTC)
- _\DateTime_ **time**		heure
- _\DateTime_ **dateinterval**	écart entre dates
- _\DateTimeImmutable_ **datetime_immutable**
- _\DateTimeImmutable_ **date_immutable**
- _\DateTimeImmutable_ **time_immutable**

#### Mutable

````
$date = new \DateTime();
echo $date->format('y-m-d');	// '2020-09-25'
$bidule = $date->modify('+3 month');
echo $bidule->format('y-m-d'); // '2020-12-25'
echo $date->format('y-m-d');	// '2020-12-25' WTF ??
````

#### Immutable

````
$date = new \DateTimeImmutable();
echo $date->format('y-m-d');	// '2020-09-25'
$bidule = $date->modify('+3 month');
echo $bidule->format('y-m-d'); // '2020-12-25'
echo $date->format('y-m-d');	// '2020-09-25'
````

## Les arrays

- _array_ **array** : sérialisé sous forme de texte dans la base
- _array_ **simple_array** : utilise implode et explode sur les virgules, tableau 1-dimension
- _array_ **json** : utilise json_decode pour la serialisation

## Les objets

- _object_ **object** : marche pas bien, il vaut mieux sérialiser puis enregistrer en text

Explications: https://www.doctrine-project.org/projects/doctrine-dbal/en/2.12/reference/types.html#object

## Les types custom

Ex : On veut un type Money.

`CREATE DOMAIN MyMoney AS DECIMAL(18,3);`

````
use Doctrine\DBAL\Types\Types
final class MoneyType extends Type
{
````

| | | 
|---|:---:| 
| getSQLDeclaration() | 'MyMoney' |
| convertToPHPValue() | Money($value) |
| convertToDatabaseValue() | $value->toDecimal() |
| getName() | 'money' |

Lien vers la doc. Doctrine : https://www.doctrine-project.org/projects/doctrine-dbal/en/2.12/reference/types.html#custom-mapping-types

