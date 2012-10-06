## L'opérateur `instanceof`

L'opérateur `instanceof` compare les constructeurs de deux opérandes. Ce n'est 
utile que lorsque l'on compare des objets personnalisés. Utilisé sur des
types internes, c'est presque aussi inutile que [l'opérateur typeof](#types.typeof).

### Comparaison d'objets personnalisés

    function Foo() {}
    function Bar() {}
    Bar.prototype = new Foo();

    new Bar() instanceof Bar; // true
    new Bar() instanceof Foo; // true

    // Ceci attribue seulement l'objet-fonction Foo
    // à Bar.prototype, mais pas à une instance 
    // existante de Foo
    Bar.prototype = Foo;
    new Bar() instanceof Foo; // false

### Utiliser `instanceof` avec les types natifs

    new String('foo') instanceof String; // true
    new String('foo') instanceof Object; // true

    'foo' instanceof String; // false
    'foo' instanceof Object; // false

Une chose importante à retenir ici est que `instanceof` ne fonctionne pas sur
des objets qui proviennent de différents contextes (ex. différents documents
dans un navigateur), puisque leurs constructeurs ne seront pas exactement les
mêmes objets.

### En conclusion

L'opérateur `instanceof` devrait être utilisé **seulement** pour traiter des
objets personnalisés qui proviennent du même contexte JavaScript. Tout comme
l'opérateur [`typeof`](#types.typeof), tout autre utilisation devrait être 
**proscrite**.

