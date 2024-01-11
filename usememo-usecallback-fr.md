Voici un distributeur de bonbons:

> Distributeur de bonbons
> Bonbons disponsibles
>
> - snickers
> - skittles
> - twix
> - milky way

Il est implémenté comme ceci:

// bloc de code

Je veux vous poser une question et que vous y réfléchissiez attentivement avant de continuer. Je vais changer le code et je veux que vous me disiez lequel aura le mieux performance.

Je vais seulement envelopper la fonction `dispense` dans `React.useCallback`:

// bloc de code

Voici encore l'original:

// bloc de code

Donc, ma question dans ce cas, lequel est le plus performant?

Je laisse de l'espace pour ne pas gâcher la réponse...

Continue de défiler vers le bas... Tu as répondu, non?

Voici la réponse:

# Pourquoi `useCallback` est le moins performant?

On entend souvent dire qu'on devrait utiliser `React.useCallback` pour ameliorer le performance et que "les fonctions inlines peut être problematique pour le performance," alors comment pourrait-il être préférable de ne pas utiliser `useCallback`?

Considérez ceci, mettez de côté notre exemple et même React: **chaque ligne de code que s'est fait executer a un coût.** Laissez-moi réviser un peu l'exemple de `useCallback` pour mieux prouver (sans changer le comportemont, seulement déplacer des lignes):

// bloc de code

Voici encore l'original:

// bloc de code

On a noté quelque chose? Regardons la différence:

// bloc de code

Ils sont exactement les mêmes, sauf la vérsion `useCallback` fait _plus_ de travail. Il faut non seulement qu'on défine la fonction, mais en plus un tableau (`[]`) et _en plus_ lancer le `React.useCallback` qui lui-même défine des attributs et éxecute des instructions.

Dans les deux cas JavaScript doit allouer de la mémoire pour la définition de la fonction en chaque rendu, et selon la façon dont `useCallback` est mis en œuvre, vous pouvez obtenir plus d'allocation pour les définitions de fonctions (en fait, ce n'est pas le cas, mais le point est toujours valable). C'est ce que j'essayais de transmettre dans ce sondage Twitter:

// tweet

