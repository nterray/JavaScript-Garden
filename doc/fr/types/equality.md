## Égalité et comparaisons

JavaScript offre différentes façons d'évaluer l'égalité des objets.

### L'opérateur d'égalité

L'opérateur d'égalité consiste en deux signes égal: `==`

JavaScript repose sur le *typage faible*. Ceci signifie que l'opérateur d'égalité
**convertit** les types afin de pouvoir les comparer.
    
    ""           ==   "0"           // false
    0            ==   ""            // true
    0            ==   "0"           // true
    false        ==   "false"       // false
    false        ==   "0"           // true
    false        ==   undefined     // false
    false        ==   null          // false
    null         ==   undefined     // true
    " \t\r\n"    ==   0             // true

La table ci-dessus montre les résultats de la convertion de type, et c'est la
principale raison pourquoi l'usage de `==` est généralement considéré comme une
mauvaise pratique. Il introduit des bugs difficiles à tracer dû à la complexité des
règles de conversion.

En plus de cela, il y a des problèmes de performance lorsque la conversion entre
en jeu. Par exemple, une chaîne de caractères doit être convertie en nombre avant qu'elle puisse
être comparée à un autre nombre.

### L'opérateur d'égalité stricte

L'opérateur d'égalité stricte consiste en **trois** signes égal: `===`.

Il fonctionne de la même manière que l'opérateur d'égalité classique, hormis le fait
qu'il n'effectue **pas** de conversion de types entre ses opérandes.

    ""           ===   "0"           // false
    0            ===   ""            // false
    0            ===   "0"           // false
    false        ===   "false"       // false
    false        ===   "0"           // false
    false        ===   undefined     // false
    false        ===   null          // false
    null         ===   undefined     // false
    " \t\r\n"    ===   0             // false

Les résultats ci-dessus sont bien plus clairs est permettent une rupture du code 
anticipée. Ceci renforce le code à un certain degré et apporte des gains de performance
dans les cas où les opérandes sont de différents types.

### Comparer des objets

Alors que les deux `==` et `===` sont statués comme des opérateurs **d'égalité**, ils
ont un comportement différents lorsqu'au moins un de leurs opérandes se trouve être
un `Object`.

    {} === {};                   // false
    new String('foo') === 'foo'; // false
    new Number(10) === 10;       // false
    var foo = {};
    foo === foo;                 // true

Ici, les deux opérateurs comparent **l'identité** et **non pas** l'égalité. Ils compareront
pour la même **instance** de l'objet, tout comme `is` en Python et la comparaison
de pointeurs en C.

### En conclusion

Il est hautement recommendé d'utiliser seulement l'opérateur **d'égalité stricte**.
Dans le cas où les types doivent être convertis, ça devrait être fait 
[explicitement](#types.casting) et ne pas être laissé à la discrétion des règles de
conversion compliquées du langage.

