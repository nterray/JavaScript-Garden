## Conversion de type

JavaScript est un langage *faiblement typé*, donc il appliquera la 
*conversion de type* à **chaque fois** que ce sera possible.

    // Tout ceci est vrai
    new Number(10) == 10; // Number.toString() est converti en un 
                          // nombre 

    10 == '10';           // Les chaînes sont convertie en Number
    10 == '+10 ';         // Tirage de cheveu avec les chaînes
    10 == '010';          // Encore
    isNaN(null) == false; // null est converti en 0
                          // qui bien sur n'est pas NaN
    
    // Tout ceci est faux
    10 == 010;
    10 == '-10';

> **Note ES5 :** Les nombres litéraux qui commencent par un `0` sont 
> interprétés comme un octal (Base 8). Le support octal pour eux a été
> **enlevé** dans le mode strict d'ECMAScript.

Dans le but d'éviter ce qui précède, l'usage de 
[l'opérateur d'égalité stricte](#types.equality) est fortement recommandé. Bien 
que celà élimine les pièges courants, il y a encore beaucoup de problèmes qui
peuvent survenir du système de typage faible de JavaScript.

### Constucteurs des types natifs

Les constructeurs des types natifs comme `Number` et `String` se comportent
différemment selon qu'ils sont utilisé avec le mot clef `new` ou non.

    new Number(10) === 10;     // False, Object et Number
    Number(10) === 10;         // True, Number et Number
    new Number(10) + 0 === 10; // True, à cause de la conversion 
                               // implicite

Utiliser un type natif comme `Number` en tant que contructeur créera un nouvel
objet `Number`, mais oublier le mot clef `new` fera se comporter la fonction
`Number` comme un convertisseur.

En plus, avoir des litéraux ou des valeurs non-objet résulte avec encore de 
la conversion de type.

La meilleure option est de convertir **explicitement** en l'un des trois types 
possibles.

### Convertir en chaîne

    '' + 10 === '10'; // true

En prefixant une chaîne vide, une valeur peut facilement être convertie 
en chaîne.

### Convertir en nombre

    +'10' === 10; // true

En utilisant l'opérateur **unaire** plus, il est possible de convertir
en nomble.

### Convertir en booléen

en utilisant l'opérateur **not** deux fois, une valeur peut être convertie
en booléen,

    !!'foo';   // true
    !!'';      // false
    !!'0';     // true
    !!'1';     // true
    !!'-1'     // true
    !!{};      // true
    !!true;    // true


