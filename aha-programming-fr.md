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
