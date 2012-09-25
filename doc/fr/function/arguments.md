## L'objet `arguments`

Chaque portée de fonctions JavaScript peut accéder à une variable spéciale `arguments`.
Cette variable contient une liste de tous les arguments qui ont été passés à la fonction.

> **Note :** Si `arguments` a déjà été définie dans la portée de la fonction
> soit par une instruction `var` ou en étant le nom d'un paramètre, alors l'objet
> `arguments` ne sera pas créé.

L'objet `arguments` n'est **pas** un `Array`. Bien qu'il partage quelque sémantique
d'un tablea - comme la propriété `length` - il n'hérite pas de `Array.prototype`
et est en fait un `Object`.

À cause de cela, il n'est **pas** possible d'utiliser les méthodes standard comme `push`,
`pop` ou `slice` sur l'objet `arguments`. Bien que l'itération avec une boucle basique
`for` marche très bien, il est nécessaire de le convertir en vrai `Array` afin d'utiliser
les méthodes standard de `Array` sur lui.

### Convertir en tableau

Le code ci-dessous retournera un nouvel `Array` contenant tous les éléments de l'objet
`argument`/

    Array.prototype.slice.call(arguments);

Puisque cette conversion est **lente**, il n'est **pas recommendé** de l'utiliser dans des
sections critiques de code.

### Passer des arguments

Ci-dessous est la façon recommendée de passer des arguments d'une fonction à une autre.

    function foo() {
        bar.apply(null, arguments);
    }
    function bar(a, b, c) {
        // faire des choses ici
    }

Une autre astuce est d'utiliser à la fois `call` et `apply` pour créer des encapsulateurs rapides et non liées.

    function Foo() {}

    Foo.prototype.methode = function(a, b, c) {
        console.log(this, a, b, c);
    };

    // Créer une version non liée de "methode"
    // Elle prend comme paramètre : this, arg1, arg2...argN
    Foo.methode = function() {

        // Resultat: Foo.prototype.methode.call(this, arg1, arg2... argN)
        Function.call.apply(Foo.prototype.methode, arguments);
    };


### Paramèters formels et index d'arguments

L'objet `arguments` crée des **accesseurs** pour chacune de ses propriétés,
ainsi que pour les paramètres formels de la fonction.

Ainsi, changer la valeur d'un paramètre formel changera la valeur de la propriété correspondante
de l'objet `arguments`, et inversement.

    function foo(a, b, c) {
        arguments[0] = 2;
        a; // 2

        b = 4;
        arguments[1]; // 4

        var d = c;
        d = 9;
        c; // 3
    }
    foo(1, 2, 3);

### Performances : mythes et réalitésPerformance Myths and Truths

L'objet `arguments` est toujours créé sauf dans dans les cas où il a été déclaré comme nom
de variable dans la fonction ou en tant qu'un des paramètres formels. Ceci qu'il soit utilisé
ou pas.

Les *accesseurs* sont **tpujours** créés, ainsi les utiliser n'a aucun impact sur les
performances, surtout pas dans un code réel où il y a plus qu'un simple accès aux propriétés
de l'objet `arguments`.

> **ES5 Note :** Ces *accesseurs** ne sont pas créés e mode strict.

Cependant, il y a un cas cas qui réduit de manière significative dans les moteurs JavaScript
modernes. Ce cas est l'utilisation de `arguments.callee`.

    function foo() {
        arguments.callee; // faire quelque chose avec l'objet de cette fonction
        arguments.callee.caller; // et l'objet appelant la fonction
    }

    function bigLoop() {
        for(var i = 0; i < 100000; i++) {
            foo(); // Devrait normalement être inliné...
        }
    }

Dans le code ci-dessus, `foo` ne peut plus être sujet à l'[inlining][1] puisque elle à besoin de connaitre
elle-même et son appelant. Ceci n'empêche pas seulement les gains qui pourraient venir de l'inlining,
mais casse aussi l'encapsulation puisque la fonction peut être dépendant du contaxte d'appel.

Il est **fortement recommendé** de ne **jamais** utiliser `arguments.callee` où l'une de ses propriétés.

> **ES5 Note :** En mode strict, `arguments.callee` lancera une `TypeError` puisque
> son usage a été déprédié.

[1]: http://en.wikipedia.org/wiki/Inlining


