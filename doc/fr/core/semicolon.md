## Insertion automatique de points-virgules

Bien que JavaScript a un style de syntaxe similaire à C, il n'enforce **pas**
l'usage des points-virgules dans le code source, il est donc possible de les
omettre.

JavaScript n'est pas un langage sans point-virgule. En fait, il a besoin des points-virgules
pour comprendre le code source. Ainsi, le parseur JavaScript les insert 
**automatiquement** à chaque fois qu'il rencontre une erreur d'analyse dûe à 
un point-virgule manquant.

    var foo = function() {
    } // erreur d'analyse, point-virgule attendu
    test()

L'insertion intervient, et le parseur essaie encore

    var foo = function() {
    }; // pas d'erreur, le parseur continue
    test()

L'insertion automatique de points-virgules est considérée comme l'une des
plus grosses erreurs de conception du langage parce qu'il *peut* changer
le comportement du code.

### Comment ça fonctionne

Le code ci-dessous n'a pas de point-virgule, donc c'est au parseur de décider
où les insérer.

    (function(window, undefined) {
        function test(options) {
            log('Tu peux pas test !')

            (options.list || []).forEach(function(i) {

            })

            options.value.test(
                'une longue chaîne à passer',
                'et une autre longue chaîne à passer'
            )

            return
            {
                foo: function() {}
            }
        }
        window.test = test

    })(window)

    (function(window) {
        window.uneBibliotheque = {}

    })(window)

Dessous se trouve le résultat du jeu de "devinette" du parseur.

    (function(window, undefined) {
        function test(options) {

            // Not inserted, lines got merged
            log('Tu peux pas test !')(options.list || []).forEach(function(i) {

            }); // <- insertion

            options.value.test(
                'une longue chaîne à passer',
                'et une autre longue chaîne à passer'
            ); // <- insertion

            return; // <- insertion, casse l'instruction return
            { // traité en tant que bloc

                // un label et une expression solitaire
                foo: function() {} 
            }; // <- insertion
        }
        window.test = test; // <- insertion

    // Les lignes sont à nouveau jointes
    })(window)(function(window) {
        window.uneBibliotheque = {}; // <- insertion

    })(window); //<- insertion

> **Note :** Le parseur JavaScript ne gère pas "correctement" les instructions
> `return` qui sont suivie d'un retour à la ligne, même si ce n'est pas forcément
> la faute de l'insertion automatique de points-virgules, ça peut être un effet
> de bord non-voulu.

Le parseur a drastiquement changé le comportement du code du dessus. Dans certains
cas, il fait un **mauvais choix**.

### Parenthèse ouvrante

Dans le cas d'une parenthèse ouvrante, le parseur n'insèrera pas de point-virgule.

    log('Tu peux pas test !')
    (options.list || []).forEach(function(i) {})

Ce code est transformé en une ligne.

    log('Tu peux pas test !')(options.list || []).forEach(function(i) {})

Les chances sont **très** grandes que `log` ne retourne pas une fonction ; ainsi 
le code ci-dessus lèvera une `TypeError` indiquant que `undefined` n'est pas une 
fonction.

### En conclusion

Il est fortement recommendé de ne **jamais** omettre les points-virgules ; de la 
même manière les accolades doivent être gardées sur la même ligne que leur instruction
correspondante et ne doivent pas être oubliées pour une instruction `if`/`else` 
d'une ligne. Chacune de ces mesures non seulement améliore la cohérence du code, 
mais empêche le parseur JavaScript de changer le comportement.

