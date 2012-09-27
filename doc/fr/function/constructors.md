## Constructeurs

Les constructeurs en JavaScript sont également différents de bien d'autres langages.
Tout appel de fonction qui est précédé du mot clef `new` se comporte comme un constructeur.

À l'intérieur du constructeur - la fonction appelée - la valeur de `this` référence un objet
nouvellement créé. Ce **nouvel** objet aura pour [prototype](#object.prototype) celui de
l'objet-fonction qui a été invoqué en tant que constructeur.

Si la fonction qui a été appelée n'a pas d'instruction `return` explicite, alors elle
retournera implicitement la valeur de `this` - le nouvel objet.


    function Foo() {
        this.bla = 1;
    }

    Foo.prototype.test = function() {
        console.log(this.bla);
    };

    var test = new Foo();

Le code ci-dessous appelle `Foo` comme constructeur et value le `protoype` de
l'objet nouvellement créé à `Foo.prototype`.

Dans le cas d'une instruction `return` explicite, la fonction retourne la valeur
spécifiée par l'instruction, **mais seulement** si la valeur retournée est un `Objet`.

    function Bar() {
        return 2;
    }
    new Bar(); // un nouvel objet

    function Test() {
        this.value = 2;

        return {
            foo: 1
        };
    }
    new Test(); // l'objet retourné

Quand le mot clef `new` est omis, la fonction ne retournera pas un nouvel objet.

    function Foo() {
        this.bla = 1; // sera attribué à l'objet global
    }
    Foo(); // undefined

Bien que l'exemple ci-dessus puisse fonctionner dans certains cas, dû au fonctionnement
de [`this`](#function.this) en JavaScript, il utilisera *l'objet global* pour la valeur de
`this`.

### Fabriques

Afin de permettre l'omission du mot clef `new`, la fonction-constructeur doit retourner
explicitement une valeur.

    function Bar() {
        var valeur = 1;
        return {
            method: function() {
                return valeur;
            }
        }
    }
    Bar.prototype = {
        foo: function() {}
    };

    new Bar();
    Bar();

Les deux appels à `Bar` retourne la même chose, un objet nouvellement créé
avec une propriété dénommée `method`, qui est une [fermeture](#function.closures).

Il faut noter que l'appel `new Bar()` n'affecte **pas** le prototype
de l'objet retourné. Alors que le protoype sera mis sur le nouvel objet, `Bar` ne retourne
jamais ce nouvel objet.

Dans l'exemple ci-dessous, il n'y a aucune différence fonctionnelle entre utiliser ou non
le mot clef `new`.


### Créer des objets avec des fabriques

Une recommendation courante est de ne **pas** utiliser `new` car oublier de l'utiliser
peut conduire à des bugs.

Pour créé un objet, il faudrait plutôt utiliser une fabrique et construire l'objet à l'intérieur
de cette fabrique.

    function Foo() {
        var obj = {};
        obj.valeur = 'blub';

        var privee = 2;
        obj.uneMethode = function(valeur) {
            this.valeur = valeur;
        }

        obj.getPrivee = function() {
            return privee;
        }
        return obj;
    }

Alors que le code ci-dessus est robuste contre l'oubli du mot clef `new` et facilite
l'usage des [variables privées](#function.closures), il ne vient pas sans inconvénient.

 1. Il utilise plus de mémoire car l'objet créé ne partage **pas** les méthodes sur un
    prototype.
 2. Pour l'héritage la fabrique doit copier toutes les méthodes d'un autre objet ou poser
    cet objet sur le prototype du nouvel objet.
 3. S'asseoir sur la chaîne de prototype juste pour rendre optionnel le mot clef `new` va
    à l'encontre de l'esprit du langage.

### En conclusion

Bien que l'omission du mot clef `new` peut conduire à des bugs, ce n'est certainement pas
une raison pour jeter l'usage des prototypes aux oubliettes. En fin de compte c'est la solution
la plus adaptée au besoins de l'application qui prime, il est primordial de choisir un seul
style de création d'objet et de **s'y maintenir**.

