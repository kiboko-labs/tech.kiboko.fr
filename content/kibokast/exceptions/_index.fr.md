---
title: "Exceptions"
date: 2020-11-20
type : "docs"
---

# Les Exceptions en php

### Qu'est qu'une exception

- Objet lancé lors d'une erreur
- Permet de revenir à un état stable
- Remonte la pile d'exécution les plus rapidement possible jusqu'à ce qu'elle soit rattrapé (gérer)
- Implémente l'interface `Throwable` qui permet de "catcher" une erreur
- A ne pas confondre avec Error (souvant lancé par PHP lui-même)

### Information d'une exception

````
public function __construct($message=""; $code=0, Throwable $previous=null) { }
````

4 éléments importants :
- Message
- Fichier source de l'exception
- Code d'exception
- Stack trace

### 4 outils

- Throw
- Try
- Catch
- Finally

### Liste des exceptions PHP

- Exceptions SPL (PHP) : https://www.php.net/manual/fr/spl.exceptions.php
- Exceptions Réservées (Zend) : https://www.php.net/manual/fr/reserved.exceptions.php

### exception Handler

````
function handler($exception) {
    echo ""
}
(...)
````

# Bonnes pratiques

- Gérer les exceptions de la plus spécifique à la moins spécifique

- Gérer plusieurs exceptions à la fois 

````
try {
}
(...)
````

- Finnaly

````
function getNumber(): int {
    try {
        echo "Hello";
    } catch() {
    } finally {
    }
}
````

# Nommage

### Bien nommer les exceptions

Mauvaise pratique :

````
(...)
````

Bonne pratique :

````
class ArgentException extends Eception{};
````

### Contenu de l'exception 

Toujours fournir un maximum d'information pour le contexte

````
(...)
````

### Utiliser le I de SOLID

````
(...)
````

Ne pas hésiter à utiliser des interfaces

### Gérer l'expetion au bon moment

````
function actionType($username)
{
    (...)
}
````

Si vous ne savez pas comment gérer l'exception, laissez la remonter. C'est surement du ressort des classes au dessus.

# Conclusion

Une exception est événement qui arrive à un moment dans l'appplication.

Cet événement intervient lorsques quelque chose de problématique est intervenue. La cause peut être trés varié.
(...)

### Sources :



