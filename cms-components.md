# Utilisation de composants

- [Introduction](#introduction)
- [Alias de composants](#aliases)
- [Utilisation de valeurs de propriétés externes](#external-property-values)
- [Passer des variables aux composants](#component-variables)
- [Personnalisation du balisage par défaut](#customizing-default-markup)
  - [Déplacement du balisage par défaut vers un partiel](#moving-default-markup)
  - [Remplacement des partiels d'un composant](#overriding-partials)
- [Le composant "View Bag"](#viewbag-component)
- [Les composants Soft](#soft-components)

Les composants sont des éléments de construction configurables qui peuvent être attachés à n'importe quelle page, partiel ou maquette.
Les composants sont une des fonctionnalités majeures d'October.
Chaque composant implémente certaines fonctionnalités qui s'ajoutent à votre site Web.
Les composants peuvent générer du balisage HTML sur une page, mais ce n'est pas nécessaire,
d'autres fonctionnalités importantes des composants sont la gestion des [requête AJAX](../ajax/introduction),
la gestion des données de formulaire et la gestion du cycle d'exécution de la page qui permet d'injecter des variables dans les pages ou de mettre en œuvre la sécurité du site Web.

Cet article décrit les bases des composants mais n'explique pas l'utilisation des [composants avec AJAX](../ajax/handlers) ou le développement des composants dans le cadre de plugins.

> **Remarque** : L'utilisation de composants à l'intérieur des partiels est soumis à certaines limitations, cela est décrit plus en détail dans l'article sur les [partiels dynamiques](partials#dynamic-partials).

<a name="introduction"></a>
## Introduction

Si vous utilisez l'interface utilisateur du backend, vous pouvez ajouter des composants à vos pages,
partiels et maquettes en cliquant sur le composant dans le panneau Composants.
Si vous utilisez un éditeur de texte, vous pouvez attacher un composant à une page ou une maquette en ajoutant son nom à la section [Configuration](themes#configuration-section) du fichier.
L'exemple suivant montre comment ajouter un composant de démonstration To-do à une page :

    title = "Démonstration des composants"
    url = "/composants"

    [demoTodo]
    maxItems = 20
    ==
    ...

Cela initialise le composant avec les propriétés définies dans la section du composant.
De nombreux composants ont des propriétés, mais ce n'est pas une exigence.
Certaines propriétés sont obligatoires et certaines propriétés ont des valeurs par défaut.
Si vous n'êtes pas sûr des propriétés prises en charge par un composant,
reportez-vous dans la documentation fournie par le développeur ou utilisez l'inspecteur dans le backend d'October.
L'inspecteur s'ouvre lorsque vous cliquez sur un composant dans le panneau des composants de la page ou la maquette.

Lorsque vous utilisez un composant, cela crée automatiquement une variable de page qui correspond au nom du composant (`demoTodo` dans l'exemple précédent).
Les composants qui fournissent un balisage HTML peuvent être rendus sur une page avec la balise `{% component %}`, comme ceci :

    {% composant 'demoTodo'%}

> **Remarque** : Si deux composants portant le même nom sont attribués simultanément à une page et à une maquette, le composant de page remplace toutes les propriétés du composant de la maquette.

<a name="aliases"></a>
## Alias de composants

Si deux plugins enregistrent des composants avec le même nom,
vous pouvez attacher un composant en utilisant son nom de classe complet et en lui affectant un _alias_ :

    [October\Demo\Components\Todo demoTodoAlias]
    maxItems = 20

Le premier paramètre de la section est le nom de la classe, le second est le nom d'alias du composant qui sera utilisé lorsqu'il sera attaché à la page.
Si vous avez spécifié un alias de composant, vous devez l'utiliser partout dans le code de page lorsque vous faites référence au composant.
Notez que l'exemple suivant fait référence à l'alias du composant :

    {% component 'demoTodoAlias' %}

Les alias vous permettent également de définir plusieurs composants de la même classe sur une même page en utilisant d'abord le nom court et un second alias.
Cela vous permet d'utiliser plusieurs instances d'un même composant sur une page.

    [demoTodo todoA]
    maxItems = 10
    [demoTodo todoB]
    maxItems = 20

<a name="external-property-values"></a>
## Utilisation de valeurs de propriétés externes

Par défaut, les valeurs des propriétés sont initialisées dans la section Configuration où le composant est défini et les valeurs des propriétés sont statiques, comme ceci :

    [demoTodo]
    maxItems = 20
    ==
    ...

Cependant, il existe un moyen d'initialiser les propriétés avec des valeurs chargées à partir de paramètres externes,
paramètres de l'URL ou paramètres des [partiels](partials) (pour les composants définis dans les partiels).
Utilisez la syntaxe `{{ paramName }}` pour les valeurs qui doivent être chargées à partir des variables des partiels :

    [demoTodo]
    maxItems = {{ maxItems }}
    ==
    ...

En supposant que dans l'exemple ci-dessus, le composant **demoTodo** est défini dans un partiel **mon-partiel-todo.htm**,
il sera initialisé avec une valeur chargée à partir de la variable maxItems du partiel :

    {% partial 'mon-partiel-todo' maxItems='10' %}

Vous pouvez utiliser la notation par points pour récupérer une valeur imbriquée à partir d'un paramètre externe :

    [demoTodo]
    maxItems = {{ data.maxItems }}
    ==
    ...

Pour charger une valeur de propriété à partir du paramètre de l'URL, utilisez la syntaxe `{{ :paramName }}`,
où le nom commence par deux points `:`, par exemple :

    [demoTodo]
    maxItems = {{ :maxItems }}
    ==
    ...

La page à laquelle appartient le composant doit définir un [paramètre d'URL](pages#url-syntax) correspondant :

    url = "/todo/:maxItems"

Dans le backend d'October, vous pouvez utiliser l'outil Inspecteur pour affecter des valeurs externes aux propriétés du composant.
Dans l'inspecteur, vous n'avez pas besoin d'utiliser les accolades pour saisir le nom du paramètre.
Chaque champ de l'inspecteur a une icône `>_` sur le côté droit, qui ouvre l'éditeur du nom d'un paramètre externe.
Entrez le nom du paramètre comme `paramName` pour les variables des partiels ou `:paramName` pour les paramètres de l'URL.

<a name="component-variables"></a>
## Passer des variables aux composants

Les composants peuvent être conçus pour utiliser des variables au moment de leur rendu, similaires aux [variables des partiels](partials#partial-variables),
ils peuvent être spécifiés après le nom du composant dans la balise `{% component %}`.
Les variables spécifiées remplaceront explicitement la valeur des [propriétés du composant](../plugin/components#component-properties),
y compris les [valeurs des propriétés externes](#external-property-values).

Dans cet exemple, la propriété **maxItems** du composant sera définie à **7** au moment du rendu du composant :

    {% component 'demoTodoAlias' maxItems='7' %}

> **Remarque** : Les composants ne prennent pas tous en charge le passage de variables lors du rendu.

<a name="customizing-default-markup"></a>
## Personnalisation du balisage par défaut

Le balisage fourni par les composants est généralement conçu comme un exemple d'utilisation du composant.
Dans certains cas, vous souhaiterez peut-être modifier l'apparence et le rendu d'un composant.
[Déplacer le balisage par défaut vers un partiel du thème](#moving-default-markup) convient pour remanier complètement un composant.
[Remplacer les partiels des composants](#overriding-partials) est utile pour le choix des parties à personnaliser

<a name="moving-default-markup"></a>
### Déplacement du balisage par défaut vers un partiel

Chaque composant peut avoir un partiel comme point d'entrée appelé **default.htm** qui est rendu lorsque la balise `{% component %}` est appelée, dans l'exemple suivant, nous supposerons que le composant est appelé **blogPost**.

    url = "blog/article"

    [blogPost]
    ==
    {% component "blogPost"%}

L'affichage sera rendu à partir du répertoire du plugin **components/blogpost/default.htm**.
Vous pouvez copier tout le balisage de ce fichier et le coller directement dans la page ou dans un nouveau partiel, appelé **blog-post.htm** par exemple.

    <h1>{{ __SELF__.post.title }}</h1>
    <p>{{ __SELF__.post.description }}</p>

À l'intérieur du balisage, vous pourrez remarquer des références à une variable appelée `__SELF__`,
cela fait référence à l'objet composant lui-même et doit être remplacé par l'alias de composant utilisé sur la page,
dans cet exemple `blogPost` :

    <h1>{{ blogPost.post.title }}</h1>
    <p>{{ blogPost.post.description }}</p>

Il s'agit de la seule modification nécessaire pour permettre au balisage de composant par défaut de fonctionner n'importe où dans le thème. Maintenant, le balisage du composant peut être personnalisé et rendu à l'aide du partiel du thème.

    {% partial 'blog-post.htm'%}

Ce processus peut être répété pour tous les autres partiels trouvés dans le répertoire des partiels du composant.

<a name="overriding-partials"></a>
### Remplacement des partiels des composants

Tous les partiels de composant peuvent être remplacés à l'aide des partiels du thème.
Si un composant appelé **channel** utilise le partiel **title.htm** :

    url = "mypage"

    [channel]
    ==
    {% component "channel" %}

Nous pouvons remplacer le partiel en créant un fichier dans notre thème appelé **partials/channel/title.htm**.

Les segments du chemin d'accès au fichier sont répartis comme suit:

| Segment       | La description                                            |
| ------------- | --------------------------------------------------------- |
| **partials**  | le répertoire des partiels du thème                       |
| **channel**   | l'alias du composant (un sous-répertoire de **partials**) |
| **title.htm** | le partiel du composant à remplacer                       |

Le nom du sous-répertoire du partiel peut être personnalisé au choix en attribuant simplement au composant un alias du même nom.
Par exemple, en attribuant au composant **channel** un alias différent **foobar**,
le répertoire de remplacement est également modifié :

    [channel foobar]
    ==
    {% component "foobar" %}

Nous pouvons maintenant remplacer le partiel **title.htm** en créant un fichier dans notre thème appelé **partials/foobar/title.htm**.

<a name="viewbag-component"></a>
## Le composant "View Bag"

Il existe un composant spécial inclus dans October appelé `viewBag` qui peut être utilisé dans n'importe quelle page ou maquette.
Il permet de définir des propriétés spontanément et d'y accéder facilement en tant que variables.
Un bon exemple d'utilisation consiste à définir un élément de menu actif dans une page :

    title = "About"
    url = "/about.html"
    layout = "default"

    [viewBag]
    activeMenu = "about"
    ==

    <p>Contenu de la page ... </p>

Toute propriété définie pour le composant est ensuite rendue disponible dans la page,
la maquette ou le partiel à l'aide de la variable `viewBag`.
Par exemple, dans cette maquette, la classe **active** est ajoutée à la "list item" `<li></li>` si la valeur `viewBag.activeMenu` est définie sur **about** :

    description = "Maquette par defaut"
    ==
    [...]

    <!-- Navigation principale -->
    <ul>
        <li class="{{ viewBag.activeMenu == 'about' ? 'active' }}">About</li>
        [...]
    </ul>

> **Remarque** : le composant viewBag est masqué dans le backend et n'est disponible que pour l'édition directe du code sur des fichiers. Il peut également être utilisé par d'autres plugins pour stocker des données.

<a name="soft-components"></a>
## Les Composants Soft

Les composants Soft sont des composants d'un thème qui continueront de fonctionner même si le composant lié n'est plus disponible.
Cela permet aux développeurs de thèmes et de sites de spécifier des composants de plugin facultatifs dans leurs thèmes qui fourniront des fonctionnalités spécifiques si le plugin et/ou le composant est présent,
tout en permettant au site de continuer à fonctionner si le composant n’existe plus.

Lorsque des composants Soft sont présents sur une page et que le composant n'est pas disponible, aucune sortie n'est générée pour le composant.

Vous pouvez définir des composants Soft en préfixant le nom du composant avec le symbole `@`.

    url = "mypage"

    [@channel]
    ==
    {% component "channel" %}

Dans cet exemple, si le composant `channel` n'est pas disponible, la balise `{% component "channel" %}` sera ignorée lors du rendu de la page.

Les composants Soft fonctionnent également avec les alias :

    url = "mypage"

    [@channel channelSection]
    ==
    {% component "channelSection" %}

Étant donné que les composants Soft ne contiennent aucune des données que le composant peut fournir normalement s'il n'est pas disponible,
vous devez vous assurer que tout balisage personnalisé gérera correctement toutes les informations de composant manquantes.
Par exemple :

    url = "mypage"

    [@channel]
    ==
    {% if channel.name %}
        <div class="channel">
            {% channel.name %}
        </div>
    {% endif %}
