## Fermetures et références

Une des fonctionalités les plus puissantes de JavaScript est la disponibilité
des *fermetures*. Avec les fermetures, les portées ont **toujours** accès à la
portée englobante dans laquelle elles sont définies. Puisque les seules portées
disponibles dans JavaScript sont les [portées de fonction](#function.scopes),
toutes les fonctions, par défaut, agissent en tant que fermetures.

### Émulation de variables privées

    function Compteur(start) {
        var compte = start;
        return {
            increment: function() {
                compte++;
            },

            get: function() {
                return compte;
            }
        }
    }

    var foo = Compteur(4);
    foo.increment();
    foo.get(); // 5

Ici, `Compteur` retourne **deux** fermetures : les fonctions `increment` et
`get`. Chacune de ces fonctions garde une **référence** à `Compteur` et donc
garde toujours un accès à la variable `compte` qui a été définie dans cette
portée.

### Pourquoi les variables privées fonctionnent

Puisqu'il n'est pas possible de référencer ou d'affecter des portées en JavaScript,
il n'y a **aucun** moyen d'accéder à la variable `compte` depuis l'exterieur. La
seule façon d'intéragir avec est de le faire via les deux fermetures.

    var foo = new Compteur(4);
    foo.hack = function() {
        compte = 1337;
    };

Le code ci-dessus ne changera **pas** la variable `compte` dans la portée de `Compteur`,
puique `foo.hack` n'a pas été définie dans **cette** portée. À la place il créera - ou
écrasera - la variable global `compte`.

### Les fermetures dans les boucles

Une erreur réccurente est d'utiliser les fermetures à l'intérieur des boucles, comme si
elles copiaient la valeur de l'index de boucle.

    for(var i = 0; i < 10; i++) {
        setTimeout(function() {
            console.log(i);
        }, 1000);
    }

Le code ci-dessus n'écrira **pas** les nombres de `0` à `9`, mais affichera simplement
le nombre `10` dix fois.

La fonction *anonyme* garde une **référence** sur `i`. Au moment où
`console.log` est appelée, la `for loop` est déjà terminée, et la valeur de
`i` vaut `10`.

Afin d'obtenir le résultat escompté, il est nécessaire de créer une **copie** de la valeur
de `i`.

### S'affranchir du problème de référence

Afin de garder une copie de la valeur de l'index de la boucle, il est préférable
d'utiliser une [enveloppe anonyme](#function.scopes).

    for(var i = 0; i < 10; i++) {
        (function(e) {
            setTimeout(function() {
                console.log(e);
            }, 1000);
        })(i);
    }

La fonction extérieur anonyme est appelée directement avec `i` comme premier argument
et recevra une copie de la **valeur** de `i` dans le paramètre `e`.

La fonction anonyme qui est passée à `setTimeout` aura maintenant une référence vers `e`,
dont la valeur n'est **pas** changée par la boucle.

Une autre possibilité consiste à retourner une fonction depuis l'enveloppe anonyme
qui aura le même comportement que le code ci-dessus.

    for(var i = 0; i < 10; i++) {
        setTimeout((function(e) {
            return function() {
                console.log(e);
            }
        })(i), 1000)
    }

