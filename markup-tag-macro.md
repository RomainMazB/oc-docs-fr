# {% macro %}

La balise `{% macro %}` vous permet de définir des fonctions personnalisées dans vos modèles, similaires aux langages de programmation habituels.

    {% macro input() %}
        ...
    {% endmacro %}

Vous pouvez également inclure le nom de la macro après la balise de fin pour une meilleure lisibilité:

    {% macro input() %}
        ...
    {% endmacro input %}

L'exemple suivant définit une fonction appelée `input()` qui prend 4 arguments, les valeurs associées sont accessibles en tant que variables dans le balisage à l'intérieur.

    {% macro input(nom, valeur, type, taille)%}
        <input
            type = "{{ type|default('text') }}"
            nom = "{{ nom }}"
            valeur = "{{ valeur|e }}"
            taille = "{{ taille|default(20) }}" />
    {% endmacro%}

> **Remarque**: Les arguments d'une macro ne spécifient pas de valeurs par défaut et sont toujours considérés comme facultatifs.

<a name="calling-macros"> </a>

## Appel de macros

Avant qu'une macro puisse être utilisée, elle doit d'abord être "importée" en utilisant la balise `{% import %}`. Si la macro est définie dans le même fichier, la variable spéciale `_self` peut être utilisée.

    {% import _self as formulaire %}

Ici, les fonctions macro sont affectées à la variable `formulaire`, disponible pour être appelée comme n'importe quelle autre fonction.

    <p>{{ formulaire.input('username') }}</p>
    <p>{{ formulaire.input('password', null, 'password') }}</p>

Les macros peuvent être définies dans [un partiel de thème](../cms/partials) et importées par leur nom. Pour importer les macros à partir d'un partiel appelé **macros/form.htm**, passez simplement le nom après la balise `import` entre guillemets sous forme de chaîne.

    {% import 'macros/form' as formulaire%}

Vous pouvez également importer des macros à partir d'un [fichier de vue système](../services/response-view#views) et celles-ci seront acceptées. Pour importer depuis **plugins/acme/blog/views/macros.htm**, transmettez simplement l'indication du chemin à la place.

    {% import 'acme.blog::macros' as formulaire %}

<a name="nested-macros"> </a>

## Macros imbriquées

Lorsque vous souhaitez utiliser une macro à l'intérieur d'une autre macro du même fichier, vous devez l'importer localement.

    {% macro input(nom, valeur, type, taille)%}
        <input
            type = "{{ type|default('text') }}"
            nom = "{{ nom }}"
            valeur = "{{ valeur|e }}"
            taille = "{{ taille|default(20) }}" />
    {% endmacro %}

    {% macro wrapped_input(nom, valeur, type, taille) %}
        {% import _self as formulaire %}

        <div class="field">
            {{ formulaire.input (nom, valeur, type, taille) }}
        </div>
    {% endmacro %}

<a name="context-variable"> </a>

## Variable de contexte

Les macros n'ont pas accès aux variables de la page en cours.

    <!-- October CMS -->
    {{ nom_du_site}}

    {% macro maFonction() %}
        <!-- NULL -->
        {{ nom_du_site }}
    {% endmacro %}

Vous pouvez passer les variables à la fonction en utilisant la variable spéciale `_context`.

    {% macro maFonction(vars) %}
        {{ vars.nom_du_site }}
    {% endmacro %}

    {% import _self as formulaire %}

    <!-- October CMS -->
    {{ formulaire.maFonction(_context) }}
