# Scaffolding commands

- [Commandes pré-définies](#scaffolding-commands)
    - [Créer un plugin](#scaffold-create-plugin)
    - [Créer un composant](#scaffold-create-component)
    - [Créer un modèle](#scaffold-create-model)
    - [Créer un contrôleur backend](#scaffold-create-controller)
    - [Créer un widget de formulaire](#scaffold-create-formwidget)
    - [Créer une commande de console](#scaffold-create-command)

<a name="scaffolding-commands"></a>
## Commandes pré-définies

Utilisez les commandes prédéfinies pour augmenter votre vitesse de développement.

<a name="scaffold-create-plugin"></a>
### Créer un plugin

La commande `create:plugin` génères un dossier de plugin avec les dossiers et fichiers de base pour le plugin. Le
paramètre détermine le nom de l'auteur et du plugin.

    php artisan create:plugin Acme.Blog

<a name="scaffold-create-component"></a>
### Créer un composant

La commande `create:component` cré la classe d'un nouveau composant et la vue par défaut de celui-ci. Le premier
paramètre détermine le nom de l'auteur et du plugin. Le second détermine le nom de la classe du composant.

    php artisan create:component Acme.Blog Post

<a name="scaffold-create-model"></a>
### Créer un modèle

La commande `create:model` génères les fichiers nécessaires pour un nouveau modèle. Le premier paramètre détermine le
nom de l'auteur et du plugin. Le second détermine le nom de la classe de la vue.

    php artisan create:model Acme.Blog Post

<a name="scaffold-create-controller"></a>
### Créer un contrôleur backend

La commande `create:controller` génères un contrôleur, ainsi que ses fichiers de configuration et vues. Le premier
paramètre détermine le nom de l'auteur et du plugin. Le second détermine le nom de la classe du contrôleur.

    php artisan create:controller Acme.Blog Posts

<a name="scaffold-create-formwidget"></a>
### Créer un widget de formulaire

La commande `create:formwidget` génères un widget de formulaire, sa vue et ses fichiers assets de base. Le premier
paramètre détermine le nom de l'auteur et du plugin. Le second détermine le nom de la classe du widget. 

    php artisan create:formwidget Acme.Blog CategorySelector

<a name="scaffold-create-command"></a>
### Créer une commande de console

La commande `create:command` génères une [nouvelle commande de console](../console/development). Le premier paramètre
détermine le nom de l'auteur et du plugin. Le second détermine le nom de la commande.

    php artisan create:command RainLab.Blog MyCommand
