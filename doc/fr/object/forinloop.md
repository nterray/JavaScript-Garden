## La boucle `for in`

Tout comme l'opérateur `in`, la boucle `for in` traverse la chaîne de prototypes
quand on itère sur les propriétés d'un objet.

> **Note :** La boucle `for in` n'itèrera **pas** sur les propriétés qui ont
> leur attribut `enumerable` valué à `false` ; par example, la propriété `length`
> d'un tableau.

    // Corruption de Object.prototype
    Object.prototype.bar = 1;

    var foo = {moo: 2};
    for(var i in foo) {
        console.log(i); // sort bar et moo
    }

Puisqu'il n'est pas possible de changer le comportement de la boucle `for in`, il
est nécessaire de filtrer les propriétés non désirées dans le corps de la boucle.
Ceci est fait en utilisant la méthode [`hasOwnProperty`](#object.hasownproperty) de
`Object.prototype`.

> **Note :** Puisque `for in` traverse toujours completement la chaîne de prototypes,
> elle deviendra plus lente avec chaque niveau d'héritage additionel d'un objet.

### Utilisation de `hasOwnProperty` pour filtrer

    // même boucle que dessus
    for(var i in foo) {
        if (foo.hasOwnProperty(i)) {
            console.log(i);
        }
    }

Ceci est la seule façon correcte de procéder. Avec l'usage de `hasOwnProperty`, elle
ne sortira que `moo`. Si `hasOwnProperty` est laissé de côté, le code peut être sujet
aux erreurs dans les cas où les objets natifs - par exemple `Object.prototype` - ont
été étendus.

Une bibliothèque largement répendue qui fait ça est [Prototype][1]. Lorsque cette
bibliothèque est incluse, les boucles `for in` qui n'utilisent pas `hasOwnProperty`
seront à coup sûr cassées.

### En conclusion

Il est recommandé de **toujours** utiliser `hasOwnProperty`. Aucune présupposition ne
devrait être faite à propos de l'environnement dans lequel le code sera execut, ou si
les prototypes natifs ont été étendus ou pas.

[1]: http://www.prototypejs.org/

