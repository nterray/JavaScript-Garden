## L'opérateur `typeof`

L'opérateur `typeof` (avec [`instanceof`](#types.instanceof)) est probablement
le plus grand défaut de conception de JavaScript, puisque il est proche de 
l'état «**completement cassé**».

Bien que `instanceof` puisse avoir quelques usages limités, `typeof` n'a seulement
qu'un cas pratique, qui s'avère ne **pas** concerner la vérification du type
d'un objet.

> **Note :** Bien que `typeof` puisse être appelé comme une fonction
> i.e. `typeof(obj)`, ce n'est pas un appel de fonction. Les deux parenthèses se
> comporteront normalement et le résultat de l'expression sera utilisé comme 
> opérande pour l'opérateur `typeof`. Il n'y a **pas** de fonction `typeof`.

### La table des types JavaScript

    Valeur              Classe     Type
    -------------------------------------
    "foo"               String     string
    new String("foo")   String     object
    1.2                 Number     number
    new Number(1.2)     Number     object
    true                Boolean    boolean
    new Boolean(true)   Boolean    object
    new Date()          Date       object
    new Error()         Error      object
    [1,2,3]             Array      object
    new Array(1, 2, 3)  Array      object
    new Function("")    Function   function
    /abc/g              RegExp     object (fonction dans Nitro/V8)
    new RegExp("miaou") RegExp     object (fonction dans Nitro/V8)
    {}                  Object     object
    new Object()        Object     object

Dans la table précédente, *Type* référence la valeur que l'opérateur `typeof`
retourne. Comme on peut clairement le voir, les valeurs sont tout sauf
cohérentes.

*Classe* référence la valeur de la propriété interne `[[Class]]` d'un 
objet.

> **Selon la spécification :** La valeur de `[[Class]]` peut être l'une des
> chaînes de caractères suivantes. `Arguments`, `Array`, `Boolean`, `Date`, `Error`, 
> `Function`, `JSON`, `Math`, `Number`, `Object`, `RegExp`, `String`.

Afin de pouvoir obtenir la valeur de `[[Class]]`, on doit utiliser la méthode
`toString` sur `Object.prototype`.

### La classe d'un objet

La spécification donne une seule manière d'accéder à la valeur de `[[Class]]`,
en utilisant `Object.prototype.toString`.

    function is(type, obj) {
        var clas = Object.prototype.toString.call(obj).slice(8, -1);
        return obj !== undefined && obj !== null && clas === type;
    }
    
    is('String', 'test'); // true
    is('String', new String('test')); // true

Dans l'exemple précédent, `Object.prototype.toString` est appelée avec la valeur de
[this](#function.this), objet dont la valeur de `[[Class]]` doit être obtenue.

> **Note ES5 :** pour plus de commodité, la valeur de `Object.prototype.toString` 
> retournée pour `null` et `undefined` a **changé** de `Object` à `Null` et `Undefined`
> en ECMAScript 5.

### Tester les variables undéfinies

    typeof foo !== 'undefined'

Le code ci-dessus vérifiera si `foo` est déclarée ou pas. La référencer résulerait
en une `ReferenceError`. C'est la seule chose pour laquelle `typeof` peut
être utile.

### En conclusion

Pour vérifier le type d'un objet, il est fortement recommandé d'utiliser
`Object.prototype.toString` parce que c'est la seule manière fiable de 
procéder. Comme montré dans la table de types ci-dessus, certaines valeurs
retournées par `typeof` ne sont pas définies dans la spécification ; elles 
peuvent donc différer en fonction des différentes implémentations.

Sauf pour vérifier si une variable est définie, `typeof` doit être évité **à tout
prix**.
