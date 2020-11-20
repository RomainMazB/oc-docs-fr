# Le moteur de templates

October étend le [moteur de template Twig](http://twig.sensiolabs.org/documentation) avec un certain nombre de fonctions, balises, filtres et variables.
Ces extensions vous permettent d'utiliser les fonctionnalités du CMS et d'accéder aux informations d'environnement de la page dans vos vues.

## Variables

Les variables sont affichées sur la page à l'aide des _doubles accolades_.

    {{ variable }}

Les variables peuvent également représenter des _expressions_.

    {{ isAjax? 'Oui' : 'Non' }}

Les variables peuvent être concaténées avec le caractère `~`.

    {{ 'Votre nom: ' ~ nom }}

October fournit des variables globales sous la variable `this`, comme indiqué dans la section **Variables**.

## Balise

Les balises sont une caractéristique unique de Twig et sont entourées de caractères `{% %}`.

    {% balise %}

Les balises fournissent un moyen plus fluide pour décrire la logique de la vue.

    {% if nuageOrageuxArrive %}
        Rester à l'intérieur
    {% else %}
        Allez dehors et profitez du beau temps
    {% endif %}

La balise `{% set %}` peut être utilisée pour définir des variables à l'intérieur de la vue.

    {% set pageActive = 'blog' %}

Les balises peuvent prendre de nombreuses syntaxes différentes et sont répertoriées dans la section **Balises**.

## Filtres

Les filtres agissent comme des modificateurs de variables pour une seule instance et sont appliqués à l'aide d'une _barre verticale_ `|`suivi du nom du filtre.

    {{ 'chaîne de caractères'|filtre }}

Les filtres peuvent prendre des arguments comme une fonction.

    {{ prix|currency('USD'))}}

Les filtres peuvent être appliqués successivement.

    {{ 'Gloire à October'|upper|replace({'October': 'Matin'}) }}

Les filtres sont répertoriés dans la section **Filtres**.

## Les fonctions

Les fonctions permettent à la logique d'être exécutée et le résultat renvoyé agit comme une variable.

    {{ fonction() }}

Les fonctions peuvent accepter des arguments.

    {{ dump(variable) }}

Les fonctions sont répertoriées dans la section **Fonctions**.

## Logique d'accès

La chose la plus importante à apprendre sur Twig est de savoir comment il accède à la couche PHP.
Pour des raisons de commodité, `{{ toto.titi }}` effectue les vérifications suivantes sur un objet PHP :

1. Vérifie si `toto` est un tableau et `titi` un élément valide.
1. Sinon, si `toto` est un objet, vérifie que `titi` est une propriété valide.
1. Sinon, si `toto` est un objet, vérifie que `titi` est une méthode valide (même si titi est le constructeur, utilisez plutôt `__construct()`).
1. Sinon, si `toto` est un objet, vérifie que `getTiti` est une méthode valide.
1. Sinon, si `toto` est un objet, vérifie que `isTiti` est une méthode valide.
1. Sinon, renvoi une valeur `null`.

## Fonctionnalités non prises en charge

Il y a certaines fonctionnalités offertes par Twig qui ne sont pas prises en charge dans October.
Elles sont répertoriées ci-dessous à côté de la fonctionnalité équivalente.

| Tag             | Équivalent                                                                             |
| --------------- | -------------------------------------------------------------------------------------- |
| `{% extend %}`  | Utilisez des [Maquettes](http://octobercms.fr/docs/cms/layouts) ou `{% placeholder %}` |
| `{% include %}` | Utilisez `{% partial %}` ou `{% content %}`                                            |
