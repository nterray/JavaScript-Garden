## Comment `this` fonctionne

JavaScript possède un concept différent de ce à quoi `this` fait référence par rapport à 
ce que font les autres langages de programmation. Dans le langage, il y a exactement **cinq** façons 
différentes de lier la valeur de `this`.

### La portée gloable

    this;

Quand on utilise `this` dans la portée globale, il référence simplement l'objet *global*.


### En appelant une fonction

    foo();

Ici, `this` fera égalemet référence à l'objet *global*.

> **ES5 Note :** En mode strict, il n'y a **plus du tout** d'objet global.
> `this` possède dans ce cas la valeur `undefined`.

### En appelant une méthode

    test.foo(); 

Dans cet exemple, `this` référence `test`.

### En appelant un constructeur

    new foo(); 

Un appel de fonction qui est précédé par le mot clef `new` agit en tant que 
[constructeur](#function.constructors). A l'intérieur de la fonction, `this` 
référence un `Object` *nouvellement créé*.

### Définition explicite de `this`

    function foo(a, b, c) {}
                          
    var bar = {};
    foo.apply(bar, [1, 2, 3]); // le tableau va s'étendre en 
    foo.call(bar, 1, 2, 3); // a = 1, b = 2, c = 3

Lorsqu'on utilise les méthodes `call` ou `apply` de `Function.prototype`, la valeur
de `this` à l'intérieur de la fonction appelée sera **explicitement valuée** au premier 
argument de l'appel de fonction correspondant.

Ainsi, dans l'exemple ci-dessus le *cas méthode* ne s'applique **pas**, et `this` à 
l'intérieur de `foo` référencera `bar`.

> **Note :** `this` **ne peut pas** être utilisé pour référencer l'objet dans un objet littéral.
> Donc `var obj = {moi: this}` ne résultera **pas** en `moi` référençant  `obj`, puisque
> `this` n'est lié qu'au travers l'un des cinq cas listés.

### Pièges communs

Alors que la plupart de ces cas sont pertinents, le premier est à considérer 
comme une autre méconception du langage car il n'a **jamais** d'usage pratique.

    Foo.methode = function() {
        function test() {
            // this est lié à l'objet global
        }
        test();
    }

L'erreur classique est de croire que `this` à l'intérieur de `test` référence `Foo` alors 
que dans les faits, *pas du tout*.

Afin de pouvoir accéder à `Foo` depuis `test`, il est nécessaire de créer une variable locale
à l'intérieur de `methode` qui référence `Foo`.

    Foo.methode = function() {
        var that = this;
        function test() {
            // Ici, utiliser that au lieu de this
        }
        test();
    }

`that` est juste un nom de variable normale, mais il est communément utilisé pour référencer
un `this` englobant. Combiné aux [fermetures](#function.closures), il peut être aussi utilisé 
pour passer la valeur de `this`.

### Affectation de méthodes

Une autre chose qui ne marche **pas** dans JavaScript est l'aliasing de fonction, qui consiste à 
**affecter** une méthode à une variable.

    var test = unObjet.testMethode;
    test();

À cause du premier cas, `test` agit maintenant comme un appel de fonction brute. Ainsi à l'intérieur, 
`this` ne référence plus `unObjet`.

Alors que la résolution à la volée de `this` peut sembler être une mauvaise idée aux premiers abords,
en fait c'est ce qui fait fonctionner [l'héritage par prototype](#object.prototype). 

    function Foo() {}
    Foo.prototype.methode = function() {};

    function Bar() {}
    Bar.prototype = Foo.prototype;

    new Bar().methode();

Lorsque `methode` est appelée sur une instance de `Bar`, `this` fait exactement référence à cet objet.

