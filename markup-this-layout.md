# this.layout

Vous pouvez accéder à l'objet représentant la maquette actuelle via `this.layout`
Cette propriété renvoie l'objet `Cms\Classes\Layout`.

## Propriétés

`this.layout` a les propriétés suivantes :

### id

Convertit le nom du fichier de la maquette et le nom du dossier en un identifiant CSS convivial.

    <body class="layout-{{ this.layout.id }}">

Si le fichier de la maquette était **default.htm**, cela générerait un nom de classe `layout-default`.

### description

La description de la maquette telle que définie par la configuration.

    <meta name="description" content="{{ this.layout.description }}">
