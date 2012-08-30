## Le prototype

JavaScript ne propose pas un héritage classique, il se base plutôt sur la programmation
orienté prototype.

Alors que c'est souvent considéré comme une des faiblesses de JavaScript, le modèle d'héritage
par prototype est en fait bien plus puissant que le modèle classique. Par exemple il est très
facile de construire un modèle classique basé sur un modèle prototype alors que l'inverse
s'avère être une tâche plus ardue.

Du fait que JavaScript se trouve être le seul langage largement utilisé basé sur l'héritage
par prototype, du temps est nécessaire pour s'adapter aux différences entre les deux
modèles.

La première différence majeure réside dans le fait que l'héritage dans JavaScript est
effectué en utilisant les fameuses *chaînes de prototypes*.

> **Note :** Utiliser simplement `Bar.prototype = Foo.prototype` aura pour résultat
> d'avoir deux objets partageant le **même** prototype. Ainsi, les changements sur le
> prototype d'un objet affectera aussi le prototype de l'autre objet, ce qui
> n'est en général pas l'effet escompté.

    function Foo() {
        this.valeur = 42;
    }
    Foo.prototype = {
        methode: function() {}
    };

    function Bar() {}

    // Affecte une instance de Foo au prototype de Bar
    Bar.prototype = new Foo();
    Bar.prototype.foo = 'Bonjour le monde !';

    // Veille à ce que le constructeur soit Bar
    Bar.prototype.constructor = Bar;

    var test = new Bar() // créer une instance de Bar

    // La chaîne de prototypes résultante
    test [instance de Bar]
        Bar.prototype [instance de Foo]
            { foo: 'Bonjour le monde!' }
            Foo.prototype
                { method: ... }
                Object.prototype
                    { toString: ... /* etc. */ }

Ci-dessus, l'objet `test` héritera à la fois de `Bar.prototype` et de `Foo.prototype` ;
il aura donc accès à la fonction `methode` qui a été définié sur Foo. Il aura aussi accès
à la propriété `valeur` de l'unique instance de `Foo` qui est son prototype. Il est important de
bien noter que `new Bar()` ne crée **pas** une nouvelle instance de `Foo`, mais réutilise
celle assigné à son prototype ; ainsi, toutes les instances de `Bar` partage la **même**
propriété `valeur`.

> **Note :** N'utilisez **pas** `Bar.prototype = Foo`, puisqu'il ne pointera pas sur
> le prototype de `Foo` mais plutôt sur l'objet fonction `Foo`. Donc la chaîne de prototypes
> ira sur `Function.prototype` au lieu de `Foo.prototype` ; `methode` ne serait donc pas non
> plus sur la chaîne de prototypes.

### Recherche de propriété

Quand on accède au propriété d'un objet, JavaScript va traverser la chaîne de prototype **en amont**
jusqu'à ce qu'il trouve une propriété avec le même nom.

Quand il atteint le sommet de la chaîne - nommé `Object.prototype` - et qu'il n'a toujours pas trouvé
la propriété requise, il retourne la valeur [undefined](#core.undefined) à la place.

### La propriété prototype

Bien que la propriété prototype est utilisé par le langage pour construire la chaîne
de prototypes, il est tout de même possible de lui assigner **n'importe quelle** valeur.
Toutefois, les types primitifs sont ignorés lorsqu'ils sont assignés à un prototype.

    function Foo() {}
    Foo.prototype = 1; // pas d'effet

Assigner des objets, de la même façon que dans l'exemple ci-dessus, fonctionne et permet la création
dynamique de chaînes de prototypes.

### Performances

Le temps de recherche des propriétés qui sont situées loin en amont de la chaîne de prototypes
peuvent avoir un impact négatif sur les performances de sections critiques de code. De plus,
essayer d'accéder à des propriétés inexistantes fera traverser toute la chaîne de prototypes.

De la même manière, quand on [itère](#object.forinloop) sur les propriétés d'un objet, **chacune**
des propriétés qui sont sur la chaîne de prototypes sera enumérée.


### Extension des prototypes natifs

Une mauvaise pratique courante est d'étendre `Object.prototype` ou un autre des prototypes natifs.

Cette technique est appelée [modification-singe][1] et casse *l'encapsulation*. Bien qu'utilisée
par des bibliothèque largement répandue comme [Prototype][2], il n'y a vraiment aucune raison
d'encombrer les types natifs avec l'ajout d'une fonctionnalité non standard.

La **seule** bonne raison d'étendre un prototype natif est pour la compatibilité
descendante des fonctionnalité des nouveaux moteur JavaScript ; par exemple [`Array.forEach`][3].

### En conclusion

Il est **obligatoire** de bien comprendre l'héritage par prototype avant
de commencer à écrire du code complexe qui se baserait dessus. Également, il faut surveiller la longueur
des chaînes de prototypes et les casser si nécessaire afin de se prémunir de problèmes de performance.
Enfin, les prototypes natifs ne devraient **jamais** être étendus à moins que ce ne soit pour des raisons
de compatibilité descendante avec les nouvelles fonctionnalités de JavaScript.

[1]: http://fr.wikipedia.org/wiki/Monkey-Patch
[2]: http://prototypejs.org/
[3]: https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Array/forEach

