---
type: style-guide
---

# Conventions <sup class="beta">beta</sup>

Ceci est le guide des conventions officielles pour du code spécifique à Vue. Si vous utilisez Vue dans vos projets, c'est la référence pour éviter les erreurs, les mauvaises décisions ou les anti-patterns. Nous ne pensons cependant pas qu'un guide de conventions soit pertinent pour toutes les équipes ou tous les projets, ce que nous encourageons ici est basé sur nos expériences passées, les technologies que nous côtoyons ainsi que nos valeurs personnelles.

En grande partie, nous allons éviter les conventions à propos du JavaScript ou du HTML en eux-même. Nous ne nous soucions pas de votre utilisation des points-virgules ou de la place de la virgule en début de ligne. Nous ne nous soucierons pas non plus de savoir si votre HTML utilise des apostrophes ou des guillemets pour les valeurs des attributs. Quelques exceptions seront faites cependant, quand nous trouvons qu'un pattern spécifique est utile dans le contexte de Vue.

> **Bientôt, nous fournirons des astuces pour la mise en application.** Même si certains point son une simple question de discipline, nous essayerons de vous montrer comment utiliser ESLint et d'autres processus automatisé pour mettre en place simplement ces conventions.

Et donc, voici les quatre catégories de règles que nous avons retenues :



## Catégories de règle

### Priorité A : Essentiel

Ces règles aident à éviter les erreurs, donc apprenez et respectez les à tout prix. Des exceptions peuvent exister, mais elles devraient être rare et prise uniquement avec un œil expert sur le JavaScript et Vue.

### Priorité B : Fortement recommandée

Ces règles ont été établie pour améliorer la lisibilité et / ou l'expérience des développeurs dans la majorité des projets. Votre code fonctionnera toujours si vous ne les suivez pas, mais ces écarts doivent être rares et justifiés.

### Priorité C : Recommandée

Là ou de multible et équivalente options existe, un choix arbitraire a été fait pour assurer la consistence. Dans ces règles, nous décrivons chaque option acceptable et suggérons un choix par défaut. Cela signifie que vous pouvez faire des choix différent sur votre propre base de code, aussi longtemps que vous êtes consistant et avez de bonnes raisons. Mais garder toujours les bonnes raisons à l'esprit ! En vous alignant sur les standards de la communauté vous pourrez :

1. amérioler votre cerveau à analyser plus facilement la plupard des codes communautaires rencontrés,
2. capable de copier et coller la plupard des exemples de code sans modification,
3. trouver de nouvelles recrues déjà habitués à votre style de codage préféré, au moins en ce qui concerne Vue.

### Priorité D : Faire attention

Certaines fonctionnalités de Vue existe pour régler des cas exceptionnelles ou rendre la migration d'une vielle version de code plus simple. Mais utiliser sans parciemonie, elle rendrons votre code difficile à maintenir et même deviendront une source de bogues. Ces règles mette en lumière des fonctionnalités potentiellement risquées, décrivant quand et pourquoi elle doivent être évitées.



## Règles de priorité A : essentiel (prévenir les erreurs)



### Nom de composant à mots multiples <sup data-p="a">essentiel</sup>

**Les noms de composant devraient toujours être des mots multiples, à l'exception du composant racine `App`.**

