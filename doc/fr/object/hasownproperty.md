## `hasOwnProperty`

Afin de vérifier si un objet possède une propriété définie sur *lui-même* et **pas**
autre part sur sa [chaîne de prototpes](#object.prototype), il est nécessaire
d'utiliser la méthode `hasOwnProperty` que chaque objet hérite de `Object.prototype`.

> **Note :** Ce n'est **pas** suffisant de vérifier si une propriété est `undefined`.
> La propriété peut très bien exister, mais avec une valeur `undefined`.

`hasOwnProperty` est la seule chose en JavaScript qui s'occupe des propriétés et
ne traverse **pas** la chaîne de prototypes.

    // Corruption de Object.prototype
    Object.prototype.bar = 1;
    var foo = {goo: undefined};

    foo.bar; // 1
    'bar' in foo; // true

    foo.hasOwnProperty('bar'); // false
    foo.hasOwnProperty('goo'); // true

Uniquement `hasOwnProperty` donnera le resultat correct et escompté. Ceci est essentiel
lorsqu'on itère sur les propriétés de n'importe quel objet. Il n'y a **pas** d'autre
moyen d'exclure les propriétés qui ne sont pas définies sur l'objet **lui-même** mais
quelque part d'autre sur sa chaîne de prototypes.

### `hasOwnProperty` en tant que propriété

JavaScript ne protège **pas** le nom de propriété `hasOwnProperty`. Ainsi, si il
s'avère qu'un objet doit possèder une propriété avec ce nom, il est nécessaire d'utiliser
un `hasOwnProperty` *extérieur* afin d'avoir des résultats corrects.

    var foo = {
        hasOwnProperty: function() {
            return false;
        },
        bar: 'terres inconnues'
    };

    foo.hasOwnProperty('bar'); // retourne toujours false

    // Utilise hasOwnProperty d'un autre objet
    // et appelé avec 'this' valué à foo
    ({}).hasOwnProperty.call(foo, 'bar'); // true

    // Il est aussi possible d'utiliser la propriété
    // hasOwnProperty de Object pour ce besoin
    Object.prototype.hasOwnProperty.call(obj, 'bar'); // true


### En conclusion

Lorsqu'on vérifie l'existence d'une propriété sur un objet, `hasOwnProperty` est
la **seule** méthode valable. Il est aussi recommendé d'utiliser `hasOwnProperty`
dans **chaque** [boucle `for in`](#object.forinloop) afin d'éviter les erreurs
provenant des [prototypes](#object.prototype) natifs étendus.

