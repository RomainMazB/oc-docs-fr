# {% content %}

La balise `{% content %}` affichera un [bloc de contenu CMS](../cms/content) sur la page.

Pour afficher le bloc de contenu appelé **contacts.htm**, vous passez le nom du fichier après la balise `content` entre guillemets sous forme de chaîne.

    {% content "contacts.htm" %}

Un bloc de contenu à l'intérieur d'un sous-répertoire peut être rendu de la même manière.

    {% content "sidebar/content.htm" %}

> **Remarque** : La [Documentation du thème](../cms/themes#subdirectories) contient plus de détails sur l'utilisation des sous-répertoires.

Les blocs de contenu peuvent être rendus sous forme de texte brut :

    {% content "readme.txt" %}

Vous pouvez également utiliser la syntaxe Markdown :

    {% content "changelog.md" %}

Les blocs de contenu peuvent également être utilisés en combinaison avec des [placeholders de la maquette](../cms/layouts#placeholders):

    {% put sidebar %}
        {% content 'sidebar-content.htm' %}
    {% endput%}

<a name="variables"></a>
## Variables

Vous pouvez transmettre des variables aux blocs de contenu en les spécifiant après le nom du fichier :

    {% content "welcome.htm" name=user.name %}

Vous pouvez également affecter de nouvelles variables à utiliser dans le contenu :

    {% partial "location" ville="Paris" pays="France" %}

À l'intérieur du contenu, les variables sont accessibles en utilisant une syntaxe de base utilisant de simples accolades :

    <p>Pays: { pays }, ville: { ville }.</p>

Vous pouvez également transmettre une collection de variables sous forme d'un simple tableau :

    {% content "welcome.htm" likes=[
        {name: 'Dogs'},
        {name: 'Fishing'},
        {nom: 'Golf'}
    ] %}

La collection de variables est accessible en utilisant des accolades ouvrant et fermant :

    <ul>
        {likes}
            <li>{name}</li>
        {/likes}
    </ul>

> **Remarque** : la syntaxe Twig n'est pas prise en charge dans les blocs de contenu, pensez à utiliser un [partiel du CMS](../cms/partials) à la place.
