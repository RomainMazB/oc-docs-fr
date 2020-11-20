# this.theme

Vous pouvez accéder à l'objet thème actuel via `this.theme`.
Cette propriété renvoie l'objet `Cms\Classes\Theme`, une référence à l'[objet de personnalisation du thème](../themes/development#customization).

## Propriétés

`this.theme` fournira un accès direct aux valeurs des champs du formulaire définies par n'importe quelle personnalisation du thème.
Il possède également les propriétés suivantes de manière native.

### id

Convertit le nom du répertoire du thème en un identifiant CSS convivial.

    <body class="theme-{{ this.theme.id }}">

Si le répertoire de thème était **siteweb**, cela générerait un nom de classe `theme-siteweb`.

### config

Un tableau contenant toutes les valeurs de configuration du thème trouvées dans le fichier `theme.yaml`.

    <meta name="description" content="{{ this.theme.config.description }}">
