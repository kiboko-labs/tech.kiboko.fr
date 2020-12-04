---
title: "Principe de substitution de Liskov"
date: 2020-12-04
type : "docs"
---

Dans la conception OOP, nous pouvons utiliser des méthodes et des principes pour produire des architectures logicielles plus :
- compréhensibles
- flexibles 
- et maintenables

#### Les principes SOLID

https://fr.wikipedia.org/wiki/SOLID_(informatique)

- **S** Responsabilité unique (Single responsibility principle) : une classe, une fonction ou une méthode doit avoir une et une seule responsabilité
- **O** Ouvert/fermé (Open/closed principle) : une entité applicative (class, fonction, module ...) doit être ouverte à l'extension, mais fermée à la modification
- **L** Substitution de Liskov (Liskov substitution principle) : une instance de type T doit pouvoir être remplacée par une instance de type G, tel que G sous-type de T, sans que cela ne modifie la cohérence du programme
- **I** Ségrégation des interfaces (Interface segregation principle) : préférer plusieurs interfaces spécifiques pour chaque client plutôt qu'une seule interface générale
- **D** Inversion des dépendances (Dependency inversion principle): il faut dépendre des abstractions, pas des implémentations

#### L : le principe de substitution de Liskov

https://fr.wikipedia.org/wiki/Principe_de_substitution_de_Liskov

Si la classe B étends la class A, alors tout objet de type A peut être remplacé par un objet de type B sans altérer les propriétés désirables du programme concerné.

**Exemple de problématique :**

````
class Voiture {
    public function avancer() {
        echo 'avancer';
    }
    public function reculer() {
        echo 'reculer';
    }
    public function changerPneu() {
        echo 'changerPneu';
    }
}
````

On gère des voitures dans notre application

plus tard dans le développement on nous dit de rajouter des voitures volantes

Une voiture volante est un voiture ?

La méthode changerPneu nous embête, qu'est qu'on peut faire ?

````
class VoitureVolante extends Voiture {
    public function changerPneu() {
        // mauvaises solutions envisageables
        
        // 1) ne rien faire

        // 2) lancer une exception
        throw new Exception('pas de pneus sur une voiture volante');

        // 3) Appeler une méthode qui fait plus ou moins la même chose
        $this->changerTrainDaterssiage(); 
    }

    public function changerTrainDaterssiage() {
        echo 'changerTrainDaterssiage';
    }
}
````

**Solution :** 
- ne pas étendre la class voiture et créer une classe dédiée Voiture volante (une voiture volante != une voiture)
- utilisation du pattern `Adapter` qui permettrai de "adapter" une voiture volante en voiture

#### Les moyens pour savoir si l'on ne respecte pas

- Vous avez dû réécrire une méthode de la classe parente car celle-ci ne doit « surtout pas » être exécutée ou ne rien faire.
- Vous avez réécrit une méthode de votre parent et vous lancez une exception, alors que celle-ci n’était pas prévue dans la classe de base.
- Vous devez modifier une classe parente pour que votre nouvelle classe fonctionne. Par exemple, ajouter des arguments optionnels.


## Ségrégation des interfaces

Aucune ne devrait dépendre de méthodes qu'il n'utilise pas.

Le but est d'avoir des interfaces plus petites pour faciliter l'exploitation.


**Exemple : **

On a une application de gestion d’avion.

On veut faire une classe avion, qui implémenterait cette interface :

````
interface Vehicule {
    public function vole();
    public function avance();
    public function atterit();
}

class Avion implements Vehicule {
    public function vole() {
    }
    public function avance() {
    }
    public function atterit() {
    }
}
````

La classe Avion avion respecte bien notre interface, tout va bien.

Désormais on créé une class Voiture :

````
class Voiture implements Vehicule {
    public function vole() {
    }
    public function avance() {
    }
    public function atterit() {
    }
}
````

Le probleme est qu'une voiture ne vole pas et n'atterit pas => notre interface est trop générique, le client (=voiture) dépend de méthodes qu'il n'a pas besoin.

**Solution :** découper notre interface trop générique en plus petites.

````
interface VehiculeRoulant {
    public function avance();
}
interface VehiculeVolant {
    public function vole();
    public function atterit();
}
````

Notre avion respecte maintenant deux interfaces, il respecte le contrat des véhicules roulants **ET** volants.

````
class Avion implements VehiculeVolant, VehiculeRoulant {
    public function avance() {
    }
    public function vole() {
    }
    public function atterit() {
    }
}
````

et notre voiture respecte une seule interface :

````
class Voiture implements  VehiculeRoulant {
    public function avance() {
    }
 }
````

## Conclusion

#### Les avantages

- code plus simple
- code plus ré-utilisable
- code plus maintenable
- code moins couplé

#### Les dérives

- à trop vouloir découper on se retrouve avec une méthode / interface


## Ressources complémentaires

<iframe width="560" height="315" src="https://www.youtube.com/embed/bOSPXRG38PU" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

https://afup.org/talks/2379-les-interfaces-l-adn-de-votre-application
