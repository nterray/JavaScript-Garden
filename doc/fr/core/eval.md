## Pourquoi ne pas utiliser `eval`

La fonction `eval` exécute une chaîne contenant un code JavaScript dans la portée
locale.

    var foo = 1;
    function test() {
        var foo = 2;
        eval('foo = 3');
        return foo;
    }
    test(); // 3
    foo; // 1

Cepandant, `eval` ne s'exécute dans la portée locale que lorsqu'elle est
**directement** *et* lorsque le nom de la fonction appelée est effectivement
`eval`.

    var foo = 1;
    function test() {
        var foo = 2;
        var bar = eval;
        bar('foo = 3');
        return foo;
    }
    test(); // 2
    foo; // 3

L'usage de `eval` devrait être évité **à tout prix**. 99.9% de ses utulisations peuvent 
être effectuées sans elles.

### `eval` déguisé

Les [fonctions timeout](#other.timeouts) `setTimeout` et `setInterval` peuvent toutes les
deux prendre une chaîne en tant que premier argument. Cette chaîne sera **toujours**
exécutée dans la portée locale puisque `eval` n'est pas appelée directement dans ce cas.

### Problèmes de sécurité

`eval` est aussi un problème de sécurité. Puisqu'elle exécute **tout** code qui
lui est passée, elle ne doit **jamais** être utilisée avec des chaînes provenant
de sources non fiables ou inconnues.

### En conclusion

`eval` ne devrait jamais être utilisée. Tout code qui l'utilise doit être remis en
cause dans son fonctionnement, sa performance et sa sécurité. Dans le cas où 
quelque chose requière `eval` pour fonctionner, il ne devrait **pas** l'utiliser en
premier lieu.
Une *meilleure conception* doit être utilisée, qui ne nécessite pas l'utilisation de
`eval`

