## Propriétés et utilisation des objets

Tout dans JavaScript se comporte comme un objet, hormis deux exceptions que sont
[`null`](#core.undefined) et [`undefined`](#core.undefined).

    false.toString() // 'false'
    [1, 2, 3].toString(); // '1,2,3'

    function Foo(){}
    Foo.bar = 1;
    Foo.bar; // 1

Il existe une idée fausse comme quoi les nombres ne peuvent pas être utilisés en tant qu'objets.
Ceci parce qu'un défaut dans l'analyseur JavaScript interprète la notation pointée sur
un nombre commme un littéral à virgule flottante.


    2.toString(); // entraine une erreur de syntaxe

Il existe des moyens de contournement qui peuvent être utilisés afin que les nombres se comportent
également comme des objets.

    2..toString(); // le deuxième point est correctement interprété
    2 .toString(); // notez l'espace à gauche du point
    (2).toString(); // 2 est évalué en premier

### Objets en tant que structure de données

Les objets dans JavaScript peuvent aussi être utilisés comme une [*table de hachage*][1], principalement
comme une association clé-valeur.

En utilisant un objet littéral - la notation `{}` - il est possible de créer un objet brut.
Ce nouvel objet [hérite](#object.prototype) de `Object.prototype` et ne possède pas de
[propriété propre](#object.hasownproperty) définie.

    var foo = {}; // un objet vide

    // un objet avec une propriété nommée 'test' valuée à 12
    var bar = {test: 12};

### Accéder aux propriétés

Les propriétés d'un objet sont accessibles de deux manières, via la notation pointée ou la notation entre crochets.

    var foo = {name: 'chaton'}
    foo.name; // chaton
    foo['name']; // chaton

    var get = 'name';
    foo[get]; // chaton

    foo.1234; // Erreur de syntaxe
    foo['1234']; // ok

Les deux notations sont identiques à l'usage, avec la seule différence que
la notation entre crochets permet de définir dynamiquement des propriétés, ainsi que
l'utilisation de nom de propriété qui auraient entrainé des erreurs de syntaxe.

### Supprimer des propriétés

La seule manière de vraiment supprimer une propriété d'un objet est d'utiliser
l'opérateur `delete`. Assigner `undefined` ou `null` à la propriété ne supprime
que la *valeur* associée à la propriété, mais pas la *clef*.

    var obj = {
        bar: 1,
        foo: 2,
        baz: 3
    };
    obj.bar = undefined;
    obj.foo = null;
    delete obj.baz;

    for(var i in obj) {
        if (obj.hasOwnProperty(i)) {
            console.log(i, '' + obj[i]);
        }
    }

L'exemple ci-dessus affiche à la fois `bar undefined` et `foo null` - uniquement `baz`
a été supprimée et n'est donc pas affichée.

### Écriture des clefs

    var test = {
        'case': 'Je suis un mot clef, je doit être écrit comme une chaîne',
        delete: 'Je suis aussi un mot clef' // entraine une erreur de syntaxe
    };

Les propriétés d'objets peuvent être écrites à la fois avec des caractères bruts ou en tant que
chaîne de caractères. À cause d'une autre méconception dans l'analyseur de JavaScript, l'exemple
ci-dessus lèvera une `SyntaxError` avant ECMAScript 5.

Cette erreur vient du fait que *delete* est un *mot clef* du langage. Il doit donc être noté
sous forme de *chaîne de caractères* pour s'assurer qu'il sera correctement interprété par les anciens
moteurs JavaScript.

[1]: http://fr.wikipedia.org/wiki/Table_de_hachage

