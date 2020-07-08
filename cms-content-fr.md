# CMS Content blocks

- [Introduction](#introduction)
- [Afficher des blocs de contenu](#rendering-content-blocks)
- [Passer des variables à un bloc de contenu](#content-variables)
  - [Variables globales](#content-global-variables)

Les blocs de contenus sont des blocs de textes, de HTML ou de [Markdown](http://daringfireball.net/projects/markdown/syntax)
qui peuvent être rédigé séparément d'une page ou d'une maquette. Ils sont fait pour contenir des contenus statics et supportent uniquement des variables de mise en forme basiques. Les [partiels](partials) sont plus flexible et devraient être utilisé pour générer du contenu dynamique.

<a name="introduction"></a>

## Introduction

Les blocs de contenus sont dans le sous-dossier **/content/** d'un dossier thème. Les extensions suivantes sont supportés :

| Extension | Description                             |
| --------- | --------------------------------------- |
| **htm**   | Utilisé pour une mise en forme HTML     |
| **txt**   | Utilisé pour du texte brut              |
| **md**    | Utilisé pour une mise en forme Markdown |

L'extension affectes la façon dont le bloc de contenu est affiché dans l'interface d'administration (avec un éditeur WYSIWYG ou avec un éditeur de texte brut) et comment le bloc est affiché sur le site. Les blocs de Markdown sont convertis en HTML avant d'être affichés.

<a name="rendering-content-blocks"></a>

## Afficher un bloc de contenu

Utilisez le balise `{% content 'file.htm' %}` pour afficher un bloc de contenu dans une [page](pages), un [partiel](partials)
ou une [maquette](layouts). Exemple d'affichage d'un bloc de contenu sur une page :

    url = "/contacts"
    ==
    <div class="contacts">
        {% content 'contacts.htm' %}
    </div>

<a name="content-variables"></a>

## Passer des variables à un bloc de contenu

Il peut être nécessaire d'avoir besoin de passer des variables à un bloc de contenu depuis du code externe. Malgré que les blocs de contenu ne supportent pas le balisage Twig, ils supportent l'utilisation de variable dans une syntaxe basique. Vous pouvez passer des variables à un bloc de contenu en les spécifiant après le nom du bloc dans la balise `{% content %}` :

    {% content 'welcome.htm' name='John' %}

À l'intérieur du bloc de contenu, la variable peut être affiché en utilisant des _accolades_ simple:

    <h1>Ceci est une démo pour { name }</h1>

<!-- TODO: changer le nom du "guide markup" -->

Vous trouverez plus d'information dans le [guide de balisage](../markup/tag-content).

<a name="content-global-variables"></a>

### Variables globales

Vous pouvez définir des variables qui sont globalement accessible à tous les blocs de contenu avec la fonction `View::share`.

    View::share('site_name', 'OctoberCMS');

Ce code peut être appelé à l'intérieur de la méthode `boot` d'un [fichier d'enregistrement de plugin](../plugin/registration).
En utilisant le code précédent, la variable `{site_name}` deviens accessible à tous les blocs de contenu.