Ceci afin de [prévenir les conflits](http://w3c.github.io/webcomponents/spec/custom/#valid-custom-element-name) avec des éléments HTML futur ou existant car toutes les balises HTML sont en un seul mot.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` js
Vue.component('todo', {
  // ...
})
```

``` js
export default {
  name: 'Todo',
  // ...
}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` js
Vue.component('todo-item', {
  // ...
})
```

``` js
export default {
  name: 'TodoItem',
  // ...
}
```
{% raw %}</div>{% endraw %}



### Données du composant data <sup data-p="a">essentiel</sup>

**La propriété `data` doit être une fonction.**

Quand vous utilisez la propriété `data` dans un composant (par ex. partout sauf sur `new Vue`), la valeur doit être une fonction qui retourne un objet.

{% raw %}
<details>
<summary>
  <h4>Explication détaillée</h4>
</summary>
{% endraw %}

Quand la valeur de la propriété `data` est un objet, elle est partagée à travers toutes les instances du composant. Imaginez, par exemple, un composant `TodoList` avec ces données :

``` js
data: {
  listTitle: '',
  todos: []
}
```

Nous voudrions réutiliser ce composant pour permettre aux utilisateurs de maintenir plusieurs listes (par ex. une liste de course, une liste de souhait, une liste de tâche, etc). Il y a cependant un problème. Comme toutes les instances du composant font référence au même objet de donnée, changer le titre de l'une des listes va aussi changer le titre de toutes les autres. Et c'est également vrai pour l'ajout, l'édition ou la suppression dans la liste.

À la place, nous voulons que chaque composant instancie ces données pour soi. Pour que cela soit possible, chaque instance doit générer un objet de données unique. En JavaScript, ceci peut-être accompli en retournant l'objet depuis une fonction :

``` js
data: function () {
  return {
    listTitle: '',
    todos: []
  }
}
```
{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` js
Vue.component('some-comp', {
  data: {
    foo: 'bar'
  }
})
```

``` js
export default {
  data: {
    foo: 'bar'
  }
}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon
``` js
Vue.component('some-comp', {
  data: function () {
    return {
      foo: 'bar'
    }
  }
})
```

``` js
// Dans un fichier `.vue`
export default {
  data () {
    return {
      foo: 'bar'
    }
  }
}
```

``` js
// Par contre l'usage par objet est possible dans
// l'instance racine de Vue, car il n'y a qu'une
// instance racine qui existe
new Vue({
  data: {
    foo: 'bar'
  }
})
```
{% raw %}</div>{% endraw %}



### Définitions de prop <sup data-p="a">essentiel</sup>

**Les définitions de prop devraient être aussi détaillé que possible.**

Dans du code acté, les définitions de prop devraient être toujours aussi détaillé que possible, en spcifiant au moins les type(s).

{% raw %}
<details>
<summary>
  <h4>Explication détaillée</h4>
</summary>
{% endraw %}

Les [definitions de prop](https://vuejs.org/v2/guide/components.html#Prop-Validation) détaillée ont deux avantages :

- Elles documentent l'API du composant, il est ainsi possible de voir comment le composant est prévu d'être utilisé.
- En développement, Vue va vous avertir si le composant fournit un type de props incorrectement formaté et vous aider ainsi à trouver des sources d'erreur.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` js
// This is only OK when prototyping
props: ['status']
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` js
props: {
  status: String
}
```

``` js
// Même mieux !
props: {
  status: {
    type: String,
    required: true,
    validator: function (value) {
      return [
        'syncing',
        'synced',
        'version-conflict',
        'error'
      ].indexOf(value) !== -1
    }
  }
}
```
{% raw %}</div>{% endraw %}



### Des clés pour `v-for` <sup data-p="a">essentiel</sup>

**Toujours utiliser `key` avec `v-for`.**

`key` avec `v-for` est _toujours_ requis sur les composants afin de maintenir l'état des composant interne aligné au sous arbres. Même pour les éléments, c'est une bonne pratique pour garder un comportement prédictible pour de la [consistance d'objet](https://bost.ocks.org/mike/constancy/) dans les animations.

{% raw %}
<details>
<summary>
  <h4>Explication détaillée</h4>
</summary>
{% endraw %}

Imaginons que nous ayouns une liste de tâches :

``` js
data: function () {
  return {
    todos: [
      {
        id: 1,
        text: 'Apprendre à utiliser `v-for`'
      },
      {
        id: 2,
        text: 'Apperndre à utiliser `key`'
      }
    ]
  }
}
```

Puis nous les trions par ordre alphabétique. Quand le DOM est mis à jour, Vue va optimiser le rendu en exécutant les mutations dans le DOM les moins couteuse possible. Cela signifie de supprimer le premier élement de la liste, puis de l'ajouter de nouveau à la fin de la liste.

Le problème c'est qu'il y a des cas où il est important de ne pas supprimer les éléments et de les laisser dans le DOM. Par exemple, vous pourriez utiliser `<transition-group>` animer un tri de liste, ou garder le focus sur un élément rendu qui est un `<input>`. Dans ces cas, ajouter une clé unique pour chaque élément (par ex. `:key="todo.id"`) va dire à Vue comment être plus prédictif.

De notre expérience, il est mieux de _toujours_ ajouter une clé unique. De cette manière vous et votre équipe n'aurez jamais à vous soucier des effets de bord. Ensuite, dans les rares scénarios critiques de performance où la constance des objets n'est pas nécessaire, vous pourrez faire une exception en connaissance de cause.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` html
<ul>
  <li v-for="todo in todos">
    {{ todo.text }}
  </li>
</ul>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` html
<ul>
  <li
    v-for="todo in todos"
    :key="todo.id"
  >
    {{ todo.text }}
  </li>
</ul>
```
{% raw %}</div>{% endraw %}



### Style des composants à portée limitée <sup data-p="a">essentiel</sup>

**Pour les applications, le style du niveau `App` au sommet et des composants de mises en page doivent être globaux, mais tous les autres styles des composants devraient être à portée limitée au composant.**

Ceci n'est pertinent que dans le cas de l'utilisation de [fichiers monofichiers](../guide/single-file-components.html). Cela _ne_ nécessite _pas_ l'ajout de [l'attribut `scoped`](https://vue-loader.vuejs.org/en/features/scoped-css.html). La portée limitée peut être faites avec les [modules CSS](https://vue-loader.vuejs.org/en/features/css-modules.html), une stratégie basée sur les classes comme [BEM](http://getbem.com/) ou d'autres bibliothèques ou conventions du même genre.

**Les composant de bibliothèques, cependant, devrait utiliser une stratégie basé sur les classes plutôt que d'utiliser d'attribut  `scoped`.**

Cela permet de surcharger les styles internes facilement, avec des noms de classes lisible par les humains avec un niveau de spécificité peu élevé qui entre en conflit vraiment très rarement.

{% raw %}
<details>
<summary>
  <h4>Explication détaillée</h4>
</summary>
{% endraw %}

Si vous développer un grand projet, et travaillez avec d'autres développeurs, ou parfois que vous incluez du HTML / CSS tiers (par ex. de Auth0), une portée limitée consistante va assurer que vos styles soient uniquement appliqué aux composants que vous souhaitiez.

Au dela de l'attribut `scoped`, utiliser des noms de classe uniques vous assure que les CSS des bibliothèques tierces ne soient pas appliqués à votre propre HTML. Par exemple, beaucoup de projet utilise les classes de nom `button`, `btn` ou `icon` donc même si vous n'utilisez pas de stratégie comme BEM, ajouter un préfix dédié à l'application ou au composant (par ex. `ButtonClose-icon`) peut vous approter une certaine protection.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` html
<template>
  <button class="btn btn-close">X</button>
</template>

<style>
.btn-close {
  background-color: red;
}
</style>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` html
<template>
  <button class="button button-close">X</button>
</template>

<!-- Utilisez l'attribut `scoped` -->
<style scoped>
.button {
  border: none;
  border-radius: 2px;
}

.button-close {
  background-color: red;
}
</style>
```

``` html
<template>
  <button :class="[$style.button, $style.buttonClose]">X</button>
</template>

<!-- Utilisez les modules CSS -->
<style module>
.button {
  border: none;
  border-radius: 2px;
}

.buttonClose {
  background-color: red;
}
</style>
```

``` html
<template>
  <button class="c-Button c-Button--close">X</button>
</template>

<!-- Utilisez des conventions BEM -->
<style>
.c-Button {
  border: none;
  border-radius: 2px;
}

.c-Button--close {
  background-color: red;
}
</style>
```
{% raw %}</div>{% endraw %}



### Noms de propriété privés names <sup data-p="a">essentiel</sup>

**Toujours utilisez le préfixe `$_` pour les propriétés privés personnalisées dans un plugin, mixin, etc. Cela permet d'éviter les conflits avec le code d'autres développeurs. Il est également possible d'iclure un nom de portée (comme par exemple : `$_yourPluginName_`).**

{% raw %}
<details>
<summary>
  <h4>Explication détaillée</h4>
</summary>
{% endraw %}

Vue utilise le préfixe `_` pour définir ses propres propriétés privés, aussi utuliser le même préfixe comme (par ex. `_update`) risque de réécrire les propriétés d'instance. Même si vous vérifiez que Vue n'utilise pas actuellement un nom de propriété particulier, il n'y a aucune garantie que le conflit n'apparaisse pas dans des versions futures.

En ce qui concerne le préfixe `$`, il suppose d'être, dans l'écosystème Vue, une propriété d'instance spéciale exposée à l'utilisateur, aussi l'utiliser pour les propriétés _privés_ ne serait pas un bon choix non plus.

À la place, nous recommandons de combiner les deux préfixes en un `$_`, comme une convention pour définir des propriétés personnelles privées et garantir qu'il n'y ai aucun conflit avec Vue.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` js
var myGreatMixin = {
  // ...
  methods: {
    update: function () {
      // ...
    }
  }
}
```

``` js
var myGreatMixin = {
  // ...
  methods: {
    _update: function () {
      // ...
    }
  }
}
```

``` js
var myGreatMixin = {
  // ...
  methods: {
    $update: function () {
      // ...
    }
  }
}
```

``` js
var myGreatMixin = {
  // ...
  methods: {
    $_update: function () {
      // ...
    }
  }
}
```

{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` js
var myGreatMixin = {
  // ...
  methods: {
    $_myGreatMixin_update: function () {
      // ...
    }
  }
}
```
{% raw %}</div>{% endraw %}



## Règles de priorité B : fortement recommandée (améliorer la lisibilité)



### Fichier composants <sup data-p="b">fortement recommandé</sup>

**Chaque fois qu'un système de build est disponible pour concaténer les fichiers, chaque composant devrait être dans son propre fichier.**

Ceci aide à trouver plus rapidement un composant quand vous avez besoin de l'éditer ou de vérifier comment il fonctionne.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` js
Vue.component('TodoList', {
  // ...
})

Vue.component('TodoItem', {
  // ...
})
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

```
components/
|- TodoList.js
|- TodoItem.js
```

```
components/
|- TodoList.vue
|- TodoItem.vue
```
{% raw %}</div>{% endraw %}



### Casse des noms de fichier des composants monofichiers <sup data-p="b">fortement recommandé</sup>

**Les noms de fichier des [composants monofichiers](../guide/single-file-components.html) devrait toujours être écrit en PascalCase ou en kebal-case.**

La PascalCase fonctionne mieux avec l'autocmopletion dans des éditeurs de code. Comme ils sont consistant avec la manière dont nous référençons les composants avec du JS(X) et des templates, utilisisez le si possible. Cependant, les casses de noms mixtes (majuscule et minuscule) peuvent parfois créer des problèmes sur des systèmes de fichier non sensibles à la casse. C'est pourquoi la kebab-case est aussi parfaitement acceptable.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

```
components/
|- mycomponent.vue
```

```
components/
|- myComponent.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

```
components/
|- MyComponent.vue
```

```
components/
|- my-component.vue
```
{% raw %}</div>{% endraw %}



### Base de nom de composant <sup data-p="b">fortement recommendé</sup>

**Les composants de base (c.-à-d. les composants de présentation, de support ou purement fonctionnels) applique un style et des conventions spécifiques à l'application et devraient tous commencer avec un même prefix, comme par exemple `Base`, `App`, ou `V`.**

{% raw %}
<details>
<summary>
  <h4>Explication détaillée</h4>
</summary>
{% endraw %}

Ces composants sont les fondations d'un système de style et de comportement consistant pour votre application. Ils doivent **seulement** contenir :

- Des éléments HTML,
- d'autres composants préfixés par une base de nom et
- Des composants d'interface utilisateur tiers

Mais ils ne doivent **jamais** contenir un état global (par ex. depuis un store Vuex).

Leurs noms inclut souvent le nom d'un des élément qu'il englobe (par ex. `BaseButton`, `BaseTable`), sauf quand aucun élément n'existe pour ce qu'ils résolvent (par ex. `BaseIcon`). Si vous construisez des composants similaires pour un contexte plus spécifique, ils devront toujours utiliser le noms de ces composants (par ex. `BaseButton` pourrait être utilisé dans `ButtonSubmit`).

Plusieurs avantages à ces conventions :

- Quand ils sont listés alphabétiquement dans des éditeurs, vos composants de base sont tous listés ensembles, les rendant plus simple à identifer.

- Comme un nom de composant devrait toujours être un nom multiple, cette convention vous aide à ne pas choisir un prefix arbitraire comme nom de composant encapsulant (par ex. `MyButton`, `VueButton`).

- Et parceque ces composants sont très fréquemment utilisés, vous pourriew vouloir simplement tous les importers pour y avoir accès partout. Un prefix rend ça possible avec webpack :

  ``` js
  var requireComponent = require.context("./src", true, /^Base[A-Z]/)
  requireComponent.keys().forEach(function (fileName) {
    var baseComponentConfig = requireComponent(fileName)
    baseComponentConfig = baseComponentConfig.default || baseComponentConfig
    var baseComponentName = baseComponentConfig.name || (
      fileName
        .replace(/^.+\//, '')
        .replace(/\.\w+$/, '')
    )
    Vue.component(baseComponentName, baseComponentConfig)
  })
  ```

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

```
components/
|- MyButton.vue
|- VueTable.vue
|- Icon.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

```
components/
|- BaseButton.vue
|- BaseTable.vue
|- BaseIcon.vue
```

```
components/
|- AppButton.vue
|- AppTable.vue
|- AppIcon.vue
```

```
components/
|- VButton.vue
|- VTable.vue
|- VIcon.vue
```
{% raw %}</div>{% endraw %}



### Nom des composants à une seule instance <sup data-p="b">fortement recommandé</sup>

**Les composants qui devraient uniquement avoir une seule instance active devrait commencer par le préfix `The` pour démontrer qu'il ne peut y en avoir qu'un.**

Cela ne signifie pas que le composant doit être utilisé seulement sur une page, mais qu'il ne peut y en avoir qu'_un par page_. Ces composants n'acceptent jamais de props car ils sont spécifiques à votre application et n'ont pas leur propre contexte dans l'application. Si vous resentez le besoin d'ajouter des props, c'est une bonne indication, cela reste une bonne indication pour signifier que c'est un composant réutilisable _qui actuellement_ n'est utilisé qu'une fois par page.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

```
components/
|- Heading.vue
|- MySidebar.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

```
components/
|- TheHeading.vue
|- TheSidebar.vue
```
{% raw %}</div>{% endraw %}



### Coupler les noms de composant reliés <sup data-p="b">fortement recommendé</sup>

**Les composants qui sont fortement liés à leur parent devrait inclure le nom du parent en tant que prefix.**

Si un composant n'a de sens que dans le contexte d'un composant parent, cette relation devrait être évidente dans son nom. Puisque les éditeurs organises les fichiers par ordre alphabetique, cela permet de garder ses fichiers proches les un des autres.

{% raw %}
<details>
<summary>
  <h4>Explication détaillée</h4>
</summary>
{% endraw %}

Vous pourriez être tenter de résoudre ce problème en rangeant les composants dans des sous dossier nommé d'après leur parent. Par exemple :

```
components/
|- TodoList/
   |- Item/
      |- index.vue
      |- Button.vue
   |- index.vue
```

ou :

```
components/
|- TodoList/
   |- Item/
      |- Button.vue
   |- Item.vue
|- TodoList.vue
```

Cela n'est pas recommandé car :

- Plusieurs fichier pourrait avoir des noms similaires, rendant la navigation rapide entre les fichiers de l'éditeur de code plus difficile.
- Plusieurs sous dossiers imbriqués augmente le temps de navigation à pour trouver un composant dans la barre de navigation.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

```
components/
|- TodoList.vue
|- TodoItem.vue
|- TodoButton.vue
```

```
components/
|- SearchSidebar.vue
|- NavigationForSearchSidebar.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

```
components/
|- TodoList.vue
|- TodoListItem.vue
|- TodoListItemButton.vue
```

```
components/
|- SearchSidebar.vue
|- SearchSidebarNavigation.vue
```
{% raw %}</div>{% endraw %}



### Ordre des mots dans un nom de composant <sup data-p="b">fortement recommandé</sup>

**Les noms de composant devraient commencer avec le mot le plus important en premier et se décliner avec le reste des mots de manière descriptive.**

{% raw %}
<details>
<summary>
  <h4>Explication détaillée</h4>
</summary>
{% endraw %}

Vous pourriez vous demander :

> « Pourquoi ne pas utiliser l'ordre des mots dans un langage naturel ? »

En anglais natif, les adjectifs et les autres descripteurs apparraissent généralement avant les noms à l'exception des mots de connexion. Par exemple :

- Coffee _with_ milk
- Soup _of the_ day
- Visitor _to the_ museum


Vous pouvez inclure ces connecteurs dans le nom de vos composants si vous le souhaitez, mais l'ordre reste important.

Notez également que **ce qui va être considéré comme le mot le plus important est relatif à votre application**. Par exemple, imaginez une application avec un formulaire de recherche. Elle pourrait inclure les composants comme suit :

```
components/
|- ClearSearchButton.vue
|- ExcludeFromSearchInput.vue
|- LaunchOnStartupCheckbox.vue
|- RunSearchButton.vue
|- SearchInput.vue
|- TermsCheckbox.vue
```

Comme vous pouvez le remarquer, il est difficile de voir quels composants sont spécifique à la recherche. Maintenant, renommons les composants en suivant notre règle :

```
components/
|- SearchButtonClear.vue
|- SearchButtonRun.vue
|- SearchInputExcludeGlob.vue
|- SearchInputQuery.vue
|- SettingsCheckboxLaunchOnStartup.vue
|- SettingsCheckboxTerms.vue
```

Puisque les éditeurs organises généralement les fichiers par ordre alphabétique, toutes les relations importantes entre les composants sont maintenant évidante.

Vous pourriez être temté de résoudre le problème différemment, en imbriquant tous les composants de recherche sous le dossier « search » et tous les composants de paramétrage sous le dossier « settings ». Nous recommandons cette approche seulement pour les applications vraiment très large (par ex. avec plus de 100 composants) pour les raisons suivantes :

- Cela prend généralement plus de temps de navigeur à travers des sous répertoires imbriqués que de scrolle à travers un seul répertoire de composants.
- Les conflits de nom (par ex. de multiple composant `ButtonDelete.vue`) rendent la navigation plus difficile pour un composant spécifique dans un éditeur de texte.
- La refactorisation devient plus difficile car le remplacement par recherche n'est pas la seule action requise pour mettre à jour les références vers des composants qui aurait bougés.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

```
components/
|- ClearSearchButton.vue
|- ExcludeFromSearchInput.vue
|- LaunchOnStartupCheckbox.vue
|- RunSearchButton.vue
|- SearchInput.vue
|- TermsCheckbox.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

```
components/
|- SearchButtonClear.vue
|- SearchButtonRun.vue
|- SearchInputQuery.vue
|- SearchInputExcludeGlob.vue
|- SettingsCheckboxTerms.vue
|- SettingsCheckboxLaunchOnStartup.vue
```
{% raw %}</div>{% endraw %}



### Les composants auto-fermant <sup data-p="b">fortement recommendé</sup>

**Les composants sans contenu devrait être auto-fermant dans les [composants monofichiers](../guide/single-file-components.html), les templates de chaine de caractère, et le [JSX](../guide/render-function.html#JSX) : mais jamais dans les templates du DOM.**

Les composants qui sont auto-fermant n'indique pas seulement qu'ils n'ont pas de contenu, mais qu'ils sont **fait** pour ne pas en avoir. C'est la différence entre une page blanche dans un livre et une qui indiquerait « Cette page est intentionnellement vide ». Votre code est aussi plus claire sans balises fermantes inutiles.

Malheureusement, le HTML ne permet pas au éléments personnalisés d'être auto-fermant. Seuls les [éléments vides officiels](https://www.w3.org/TR/html/syntax.html#void-elements) peuvent l'être. C'est pourquoi cette stratégie n'est seulement possible quand les templates Vue sont compilés avant qu'ils soient injecté dans le DOM. Ensuite du code HTML valide au spécification est fournit au DOM.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` html
<!-- Dans les composants monofichiers, les templates avec chaine de caractères et en JSX -->
<MyComponent></MyComponent>
```

``` html
<!-- Dans les templates du DOM -->
<my-component/>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` html
<!-- Dans les composants monofichiers, les templates avec chaine de caractères et en JSX -->
<MyComponent/>
```

``` html
<!-- Dans les templates du DOM -->
<my-component></my-component>
```
{% raw %}</div>{% endraw %}



### Casse des noms de composant dans les templates <sup data-p="b">fortement recommandé</sup>

**Dans la plupard des projets, les noms de composant devrait toujours être écrit en PascalCase dans les [composants monofichiers](../guide/single-file-components.html) mais en kebab-case pour les templates dans le DOM.**

PascalCase a plusieurs avantage comparé à la kebab-case :

- Les éditeurs peuvent autocomplété un nom de composant dans les templates car la PascalCase est également utilisé en JavaScript.
- `<MyComponent>` est visuellement plus distinct qu'un élément HTML d'un seul mot comme `<my-component>` car il y a deux caracères de différenciation (les deux capitales) plutôt que juste un (l'hyphène).
- Si vous utilisez des composants personnalisés qui ne sont pas des éléments Vue dans vos templates, comme les Web Composants, la PascalCase vous assure que les composants Vue reste bien différentiable.

Malheureusement, du fait de l'insensibilité à la casse du HTML, les templates du DOM doivent resté en kebab-case.

Notez également que si vous avez déjà largement utilisé la kebab-case de manière consistante avec vos conventions HTML à travers tous vos projets, cela peut être plus important que les avantages listés plus haut. Dans de tel cas, **utiliser la kebab-case partout est également acceptable.**

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` html
<!-- Dans les composants monofichiers, les templates avec chaine de caractères -->
<mycomponent/>
```

``` html
<!-- Dans les composants monofichiers, les templates avec chaine de caractères -->
<myComponent/>
```

``` html
<!-- Dans les templates du DOM -->
<MyComponent></MyComponent>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` html
<!-- Dans les composants monofichiers, les templates avec chaine de caractères -->
<MyComponent/>
```

``` html
<!-- Dans les templates du DOM -->
<my-component></my-component>
```

OU

``` html
<!-- Partout -->
<my-component></my-component>
```
{% raw %}</div>{% endraw %}



### Casse des noms de composant en JS/JSX <sup data-p="b">fortement recommandé</sup>

**Les noms de composant en JS/[JSX](../guide/render-function.html#JSX) devrait toujours être en PascalCase, cependant il peuvent être en kebab-case à l'intérieur des chaine de caractères pour de simple application qui utilise seulement des composants globaux avec `Vue.component`.**

{% raw %}
<details>
<summary>
  <h4>Explication détaillée</h4>
</summary>
{% endraw %}

En JavaScript, la PascalCase est la convention pour les classes et les prototypes, c.-à-d. tout ce qui peut avoir des instances distinctes. Les composants Vue ont également des instances, donc cela est logique d'utiliser également la PascalCase. De plus, utiliser la PascalCase dans le JSX (et les templates) permet une lecture de code plus simple pour la distinction avec des éléments HTML.

Cependant, pour les applications que n'utilise **que** des composants globaux via `Vue.component` nous recommandons la kebab-case à la place. Les raisons sont :

- Il est rare que les composants globaux soit référencé en JavaScript, donc suivre les conventions du JavaScript à moins de sence.
- Ces applications inclus beaucoup de template depuis le DOM, ou là [kebab-case **doit** être utilisée](#Casse-des-noms-de composant-dans-les-templates-fortement-recommande).

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` js
Vue.component('myComponent', {
  // ...
})
```

``` js
import myComponent from './MyComponent.vue'
```

``` js
export default {
  name: 'myComponent',
  // ...
}
```

``` js
export default {
  name: 'my-component',
  // ...
}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` js
Vue.component('MyComponent', {
  // ...
})
```

``` js
Vue.component('my-component', {
  // ...
})
```

``` js
import MyComponent from './MyComponent.vue'
```

``` js
export default {
  name: 'MyComponent',
  // ...
}
```
{% raw %}</div>{% endraw %}



### Nom de composant à nom complet <sup data-p="b">fortement recommandé</sup>

**Les noms de composant devrait préférer des noms complets à des abréviations.**

L'autocomplétion dans les éditeurs rend le cout d'un long nom de composant insignifiant alors que la clareté qu'il amène est sans valeur. Les abréviations non communes, en particulier, sont à éviter.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

```
components/
|- SdSettings.vue
|- UProfOpts.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

```
components/
|- StudentDashboardSettings.vue
|- UserProfileOptions.vue
```
{% raw %}</div>{% endraw %}



### Casse des noms de prop <sup data-p="b">fortement recommandé</sup>

**Les noms de prop devrait toujours utilisé la camelCase lors de leur déclaration, mais la kebab-case dans les templates et les [JSX](../guide/render-function.html#JSX).**

Nous suivons simplement les conventions de chaque language. Dans le JavaScript, la camelCase est plus naturelle. Dans le HTML, c'est la kebab-case.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` js
props: {
  'greeting-text': String
}
```

``` html
<WelcomeMessage greetingText="hi"/>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` js
props: {
  greetingText: String
}
```

``` html
<WelcomeMessage greeting-text="hi"/>
```
{% raw %}</div>{% endraw %}



### Éléments avec attributs multiples <sup data-p="b">fortement recommendé</sup>

**Des éléments avec de multiples attributs devraient occupés plusieurs lignes, avec un attribut par ligne.**

En JavaScript, scinder les objets avec de multiples propriétés sur plusieurs ligne est largement considéré comme une bonne convention, car cela rend le code plus facile à lire. Nos templates et [JSX](../guide/render-function.html#JSX) suivent les mêmes règles.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` html
<img src="https://vuejs.org/images/logo.png" alt="Vue Logo">
```

``` html
<MyComponent foo="a" bar="b" baz="c"/>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` html
<img
  src="https://vuejs.org/images/logo.png"
  alt="Vue Logo"
>
```

``` html
<MyComponent
  foo="a"
  bar="b"
  baz="c"
/>
```
{% raw %}</div>{% endraw %}



### Expressions simples dans les templates <sup data-p="b">fortement recommandé</sup>

**Les templates de composant devrait seulement inclure de simple expressions, avec les expressions les plus complexe refactorisés dans des propriétés calculées ou des méthodes.**

Les expressions complexes dans vos templates les rendent moins déclaratif. Nous devrions voir _ce qui_ devrait apparaitre, pas _comment_ ça devrait apparaitre quand la valeur est calculée. Les propriétés calculées et les méthodes permettent également de rendre le code réutilisable.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` html
{{
  fullName.split(' ').map(function (word) {
    return word[0].toUpperCase() + word.slice(1)
  }).join(' ')
}}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` html
<!-- In a template -->
{{ normalizedFullName }}
```

``` js
// L'expression complexe a été bougée dans une propriété calculée
computed: {
  normalizedFullName: function () {
    return this.fullName.split(' ').map(function (word) {
      return word[0].toUpperCase() + word.slice(1)
    }).join(' ')
  }
}
```
{% raw %}</div>{% endraw %}



### Propriétés calculées simples <sup data-p="b">fortement recommendé</sup>

**Les propriétés calculées complexes devraient être scindée en plusieurs propriétés plus simple autant que possible.**

{% raw %}
<details>
<summary>
  <h4>Explication détaillée</h4>
</summary>
{% endraw %}

Les propriétés calculées simple et bien nommées sont :

- __Facile à tester__

  Quand chaque propriété calculée contient seulement une expression simple, avec peu de dépendances, il est plus facile d'écrire des test pour confirmer qu'elle fonctionne correctement.

- __Facile à lire__

  Simplifier les propriétés calculées vous force à donner à chaque valeur un nom descriptif, même si elle ne sera pas réutilisé. Cela permet aux autres développeurs (et vous dans le futur) de plus facilement vous concentrer sur le code qui vous intéresse et de ne pas vous soucier du reste.

- __Plus adaptable aux changements__

  N'importe quelle valeur qui peut être nommée pourra être utile pour la vue. Par exemple, nous décidons d'afficher un message expliquant à l'utilisateur combien d'argent il a gagnée. Nous pourrions décider de calculer les taxes associées, mais peut-être de les afficher indépendemment au lieu qu'elle soit une partie du prix final.

  Des propriétés calculées courte et spécifque permettent plus de liberté sur la manière dont les informations seront utilisés ce qui nécessite moins de refactorisation en cas de changements.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` js
computed: {
  price: function () {
    var basePrice = this.manufactureCost / (1 - this.profitMargin)
    return (
      basePrice -
      basePrice * (this.discountPercent || 0)
    )
  }
}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` js
computed: {
  basePrice: function () {
    return this.manufactureCost / (1 - this.profitMargin)
  },
  discount: function () {
    return this.basePrice * (this.discountPercent || 0)
  },
  finalPrice: function () {
    return this.basePrice - this.discount
  }
}
```
{% raw %}</div>{% endraw %}



### Valeur d'attribut entre guillemet <sup data-p="b">fortement recommandé</sup>

**Les attributs HTML non vides devraient toujours être entre guillemet (ou aposthophe, celui qui n'est pas utiliser dans le JS).**

Alors que les valeurs d'attributs sans espace ne sont pas requise en HTML, cela est mêne à ne _jamais_ mettre d'espace, laissant place à des valeurs moins lisibles.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` html
<input type=text>
```

``` html
<AppSidebar :style={width:sidebarWidth+'px'}>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` html
<input type="text">
```

``` html
<AppSidebar :style="{ width: sidebarWidth + 'px' }">
```
{% raw %}</div>{% endraw %}



### Abréviations de directive <sup data-p="b">fortement recommendé</sup>

**Les abréviations de directive (`:` pour `v-bind:` et `@` pour `v-on:`) devrait toujours être utilisé ou jamais.**

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` html
<input
  v-bind:value="newTodoText"
  :placeholder="newTodoInstructions"
>
```

``` html
<input
  v-on:input="onInput"
  @focus="onFocus"
>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` html
<input
  :value="newTodoText"
  :placeholder="newTodoInstructions"
>
```

``` html
<input
  v-bind:value="newTodoText"
  v-bind:placeholder="newTodoInstructions"
>
```

``` html
<input
  @input="onInput"
  @focus="onFocus"
>
```

``` html
<input
  v-on:input="onInput"
  v-on:focus="onFocus"
>
```
{% raw %}</div>{% endraw %}



## Règles de priorité C : recommandée (minimiser les choix arbitraires et la surcharge cognitive)



### Ordre des options de composants / instances <sup data-p="c">recommandée</sup>

**Les options des instances / composants devraient être ordonnée avec consistance.**

Voici l'ordre par défaut que nous recommandons pour les options de composant. Elles sont séparés en catégories ainsi vous saurez où ajouter vos nouvelles propriétés des plugins.

1. **Effets de bord** (créer des effets en dehors du composant)
  - `el`

2. **Attention globale** (nécessite des connaissances au-delà du composant)
  - `name`
  - `parent`

3. **Type de composant** (change le type du composant)
  - `functional`

4. **Modificateurs de template** (change la manière dont les templates sont compilés)
  - `delimiters`
  - `comments`

5. **Dépendances des templates** (les ressources utilisées dans le template)
  - `components`
  - `directives`
  - `filters`

6. **Composition** (fusionner des propriétés en dans les options)
  - `extends`
  - `mixins`

7. **Interface** (l'interface du composant)
  - `inheritAttrs`
  - `model`
  - `props` / `propsData`

8. **État local** (propriétés réactives locales)
  - `data`
  - `computed`

9. **Évènements** (fonctions de rappel déclenchées par des évènements réactifs)
  - `watch`
  - Évènements du cycle de vie (dans leur ordre d'appel)

10. **Propriétés non réactives** (Propriété d'instance indépendantes du système de réactivité)
  - `methods`

11. **Rendu** (description déclarative de la sortie du composant)
  - `template` / `render`
  - `renderError`



### Ordre des attributs des élements <sup data-p="c">recommandée</sup>

**Les attributs d'éléments (incluant les composants) devrait êrte ordonné avec consistance.**

Voici l'ordre par défaut que nous recommandons pour les options de composant. Elles sons séparés en catégories, ainsi vous saurez où ajouter vos attributs et directives personnalisées.

1. **Définition** (fournit les options du composant)
  - `is`

2. **Rendu de liste** (crée des variations multiples du même élément)
  - `v-for`

3. **Conditions** (comment l'élément est rendu / affiché)
  - `v-if`
  - `v-else-if`
  - `v-else`
  - `v-show`
  - `v-cloak`

4. **Modificateurs de rendu** (change la manière de faire le rendu de l'élément)
  - `v-pre`
  - `v-once`

5. **Attention globale** (nécessite des connaissances au-delà du composant)
  - `id`

6. **Attributs uniques** (attributs nécessitant une valeur unique)
  - `ref`
  - `key`
  - `slot`

7. **Liaison bidirectionnelle** (combiner les liaisons descendantes et les évènements montants)
  - `v-model`

8. **Autres attributs** (tous les attributs qui sont liés ou non liés)

9. **Évènements** (Gestionnaire d'évènement de composant)
  - `v-on`

10. **Contenu** (écrase le contenu d'un élement)
  - `v-html`
  - `v-text`



### Ligne vide dans les options d'instance / de composant <sup data-p="c">recommandée</sup>

**Vous pourriez ajouter une ligne vide entre de multiple propriétés, en particulier si la longeur de votre options dépasse la taille de l'écran.**

Quand un composants commence à devenir difficile à lire, ajouter des espaces entres des propriétés multilignes peut les rendre de nouveau simple à lire. Dans divers éditeurs, comme Vim, formater les options ainsi peut rendre la navigation plus simple avec le clavier.
{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` js
props: {
  value: {
    type: String,
    required: true
  },

  focused: {
    type: Boolean,
    default: false
  },

  label: String,
  icon: String
},

computed: {
  formattedValue: function () {
    // ...
  },

  inputClasses: function () {
    // ...
  }
}
```

``` js
// Sans espace est aussi bien, du moment que le composant
// reste simple à lire et à parcourrir.
props: {
  value: {
    type: String,
    required: true
  },
  focused: {
    type: Boolean,
    default: false
  },
  label: String,
  icon: String
},
computed: {
  formattedValue: function () {
    // ...
  },
  inputClasses: function () {
    // ...
  }
}
```
{% raw %}</div>{% endraw %}



### Ordre des balises principales des composants monofichiers <sup data-p="c">recommended</sup>

**[Les composants monofichiers](../guide/single-file-components.html) devraient toujours être remplis avec l'ordre des balises suivant `template`, `script`, et `style`, avec `<style>` en dernier, car au moins l'un des deux autres est toujours nécessaire.**

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` html
<style>/* ... */</style>
<template>...</template>
<script>/* ... */</script>
```

``` html
<!-- ComponentA.vue -->
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` html
<!-- ComponentA.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```

``` html
<!-- ComponentA.vue -->
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>
```
{% raw %}</div>{% endraw %}



## Règles de priorité D : faire attention (potentiellement dangereux)



### `v-if` / `v-if-else` / `v-else` sans `key` <sup data-p="d">faire attention</sup>

**Il est généralement mieu d'utiliser `key` avec `v-if` + `v-else`, si les éléments sont de même type (c.-à-d. avec deux éléments `<div>`).**

Par défaut, Vue met à jour le DOM aussi efficacement que possible. Cela signifie que quand il alterne entre deux éléments du même type, il va simplement changer l'élément existant, sans en ajouter ou en retirer à la place. Cela peut avoir des [effets non souhaités](https://jsfiddle.net/chrisvfritz/bh8fLeds/) si ces éléments ne doivent pas être considérés comme les mêmes.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` html
<div v-if="error">
  Error: {{ error }}
</div>
<div v-else>
  {{ results }}
</div>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` html
<div v-if="error" key="search-status">
  Error: {{ error }}
</div>
<div v-else key="search-results">
  {{ results }}
</div>
```

``` html
<p v-if="error">
  Error: {{ error }}
</p>
<div v-else>
  {{ results }}
</div>
```
{% raw %}</div>{% endraw %}



### Éviter les sélecteurs par nom de balise avec `scoped` <sup data-p="d">faire attention</sup>

**Les sélécteurs par nom de balise devraient être évités avec `scoped`.**

Préférez les sélecteurs de classes plutôt que les sélecteurs de nom dans des styles `scoped` car utiliser un large nombre de sélecteur par nom de balise est lent.

{% raw %}
<details>
<summary>
  <h4>Explication détaillée</h4>
</summary>
{% endraw %}

Dans les styles avec portée, Vue ajoute un attribut unique à l'élément du composant, comme `data-v-f3f3eg9`. Quand les sélecteurs sont modifiés ils ne concordent que avec les éléments avec l'attribut en question (par ex. `button[data-v-f3f3eg9]`).

Le problème est qu'utiliser un grand nombre de [sélecteur par attribut d'élément]((http://stevesouders.com/efws/css-selectors/csscreate.php?n=1000&sel=a%5Bhref%5D&body=background%3A+%23CFD&ne=1000) (par ex. `button[data-v-f3f3eg9]`) va être considérablement plus lent que [par attribut classe](http://stevesouders.com/efws/css-selectors/csscreate.php?n=1000&sel=.class%5Bhref%5D&body=background%3A+%23CFD&ne=1000) (par ex. `.btn-close[data-v-f3f3eg9]`) et donc les sélecteurs par classes devraient être préféré autant que possible.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` html
<template>
  <button>X</button>
</template>

<style scoped>
button {
  background-color: red;
}
</style>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` html
<template>
  <button class="btn btn-close">X</button>
</template>

<style scoped>
.btn-close {
  background-color: red;
}
</style>
```
{% raw %}</div>{% endraw %}



### Communication parent-enfant implicite <sup data-p="d">faire attention</sup>

**Les évènements et props devraient être préférés pour la communication entre composant parent-enfant, au lieu de l'utilisation de `this.$parent` ou les props mutés.**

Une application Vue idéale est que les props descendent et que les évènements remontent. Rester dans cette convention rend vos composants plus simple à comprendre. Cependant, il y a quelques cas où la mutation des props ou `this.$parent` peut simplifier l'utilisation de deux composants qui sont fortement couplés.

Le problème est qu'il y a beaucoup de _cas simples_ où leurs utilisations offre de la facilité. Attention : ne vous laissez pas séduire par la simplicité (rester capable de comprendre le flux de votre état) par l'utilisation de code plus simple (plus court).

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` js
Vue.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  template: '<input v-model="todo.text">'
})
```

``` js
Vue.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  methods: {
    removeTodo () {
      var vm = this
      vm.$parent.todos = vm.$parent.todos.filter(function (todo) {
        return todo.id !== vm.todo.id
      })
    }
  },
  template: `
    <span>
      {{ todo.text }}
      <button @click="removeTodo">
        X
      </button>
    </span>
  `
})
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` js
Vue.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  template: `
    <input
      :value="todo.text"
      @input="$emit('input', $event.target.value)"
    >
  `
})
```

``` js
Vue.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  template: `
    <span>
      {{ todo.text }}
      <button @click="$emit('delete')">
        X
      </button>
    </span>
  `
})
```
{% raw %}</div>{% endraw %}



### Non gestion de l'état par flux <sup data-p="d">faire attention</sup>

**[Vuex](https://github.com/vuejs/vuex) devrait être préféré pour une gestion globale d'étatà la place de  `this.$root` ou d'un canal global d'évènement.**

Gérer l'état avec `this.$root` et / ou utiliser un [canal d'évènement global](https://fr.vuejs.org/v2/guide/migration.html#dispatch-et-broadcast-remplaces) peut faciliter la tâche dans des cas vraiment simple. Cependant cela n'est pas approprié pour la plupard des applications. Vuex n'offre pas seulement une place centrale pour gérer l'état mais également des outils pour organiser, tracer et déboguer les changements d'états.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mauvais

``` js
// main.js
new Vue({
  data: {
    todos: []
  },
  created: function () {
    this.$on('remove-todo', this.removeTodo)
  },
  methods: {
    removeTodo: function (todo) {
      var todoIdToRemove = todo.id
      this.todos = this.todos.filter(function (todo) {
        return todo.id !== todoIdToRemove
      })
    }
  }
})
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bon

``` js
// store/modules/todos.js
export default {
  state: {
    list: []
  },
  mutations: {
    REMOVE_TODO (state, todoId) {
      state.list = state.list.filter(todo => todo.id !== todoId)
    }
  },
  actions: {
    removeTodo ({ commit, state }, todo) {
      commit('REMOVE_TODO', todo.id)
    }
  }
}
```

``` html
<!-- TodoItem.vue -->
<template>
  <span>
    {{ todo.text }}
    <button @click="removeTodo(todo)">
      X
    </button>
  </span>
</template>

<script>
import { mapActions } from 'vuex'

export default {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  methods: mapActions(['removeTodo'])
}
</script>
```
{% raw %}</div>{% endraw %}



{% raw %}
<script>
(function () {
  var enforcementTypes = {
    none: '<span title="Il n\'y a malheureusement aucan moyen de forcer ces règles.">auto discipline</span>',
    runtime: 'runtime error',
    linter: '<a href="https://github.com/vuejs/eslint-plugin-vue#eslint-plugin-vue" target="_blank">plugin:vue/recommended</a>'
  }
  Vue.component('sg-enforcement', {
    template: '\
      <span>\
        <strong>Forçage</strong>:\
        <span class="style-rule-tag" v-html="humanType"/>\
      </span>\
    ',
    props: {
      type: {
        type: String,
        required: true,
        validate: function (value) {
          Object.keys(enforcementTypes).indexOf(value) !== -1
        }
      }
    },
    computed: {
      humanType: function () {
        return enforcementTypes[this.type]
      }
    }
  })

  // new Vue({
  //  el: '#main'
  // })
})()
</script>
{% endraw %}