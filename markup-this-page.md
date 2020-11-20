# this.page

Vous pouvez accéder à l'objet représentant la page actuelle via `this.page`.
Cette propriété renvoie l'objet `Cms\Classes\Page`.
Cet objet est également [accessible dans la section code PHP](../cms/pages/#page-variables)

## Propriétés

`this.page` a les propriétés suivantes.

### layout

Référence au nom de la maquette utilisée par cette page, si elle est définie.

À ne pas confondre avec `this.layout`.

    {{ this.page.layout }}

### id

Convertit le nom du fichier de la page et le nom du dossier en un identifiant CSS convivial.

    <body class="page-{{ this.page.id }}">

Si le fichier était **home/index.htm**, il générerait un nom de classe `page-home-index`.

### title

Le titre de la page tel que défini par la configuration.

    <h1>{{ this.page.title }}</h1>

### description

La description de la page telle que définie par la configuration.

    <p>{{ this.page.description }}</p>

### meta_title

Un autre champ `title`, généralement plus descriptif à des fins de référencement.

    <title>{{ this.page.meta_title }}</title>

### meta_description

Un autre champ `description`, généralement plus descriptif à des fins de référencement.

    <meta name="description" content="{{ this.page.meta_description }}">

### hidden

Les pages masquées ne sont accessibles qu'aux utilisateurs connectés au back-end.

    {% if this.page.hidden %}
        <p>Note aux autres administrateurs: nous travaillons actuellement sur cette page.</p>
    {% endif %}

### fileName

Nom du fichier de la page dans le thème avec l'extension.

### baseFileName

Nom du fichier de la page dans le thème sans l'extension.
