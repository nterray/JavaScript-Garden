## Portées et espaces de nommage

Bien que JavaScripe gère bien la syntaxe d'une paire d'accolades pour les blocs,
il ne le gère **pas** les portées de blocs ; par conséquent, tout ce qui est dans le
langage est la *portée de fonction*.

    function test() { // une portée
        for(var i = 0; i < 10; i++) { // ne compte pas
            // pour une portée
        }
        console.log(i); // 10
    }

> **Note :** Lorsqu'il n'est pas utilisé dans une affectation, une instruction return
> ou en tant qu'argument de fonction, la notation `{...}` sera interpretée en tant
> qu'instruction de bloc et non **pas** comme un objet littéral. Ceci, accompagné de
> [l'insertion automatique des points-virgules](#core.semicolon), peut être la cause
> d'erreurs subtiles.

Il n'y a également aucum espace de nommage en JavaScript, ce qui signifie que tout est
défini dans un espace *partagé commun*.

Chaque fois qu'une variable est référencée, JavaScript va remonter à traversir la totalité
des portées jusqu'à ce qu'il la trouve. Si jamais il atteind la portée globale et qu'il
n'a toujours pas trouvé le nom requis, il lèvera une `ReferenceError`.

### Le fléau des variables globales

    // script A
    foo = '42';

    // script B
    var foo = '42'

Les deux scripts n'ont **pas** le même comportement. Le script A défini une variable
appelée `foo` dans la portée *globale*, et le script B defini un `foo` dans la portée
*courante*.

Encore une fois, ça n'a **pas** du tout le *même effet* : ne pas utiliser `var` peut avoir
d'importantes implications.

    // portée globale
    var foo = 42;
    function test() {
        // portée locale
        foo = 21;
    }
    test();
    foo; // 21

Oublier l'instruction à l'interieur de la fonction `test` écrasera la valeur de `foo`.
Alors que ça ne semble pas poser de problème au premier abord, avoir des milliers de
lignes de JavaScript sans utiliser `var` introduira d'horribles bugs.

    // portée globale
    var items = [/* une liste */];
    for(var i = 0; i < 10; i++) {
        subLoop();
    }

    function subLoop() {
        // portée de subLoop
        for(i = 0; i < 10; i++) { // instruction var manquante
            // faisons des choses extraordinaire
        }
    }

La boucle exterieure terminera après le premier appel de `subLoop`, puisque cette
dernière écrase la valeur globale de `i`. Utiliser `var` pour la seconde boucle `for`
aurait évité facilement cette erreur. L'instruction `var` ne devrait **jamais** être
oubliée à moins que modifier la portée extérieure soit *l'effet désiré*.

### Variables locales

Les seules variables locales en JavaScript sont les paramètres de [fonction](#function.general)
et les variables déclarées via l'intruction `var`.

    // portée globale
    var foo = 1;
    var bar = 2;
    var i = 2;

    function test(i) {
        // portée locale de la fonction test
        i = 5;

        var foo = 3;
        bar = 4;
    }
    test(10);

Alors que `foo` et `i` sont des variables à l'intérieur de la portée de la fonction
`test`, l'affectation de `bar` écrasera la variable globale qui porte le même nom.

### Hissage

JavaScript **hisse** les déclarations. Cela signifie que l'instruction `var` et la
déclaration `function` seront déplacés au dessus de leur portée englobante.

    bar();
    var bar = function() {};
    var uneValeur = 42;

    test();
    function test(data) {
        if (false) {
            goo = 1;

        } else {
            var goo = 2;
        }
        for(var i = 0; i < 100; i++) {
            var e = data[i];
        }
    }

Le code ci-dessus sera transformer avant qu'une execution ne démarre. JavaScript déplace
les instructions `var` ainsi que les déclarations de `function` au dessus de la portée englobante
la plus proche.

    // instructions var déplacées ici
    var bar, someValue; // par defaut 'undefined'

    // les déclarations de fonction sont aussi remontées
    function test(data) {
        var goo, i, e; // l'absence de portée de bloc les placent ici
        if (false) {
            goo = 1;

        } else {
            goo = 2;
        }
        for(i = 0; i < 100; i++) {
            e = data[i];
        }
    }

    bar(); // lève une TypeError puique bar est encore 'undefined'
    someValue = 42; // les affectations ne sont pas impactées par le hissage
    bar = function() {};

    test();

L'absence de portée de bloc ne déplace pas seulement les instructions hors des
boucles, elle entraîne des constructions de certain `if` contre-intuitives.

Dans le code original, bien que l'instruction `if` semble modifier la *variable
globale* `goo`, elle modifie en fait la *variable locale* - après que le hissage
soit intervenu.

Sans la connaissance sur le *hissage*, le code ci-dessous semble lever une
`ReferenceError`.

    // vérifie la bonne initialisation de SomeImportantThing
    if (!SomeImportantThing) {
        var SomeImportantThing = {};
    }

Bien entendu, le code fonctionnera car l'instruction `var` a été déplacée au sommet
de la *portée globale*.

    var SomeImportantThing;

    // du code peut initialiser SomeImportantThing ici, ou pas

    // on s'assure qu'il l'est ici
    if (!SomeImportantThing) {
        SomeImportantThing = {};
    }

### Ordre de résolution des noms

Toutes les portées en JavaScript, y compris la *portée globale*, possèdent le variable
spéciale [`this`](#function.this), definie en elles, qui référence *l'objet courrant*.

Les portées de fonction possèdent également la variable [`arguments`](#function.arguments),
définie en elle, qui contient les arguments qui sont passé à la fonction.

Par exemple, quand JavaScript essaie d'accéder à la variable nommée `foo` à l'intérieur de
la portée d'une fonction, il recherchera selon l'ordre suivant :

 1. Dans le cas où il y a une instruction `var foo` dans la portée courante, utilise la.
 2. Si l'un des paramètres est nommé `foo`, utilise le.
 3. Si la fonction elle-même est nommée `foo`, utilise la.
 4. Va sur la portée immédatement extérieure, et recommence à **#1**.

> **Note :** Avoir un paramètre dénommé `arguments` **empechera** la création par défaut de
> l'objet `arguments`.

### Espaces de nommage

Un problème fréquent d'avoir un seul espace de nommage global est la probabilité de rencontrer
des problèmes de collision de noms. Avec JavaScript, ce risque peut être évité facilement avec
l'aide *d'enveloppe anonymes*.

    (function() {
        // un espace de nommage autonome

        window.foo = function() {
            // une fermeture exposée
        };

    })(); // execute la fonction immédiatement


Les fonctions anonymes sont considérées comme des [expressions](#function.general) ; aussi afin
d'être évaluée, elles doivent être d'abord évaluées.

    ( // évalue la fonction à l'intérieur des parenthèses
    function() {}
    ) // et retourne l'objet-fonction
    () // appelle le résultat de l'évaluation

Il y a d'autre façons d'évaluer et d'appeller directement l'expression-fonction. Bien que
différentes dans la syntaxe, le résultat sera le même.

    // D'autres styles d'invocation directe
    !function(){}()
    +function(){}()
    (function(){}());
    // etc.

### En conclusion

Il est recommandé de toujours utiliser un *encapsuleur anonyme* pour envolopper le code
dans son propro namespace. Ceci ne protège pas seulement le code d'une éventuelle collision
de noms, mais ça permet une meilleure modularité des programmes.

De plus, l'usage de variables globales ist considéré comme une **mauvaise pratique**. **Tout**
usage de celles-ci est un indicateur d'un code mal écrit sujet aux erreurs et difficile
à maintenir.
