## Expressions et déclarations des fonctions

Les fonctions dans JavaScript sont des objets à part entière. Ce qui veut dire
qu'elle peuvent être utilisées comme n'importe quelle autre valeur. L'utilisation
la plus fréquente de cette fonctionnalité est de passer une *fonction anonyme* comme
 fonction de rappel à une autre, souvent une fonction asynchrone.

### La déclaration `function`

    function foo() {}

La fonction ci-dessus est [hissée](#function.scopes) avant que l'execution du programme
ne démarre. Ainsi, elle est disponible *partout* dans la portée dans laquelle elle a
été définie, même si elle est appelée avant sa définition dans le code source.

    foo(); // Fonctionne, foo est créée avant l'execution de ce code
    function foo() {}

### L'expression `function`

    var foo = function() {};

Cet exemple affecte la fonction innomée et *anonyme* à la variable foo.

    foo; // 'undefined'
    foo(); // lève une TypeError
    var foo = function() {};

Dû au fait que `var` est une déclaration qui hisse le nom de la variable `foo`
avant l'execution effective du code ne démarre, `foo` est déjà définie quand
le script est executé.

Mais comme les affectations ne faites que pendant l'execution, la valeur de `foo`
est par défaut [undefined](#core.undefined) avant que le code correspondant ne soit executé.

### Expression de fonctions nommées

Un autre cas spécial est l'affectation de fonctions nommées.

    var foo = function bar() {
        bar(); // Fonctionne
    }
    bar(); // ReferenceError

Ici, `bar` n'est pas disponible dans la portée extérieure, puisque la fonction est affectée à `foo`.
Cependant, à l'intérieur de `bar`, elle est disponible. Ceci est dû à la manière de fonctionner
de la [résolution de nom](#function.scopes) en JavaScript, le nom de la fonction étant *toujours*
disponible à l'intérieur de la portée locale de la fonction elle-même.

