# Développement des thèmes

- [Fichier d'informations du thème](#theme-information)
- [Fichier de version](#version-file)
- [Image d'aperçu du thème](#preview-image)
- [Personnalisation du thème](#customization)
- [Dépendances du thème](#dependencies)

Le répertoire du thème peut inclure les fichiers **theme.yaml**, **version.yaml** et **assets/images/theme-preview.png**. Ces fichiers sont facultatifs pour le développement local mais requis pour les thèmes publiés sur la Marketplace d'OctoberCMS.

<a name="theme-information"></a>
## Fichier d'informations du thème

Le fichier d'informations du thème **theme.yaml** contient la description du thème, le nom de l'auteur, l'URL du site Web de l'auteur et d'autres informations. Le fichier doit être placé dans le répertoire racine du thème:

    themes/
      demo/
        theme.yaml    <=== Fichier d'informations du thème

Les champs suivants sont pris en charge dans le fichier **theme.yaml** :

| Champ            | Description                                                                                                                                                                                                                                                                                                                                                     |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **name**         | spécifie le nom du thème, obligatoire.                                                                                                                                                                                                                                                                                                                          |
| **author**       | spécifie le nom de l'auteur, obligatoire.                                                                                                                                                                                                                                                                                                                       |
| **homepage**     | spécifie l'URL du site Web de l'auteur, obligatoire.                                                                                                                                                                                                                                                                                                            |
| **description**  | la description du thème, obligatoire.                                                                                                                                                                                                                                                                                                                           |
| **previewImage** | image d'aperçu personnalisée, chemin relatif au répertoire du thème, par exemple: `assets/images/preview.png`, facultatif.                                                                                                                                                                                                                                      |
| **code**         | le code du thème, facultatif. Cette valeur est utilisée sur la Marketplace d'OctoberCMS pour initialiser celle identifiant unique du thème. Si le code du thème n'est pas fourni, le nom du répertoire du thème sera utilisé comme code. Lorsqu'un thème est installé à partir de la Marketplace, le code est utilisé comme nouveau nom du répertoire du thème. |
| **form**         | un tableau de configuration ou une référence à un fichier de définition de champ de formulaire, utilisé pour [la personnalisation du thème](#customization), facultatif.                                                                                                                                                                                        |
| **require**      | un tableau de noms de plugins utilisés comme [dépendances du thème](#dependencies), facultatif.                                                                                                                                                                                                                                                                 |

Exemple de fichier d'informations du thème :

    nom: "Démo d'OctoberCMS"
    description: "Démontre les concepts de base du thème du front-end."
    auteur: "OctoberCMS"
    page d'accueil: "https://octobercms.fr"
    code: "demo"

<a name="version-file"></a>
## Fichier de version

Le fichier de version du thème **version.yaml** définit la version actuelle du thème et le journal des modifications.
Le fichier doit être placé dans le répertoire racine du thème :

    themes/
      demo/
        ...
        version.yaml    <=== Theme version file

Le format du fichier est le suivant :

    1.0.1: Initialisation du thème
    1.0.2: Ajout de nouvelles fonctionnalités
    1.0.3: Certaines fonctionnalités sont supprimées

<a name="preview-image"></a>
## Image d'aperçu du thème

L'image d'aperçu du thème est utilisée dans le sélecteur de thème dans l'interface d'administration.
Le fichier image **theme-preview.png** doit être placé dans le répertoire **assets/images** du thème :

    themes/
      demo/
        assets/
          images/
            theme-preview.png    <=== Image d'aperçu du thème

La largeur de l'image doit être d'au moins 600 pixels. Le rapport hauteur/largeur idéal est de 1.5, par exemple 600x400px.

<a name="customization"></a>
## Personnalisation du thème

Les thèmes peuvent prendre en charge des valeurs de configuration en définissant une clé `form` dans le fichier d'informations du thème.
Cette clé doit contenir un tableau de configuration ou une référence à un fichier de définition de champ de formulaire, voir [champs de formulaire](../backend/forms#form-fields) pour plus d'informations.

Ci-après un exemple de définition d'un champ de configuration pour le nom du site Web appelé **nom_site** :

    nom: Mon thème
    # [...]

    form:
        fields:
            nom_site:
                label: Nom du site
                comment: Le nom du site Web tel qu'il devrait apparaître sur le front-end
                default: Mon site est incroyable!

> **Remarque** : Si vous utilisez des champs imbriqués avec une syntaxe de tableau (`contact[nom]`, `contact[email]` etc.), vous devez ajouter le niveau supérieur au tableau `jsonable` de la vue `ThemeData` en utilisant le code suivant :

    \Cms\Models\ThemeData::extend(function ($model) {
        $model->addJsonable('contact');
    });

La valeur peut alors être récupérée dans n'importe quel modèle du thème en utilisant la [variable de page](../cms/markup#default-variables) appelée `this.theme`.

    <h1>Bienvenue dans {{ this.theme.nom_site }}!</h1>

Vous pouvez également définir la configuration dans un fichier séparé, dont le chemin est relatif au thème.
La définition suivante va générer les champs du formulaire à partir du fichier **config/fields.yaml** à l'intérieur du thème.

    name: Mon thème
    # [...]

    form: config/fields.yaml

<a name="combiner-vars"></a>
### Variables du combinateur

Les ressources combinées à l'aide du [filtre et combinateur](../markup/filter-theme) `|theme` peuvent avoir des valeurs transmises aux filtres qui les prennent en charge, tels que le filtre LESS. Spécifiez simplement l'option `assetVar` lors de la définition du champ du formulaire, la valeur doit contenir le nom de la variable souhaité.

    form:
        fields:
            # [...]

            couleur_du_lien:
                label: La couleur du lien
                type: colorpicker
                assetVar: 'link-color'

Dans l'exemple ci-dessus, la valeur de la couleur sélectionnée sera disponible dans le fichier less en tant que `@link-color`.
En supposant que nous ayons la référence de la feuille de style suivante :

    <link href="{{ ['assets/less/theme.less']|theme }}" rel="stylesheet">

ci-après un exemple d'utilisation dans **themes/demo/assets/less/theme.less** :

    a { color: @link-color }

<a name="dependencies"></a>
## Dépendances du thème

Un thème peut dépendre de plugins en définissant une option **require** dans le [Fichier d'informations du thème](#theme-information),
l'option doit fournir un tableau de noms de plugins qui sont considérés comme des dépendances.
Un thème qui dépend de **Acme.Blog** et **Acme.User** peut définir cette dépendance de la manière suivante :

    name: "Démo d'OctoberCMS"
    # [...]

    require:
        - Acme.User
        - Acme.Blog

Lorsque le thème est installé pour la première fois, le système tentera d'installer les plugins requis en même temps.
