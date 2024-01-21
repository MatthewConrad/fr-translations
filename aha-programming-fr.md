> **Remarque du traducteur:** Ceci est une traduction de l'article de Kent C. Dodds nommé «AHA Programming» ([EN 🇺🇸](https://kentcdodds.com/blog/aha-programming)).

# DRY

DRY (un acronym qui veut dire Don't Repeat Yourself — [«Ne vous répètez pas»](https://fr.wikipedia.org/wiki/Ne_vous_r%C3%A9p%C3%A9tez_pas)) est un vieux fondement de logiciels qu'est résumé par Wikipedia comme ceci:

> Dans un système, toute connaissance doit avoir une représentation unique, non ambiguë, faisant autorité

C'est une bonne pratique que j'adopte typiquement, bien que je ne la suive pas aussi strictement que cette définition ne le suggère. Le problème principal que j'ai avec [la duplication de code](https://fr.wikipedia.org/wiki/Duplication_de_code) (qui veut dire copier-coller, l'opposé de DRY) consiste à découvrir un bug, le corriger, puis se rendre compte qu'il faut aussi le corriger ailleurs.

Autrefois, j'ai hérité d'un codebase qui utilisait forcément la duplication de code et j'au dû corriger un bug à huit places differents! Comme c'est irritant! Il aurait **beaucoup** aidé à extraire ce code dans une fonction qui pourrait se fait exécuter n'importe où.

# WET

Il y a un autre concept que des gens ont appelé la programmation WET, qui veut dire Write Everything Twice — «Écrivez tout deux fois». C'est tout aussi sévère et hyper particulière. [Conlin Durbin](https://twitter.com/CallMeWuz) l'a défini ainsi ([EN 🇺🇸](https://dev.to/wuz/stop-trying-to-be-so-dry-instead-write-everything-twice-wet-5g33)):

> Vous pouvez se demander «je ne l'ai pas déjà écrit?» deux fois, mais pas trois.

Dans le même codebase que j'ai déjà mentionné, il y avait trop d'abstraction, ce qui était encore plus nuisible que la duplication. C'était code pour plusieurs controllers AngularJS qui passaient `this` à une function, et cette function ajoutait certaines capacités à l'instance du controller en forçant de ses méthodes et ses proprietés. Je devine que c'était une sorte de faux héritage. C'était **très** déroutant et difficile à suivre, et j'étais terrifié à l'idée de faire des changements dans cette zone du codebase.

Le code était réutilisé dans plus de trois places, mais l'abstraction était mauvaise et j'aurais préféré que le code soit dupliqué.

# AHA

`AHA` (prononcé comme «Aha!» comme si tu avais soudainement fait une découverte) est un acronym que j'ai [pris de Cher Scarlett](https://twitter.com/cherthedev/status/1112819136147742720) qui veut dire:

> Avoid Hasty Abstractions — Évitez Les Abstractions Hâtives

Le façon que je réfléchir à ce principe est merveilleusement décrit à [Sandi Metz](https://twitter.com/sandimetz):

> Préférez la duplication à l'abstraction

Je veux que vous relisez cette perle de sagesse, puis lisez le fantastique article de blog de Sandi sur le sujet: [The Wrong Abstraction (EN 🇺🇸)](https://www.sandimetz.com/blog/2016/1/20/the-wrong-abstraction).

Voici un autre principe connexe important:

> Optimisez tout d'abord pour le changement

Je pense que la clé est qu'on ne sait pas quel sera l'avenir du code. On pourrait passer plusieurs semaines en optimisant du code pour la performance, ou en créant le meilleur API pour notre nouvelle abstraction, pour découvrir seulement le lendemain qu'on faisait des suppositions erronées et que l'API a besoin d'une refonte complète, ou que on n'a plus besoin de la fonctionnalité pour laquelle le code a été écrit. On ne sait pas avec certitude. On sait seulement que les trucs vont probablement changer, et s'ils ne changent jamais, on ne modifiera pas le code donc on se fiche de son apparance.

Pour plus de clarté, je ne suggère pas l'anarchie. Je suggère qu'on se souvienne qu'on ne connait pas les prérequis qui seront imposeés à notre code à l'avenir.

Donc je suis d'accord avec la duplication de code jusqu'à ce que vous vous soyez sûr de connaître le cas d'utilization de ce code dupliqué. Quelles parties du code sont différentes qui seraient des biens arguments pour votre fonction? Après que ce code s'exécute à plusieurs endroits, les points communs vont exiger l'abstraction et vous serez dans le bon état d'esprit pour réaliser cette abstraction.

Si vous faites une abstraction assez tôt, vous penserez que la fonction ou le composant est parfait pour votre cas d'utilization, donc vous forcerez le code à s'adapter à ce cas. Il se répète plusieurs fois jusqu'à ce que l'abstraction soit votre application entière dans des boucles et des déclarations `if`.

Il y a quelques années, j'ai été embauché pour réexaminer le codebase d'une entreprise, et j'ai utilisé un outil nommé [jsinspect](https://github.com/danielstjules/jsinspect) pour identifier des tranches de code dupliqué afin de leur montrer où l'abstraction serait utile. Ils avaient beaucoup de code dupliqué, et de mon point de vue, il était évident à quoi devraient ressembler les abstractions.

**Je pense que le point à retenir** de la «programmation AHA» est qu'il ne faut pas être dogmatique lorsque vous écrivez des abstractions, mais plutôt écrire l'abstraction quand cela vous convient, et n'ayez pas peur de dupliquer le code jusqu'à ce que vous y arriviez.

# Conclusion

Je trouve qu'une approche mesurée et pragmatique des principes de logiciel est importante. C'est pourqoui je préfère `AHA` à `DRY` ou `WET`. Il a l'intention de vous aider à être attentif à vos abstractions sans dicter des règles stricts sur quand il est approprié d'éxtraire une pièce de code dans une fonction ou un module.

J'espere que cela vous sera utile. Si vous vous trouvez embourbé dans de mauvaises abstractions, reprenez courage! Sandi donne de bonnes étapes pour s'en échapper dans son article de blog ([EN 🇺🇸](https://www.sandimetz.com/blog/2016/1/20/the-wrong-abstraction)). Bonne chance!
