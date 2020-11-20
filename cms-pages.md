# Les pages du CMS

- [Introduction](#introduction)
- [Configuration de la page](#configuration)
  - [Syntaxe de l'URL](#url-syntax)
- [Pages dynamiques](#dynamic-pages)
  - [Cycle de vie d'exécution de la page](#page-life-cycle)
  - [Renvoi d'une réponse personnalisée](#life-cycle-response)
  - [Manipulation des formulaires](#handling-forms)
- [Page 404](#404-page)
- [Page d'erreur](#error-page)
- [Variables de la page](#page-variables)
- [Injection de ressources dans la page par programmation](#injecting-assets)

<a name="introduction"></a>
## Introduction

Tous les sites web ont des pages. Dans October, les pages sont représentées par des modèles de page.
Les fichiers de modèle de page résident dans le sous-répertoire **/pages** du répertoire d'un thème.
Les noms des fichiers de page n'affectent pas le routage, mais c'est une bonne pratique de nommer vos pages selon leurs fonctions.
Les fichiers doivent avoir l'extension **htm**.
Les sections [configuration](themes#configuration-section) et balisage [Twig](themes#twig-section) sont obligatoires pour les pages,
mais la [section PHP](themes#php-section) est facultative. Ci-dessous l'exemple le plus simple d'une page d'accueil :

    url = "/"
    ==
    <h1>Bonjour tous le monde!</h1>

<a name="configuration"></a>
## Configuration de la page

La configuration de la page est définie dans la [section Configuration](themes#configuration-section) du fichier de la page.
La configuration définit les paramètres de la page, requis pour le routage et le rendu de la page et de ses [composants](components),
qui sont expliqués dans un autre article. Les paramètres de configuration suivants sont pris en charge pour les pages :

| Paramètres      | Description                                                                                                                                            |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **url**         | l'URL de la page, obligatoire. La syntaxe de l'URL est décrite ci-dessous.                                                                             |
| **title**       | le titre de la page, obligatoire.                                                                                                                      |
| **layout**      | la [maquette](layouts) de la page, facultative. Si spécifié, doit contenir le nom du fichier de la maquette, sans l'extension, par exemple: `default`. |
| **description** | la description de la page pour l'interface d'administration, facultative.                                                                              |

<a name="url-syntax"></a>
### Syntaxe de l'URL

L'URL de la page est définie avec le paramètre de la configuration `url`.
Les URL doivent commencer par le caractère barre oblique `(/)` et peuvent contenir aussi des paramètres.
Les URL sans paramètres sont fixes et strictes. Dans l'exemple suivant, l'URL de la page est `/blog`.

    url = "/blog"

Les URL avec paramètres sont plus flexibles.
Une page avec le modèle d'URL défini dans l'exemple suivant serait affichée pour toute adresse comme `/blog/post/quelque-chose`.
Les paramètres de l'URL sont aussi accessibles par les composants d'October ou à partir de la section du [code PHP](themes#php-section) de la page.

    url = "/blog/post/:post_id"

Voici comment accéder au paramètre de l'URL depuis la section PHP de la page (voir la section [pages dynamiques](#dynamic-pages) pour plus de détails) :

    url = "/blog/post/:post_id"
    ==
    function onStart()
    {
        $post_id = $this->param('post_id');
    }
    ==

Les noms des paramètres doivent être compatibles avec les noms de variables PHP. Pour rendre un paramètre facultatif,
ajoutez un point d'interrogation après son nom :

    url = "/blog/post/:post_id?"

Les paramètres au milieu de l'URL ne peuvent pas être facultatifs. Dans l'exemple suivant,
le paramètre `:post_id` est marqué comme facultatif,mais est traité comme requis.

    url = "/blog/:post_id?/comments"

Les paramètres facultatifs peuvent avoir des valeurs par défaut qui sont utilisées comme valeurs de secours au cas où la valeur réelle du paramètre n'est pas présente dans l'URL.
Les valeurs par défaut ne peuvent contenir aucun astérisque, symbole de barre verticale `(|)` ou point d'interrogation.
La valeur par défaut est spécifiée après le point d'interrogation.

Dans l'exemple suivant, le paramètre `category_id` serait à `10` pour l'URL `/blog/catégorie`.

    url = "/blog/category/:category_id?10"

Vous pouvez également utiliser des expressions régulières pour valider les paramètres.
Pour ajouter une expression de validation, ajoutez un symbole de barre verticale `(|)` après le nom du paramètre ou le point d'interrogation et spécifiez l'expression voulue.
Le symbole de barre oblique `(/)` n'est pas autorisé dans ces expressions.

Exemples :

    url = "/blog/:post_id|^[0-9]+$/comments" - cela correspondra à /blog/10/comments
    ...
    url = "/blog/:post_id|^[0-9]+$" - cela correspondra à /blog/3
    ...
    url = "/blog/:post_name?|^[a-z0-9\-]+$" - cela correspondra à /blog/mon-blog-post

Il est possible d'utiliser un **paramètre générique** spécial en plaçant un **astérisque** après le paramètre.
Contrairement aux paramètres réguliers, les paramètres génériques peuvent correspondre à un ou plusieurs segments de l'URL.
Une URL ne peut contenir qu'un seul paramètre générique et ne peut pas utiliser d'expressions régulières ou être suivie d'un paramètre facultatif.

    url = "/blog/:category*/:slug"

Les paramètres génériques eux-mêmes peuvent être rendus facultatifs tout en faisant précéder l'astérisque du signe `?`.

    url = "/blog/:slug?*"

Par exemple, une URL comme `/couleur/:couleur/marque/:marque*/modifier` correspondra à `/couleur/maron/marque/volkswagen/beetle/retro/modifier` et extraira les valeurs de paramètre suivantes:

    <div class="content-list" markdown="1">
    - couleur: `marron` 
    - marque:`volkswagen/beetle/retro`
    </div>

> **Remarque** : Les sous-répertoires n'affectent pas les URL de la page, l'URL est définie uniquement avec le paramètre **url**.

<a name="dynamic-pages"></a>
## Pages dynamiques

Dans la [section Twig](themes#twig-section) d'un modèle de page, vous pouvez utiliser toutes [les fonctions, filtres et balises fournis par October](../markup).
Toute page dynamique nécessite des **variables**. Dans October, les variables peuvent être préparées par la page,
la [section PHP](themes#php-section) de la maquette ou par les [composants](components).
Dans cet article, nous décrivons comment préparer des variables dans la section PHP.

<a name="page-life-cycle"></a>
### Cycle de vie d'exécution de la page

Il existe des fonctions spéciales qui peuvent être définies dans la section PHP des pages et des maquettes : `onInit`, `onStart` et `onEnd`.
La fonction `onInit` est exécutée lorsque tous les composants sont initialisés et avant que les requêtes AJAX ne soient traitées.
La fonction `onStart` est exécutée au début de l'exécution de la page.
La fonction `onEnd` est exécutée avant le rendu de la page et après l'exécution des composants de la page.
Dans les fonctions `onStart` et `onEnd`, vous pouvez injecter des variables dans l'environnement Twig.
Pour passer des variables à la page, utilisez la `notation tableau` :

    url = "/"
    ==
    fonction onStart ()
    {
        $ this['bonjour'] = "Bonjour tout le monde!";
    }
    ==
    <h3> {{ bonjour }} </h3>

L'exemple suivant est plus avancé.
Il montre comment charger une collection d'articles de blog à partir de la base de données et l'afficher sur la page (le plugin Acme\Blog est imaginaire) :

    url = "/blog"
    ==
    use Acme\Blog\Classes\Post;

    function onStart()
    {
      $this['posts'] = Post::orderBy('created_at', 'desc')->get();
    }
    ==
    <h2>Derniers articles</h2>
    <ul>
        {% for post in posts %}
            <h3>{{ post.title }}</h3>
            {{ post.content }}
        {% endfor %}
    </ul>

Les variables par défaut et les extensions Twig fournies par October sont décrites dans le [guide de balisage](../markup).
La séquence d'exécution des gestionnaires est décrite dans l'article relatif aux [maquettes dynamiques](layouts#dynamic-layouts).

<a name="life-cycle-response"></a>
### Renvoi d'une réponse personnalisée

Toutes les méthodes définies dans le cycle de vie d'exécution ont la possibilité d'arrêter le processus et renvoyer une réponse,
il suffit de retourner une réponse dans la fonction de cycle de vie.
L'exemple ci-dessous ne charge aucun contenu de la page et renvoie à la place la chaîne _Bonjour tout le monde !_ au navigateur :

    fonction onStart ()
    {
        return 'Bonjour tout le monde !';
    }

Un exemple plus utile pourrait être de déclencher une redirection à l'aide de la façade `Redirect`:

    public function onStart()
    {
        return Redirect::to('http://google.fr');
    }

<a name="handling-forms"></a>
### Gestion des formulaires

Vous pouvez gérer les formulaires habituels avec des méthodes de gestionnaire définies dans la [section PHP](themes#php-section)
de la page ou la maquette (la gestion des requêtes AJAX est expliquée dans l'article du [Framework AJAX](../ajax/introduction)).
Utilisez la fonction [`form_open()`](markup#standard-form) pour définir un formulaire qui fait référence à un gestionnaire d'événements.

Exemple :

    {{ form_open({ request: 'onHandleForm' }) }}
        Veuillez saisir une chaîne de caractères: <input type="text" name="valeur"/>
        <input type="submit" value="Soumettre!"/>
    {{ form_close() }}
    <p>Dernière valeur soumise: {{ derniereValeur }}</p>

La fonction `onHandleForm` peut être définie dans la [section PHP](themes#php-section) de la page ou la maquette, comme ceci :

    function onHandleForm()
    {
        $this['derniereValeur'] = post('valeur');
    }

Le gestionnaire charge la valeur avec la fonction `post` et initialise la variable d'attribut `derniereValeur` de la page qui est affichée sous le formulaire dans le premier exemple.

> **Remarque** : Si un gestionnaire du même nom est défini dans la maquette, la page ou un [composant](composants) de la page, October exécutera le gestionnaire de la page. Si un gestionnaire est défini dans un composant et une maquette, le gestionnaire de la maquette sera exécuté. La priorité du gestionnaire est: page, maquette, composant.

Si vous souhaitez faire référence à un gestionnaire défini dans un [composant](composants) spécifique,
utilisez le nom ou l'alias du composant dans la référence du gestionnaire :

    {{form_open ({request: 'monComposant::onHandleForm'})}}

<a name="404-page"></a>
## Page 404

Si le thème contient une page avec l'URL `/404`, il s'affiche lorsque le système ne trouve pas la page demandée.

<a name="error-page"></a>
## Page d'erreur

Par défaut, toutes les erreurs seront affichées avec une page d'erreur détaillée contenant le contenu du fichier,
le numéro de ligne et la trace d'appels où l'erreur s'est produite.
Vous pouvez afficher une page d'erreur personnalisée en définissant la valeur de la configuration `debug` sur **false** dans le fichier `config/app.php` et en créant une page avec l'URL `/error`.

<a name="page-variables"></a>
## Variables de page

Les propriétés d'une page sont accessibles dans la [section de code PHP](../cms/themes#php-section) ou dans les [composants](../cms/components) en référençant `$this->page`.

    fonction onEnd ()
    {
        $ this->page->title = 'Un titre différent de la page';
    }

Ils sont également accessibles dans le balisage à l'aide de la variable [`this.page`](../markup/this-page).

Par exemple, pour renvoyer le titre d'une page :

    <p> Le titre de cette page est: {{ this.page.title }} </p>

Pour plus d'information, référez-vous à [`this.page` dans le guide de balisage](../markup/this-page).

<a name="injecting-assets"></a>
## Injection de ressources de la page par programmation

Si nécessaire, vous pouvez injecter des ressources (feuilles de style CSS et fichiers JavaScript) dans les pages avec les méthodes `addCss` et `addJs` du contrôleur.
Cela pourrait être fait dans la fonction `onStart` définie dans la [section PHP](themes#php-section) d'une page ou d'une [maquette](layout).

Exemple :

    function onStart()
    {
        $this->addCss('assets/css/hello.css');
        $this->addJs('assets/js/app.js');
    }

Si le chemin spécifié dans l'argument des méthodes `addCss` et `addJs` commence par une barre oblique `(/)`,
il sera relatif à la racine du site Web. Si le chemin d'accès à la ressource ne commence pas par une barre oblique, il est relatif au thème.

Les ressources injectées peuvent être combinés en les passant sous forme de tableau :

    function onStart()
    {
        $this->addCss(['assets/css/hello.css', 'assets/css/goodbye.css']);
        $this->addJs(['assets/js/app.js', 'assets/js/nav.js']);
    }

Les ressources LESS et SCSS peuvent être injectés et compilés à l'aide du combineur :

    function onStart()
    {
        $this->addCss(['assets/less/base.less']);
    }

Pour utiliser les ressources injectées dans les pages ou les [maquettes](layouts), utilisez les balises [{% styles %}](../markup/tag-styles) et [{% scripts %}](../markup/tag-scripts).

Exemple :

    <head>
        ...
        {% styles %}
    </head>
    <body>
        ...
        {% scripts %}
    </body>
