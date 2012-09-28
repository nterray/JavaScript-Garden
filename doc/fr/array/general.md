## Propriétés et itération de tableau

Bien que les tableaux en JavaScript soient des objets, il n'y a aucune bonne raison
d'utiliser les boucles [`for in`](#object.forinloop). En fait il y a moult raisons
**contre** l'usage de `for in` sur des tableaux.

> **Note :** Les tableaux JavaScript ne sont **pas** des *tableaux associatifs*. JavaScript
> a seulement des [objets](#object.general) pour associer des clefs à des valeurs. Et alors que
> les tableaux associatifs **préservent** l'ordre, les objets ne le font pas.

Puisque les boucles `for in` enumèrent toutes les propriétés qui sont sur la chaîne de
protopype et comme le seul moyen d'exclure ces propriétés est d'utiliser
[`hasOwnProperty`](#object.hasownproperty), c'est facilement **20 fois** plus lent qu'une
boucle `for` normale.

### Itération

Afin d'obtenir les meilleures performances lors du parcours de tableaux, il est préférable
d'utiliser la boucle `for` classique.

    var liste = [1, 2, 3, 4, 5, ...... 100000000];
    for(var i = 0, l = liste.length; i < l; i++) {
        console.log(liste[i]);
    }

Il y a une autre astuce, qui est la mise en cache de la taille du tableau via `l = list.length`.

Bien que la propriété `length` est définie sur le tableau lui-même, il y a quand même
un surcoût à effectuer une recherche à chaque itération de la boucle. Et même si des
navigateurs modernes **peuvent** appliquer des optimisations dans ce cas, il n'y a aucun
moyen de savoir si le code va tourner ou pas sur l'un de ces navigateurs.

En fait, la boucle peut être presque **deux fois plus lente** si on oublie de mettre
en cache la taille du tableau.

### La propriété `length`

Alors que le *getter* de la propriété `length` retourne simplement le nombre d'éléments
contenus dans le tableau, le *setter* peut être utilisé pour **tronquer** le tableau.

    var foo = [1, 2, 3, 4, 5, 6];
    foo.length = 3;
    foo; // [1, 2, 3]

    foo.length = 6;
    foo.push(4);
    foo; // [1, 2, 3, undefined, undefined, undefined, 4]

Assigner une plus ptite longueur tronque le tableau. L'augmenter crée un tableau fragmenté.

### En conclusion

Pour de meilleures performances, il est recommandé de toujours utiliser la simple boucle `for`
et de mettre en cache la propriété `length`. L'usage de `for in` sur un tableau est un
signe de code mal écrit sujet aux bugs et aux mauvaises performances.

