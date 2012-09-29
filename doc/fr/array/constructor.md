## Le constructeur `Array`

Puisque le constructor `Array` est ambigu quant à sa gestion des paramètres,
il est fortement recommendé de toujours utiliser la notation `[]` lorsqu'on crée
des tableaux.

    [1, 2, 3]; // Resultat: [1, 2, 3]
    new Array(1, 2, 3); // Resultat: [1, 2, 3]

    [3]; // Resultat: [3]
    new Array(3); // Resultat: []
    new Array('3') // Resultat: ['3']

Dans les cas où il n'y a qu'un argument passé au constructeur `Array` et quand cet
argument est un `Number`, le constructeur retournera un tableau *fragmenté* avec la
propriété `length` qui prend la valeur de l'argument. Notons que *seule* la propriété
`length` du nouveau tableau sera positionné de cette façon ; les index du tableau ne
seront pas initialisés.

    var arr = new Array(3);
    arr[1]; // undefined
    1 in arr; // false, l'index n'a pas été défini

La possibilité de pouvoir définir la longueur d'un tableau en amont est pratique
dans peu de cas, comme répéter une chaine, ce qui permet d'éviter l'usage d'une
boucle `for loop`.

    new Array(count + 1).join(chaine_a_repeter);

### En conclusion

L'usage du constructeur devrait être évité autant que faire se peut. Les littéraux
sont préférables. Ils sont plus courts et possèdent une syntaxe plus claire. De plus,
ils améliorent la lisibilité du code.
