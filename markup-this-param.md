# this.param

Vous pouvez accéder aux paramètres de l'URL actuels via `this.param` et il renvoie un tableau PHP.

## Accès aux paramètres de la page

Cet exemple montre comment accéder au paramètre d'URL `tab` dans une page.

    url = "/account/:tab"
    ==
    {% if this.param.tab == 'details' %}

        <p>Voici tous vos détails</p>

    {% elseif this.param.tab == 'historique' %}

        <p>Vous regardez un souffle du passé</p>

    {% endif %}

Si le nom du paramètre est également une variable, la syntaxe du tableau peut être utilisée.

    url = "/account/:post_id"
    ==
    {% set name = 'post_id' %}

    <p>L'ID de l'article est: {{ this.param[name] }}</p>
