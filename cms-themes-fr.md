# Themes du CMS

- [Introduction](#introduction)
- [Structure du répertoire](#directory-structure)
  - [Sous-répertoires](#subdirectories)
- [Structure du modèle](#template-structure)
  - [Section de configuration](#configuration-section)
  - [Section du code PHP](#php-section)
  - [Section de balisage Twig](#twig-section)
- [Journalisation des thèmes](#theme-logging)
- [Thème contrôlé par la base de données](#database-driven-themes)

<a name="introduction"></a>

## Introduction

Les thèmes définissent l'apparence de votre site Web ou application Web créé avec October. Les thèmes dans October sont entièrement sauvegardés sur fichiers et peuvent être gérés avec n'importe quel système de contrôle de version, par exemple, Git. Cette page présente une description de haut niveau des thèmes dans October. Vous trouverez plus de détails sur les [pages](pages), les [partiels](partials), les [maquettes](layouts) et les [blocs de contenu](content) dans les articles correspondants.

> **REMARQUE:** les thèmes peuvent stocker des modèles dans la base de données si le parmètre `cms.databaseTemplates` est activé, consultez la section des [thèmes contrôlés par la base de données](#database-driven-themes) pour plus d'informations.

Les thèmes sont des dossiers qui résident par défaut dans le dossier **/themes**. Les thèmes peuvent contenir les élements suivants:

| Élément                         | Description                                                                                                                                            |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [Les pages](pages)              | représentent les pages du site web.                                                                                                                    |
| [Les partiels](partials)        | contiennent des morceaux réutilisables de balisage HTML.                                                                                               |
| [Les maquettes](layouts)        | définissent la structure de page.                                                                                                                      |
| [Les blocs de contenu](content) | blocs de texte, HTML ou [Markdown](http://daringfireball.net/projects/markdown/syntax) qui peuvent être rédigé séparément d'une page ou d'une maquette |
| **Les fichiers des ressources** | sont des fichiers des ressources comme des images, des feuilles de style CSS et des fichiers JavaScript                                                |

<a name="directory-structure"></a>

## Structure du répertoire

Dans l'exemple ci-dessous, figure la structure du répertoire d'un thème. Chaque thème dans October est représenté par un répertoire distinct et généralement, un seul thème actif est utilisé pour afficher le site Web.  
Cet exemple affiche le répertoire du thème "website".

    themes/
      website/           <=== Le thème commence ici
        pages/           <=== Dossier des Pages
          home.htm
        layouts/         <=== Dossier des maquettes
          default.htm
        partials/        <=== Dossier des partiels
          sidebar.htm
        content/         <=== Dossier des Fichiers de contenu
          intro.htm
        assets/          <=== Dossier des fichiers des ressources
          css/
            my-styles.css
          js/
          images/

> Le thème actif est défini avec le paramètre `activeTheme` dans le fichier `config/cms.php` ou avec le sélecteur de thème sur la page Réglages > Thème frontal dans l'interface d'administration. Le thème défini avec le sélecteur de thème remplace la valeur du fichier `config/cms.php`.

<a name="subdirectories"></a>

### Sous-répertoires

October prend en charge un seul niveau de sous-répertoires pour les **pages**, les **partiels**, les **maquettes** et les **blocs de contenu**, tandis que le répertoire des ressources peut avoir n'importe quelle structure. Cela simplifie l'organisation des grands sites Web. Dans l'exemple ci-dessous, vous pouvez voir que les répertoires **pages** et **partials** contiennent le sous-répertoire **blog** et le répertoire **content** contient le sous-répertoire **home**.

    themes/
      website/
        pages/
          home.htm
          blog/                  <=== Sous-répertoire
            archive.htm
            category.htm
        partials/
          sidebar.htm
          blog/                  <=== Sous-répertoire
            category-list.htm
        content/
          footer-contacts.txt
          home/                  <=== Sous-répertoire
            intro.htm
        ...

Pour référencier à un fichier **partiel** ou à un **bloc de contenu** d'un sous-répertoire, vous devez spécifier le nom du sous-répertoire avant le nom du fichier.
Exemple de rendu d'un partiel à partir d'un sous-répertoire:

    {% partial "blog/category-list" %}

> **Remarque**: Les chemins d'accès aux modèles sont toujours absolus. Si, dans un partiel, vous rendez un autre partiel à partir du même sous-répertoire, vous devez toujours spécifier le nom du sous-répertoire.

<a name="template-structure"></a>

## Structure du modèle

Les pages, les partiels et les maquettes peuvent inclure jusqu'à 3 sections: **configuration**, **code PHP** et **balisage Twig**. Les sections sont séparées avec la séquence `==`.  
Par exemple:

    url = "/blog"
    layout = "default"
    ==
    function onStart()
    {
        $this['posts'] = ...;
    }
    ==
    <h3>Archive du blog</h3>
    {% for post in posts %}
        <h4>{{ post.title }}</h4>
        {{ post.content }}
    {% endfor %}

<a name="configuration-section"></a>

### Section de configuration

La section de configuration définit les paramètres du modèle. Les paramètres de configuration prises en charge sont spécifiques à différents modèles du CMS et sont décrits dans les articles de la documentation correspondante. La section de configuration utilise le simple [format INI](http://en.wikipedia.org/wiki/INI_file), où les valeurs des paramètres sont placées entre guillemets.
Exemple de section de configuration pour un modèle de page:

    url = "/blog"
    layout = "default"

    [component]
    parameter = "valeur"

<a name="php-section"></a>

### Section de code PHP

Le code dans la section PHP s'exécute à chaque fois avant le rendu du modèle. La section PHP est facultative pour tous les modèles du CMS et son contenu dépend du type de modèle où elle est définie. La section de code PHP peut contenir des balises PHP d'ouverture et de fermeture facultatives pour activer la coloration syntaxique dans les éditeurs de texte. Les balises d'ouverture et de fermeture doivent toujours être spécifiées sur une ligne différente du séparateur de section `==`.

    url = "/blog"
    layout = "default"
    ==
    <?
    function onStart()
    {
        $this['posts'] = ...;
    }
    ?>
    ==
    <h3>Archive du blog</h3>
    {% for post in posts %}
        <h4>{{ post.title }}</h4>
        {{ post.content }}
    {% endfor %}

Dans la section PHP, vous pouvez uniquement définir des fonctions et faire référence aux namespaces avec le mot-clé PHP `use`. Aucun autre code PHP n'est autorisé dans la section PHP. En effet, la section PHP est convertie en classe PHP lorsque la page est analysée.
Exemple d'utilisation avec du namespace:

    url = "/blog"
    layout = "default"
    ==
    <?
    use Acme\Blog\Classes\Post;

    function onStart()
    {
        $this['posts'] = Post::get();
    }
    ?>
    ==

De manière générale pour définir des variables, vous devez utiliser la méthode d'accès au tableau sur `$this`, bien que pour simplifier vous pouvez utiliser **l'accès aux objets en lecture seule**, par exemple:

    // Écrire via tableau
    $this['foo'] = 'bar';

    // Lire via tableau
    echo $this['foo'];

    // Lecture seule via objet
    echo $this->foo;

<a name="twig-section"></a>

### Section de balisage Twig

La section Twig définit les balisages à rendre par le modèle. Dans la section Twig, vous pouvez utiliser des fonctions, des balises et des filtres [fournis par October](../markup) ainsi que toutes les [fonctionnalités natives de Twig](http://twig.sensiolabs.org/documentation) ou celles [fourni par les plugins](../plugin/registration#extending-twig). Le contenu de la section Twig dépend du type de modèle (page, mmaquettes ou partiel). Vous pouvez trouver plus d'informations sur des éléments Twig spécifiques plus loin dans la documentation.

Pour plus d'informations, réferez-vous au [guide de balisage](../markup).

<a name="theme-logging"> </a>

## Journalisation du thème

October CMS est livré avec une fonctionnalité très utile, désactivée par défaut, appelée Journalisation du thème .

Puisque les maquettes et les pages stockent la plupart des données dans des fichiers, il est possible que vous ou vos clients perdiez accidentellement du contenu. Par exemple, changer la maquette d'une page modifiera la structure de la page et, en tant que tel, entraînera une perte de données.

Pour activer la journalisation du thème, accédez simplement à **Réglages -> Paramètres du journal** et activez **Enregistrement des modifications sur le thème**. Toutes les modifications sont désormais enregistrées.

Le journal des modifications du thème peut être consulté dans **Réglages -> Theme log**. Chaque modification a un aperçu de ce qui a été ajouté/supprimé, ainsi qu'une copie du fichier modifié avant et après. Vous pouvez utiliser ces informations pour décider de l'action appropriée, pour faciliter la réversion de ces modifications, si nécessaire.

<a name="database-driven-themes"> </a>

## Thèmes contrôlé par la base de données

October CMS est livré avec une autre fonctionnalité très utile, désactivée par défaut, appelée _Database Driven Themes_. Lorsque cette fonctionnalité est activée (en définissant le paramètre `cms.databaseTemplates` sur `true`, ou `null` lorsque `app.debug` vaut `false`), la couche de la base de données stocke tous les fichiers CMS modifiés dans la base de données. Les fichiers qui ne sont pas modifiés continuent d'être chargés à partir du système de fichiers. Il existe une commande de console [`theme:sync $themeDir`](../console/commands#theme-sync-command) qui peut être utilisée pour synchroniser les modifications entre le système de fichiers et la base de données.

Les fichiers modifiés dans la base de données sont mis en cache pour indiquer qu'ils doivent être chargés à partir de la base de données.

> **REMARQUE:** Tous les éléments du modèle du CMS (par ex. `Layout`, `Page`, `Content`, `Partial`, `Meta`, etc.) sont stockés dans la base de données lorsque cette fonction est activée et qu'une modification est apporté au modèle en question, cependant, les fichiers des ressources du théme **ne le seront pas**.
