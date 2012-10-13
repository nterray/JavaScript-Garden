## `undefined` et `null`

JavaScript possède deux valeurs distinctes pour `rien`, la plus utile des deux
étant `undefined`.

### La valeur `undefined`

`undefined` est un type avec une seule valeur : `undefined`.

le langage défini également une variable globale qui a la valeur de `undefined` ;
cette variable est aussi dénommée `undefined`. Cependant, cette variable n'est **pas**
une constante ni un mot clef du langage. Ceci signifie que cette *valeur* peut
être facilement écrasée.

> **Note ES5 :** `undefined` dans ECMAScript 5 n'est **plus** *écrasable* en mode strict
> mais son nom peut être éclipsé par exemple par une fonction avec le nom `undefined`.

Quelques exemples lorsque la valeur `undefined` est retournée :

 - Accéder à la variable (non modifiée) globale `undefined`.
 - Accéder à une variable déclarée *mais par* encore initialisée.
 - Retour implicite de fonction à cause d'une instruction `return` manquante.
 - Les instructions `return` qui ne retournent rien explicitement.
 - La recherche de propriété non existante.
 - Les paramètres de fonction qui n'ont pas de valeur explicitement passée.
 - Tout ce qui a pu être valué à `undefined`.
 - Toute expression de la forme `void(expression)`.

### Gestion des changements à la valeur `undefined`

Puisque la variable globale `undefined` ne possède qu'une copie de la *valeur* de
`undefined`, lui assigner une nouvelle valeur ne change **pas** la valeur du
*type* `undefined`.

Cepandant, afin de pouvoir comparer quelque chose avec la valeur de `undefined`,
il est nécessaire tout d'abord de récupérer la valeur de `undefined`.

Pour protéger le code contre l'écrasement possible d'une variable `undefined`, une
technique courante est d'ajouter un paramètre supplémentaire à un 
[encapsuleur anonyme](#function.scopes) qui ne prendra pas d'argument.

    var undefined = 123;
    (function(something, foo, undefined) {
        // undefined dans la portée locale
        // référence la bonne valeur

    })('Bonjour le monde', 42);

Une autre façon d'obtenir le même effet consiste à faire une déclaration à 
l'intérieur de l'encapsuleur.

    var undefined = 123;
    (function(something, foo) {
        var undefined;
        ...

    })('Bonjour le monde', 42);

La seule différence ici est que cette version utilise 4 octets de plus dans le
cas où le code est minifié et qu'il n'y a pas d'autre instruction `var` à 
l'intérieur de l'encapsuleur.

### Utilisations de `null`

Alors que `undefined` dans le contexte du langage JavaScript est la plupart du
temps utilisé dans le sens d'un traditionel *null*, le vrai `null` (à la fois
litéral et type) est plus ou moins juste un autre type.

Il est utilisé dans quelques commandes internes JavaScript (comme déclarer la 
fin de la chaîne de prototype en effectuant `Foo.prototype = null`), mais dans
la plupart des situations il peut être remplacé par `undefined`.

