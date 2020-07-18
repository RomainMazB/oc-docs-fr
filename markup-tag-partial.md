# {% partial %}

La balise `{% partial %}` analysera un [partiel du CMS](../cms/partials) et rendra le contenu du partiel dans la page. Pour afficher un partiel appelé **footer.htm**, passez simplement le nom après la balise `partial` entre guillemets sous forme de chaîne.

    {% partial "footer" %}

Un partiel à l'intérieur d'un sous-répertoire peut être rendu de la même manière.

    {% partial "sidebar/menu"%}

> **Remarque**: La [Documentation du thème](../cms/themes#subdirectories) contient plus de détails sur l'utilisation des sous-répertoires.

Le nom du partiel peut également être une variable:

    {% set tabName = "profil" %}
    {% partial tabName %}

<a name="variables"> </a>

## Variables

Vous pouvez passer des variables aux partiels en les spécifiant après le nom du partiel:

    {% partial "blog-posts" posts=posts %}

Vous pouvez également affecter de nouvelles variables à utiliser dans le partiel:

    {% partial "location" ville="Paris" pays="France" %}

À l'intérieur du partiel, les variables sont accessibles comme toute autre variable de balisage:

    <p>Pays: {{ pays }}, ville: {{ ville }}.</p>

<a name="checking-partial-exits"> </a>

## La vérification de l'existance d'un partiel

Dans n'importe quel modèle, vous pouvez vérifier si un partiel existe en utilisant la fonction `partial()`. Cela vous permet de générer un balisage différent selon que le partiel existe ou non. Exemple:

    {% set cardPartial = 'mes-cartes/' ~ cardCode %}

    {% if partial(cardPartial)%}
        {% partial cardPartial %}
    {% else %}
        <p>Carte introuvable!</p>
    {% endif %}
