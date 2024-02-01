> **Remarque du traducteur:** Ceci est une traduction de l'article de Kent C. Dodds nommé «useEffect vs useLayoutEffect» ([EN 🇺🇸](https://kentcdodds.com/blog/useeffect-vs-uselayouteffect)).

Les deux peuvent être utilisés pour faire effectivement la même chose, mais leurs cas d'utilisation sont légèrement différents. Alors, voici des règles à prendre en considération lorsque vous décidez quel Hook React utiliser.

# useEffect

99% du temps, c'est ce que vous souhaitez utiliser. Quand les hooks sont stables et que vous révisez vos composants à base de classe pour utiliser des hooks, vous déménagerez probablement le code de `componentDidMount`, `componentDidUpdate`, et `componentWillUnmount` vers `useEffect`. 

Le seul avertissement est que cela est exécuté après que React fait le rendu de votre composant et garantit que votre fonction d'effet ne bloque pas l'affichage du navigateur. Cela distingue le comportement des composants à base de classe, où `componentDidMount` et `componentDidUpdate` sont exécutés synchroniquement après l'affichage. Ce mode est plus efficace et souvent plus désirable.

Toutefois, si votre effet modifie le DOM (via un Ref de nœud de DOM) **et** que le modification en place du DOM changera l'apparence du nœud de DOM entre le moment où il fait son rendu et celui où votre effet le modifie, vous devriez utiliser `useLayoutEffect`, **pas** `useEffect`. Sinon, l'utilsateur pourrait voir une vacillante d'affichage lorsque vos modifications de DOM prennent effet. **En général, c'est le seul moment où vouz souhaitez éviter `useEffect` et utiliser à la place `useLayoutEffect`.**

# useLayoutEffect

Cela est exécuté immédiatement après que React a effectué toutes les modifications du DOM. Cela pourrait étre utile si vous avez besoin de faire des mesures du DOM (comme aller chercher la position de défilement ou d'autres styles d'un élément) et ensuite faire des modifications du DOM **ou** déclencher un affichage synchronisé en mettant à jour l'état local.

En ce qui concerne la planification, cela fonctionne de la même manière que `componentDidMount` et `componentDidUpdate`. Votre code est exécuté immédiatement après que le DOM a été mis à jour, mais avant que le navigateur n'ait la possibilité de «dessiner» ces changements (en fait, l'utilisateur ne voit pas les nouvelles modifications jusqu'à ce que le navigateur les redessine).

# Résumé

- **useLayoutEffect**: si vous avez besoin de modifier le DOM et/ou d'effectuer des mesures
- **useEffect**: si vous n'avez pas du tout besoin d'interagir avec le DOM ou que vos modifications du DOM ne sont pas perceptibles (sérieusement, vous devrez la plupart du temps utiliser ceci)

# Un cas en particulier 

Une autre situation où vous pourriez préférer utiliser `useLayoutEffect` au lieu de `useEffect` est lorsque vous mettez à jour une valeur (comme un `ref`) et que vous voudriez garantir que le valuer est mise à jour avant qu'un autre code ne soit executé. Par exemple:

```tsx
const ref = React.useRef()
React.useEffect(() => {
  ref.current = 'une certaine valeur'
})

// plus tard, dans un autre hook ou quelque chose
React.useLayoutEffect(() => {
  console.log(ref.current) // <-- ce consigne une ancienne valeur car cela est exécuté en premier!
})
```

Donc dans situations comme ca, la solution est `useLayoutEffect`.

# Conclusion

Tout dépend des comportements par défaut. Le comportement par défaut permet au navigateur de redessiner en fonction des mises à jour du DOM avant que React n'exécute votre code. Ca signifie que votre code ne bloquera pas le navigateur, et que l'utilisateur verra des mises à jour du DOM plus tôt. Donc, vous devriez vous tenir à `useEffect` la plupart du temps.