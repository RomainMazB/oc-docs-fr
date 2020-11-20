# Maquettes du CMS

- [Introduction](#introduction)
- [Les placeholders](#placeholders)
- [Maquettes dynamiques](#dynamic-layouts)
  - [Cycle de vie de l'exécution d'une maquette](#layout-life-cycle)

<a name="introduction"></a>
## Introduction

Les maquettes définissent la structure de la page, c'est-à-dire tout ce qui se répète sur une page, comme l'en-tête et le pied de la page. Les maquettes contiennent souvent la balise HTML ainsi que les balises HEAD, TITLE et BODY.

Les maquettes résident dans le sous-répertoire **/layouts** du répertoire du thème. Les fichiers de la maquette doivent avoir l'extension **htm**.
Dans le fichier de la maquette, vous devez utiliser la balise `{% page %}` pour afficher le contenu de la page.
Exemple d'une simple maquette :

    <html>
        <body>
            {% page %}
        </body>
    </html>

Pour utiliser une maquette pour une [page](pages), la page doit faire référence au nom du fichier de la maquette (sans extension) dans la section [configuration](themes#configuration-section). N'oubliez pas que si vous référez une maquette à partir d'un sous-répertoire, vous devez spécifier le nom du sous-répertoire. Exemple de modèle de page utilisant la maquette default.htm:

    url = "/"
    layout = "default"
    ==
    <p> Bonjour tout le monde! </p>

Lorsque cette page est demandée, son contenu est fusionné avec la maquette, ou plus précisément, la balise `{% page %}` de la maquette est remplacée par le contenu de la page.
L'exemple précédent générerait le balisage suivant :

    <html>
        <body>
            <p> Bonjour tout le monde! </p>
        </body>
    </html>

Notez que vous pouvez afficher des [partiels](partials) dans des maquettes. Cela vous permet de partager les éléments de balisage communs entre différentes maquettes. Par exemple, vous pouvez avoir un partiel qui génère les liens CSS et JavaScript du site Web. Cette approche simplifie la gestion des ressources, si vous souhaitez ajouter une référence JavaScript, vous devez modifier un seul partiel au lieu de modifier toutes les maquettes.

La section [configuration](themes#configuration-section) est facultative pour les maquettes. Les paramètres de configuration pris en charge sont **name** et **description**. Les paramètres sont facultatifs et utilisés dans l'interface d'administration.
Exemple de modèle de maquette avec une description :

    description = "Basic layout example"
    ==
    <html>
        <body>
            {% page %}
        </body>
    </html>

<a name="placeholders"></a>
## Placeholders

Les placeholders permettent aux pages d'injecter du contenu dans la maquette. Les placeholders sont définis dans les maquettes avec la balise `{% placeholder %}`. L'exemple suivant montre un modèle de maquette avec un placeholder **head** définie dans la section HTML HEAD.

    <html>
        <head>
            {% placeholder head %}
        </head>
        ...

Les pages peuvent injecter du contenu dans les placeholders à l'aide des balises `{% put %}` et `{% endput %}`. L'exemple suivant illustre un modèle de page simple qui injecte un lien CSS dans le placeholders **head** défini dans l'exemple précédent.

    url = "/ma-page"
    layout = "default"
    ==
    {% put head %}
        <link href="/themes/demo/assets/css/page.css" rel="stylesheet">
    {% endput %}

    <p>Le contenu de la page va ici.</p>

Vous trouverez plus d'informations sur les placeholders dans [le guide de balisage](../markup/tag-placeholder).

<a name="dynamic-layouts"></a>
## Maquettes dynamiques

Les maquettes comme les pages, peuvent utiliser toutes les fonctionnalités Twig. Veuillez consulter la documentation des [pages dynamiques](pages#dynamic-pages) pour plus de détail.

<a name="layout-life-cycle"></a>
### Cycle de vie de l'exécution d'une maquette

Dans la [section PHP](themes#php-section) de la maquette, vous pouvez définir les fonctions suivantes pour gérer le cycle de vie d'exécution de la page: `onInit`, `onStart`, `onBeforePageStart` et `onEnd`.

La fonction `onInit` est exécutée lorsque tous les composants sont initialisés et avant que les requêtes AJAX ne soient traitées.
La fonction `onStart` est exécutée au début du traitement de la page.
La fonction `onBeforePageStart` est exécutée après l'exécution des composants de la maquette, mais avant l'exécution de la fonction `onStart` de la page.
La fonction `onEnd` est exécutée après le rendu de la page.

La séquence d'exécution des gestionnaires est la suivante :

1. Fonction `onInit()` de la maquette.
1. Fonction `onInit()` de la page.
1. Fonction `onStart()` de la maquette.
1. Fonction `onRun()` des composants de la maquette.
1. Fonction `onBeforePageStart()` de la maquette.
1. Fonction `onStart()` de la page.
1. Fonction `onRun()` des composants de la page.
1. Fonction `onEnd()` de la page.
1. Fonction `onEnd()` de la maquette.
