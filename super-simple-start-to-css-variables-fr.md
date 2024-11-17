> **Remarque du traducteur:** Ceci est une traduction de l'article de Kent C. Dodds nommé «Super Simple Start to css variables» ([EN 🇺🇸](https://kentcdodds.com/blog/super-simple-start-to-css-variables)).

Si vous êtes comme moi, vous êtes un peu en retard sur les variables CSS. Peut être que vous avez apprécié utiliser de _vraies_ variables JavaScript avec CSS-dans-JS ([EN 🇺🇸](https://emotion.sh/)). Mais bon sang, des gens intelligents que je connais et que je respecte disent qu'ils sont extraordinaires et je devrais saisir cette fonctionnalité relativement nouvelle.

Lorsque je commence quelque chose, je suis vraiment reconnaissant pour un début très simple, alors voilà.

J'ai entendu un peu des «variables CSS» et des «propriétés personnalisées CSS» dans la conversation et je n'ai pas vraiment compris la différence. En fait, [ils sont la même chose](https://developer.mozilla.org/fr/docs/Web/CSS/Using_CSS_custom_properties). 🤦‍♂️ Le term technique est «les propriétés personnalisées CSS», mais il est souvent appelé «les variables CSS».

Donc, voilà. Votre démarrage rapide vers des propriétés personnalisées:

```html
<html>
  <style>
    :root {
      --color: blue;
    }
    .my-text {
      color: var(--color);
    }
  </style>
  <body>
    <div class="my-text">This will be blue</div>
  </body>
</html>
```

`:root` est une pseudo-classe qui vous permet de cibler `<html>`. En réalité, c'est exactement la même chose que si on avait utilisé `html` au lieu de `:root` (sauf que `:root` a une spécificité plus élevée).

Comme le reste dans CSS, vous pouvez isoler votre propriétés personnalisées aux sections du document. Ainsi, au lieu de `:root`, on pourrait utiliser des sélecteurs habituelles:

```html
<html>
  <style>
    .blue-text {
      --color: blue;
    }
    .red-text {
      --color: red;
    }
    .my-text {
      color: var(--color);
    }
  </style>
  <body>
    <div class="blue-text">
      <div class="my-text">This will be blue</div>
    </div>
    <div class="red-text">
      <div class="my-text">This will be red</div>
    </div>
  </body>
</html>
```

La cascade s'applique aussi ici:

```html
<html>
  <style>
    .blue-text {
      --color: blue;
    }
    .red-text {
      --color: red;
    }
    .my-text {
      color: var(--color);
    }
  </style>
  <body>
    <div class="blue-text">
      <div class="my-text">This will be blue</div>
      <div class="red-text">
        <div class="my-text">
          This will be red (even though it's within the blue-text div, the
          red-text is more specific).
        </div>
      </div>
    </div>
  </body>
</html>
```

Vous pouvez aussi modifier la valeur de la propriété personnalisée en utilisant JavaScript:

```html
<html>
  <style>
    .blue-text {
      --color: blue;
    }
    .red-text {
      --color: red;
    }
    .my-text {
      color: var(--color);
    }
  </style>
  <body>
    <div class="blue-text">
      <div class="my-text">
        This will be green (because the JS will change the color property)
      </div>
      <div class="red-text">
        <div class="my-text">
          This will be red (even though it's within the blue-text div, the
          red-text is more specific).
        </div>
      </div>
    </div>
    <script>
      const blueTextDiv = document.querySelector(".blue-text");
      blueTextDiv.style.setProperty("--color", "green");
    </script>
  </body>
</html>
```

Donc voilà, c'est votre debut très simple. Je n'ai pas abordé pourquoi vous voudriez faire ça. Google est votre ami pour le découvrir, ou laissez simplement votre imagination s'emballe. Avec un peu de chance, ça vous aidera à initier rapidement à l'idée. Bonne chance!

PS: Êtes-vous intéressé à savoir comment les variables CSS sont utilisés dans React? Rensignez-vous ça: [Use CSS Variables instead of React Context (EN 🇺🇸)](https://epicreact.dev/css-variables).