Le **contexte** : Chaque phrase en Gherkin est associée à une méthode PHP grâce aux classes de contexte.
La phrase est située dans une annotation au-dessus de la fonction à appeler.
L’annotation peut contenir une expression rationnelle ou une phrase avec des placeholders commençant par deux points. 
Chaque parenthèse de capture de l’expression rationnelle ou placeholder sera un argument
de la méthode PHP appelée pour exécuter la phrase du test.

Exemple: 
```
    /**
     * Click on cookies if visible
     * Example: When I accept cookies by clicking on "Help Icon".
     *
     * @When /^(?:|I )accept cookies by clicking on "(?P<element>[\w\s]+)"$/
     *
     * @param $element
     *
     * @throws ElementNotVisible
     * @throws NoSuchElement
     */
    public function iAcceptCookiesByClickingOn($element): void
    {
        if ($this->hasElement($element) && $this->isElementVisible($element)) {
            $this->createElement($element)->click();
        }
    }
```