Je voudrais aussi mentionner que dans le rendu deuxiéme du composant, la fonction `dispense` orginale s'est fait supprimer dans la récupération de l'espace mémoire (liberer de l'espace mémoire), alors un nouveau s'est fait créer. Toutefois, avec `useCallback` la fonction `dispense` orginale ne s'est pas fait supprimer dans la récupération d'espace et un nouveau s'est fait créer, donc vous êtes aussi dans une situation pire du point de vue de le mémoire.

En relation, si vous avez des dépendances c'est possible que React garde une référence aux fonctions précédentes parce que la memoization veut typiquement dire qu'on conserve des copies de valeurs anciennes pour retourner si on obtient les mêmes dépendances qu'avant. Les plus perspicaces noteront que ça signifie que React doit aussi garde une référence aux dépéndences pour ce contrôle d'égalité (d'ailleurs, cela se produit probablement de toute façon grace à la clôture, mais cela vaut la peine de le mentionner).

# En quoi `useMemo` est-il différent mais en même temps comparable?

`useMemo` est comparable à `useCallback`, sauf qu'il vous permet d'appliquer la memoization à n'importe quel type de valeur (pas seulement aux fonctions). Il fait cela en acceptant une fonction qui renvoie la valeur alors cette fonction-là _n'est appelée que_ quand la valeur doit être récupérée (typiquement, cela n'arrive qu'une fois, à chaque fois qu'un membre du tableau de dépendances change entre un rendu et l'autre).

Donc, si je ne veux pas initialiser le tableau `initialCandies` dans chaque rendu, je pourrais faire ce change:

// bloc de code

J'éviterais ce problème-là, mais les économies serait tellement minimales que le coût de faire plus compléxe le code ne pas vaut la peine. En fait, c'est probablement plus mauvais d'utiliser `useMemo` pour ça parce qu'on fait un appel de fonction et ce code-là défine des attributs, etc.

Dans ce scénario, il serait mieux de faire ce change:

// bloc de code

Pourtant parfois vous n'avez pas ce luxe-là parce que la valeur est dérivée de `props` ou d'autres variables qui sont initialisés dans le corps de la fonction.

Le fait est que cela n'a pas d'importance de toute façon. L'avantage de l'optimisation du code-là est tellement miniscule que votre temps serait _très_ mieux consacré à l'amélioration de votre produit.

# Dans quel but?

Le but est ceci:

**Les optimisations de exécution ne sont pas gratuites. Elles ont _toujours_ un coût mais les avantages offerts ne le couvrent _pas_ toujours.**

Par suite, _optimiser de manière responsable._

# Alors quand dois-je `useMemo` et `useCallback`?

Il existe deux raisons ces deux hooks sont inclus dans React.

1. L'égalité référentielle
2. Les opérations coûteuses en calcul

# L'égalité référentielle

Si vous avez un niveau débutant en programmation/JavaScript, vous apprendrez rapidement que les types primitifs n'ont pas le même comportement lors de l'application de l'opérateur d'égalité.

// bloc de code
// comment: NOTE: React utilise en fait Object.is, mais il est très similaire à ===

Sans trop entrer dans les détails, il suffit de dire que lorsque vous définissez un objet dans votre composant de fonction React, il ne sera pas égal en référence à la derniere fois que le même objet a été défini (même s'il a toutes les mêmes proprietés et valeurs).

Il existe deux scénarios dans lesquels l'égalité référentielle est importante dans React; examinons-les un par un.

## Les listes de dépéndances

Examinons un exemple.

> Attention: ne pas trop analyser le code suivant, qui est vraiment artificiel. Concentrez-vous sur les concepts, s'il vous plaît.

// bloc de code
// comment: On veut que ceci soit réexécuté si bar ou baz changent

Ce code est problematic parce que `useEffect` fera une vérification d'égalité référentielle sur `options` entre chaque rendu, et grâce au maniére JavaScript fonctionne, `options` sera noueveau chaque fois. Lorsque React vérifie si `option` a changé entre un rendu et l'autre il sera toujours évalué à vrai, alors la fonction de rappel sera exécutée aprés chaque rendu plutôt que uniquement lorsque `bar` et `baz` changent.

Il y a deux solutions pour cela:

// bloc de code
// comment: On veut que ceci soit réexécuté si bar ou baz changent

S'il s'agissait d'un scénario réel, ce serait une excellente option pour résourdre le problème.

Cependant, il existe un scénario dans lequel cette solution n'est pas pratique. Si `bar` ou `baz` est un objet, un tableau, ou une fonction pas primitif.

// bloc de code

C'est exactement la raison d'etre pour `useCallback` et `useMemo`. Donc voilà la méthode pour résoudre ce problème-la dans son intégralité:

// bloc de code

> Notez que la même chose s'applique à la liste de dépendances à la liste de dépendances transmise à `useEffect`, `useLayoutEffect`, `useCallback`, et `useMemo`.

## `React.memo` (et ses amis)

> Attention: ne pas trop analyser le code suivant, qui est vraiment artificiel. Concentrez-vous sur les concepts, s'il vous plaît.

Regardons ceci:

// bloc de code

Chaque fois que vous cliquez l'un ou l'autre de ces boutons l'état de `DualCount` change. Par conséquent `DualCount` re-rend, ce qui à son tour re-rendra les deux `CountButton`s. Pourtant, celui qui est cliqué est le seul qui a besoin de re-rendre, non? Donc si vouz cliquez le prémiere, le deuxiéme s'est fait re-rendre, mais rien ne change. On appelle cela une "re-rendu inutile."

LA PLUPART DU TEMPS VOUS NE DEVRIEZ PAS VOUS SOUCIER D'OPTIMIZER LES RENDUS INUTILES. React est TRÈS vite et il y a tellement de choses auxquelles je peux penser à faire avec votre temps qui seraient mieux que d'optimiser des choses comme celle-ci. En fait, au cours des trois années où Kent a travaillé chez PayPal, et même pendant tout le temps qu'il a utilisé React, il n'a jamais devoir faire de telles optimisations.

Neanmoins, il existe des situations dans lesquelles le rendu peut prendre beaucoup de temps, comme des graphiques et animations très intéractives. Grace au caractère pragmatique de React il y a un issue de secours:

// bloc de code

Désormais React re-rendra `CountButton` seulement si ses props changent. Super! Mais on a encore beaucoup à faire. Rappelez-vous de l'égalité référentielle? Dans les fonctions du composant `DualCounter`, on définit les fonctions `increment1` et `increment2`, donc chaque fois `DualCounter` s'est fait re-rendre ces fonctions-là seront nouvelles et React re-rendra de toute façon les deux `CountButton`s.

C'est l'autre situation dans laquelle `useCallback` et `useMemo` peuvent être utiles:

// bloc de code

Désormais on peut esquiver les rendus inutiles de `CountButton`.

Je voudrais répéter que je conseille fortement contre utiliser sans évaluer `React.memo` (et ses amis `PureComponent` et `shouldComponentUpdate`). Ces optimisations-là ont un coût et vous devez connaître à la fois leurs coûts et leurs avantages pour déterminer si elles valent la peine dans votre cas. Comme on l'a observé précédemment, il peut être difficile de réussir à tout moment donc vous pourriez ne recevoir aucune prestation.

# Les opérations coûteuses en calcul

Ces opérations sont l'autre raison que `useMemo` est inclus dans React. L'avantage de `useMemo` est que vous pouvez prendre une valeur:

// bloc de code

Et l'obtenir paresseusement:

// bloc de code

Ce n'est pas vraiment utile pour le cas ci-dessus, mais imaginez que vous avez une fonction qui calcule de manière synchrone une valeur coûteuse en calcul. (Peu d'applications sont obligées de le faire, mais c'est un exemple.)

// bloc de code

Si vous avez les bonnes valeurs de `iteration` ou `multiplier`, cela peut être assez lent et vous ne pouvez pas faire grand-chose pour l'améliorer spécifiquement. Vous ne pouvez pas comme par magie rendre plus rapide le matériel informatique de vos utilisateurs. Pourtant vous _pouvez_ faire en sorte que vous ne devez pas calculer la même valeur deux fois en suite. C'est ce que fera `useMemo`:

// bloc de code

Cela fonctionne car même si vous définissez la valeur pour calculer les nombres premiers en chaque rendu (ce qui est très rapide), React appel ce fonction-là seulement lorsqu'il a besoin de la valeur. En plus, React conserve des valeurs précédents en fonction des entrées et renverra la valeur précédente en fonction des entrées précédentes. C'est la memoization au travail.

# Conclusion

Rappelez-vous que chaque abstraction et optimisation de exécution ont un coût. Si vous appliquez le [AHA Programming principle](https://kentcdodds.com/blog/aha-programming) et attendez que c'est soit urgent avant d'appliquer l'optimisation, vous vous épaargnerez des coûts sans en récolter les bénéfices. Vous risquez de rendre plus complexe le code pour vos collègues, ou de réduire le performance en appelant les hooks et en empêchant le récupération d'espace. Ces sont de coûts acceptables si vous obtenez assez d'avantages de performance, mais **c'est préférable de les mesurer d'abord.**

Lectures connexes:

- React FAQ: ["Are Hooks slow because of creating functions in render?"](https://reactjs.org/docs/hooks-faq.html#are-hooks-slow-because-of-creating-functions-in-render)
- [Ryan Florence](https://twitter.com/ryanflorence): [React, Inline Functions, and Performance](https://reacttraining.com/blog/react-inline-functions-and-performance)

En aparté, si vous vous inquiétez de la transition vers les hooks et le besoin de définer des fonctions dans notre componants de fonction, rappelez-vous qu'on avait définer des méthods dans la phase de rendu depuis le début. Par exemple:

// bloc de code
// comment: VOILA! C'est une fonction qui est définée dans le méthode de rendu! Les hooks n'ont pas introduit ce concept, on le fait depuis le commencement.
