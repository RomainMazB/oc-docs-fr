# Les partiels du CMS

- [Introduction](#introduction)
- [Affichage des partiels](#rendering-partials)
- [Passer des variables aux partiels](#partial-variables)
- [Partiels dynamiques](#dynamic-partials)
  - [Cycle de vie d'exécution des partiels](#partial-life-cycle)
  - [Limitations du cycle de vie](#life-cycle-limitations)

<a name="introduction"></a>

## Introduction

Les partiels contiennent des morceaux de balisage Twig réutilisables qui peuvent être utilisés n'importe où sur le site Web. Les partiels sont extrêmement utiles pour les éléments de la page qui se répètent sur différentes pages ou maquette. Un bon exemple de partiel est le pied de la page qui est utilisé dans différentes [maquette](layouts). De plus, les partiels sont requis [pour mettre à jour le contenu d'une page avec AJAX](../ajax/update-partials).

Les fichiers des partiels résident dans le sous-répertoire **/partials** du répertoire de thèmes. Les fichiers des partiels doivent avoir l'extension **htm**.
Exemple d'un simple partiel:

    <p> Ceci est un partiel </p>

La section [configuration](themes#configuration-section) est facultative pour les partiels et peut contenir le paramètre facultatif **description** qui est affiché dans l'interface d'administration.
L'exemple suivant montre un partiel avec description:

    description = "Partiel de démonstartion"
    ==
    <p> Ceci est partiel </p>

La section de configuration du partiel peut également contenir des définitions de composants. Les [composants](components) sont expliqués dans un autre article.

<a name="rendering-partials"></a>

## Affichage des partiels

La balise `{% partial "non-du-partiel" %}` Twig rend un partiel. La balise a un seul paramètre requis, le nom du fichier partiel sans l'extension. N'oubliez pas que si vous référez un partiel à partir d'un sous-répertoire, vous devez spécifier le nom du sous-répertoire. La balise `{% partial %}` peut être utilisée à l'intérieur d'une page, d'une maquette ou d'un autre partiel.
Exemple de page faisant référence à un partiel:

    <div class = "sidebar">
        {% partial "contacts-lateraux" %}
    </div>

<a name="partial-variables"></a>

## Passer des variables aux partiels

Vous constaterez que vous devez souvent passer des variables à un partiel à partir d'un code externe. Cela rend les partiels encore plus utiles. Par exemple, vous pouvez avoir un partiel qui affiche une liste des articles de blog. Si vous pouvez transmettre la collection des articles au partiel, le même partiel peut être utilisé sur la page d'archive du blog, sur la page de catégorie de blog, etc. Vous pouvez transmettre des variables aux partiels en les spécifiant après le nom du partiel dans la balise `{% partial %}`:

    <div class = "sidebar">
        {% partial "articles" posts=posts%}
    </div>

Vous pouvez également affecter de nouvelles variables à utiliser dans le partiel:

    <div class = "sidebar">
        {% partial "contacts-lateraux" ville="Paris" pays="France" %}
    </div>

À l'intérieur du partiel, les variables sont accessibles comme n'importe quelle autre variable de balisage:

    <p> Pays: {{ pays }}, ville: {{ ville }}. </p>

<a name="dynamic-partials"></a>

## Partiels dynamiques

Les partiels, comme les pages, peuvent utiliser toutes les fonctionnalités Twig. Veuillez consulter la documentation des [pages dynamiques](pages#dynamic-pages) pour plus de détails.

<a name="partial-life-cycle"></a>

### Cycle de vie d'exécution des partiels

Il existe des fonctions spéciales qui peuvent être définies dans la section PHP des partiels: `onStart` et `onEnd`. La fonction `onStart` est exécutée avant l'affichage du partiel et avant l'exécution des partiels des [composants](components). La fonction `onEnd` est exécutée avant l'affichage du partiel et après l'exécution des partiels des [composants](components). Dans les fonctions `onStart` et `onEnd`, vous pouvez injecter des variables dans l'environnement Twig. Utilisez la `notation tableau` pour passer des variables à la page:

    ==
    function onStart()
    {
        $this['bonjour'] = "Bonjour tout le monde!";
    }
    ==
    <h3>{{ bonjour }}</h3>

Le langage de template fourni par October est décrit dans le [guide de balisage](../markup). La séquence globale d'exécution des gestionnaires est décrite dans la section des [maquettes dynamiques](layouts#dynamic-layouts).

<a name="life-cycle-limitations"></a>

### Limitations du cycle de vie

Puisqu'elles sont instanciées tardivement, pendant le temps de l'affichage de la page, certaines limitations s'appliquent au cycle de vie des partiels. Ils ne suivent pas le processus d'exécution standard, comme décrit dans le [cycle de vie d'exécution de la maquette](layouts#dynamic-layouts). Les limitations suivantes doivent être notées:

1. Les événements AJAX ne sont pas enregistrés et ne fonctionneront pas normalement.
1. Les fonctions du cycle de vie ne peuvent pas renvoyer de valeur.
1. Le traitement des données des formulaires aura lieu au moment de l'affichage du partiel.

En général, l'utilisation des composants dans les partiels est conçue pour
des composants de base qui rendent un balisage simple sans beaucoup de traitement, comme un bouton _Like_ ou _Tweet_.
