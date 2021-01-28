# Backend forms

- [Introduction](#introduction)
- [Configurer le comportement du formulaire](#configuring-form)
    - [Créer une page](#form-create-page)
    - [Mettre à jour une page](#form-update-page)
    - [Aperçu de la page](#form-preview-page)
- [Définition des champs du formulaire](#form-fields)
    - [Options des onglets](#form-tab-options)
    - [Options des champs](#form-field-options)
- [Types de champs disponibles](#field-types)
- [Form widgets](#form-widgets)
- [Form views](#form-views)
    - [Create view](#form-create-view)
    - [Update view](#form-update-view)
    - [Preview view](#form-preview-view)
- [Applying conditions to fields](#field-conditions)
    - [Input preset converter](#field-input-preset)
    - [Trigger events](#field-trigger-events)
    - [Field dependencies](#field-dependencies)
    - [Preventing a field from being submitted](#prevent-field-submission)
- [Extending form behavior](#extend-form-behavior)
    - [Overriding controller action](#overriding-action)
    - [Overriding controller redirect](#overriding-redirect)
    - [Extending form model query](#extend-model-query)
    - [Extending form fields](#extend-form-fields)
    - [Filtering form fields](#filter-form-fields)
- [Validating form fields](#validate-form-fields)

<a name="introduction"></a>
## Introduction

**Le comportement du formulaire** est un modificateur de contrôleur utilisé pour ajouter facilement des fonctionnalités de formulaire à une page back-end. 
Le comportement fournit trois pages appelées Create, Update et Preview. 
La page Preview est une version en lecture seule de la page Update. 
Lorsque vous utilisez le comportement du formulaire, vous n'avez pas besoin de définir les actions  `create`, `update` et `preview` dans le contrôleur - le comportement le fait pour vous. 
Cependant, vous devez fournir les fichiers de vue correspondants.

Le comportement du formulaire dépend de la forme [définitions des champs](#form-fields) et d'un [modèle de classe](../database/model). 
Afin d'utiliser le comportement du formulaire, vous devez l'ajouter à la propriété `$implement` de la classe de contrôleur. 
De plus, la propriété `$formConfig` de la classe doit être définie et sa valeur doit se référer au fichier YAML utilisé pour configurer les options de comportement.

    namespace Acme\Blog\Controllers;

    class Categories extends \Backend\Classes\Controller
    {
        public $implement = ['Backend.Behaviors.FormController'];

        public $formConfig = 'form_config.yaml';
    }

> **Remarque** : Très souvent, la formulaire et le [comportement de liste](lists) sont utilisés ensemble dans un même contrôleur.

<a name="configuring-form"></a>
## Configurer le comportement du formulaire

Le fichier de configuration mentionné dans la propriété  `$formConfig` est défini au format YAML.
Le fichier doit être placé dans le [répertoire views](controllers-views-ajax/#introduction) du contrôleur.
Voici un exemple de fichier de configuration du comportement d'un formulaire typique :

    # =============================================
    #  Configuration du comportement du formulaire
    # =============================================

    name: Blog Category
    form: $/acme/blog/models/post/fields.yaml
    modelClass: Acme\Blog\Post

    create:
        title: New Blog Post

    update:
        title: Edit Blog Post

    preview:
        title: View Blog Post

Les champs suivants sont obligatoires dans le fichier de configuration du formulaire :

Champ | Description
------------- | -------------
**name** | le nom de l'objet géré par ce formulaire
**form** | un tableau de configuration ou une référence à un fichier de définition de champ de formulaire, voir [champs de formulaire](#form-fields)
**modelClass** | un nom de classe modèle, les données du formulaire sont chargées et sauvegardées par rapport à ce modèle

Les options de configuration énumérées ci-dessous sont facultatives. 
Définissez-les si vous souhaitez que le comportement du formulaire prenne en charge les pages [Create](#form-create-page), [Update](#form-update-page) or [Preview](#form-preview-page) pages.

Option | Description
------------- | -------------
**defaultRedirect** | utilisé comme page de redirection par défaut lorsqu'aucune page de redirection spécifique n'est définie
**create** | un tableau de configuration ou une référence à un fichier de configuration pour la page Créer
**update** | un tableau de configuration ou une référence à un fichier de configuration pour la page de mise à jour
**preview** | un tableau de configuration ou une référence à un fichier de configuration pour la page d'aperçu

<a name="form-create-page"></a>
### Créer une page

Pour prendre en charge la page Créer, ajoutez la configuration suivante au fichier YAML :

    create:
        title: New Blog Post
        redirect: acme/blog/posts/update/:id
        redirectClose: acme/blog/posts
        flashSave: Post has been created!

Les options de configuration suivantes sont prises en charge pour la page Créer :

Option | Description
------------- | -------------
**title** | un titre de page, peut faire référence à une [chaîne de localisation](../plugin/localization)
**redirect** | page de redirection lorsque l'enregistrement est sauvegardé
**redirectClose** | page de redirection lorsque l'enregistrement est sauvegardé et que la variable postale **close** est envoyée avec la demande
**flashSave** | message flash à afficher lorsque l'enregistrement est sauvegardé, peut se référer à une [chaîne de localisation](../plugin/localization)
**form** | remplace les définitions des champs de formulaire par défaut pour la page de création uniquement

<a name="form-update-page"></a>
### Mettre à jour une page

Pour prendre en charge la page de mise à jour, ajoutez la configuration suivante au fichier YAML :

    update:
        title: Edit Blog Post
        redirect: acme/blog/posts
        flashSave: Post updated successfully!
        flashDelete: Post has been deleted.

Les options de configuration suivantes sont prises en charge pour la page de mise à jour :

Option | Description
------------- | -------------
**title** | un titre de page, peut faire référence à une [chaîne de localisation](../plugin/localization)
**redirect** | page de redirection lorsque l'enregistrement est sauvegardé
**redirectClose** | page de redirection lorsque l'enregistrement est sauvegardé et que la variable postale **close** est envoyée avec la demande
**flashSave** | message flash à afficher lorsque l'enregistrement est sauvegardé, peut se référer à une [chaîne de localisation](../plugin/localization)
**flashDelete** | message flash à afficher lorsque l'enregistrement est supprimé, peut faire référence à une [chaîne de localisation](../plugin/localization)
**form** | remplace les définitions des champs de formulaire par défaut pour la page de mise à jour uniquement

<a name="form-preview-page"></a>
### Aperçu de la page

Pour prendre en charge la page de prévisualisation, ajoutez la configuration suivante au fichier YAML :

    preview:
        title: View Blog Post

Les options de configuration suivantes sont prises en charge pour la page d'aperçu:

Option  | Description
------------- | -------------
**title** | un titre de page, peut faire référence à une [chaîne de localisation](../plugin/localization).
**form** | remplace les définitions des champs de formulaire par défaut pour la page aperçu uniquement

<a name="form-fields"></a>
## Définition des champs du formulaire

Les champs du formulaire sont définis avec le fichier YAML.
La configuration des champs de formulaire est utilisée par le comportement du formulaire pour créer les contrôles de formulaire et les lier aux champs modèles. 
Le fichier est placé dans un sous-répertoire du répertoire **models** d'un plugin. 
Le nom du sous-répertoire correspond au nom de la classe du modèle écrit en minuscules.  
Le nom du fichier n'a pas d'importance, mais **fields.yaml** et **form_fields.yaml** sont des noms communs.  
Exemple d'emplacement de fichier des champs de formulaire :

    plugins/
      acme/
        blog/
          models/            <=== Plugin models directory
            post/            <=== Model configuration directory
              fields.yaml    <=== Model form fields config file
            Post.php         <=== model class

Les champs peuvent être placés dans trois zones, la **zone extérieure**, les **onglets primaires** ou les **onglets secondaires**. 
L'exemple suivant montre le contenu typique d'un fichier de définition des champs de formulaire.

    # =======================================
    #  Définitions des champs du formulaire
    # =======================================

    fields:
        blog_title:
            label: Blog Title
            description: The title for this blog

        published_at:
            label: Published date
            description: When this blog post was published
            type: datepicker

        [...]

    tabs:
        fields:
            [...]

    secondaryTabs:
        fields:
            [...]

Les champs des modèles liés peuvent être rendus avec le [Widget de relation](#widget-relation) ou le [Gestionnaire de relation](relations#relationship-types). 
L'exception est un champ lié à OneToOne ou morphOne, qui doit être défini comme **relation[field]** 
et peut ensuite être spécifié comme n'importe quel autre champ du modèle :

        user_name:
            label: User Name
            description: The name of the user
        avatar[name]:
            label: Avatar
            description: will be saved in the Avatar table
        published_at:
            label: Published date
            description: When this blog post was published
            type: datepicker

        [...]

<a name="form-tab-options"></a>
### Options des onglets

Pour chaque définition d'onglet, à savoir les  `tabs` et les `secondaryTabs`, vous pouvez spécifier les options suivantes :

Option | Description
------------- | -------------
**stretch** | précise si cette languette s'étire pour s'adapter à la taille du parent
**defaultTab** | l'onglet par défaut pour l'attribution des champs. Par défaut : Misc
**icons** | attribuer des icônes aux onglets en utilisant les noms des onglets comme clé
**lazy** | un tableau d'onglets à charger dynamiquement lorsqu'on clique dessus. Utile pour les onglets qui contiennent une grande quantité de contenu
**cssClass** | assigne une classe CSS au conteneur de l'onglet
**paneCssClass** | attribue une classe CSS à un onglet individuel. La valeur est un tableau, la clé est l'index ou le libellé de l'onglet, la valeur est la classe CSS. Elle peut également être spécifiée comme une chaîne de caractères, auquel cas la valeur sera appliquée à tous les onglets

> **Remarque** : Il n'est pas recommandé d'utiliser un chargement lazy sur les onglets dont les champs sont affectés par des déclencheurs

    tabs:
        stretch: true
        defaultTab: User
        cssClass: text-blue
        lazy:
            - Groups
        paneCssClass:
            0: first-tab
            1: second-tab
        icons:
            User: icon-user
            Groups: icon-group

        fields:
            username:
                type: text
                label: Username
                tab: User

            groups:
                type: relation
                label: Groups
                tab: Groups

<a name="form-field-options"></a>
### Options des champs

Pour chaque champ, vous pouvez spécifier ces options (le cas échéant) :

Option | Description
------------- | -------------
**label** | un nom lors de l'affichage du champ du formulaire à l'utilisateur.
**type** | définit comment ce champ doit être rendu (voir [Types de champs disponibles](#field-types) ci-dessous). Default: text.
**span** | aligne le champ du formulaire sur un côté. Options : auto, gauche, droite, tempête, plein. Valeur par défaut : full. Le paramètre "storm" permet d'afficher le formulaire sous forme de grille, en utilisant la propriété "cssClass", par exemple, "cssClass : col-xs-4"
**size** | spécifie une taille de champ pour les champs qui l'utilisent, par exemple, le champ textarea. Options : tiny, small, large, huge, giant
**placeholder** | si le champ supporte une valeur placeholder
**comment** | place un commentaire descriptif sous le champ
**commentAbove** | place un commentaire au-dessus du champ
**commentHtml** | autoriser le balisage HTML à l'intérieur du commentaire. Options: true, false.
**default** | spécifier la valeur par défaut du champ. Pour les widgets "drop-down", "checkboxlist", "radio" et "balloon-selector", vous pouvez spécifier ici une clé d'option pour qu'elle soit sélectionnée par défaut
**defaultFrom** | prend la valeur par défaut de la valeur d'un autre champ
**tab** | assigne le champ à un onglet
**cssClass** | attribue une classe CSS au conteneur de champ
**readOnly** | empêche la modification du champ. Options: true, false.
**disabled** | empêche la modification du champ et l'exclut des données sauvegardées. Options: true, false.
**hidden** | cache le champ de la vue et l'exclut des données sauvegardées. Options: true, false.
**stretch** | précise si ce champ s'étend pour s'adapter à la taille du parent
**context** | précise le contexte à utiliser pour l'affichage du champ. Le contexte peut également être transmis en utilisant un symbole "@ " dans le nom du champ, par exemple, " nom@mise à jour "
**dependsOn** | un tableau de noms d'autres champs ce champ [dépend de](#field-dependencies), lorsque les autres champs sont modifiés, ce champ sera mis à jour
**trigger** | spécifier les conditions pour ce champ en utilisant [événements déclencheurs](#field-trigger-events)
**preset** | permet à la valeur du champ d'être initialement fixée par la valeur d'un autre champ, convertie à l'aide du [convertisseur de préréglage d'entrée](#field-input-preset)
**required** | place un astérisque rouge à côté de l'étiquette du champ pour indiquer qu'il est obligatoire (veillez à configurer la validation sur le modèle car cela n'est pas appliqué par le contrôleur de formulaires)
**attributes** | spécifier des attributs HTML personnalisés à ajouter à l'élément de champ de formulaire
**containerAttributes** | spécifier des attributs HTML personnalisés à ajouter au conteneur de champs de formulaire
**permissions** | les [permissions](users#users-and-permissions) que l'utilisateur backend actuel doit avoir pour que le champ soit utilisé. Prend en charge soit une chaîne pour une seule permission, soit un ensemble de permissions dont une seule est nécessaire pour accorder l'accès

<a name="field-types"></a>
## Types de champs disponibles

Il existe différents types de champs natifs qui peuvent être utilisés pour le paramètre **type**. Pour les champs de formulaire plus avancés, 
un [widget de formulaire](#form-widgets) peut être utilisé à la place.

<style>
    .collection-method-list {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        column-gap: 2em; -moz-column-gap: 2em; -webkit-column-gap: 2em;
    }

    .collection-method-list a {
        display: block;
    }
</style>

<div class="content-list collection-method-list" markdown="1">
- [Texte](#field-text)
- [Nombre](#field-number)
- [Mot de passe](#field-password)
- [E-mail](#field-email)
- [Zone de texte multi-ligne](#field-textarea)
- [Liste déroulante](#field-dropdown)
- [Liste Radio](#field-radio)
- [Sélecteur de ballons](#field-balloon)
- [Case à cocher](#field-checkbox)
- [Liste de Case à cocher](#field-checkboxlist)
- [Passer à](#field-switch)
- [Section](#field-section)
- [Partiel](#field-partial)
- [Indice](#field-hint)
- [Widget](#field-widget)
</div>

<a name="field-text"></a>
### Texte

`text` - rend une zone de texte d'une seule ligne. C'est le type utilisé par défaut si aucun n'est spécifié.

    blog_title:
        label: Blog Title
        type: text

<a name="field-number"></a>
### Nombre

`number` - rend une boîte de texte d'une seule ligne qui ne prend que des chiffres.

    your_age:
        label: Your Age
        type: number
        step: 1  # defaults to 'any'
        min: 1   # defaults to not present
        max: 100 # defaults to not present

Si vous souhaitez valider ce champ côté serveur lors de la sauvegarde pour vous assurer qu'il est numérique,
 veuillez utiliser la propriété `$rules` de votre modèle, comme cela :
 
    /**
     * @var array Validation rules
     */
    public $rules = [
        'your_age' => 'numeric',
    ];

Pour plus d'informations sur la validation des modèles, veuillez consulter [la page de documentation](https://octobercms.com/docs/services/validation#rule-numeric).

<a name="field-password"></a>
### Mot de passe

`password ` - rend un champ de mot de passe d'une seule ligne.

    user_password:
        label: Password
        type: password

<a name="field-email"></a>
### E-mail

`email` - rend une boîte de texte d'une seule ligne avec le type `email`, déclenchant un clavier spécialisé dans le courrier électronique dans les navigateurs mobiles.

    user_email:
        label: Email Address
        type: email

Si vous souhaitez valider ce champ lors de la sauvegarde pour vous assurer qu'il s'agit d'une adresse électronique correctement formatée, veuillez utiliser la propriété `$rules` de votre modèle, comme cela :

    /**
     * @var array Validation rules
     */
    public $rules = [
        'user_email' => 'email',
    ];

Pour plus d'informations sur la validation des modèles, veuillez consulter [la page de documentation](https://octobercms.com/docs/services/validation#rule-email)

<a name="field-textarea"></a>
### Zone de texte multi-ligne

`textarea` - rend une zone de texte multiligne. 
Une taille peut également être spécifiée avec des valeurs possibles: tiny, small, large, huge, giant

    blog_contents:
        label: Contents
        type: textarea
        size: large

<a name="field-dropdown"></a>
### Liste déroulante

`dropdown` - affiche un menu déroulant avec des options spécifiques.
Il y a 4 façons de fournir les options de la liste déroulante.
La première méthode définit les `options` directement dans le fichier YAML :

    status_type:
        label: Blog Post Status
        type: dropdown
        default: published
        options:
            draft: Draft
            published: Published
            archived: Archived

La deuxième méthode définit des options avec une méthode déclarée dans la classe du modèle. 
Si l'élément options est omis, le cadre s'attend à ce qu'une méthode portant le nom `get*FieldName*Options` soit définie dans le modèle. 
En utilisant l'exemple ci-dessus, le modèle devrait avoir la méthode `getStatusTypeOptions`. 
Le premier argument de cette méthode est la valeur actuelle de ce champ et le second est l'objet de données actuel pour l'ensemble du formulaire. 
Cette méthode doit retourner un tableau d'options au format **key => label**.

    status_type:
        label: Blog Post Status
        type: dropdown

Fournir les options du menu déroulant dans la classe modèle :

    public function getStatusTypeOptions($value, $formData)
    {
        return ['all' => 'All', ...];
    }

La troisième méthode globale `getDropdownOptions` peut également être définie dans le modèle, elle sera utilisée pour tous les types de champs déroulants du modèle. 
Le premier argument de cette méthode est le nom du champ, le deuxième est la valeur actuelle du champ, et le troisième est l'objet de données actuel pour l'ensemble du formulaire.
Elle doit renvoyer un tableau d'options au format  **key => label**.

    public function getDropdownOptions($fieldName, $value, $formData)
    {
        if ($fieldName == 'status') {
            return ['all' => 'All', ...];
        }
        else {
            return ['' => '-- none --'];
        }
    }

La quatrième méthode utilise une méthode spécifique déclarée dans la classe du modèle.  
Dans l'exemple suivant, la méthode `listStatuses` doit être définie dans la classe du modèle.  
Cette méthode reçoit les mêmes arguments que la méthode `getDropdownOptions`, et doit retourner un tableau d'options au format **key => label**.

    status:
        label: Blog Post Status
        type: dropdown
        options: listStatuses

Fournir les options du menu déroulant à la classe modèle :

    public function listStatuses($fieldName, $value, $formData)
    {
        return ['published' => 'Published', ...];
    }

Pour définir le comportement lorsqu'il n'y a pas de sélection, vous pouvez spécifier une valeur `emptyOption` pour inclure une option vide qui peut être resélectionnée.

    status:
        label: Blog Post Status
        type: dropdown
        emptyOption: -- no status --

Vous pouvez également utiliser l'option `placeholder` pour utiliser une option vide "à sens unique" qui ne peut pas être resélectionnée.

    status:
        label: Blog Post Status
        type: dropdown
        placeholder: -- select a status --

Par défaut, la liste déroulante comporte une fonction de recherche, permettant de sélectionner rapidement une valeur. 
Cette fonction peut être désactivée en réglant l'option `showSearch` sur `false`.

    status:
        label: Blog Post Status
        type: dropdown
        showSearch: false

<a name="field-radio"></a>
### Liste Radio

`radio` - rend une liste d'options radio, où un seul élément peut être sélectionné à la fois.

    security_level:
        label: Access Level
        type: radio
        default: guests
        options:
            all: All
            registered: Registered only
            guests: Guests only

Les listes de radio peuvent également soutenir une description secondaire.

    security_level:
        label: Access Level
        type: radio
        options:
            all: [All, Guests and customers will be able to access this page.]
            registered: [Registered only, Only logged in member will be able to access this page.]
            guests: [Guests only, Only guest users will be able to access this page.]

Les listes de radio proposent trois façons de définir les options, exactement comme le [type de champ déroulant](#field-dropdown).
Pour les listes radio, la méthode peut retourner soit le tableau simple : **key => value** ou un tableau de tableaux pour fournir les descriptions : **key => [label, description]**. 
Les options peuvent être affichées en ligne les unes avec les autres au lieu de lignes séparées en spécifiant `cssClass: 'inline-options'` dans la configuration du champ radio.

<a name="field-balloon"></a>
### Balloon Selector

`balloon-selector` - renders a list, where only one item can be selected at a time.

    gender:
        label: Gender
        type: balloon-selector
        default: female
        options:
            female: Female
            male: Male

Balloon selectors support three ways of defining the options, exactly like the [dropdown field type](#field-dropdown).

<a name="field-checkbox"></a>
### Checkbox

`checkbox` - renders a single checkbox.

    show_content:
        label: Display content
        type: checkbox
        default: true

<a name="field-checkboxlist"></a>
### Checkbox List

`checkboxlist` - renders a list of checkboxes.

    permissions:
        label: Permissions
        type: checkboxlist
        # set to true to explicitly enable the "Select All", "Select None" options
        # on lists that have <=10 items (>10 automatically enables it)
        quickselect: true
        default: open_account
        options:
            open_account: Open account
            close_account: Close account
            modify_account: Modify account

Checkbox lists support three ways of defining the options, exactly like the [dropdown field type](#field-dropdown) and also support secondary descriptions, found in the [radio field type](#field-radio). Options can be displayed inline with each other instead of in separate rows by specifying `cssClass: 'inline-options'` on the checkboxlist field config.

<a name="field-switch"></a>
### Switch

`switch` - renders a switchbox.

    show_content:
        label: Display content
        type: switch
        comment: Flick this switch to display content
        on: myauthor.myplugin::lang.models.mymodel.show_content.on
        off: myauthor.myplugin::lang.models.mymodel.show_content.off

<a name="field-section"></a>
### Section

`section` - renders a section heading and subheading. The `label` and `comment` values are optional and contain the content for the heading and subheading.

    user_details_section:
        label: User details
        type: section
        comment: This section contains details about the user.

<a name="field-partial"></a>
### Partial

`partial` - renders a partial, the `path` value can refer to a partial view file otherwise the field name is used as the partial name. Inside the partial these variables are available: `$value` is the default field value, `$model` is the model used for the field and `$field` is the configured class object `Backend\Classes\FormField`.

    content:
        type: partial
        path: $/acme/blog/models/comments/_content_field.htm

<a name="field-hint"></a>
### Hint

`hint` - identical to a `partial` field but renders inside a hint container that can be hidden by the user.

    content:
        type: hint
        path: content_field

<a name="field-widget"></a>
### Widget

`widget` - renders a custom form widget, the `type` field can refer directly to the class name of the widget or the registered alias name.

    blog_content:
        type: Backend\FormWidgets\RichEditor
        size: huge

<a name="form-widgets"></a>
## Form widgets

There are various form widgets included as standard, although it is common for plugins to provide their own custom form widgets. You can read more on the [Form Widgets](widgets#form-widgets) article.

<div class="content-list collection-method-list" markdown="1">
- [Code editor](#widget-codeeditor)
- [Color picker](#widget-colorpicker)
- [Data table](#widget-datatable)
- [Date picker](#widget-datepicker)
- [File upload](#widget-fileupload)
- [Markdown editor](#widget-markdowneditor)
- [Media finder](#widget-mediafinder)
- [Nested Form](#widget-nestedform)
- [Record finder](#widget-recordfinder)
- [Relation](#widget-relation)
- [Repeater](#widget-repeater)
- [Rich editor / WYSIWYG](#widget-richeditor)
- [Tag list](#widget-taglist)
</div>

<a name="widget-codeeditor"></a>
### Code editor

`codeeditor` - renders a plaintext editor for formatted code or markup. Note the options may be inherited by the code editor preferences defined for the Administrator in the back-end.

    css_content:
        type: codeeditor
        size: huge
        language: html

Option | Description
------------- | -------------
**language** | code language, for example, php, css, js, html. Default: php.
**showGutter** | shows a gutter with line numbers. Default: true.
**wrapWords** | breaks long lines on to a new line. Default true.
**fontSize** | the text font size. Default: 12.

<a name="widget-colorpicker"></a>
### Color picker
`colorpicker` - renders controls to select a hexadecimal color value.

    color:
        label: Background
        type: colorpicker

Option | Description
------------- | -------------
**availableColors** |  list of available colors.
**allowEmpty** | allows empty input value. Default: false

There are two ways to provide the available colors for the colorpicker. The first method defines the `availableColors` directly as a list of hex color codes in the YAML file:

    color:
        label: Background
        type: colorpicker
        availableColors: ['#000000', '#111111', '#222222']

The second method uses a specific method declared in the model's class.  This method should return an array of hex colors in the same format as in the example above. The first argument of this method is the field name, the second is the currect value of the field, and the third is the current data object for the entire form.

    color:
        label: Background
        type: colorpicker
        availableColors: myColorList

Supplying the available colors in the model class:

    public function myColorList($fieldName, $value, $formData)
    {
        return ['#000000', '#111111', '#222222']
    }

If the `availableColors` field in not defined in the YAML file, the colorpicker uses a set of 20 default colors.

<a name="widget-datatable"></a>
### Data table

`datatable` - renders an editable table of records, formatted as a grid. Cell content can be editable directly in the grid, allowing for the management of several rows and columns of information.

> **Remarque** : In order to use this with a model, the field should be defined as a `jsonable` attribute, or as another attribute that can handle storing arrayed data.

    data:
        type: datatable
        adding: true
        btnAddRowLabel: Add Row Above
        btnAddRowBelowLabel: Add Row Below
        btnDeleteRowLabel: Delete Row
        columns: []
        deleting: true
        dynamicHeight: true
        fieldName: null
        height: false
        keyFrom: id
        recordsPerPage: false
        searching: false
        toolbar: []

#### Table configuration

The following lists the configuration values of the data table widget itself.

Option | Description
------ | -----------
**adding** | allow records to be added to the data table. Default: `true`.
**btnAddRowLabel** | defines a custom label for the "Add Row Above" button.
**btnAddRowBelowLabel** | defines a custom label for the "Add Row Below" button.
**btnDeleteRowLabel** | defines a custom label for the "Delete Row" button.
**columns** | an array representing the column configuration of the data table. See the *Column configuration* section below.
**deleting** | allow records to be deleted from the data table. Default: `false`.
**dynamicHeight** | if `true`, the data table's height will extend or shrink depending on the records added, up to the maximum size defined by the `height` configuration value. Default: `false`.
**fieldName** | defines a custom field name to use in the POST data sent from the data table. Leave blank to use the default field alias.
**height** | the data table's height, in pixels. If set to `false`, the data table will stretch to fit the field container.
**keyFrom** | the data attribute to use for keying each record. This should usually be set to `id`.
**postbackHandlerName** | specifies the AJAX handler name in which the data table content will be sent with. When set to `null` (default), the handler name will be auto-detected from the request name used by the form which contains the data table. It is recommended to keep this as `null`.
**recordsPerPage** | the number of records to show per page. If set to `false`, the pagination will be disabled.
**searching** | allow records to be searched via a search box. Default: `false`.
**toolbar** | an array representing the toolbar configuration of the data table.

#### Column configuration

The data table widget allows for the specification of columns as an array via the `columns` configuration variable. Each column should use the field name as a key, and the following configuration variables to set up the field.

Example:

    columns:
        id:
            type: string
            title: ID
            validation:
                integer:
                    message: Please enter a number
        name:
            type: string
            title: Name


Option | Description
------ | -----------
**type** | the input type for this column's cells. Must be one of the following: `string`, `checkbox`, `dropdown` or `autocomplete`.
**options** | for `dropdown` and `autocomplete` columns only - this specifies the AJAX handler that will return the available options, as an array. The array key is used as the value of the option, and the array value is used as the option label.
**readOnly** | whether this column is read-only. Default: `false`.
**title** | defines the column's title.
**validation** | an array specifying the validation for the content of the column's cells. See the *Column validation* section below.
**width** | defines the width of the column, in pixels.

#### Column validation

Column cells can be validated against the below types of validation. Validation should be specified as an array, with the type of validation used as a key, and an optional message specified as the `message` attrbute for that validation.

Validation | Description
---------- | -----------
**float** | Validates the data as a float. An optional boolean `allowNegative` attribute can be provided, allowing for negative float numbers.
**integer** | Validates the data as an integer. An optional boolean `allowNegative` attribute can be provided, allowing for negative integers.
**length** | Validates the data to be of a certain length. An integer `min` and `max` attribute must be provided, representing the minimum and maximum number of characters that must be entered.
**regex** | Validates the data against a regular expression. A string `pattern` attribute must be provided, defining the regular expression to test the data against.
**required** | Validates that the data must be entered before saving.

<a name="widget-datepicker"></a>
### Date picker

`datepicker` - renders a text field used for selecting date and times.

    published_at:
        label: Published
        type: datepicker
        mode: date

Option | Description
------------- | -------------
**mode** | the expected result, either date, datetime or time. Default: datetime.
**format** | provide an explicit date display format. Eg: Y-m-d
**minDate** | the minimum/earliest date that can be selected. Default: 2000-01-01.
**maxDate** | the maximum/latest date that can be selected. Default: 2020-12-31.
**firstDay** | the first day of the week. Default: 0 (Sunday).
**showWeekNumber** | show week numbers at head of row. Default: false
**ignoreTimezone** | store date and time exactly as it is displayed, ignoring the backend specified timezone preference.

<a name="widget-fileupload"></a>
### File upload

`fileupload` - renders a file uploader for images or regular files. **The field name must use an attachOne or attachMany relation.**

    avatar:
        label: Avatar
        type: fileupload
        mode: image
        imageHeight: 260
        imageWidth: 260
        thumbOptions:
            mode: crop
            offset:
                - 0
                - 0
            quality: 90
            sharpen: 0
            interlace: false
            extension: auto

Option | Description
------------- | -------------
**mode** | the expected file type, either file or image. Default: image.
**imageWidth** | if using image type, the image will be resized to this width, optional.
**imageHeight** | if using image type, the image will be resized to this height, optional.
**fileTypes** | file extensions that are accepted by the uploader, optional. Eg: `zip,txt`
**mimeTypes** | MIME types that are accepted by the uploader, either as file extension or fully qualified name, optional. Eg: `bin,txt`
**maxFilesize** | file size in Mb that are accepted by the uploader, optional. Default: from "upload_max_filesize" param value
**useCaption** | allows a title and description to be set for the file. Default: true
**prompt** | text to display for the upload button, applies to files only, optional.
**thumbOptions** | options to pass to the thumbnail generating method for the file
**attachOnUpload** | Automatically attaches the uploaded file on upload if the parent record exists instead of using deferred binding to attach on save of the parent record. Defaults to false.

<a name="widget-markdowneditor"></a>
### Markdown editor

`markdown` - renders a basic editor for markdown formatted text.

    md_content:
        type: markdown
        size: huge
        mode: split

Option | Description
------------- | -------------
**mode** | the expected view mode, either tab or split. Default: tab.

<a name="widget-mediafinder"></a>
### Media finder

`mediafinder` - renders a field for selecting an item from the media manager library. Expanding the field displays the media manager to locate a file. The resulting selection is a string as the relative path to the file.

    background_image:
        label: Background image
        type: mediafinder
        mode: image

Option | Description
------------- | -------------
**mode** | the expected file type, either file or image. Default: file.
**prompt** | text to display when there is no item selected. The `%s` character represents the media manager icon.
**imageWidth** | if using image type, the preview image will be displayed to this width, optional.
**imageHeight** | if using image type, the preview image will be displayed to this height, optional.

> **Remarque** : Unlike the [File Upload form widget](#widget-fileupload), the Media Finder form widget stores its data as a string representing the path to the image selected within the Media Library.

<a name="widget-nestedform"></a>
### Nested Form
`nestedform` - renders a nested form as the contents of this field, returns data as an array of the fields contained.

> **Remarque** : In order to use this with a model, the field should be defined as a `jsonable` attribute, or as another attribute that can handle storing arrayed data.

    content:
        type: nestedform
        usePanelStyles: false
        form:
            fields:
                added_at:
                    label: Date added
                    type: datepicker
                details:
                    label: Details
                    type: textarea
                title:
                    label: This the title
                    type: text
            tabs:
                meta_title:
                    lable: Meta Title
                    tab: SEO
                color:
                    label: Color
                    type: colorpicker
                    tab: Design
            secondaryTabs:
                is_active:
                    label: Active
                    type: checkbox
                logo:
                    label: Logo
                    type: mediafinder
                    mode: image

A nested form supports the same syntax as a form itself, including tabs and secondaryTabs. The jsonsable attribute, has the structure of your form definition. It's even possible to use nested forms inside a nested form.

Option | Description
------------- | -------------
**form**  | same as in [form definition](#form-fields)
**usePanelStyles** | defines if a panel like look is applied or not (defaults true)

<a name="widget-recordfinder"></a>
### Record finder

`recordfinder` - renders a field with details of a related record. Expanding the field displays a popup list to search large amounts of records. Supported by singular relationships only.

    user:
        label: User
        type: recordfinder
        list: ~/plugins/rainlab/user/models/user/columns.yaml
        recordsPerPage: 10
        title: Find Record
        prompt: Click the Find button to find a user
        keyFrom: id
        nameFrom: name
        descriptionFrom: email
        conditions: email = "bob@example.com"
        scope: whereActive
        searchMode: all
        searchScope: searchUsers
        useRelation: false
        modelClass: RainLab\User\Models\User

Option | Description
------------- | -------------
**keyFrom** | the name of column to use in the relation used for key. Default: id.
**nameFrom** | the column name to use in the relation used for displaying the name. Default: name.
**descriptionFrom** | the column name to use in the relation used for displaying a description. Default: description.
**title** | text to display in the title section of the popup.
**prompt** | text to display when there is no record selected. The `%s` character represents the search icon.
**list** | a configuration array or reference to a list column definition file, see [list columns](lists#list-columns).
**recordsPerPage** | records to display per page, use 0 for no pages. Default: 10
**conditions** | specifies a raw where query statement to apply to the list model query.
**scope** | specifies a [query scope method](../database/model#query-scopes) defined in the **related form model** to apply to the list query always. The first argument will contain the model that the widget will be attaching its value to, i.e. the parent model.
**searchMode** | defines the search strategy to either contain all words, any word or exact phrase. Supported options: all, any, exact. Default: all.
**searchScope** | specifies a [query scope method](../database/model#query-scopes) defined in the **related form model** to apply to the search query, the first argument will contain the search term.
**useRelation** | Flag for using the name of the field as a relation name to interact with directly on the parent model. Default: true. Disable to return just the selected model's ID
**modelClass** | Class of the model to use for listing records when useRelation = false

<a name="widget-relation"></a>
### Relation

`relation` - renders either a dropdown or checkbox list according to the field relation type. Singular relationships display a dropdown, multiple relationships display a checkbox list. The label used for displaying each relation is sourced by the `nameFrom` or `select` definition.

    categories:
        label: Categories
        type: relation
        nameFrom: title

Alternatively, you may populate the label using a custom `select` statement. Any valid SQL statement works here.

    user:
        label: User
        type: relation
        select: concat(first_name, ' ', last_name)

You can also provide a model scope to use to filter the results with the `scope` property.

Option | Description
------------- | -------------
**nameFrom** | a model attribute name used for displaying the relation label. Default: name.
**select** | a custom SQL select statement to use for the name.
**order** | an order clause to sort options by. Example: `name desc`.
**emptyOption** | text to display when there is no available selections.
**scope** | specifies a [query scope method](../database/model#query-scopes) defined in the **related form model** to apply to the list query always.

<a name="widget-repeater"></a>
### Repeater

`repeater` - renders a repeating set of form fields defined within.

    extra_information:
        type: repeater
        titleFrom: title_when_collapsed
        form:
            fields:
                added_at:
                    label: Date added
                    type: datepicker
                details:
                    label: Details
                    type: textarea
                title_when_collapsed:
                    label: This field is the title when collapsed
                    type: text

Option | Description
------------- | -------------
**form** | a reference to form field definition file, see [backend form fields](#form-fields). Inline fields can also be used.
**prompt** | text to display for the create button. Default: Add new item.
**titleFrom** | name of field within items to use as the title for the collapsed item.
**minItems** | minimum items required. Pre-displays those items when not using groups. For example if you set **'minItems: 1'** the first row will be displayed and not hidden.
**maxItems** | maximum number of items to allow within the repeater.
**groups** | references a group of form fields placing the repeater in group mode (see below). An inline definition can also be used.
**style** | the behavior style to apply for repeater items. Can be one of the following: `default`, `collapsed` or `accordion`. See the **Repeater styles** section below for more information.

The repeater field supports a group mode which allows a custom set of fields to be chosen for each iteration.

    content:
        type: repeater
        prompt: Add content block
        groups: $/acme/blog/config/repeater_fields.yaml

This is an example of a group configuration file, which would be located in **/plugins/acme/blog/config/repeater_fields.yaml**. Alternatively these definitions could be specified inline with the repeater.

    textarea:
        name: Textarea
        description: Basic text field
        icon: icon-file-text-o
        fields:
            text_area:
                label: Text Content
                type: textarea
                size: large

    quote:
        name: Quote
        description: Quote item
        icon: icon-quote-right
        fields:
            quote_position:
                span: auto
                label: Quote Position
                type: radio
                options:
                    left: Left
                    center: Center
                    right: Right
            quote_content:
                span: auto
                label: Details
                type: textarea

Each group must specify a unique key and the definition supports the following options.

Option | Description
------------- | -------------
**name** | the name of the group.
**description** | a brief description of the group.
**icon** | defines an icon for the group, optional.
**fields** | form fields belonging to the group, see [backend form fields](#form-fields).

> **Remarque** : The group key is stored along with the saved data as the `_group` attribute.

#### Repeater styles

The `styles` attribute of the repeater widget controls the behaviour of repeater items. There are three different types of styles available for developers:

- **default:** Shows all the repeater items as expanded on page load. This is the default current behavior, and will be used if style is not defined in the repeater widget's configuration.
- **collapsed:** Shows all the repeater items as collapsed (minimised) on page load. The user can collapse or expand items as they wish.
- **accordion:** Shows only the first repeater item as expanded on load, with all others collapsed. When another item is exanded, any other expanded item is collapsed, effectively making it so that only one item is expanded at a time.

<a name="widget-richeditor"></a>
### Rich editor / WYSIWYG

`richeditor` - renders a visual editor for rich formatted text, also known as a WYSIWYG editor.

    html_content:
        type: richeditor
        toolbarButtons: bold|italic
        size: huge

Option | Description
------------- | -------------
**toolbarButtons** | which buttons to show on the editor toolbar.

The available toolbar buttons are:

    fullscreen, bold, italic, underline, strikeThrough, subscript, superscript, fontFamily, fontSize, |, color, emoticons, inlineStyle, paragraphStyle, |, paragraphFormat, align, formatOL, formatUL, outdent, indent, quote, insertHR, -, insertLink, insertImage, insertVideo, insertAudio, insertFile, insertTable, undo, redo, clearFormatting, selectAll, html

> **Remarque** : `|` will insert a vertical separator line in the toolbar and `-` a horizontal one.

<a name="widget-taglist"></a>
### Tag list

`taglist` - renders a field for inputting a list of tags.

    tags:
        type: taglist
        separator: space

A tag list can support three ways of defining the `options`, exactly like the [dropdown field type](#field-dropdown).

    tags:
        type: taglist
        options:
            - Red
            - Blue
            - Orange

You may use the `mode` called **relation** where the field name is a [many-to-many relationship](../database/relations#many-to-many). This will automatically source and assign tags via the relationship. If custom tags are supported, they will be created before assignment.

    tags:
        type: taglist
        mode: relation

Option | Description
------------- | -------------
**mode** | controls how the value is returned, either string, array or relation. Default: string
**separator** | separate tags with the specified character, either comma or space. Default: comma
**customTags** | allows custom tags to be entered manually by the user. Default: true
**options** | specifies a method or array for predefined options. Set to true to use model `get*Field*Options` method. Optional.
**nameFrom** | if relation mode is used, a model attribute name for displaying the tag name. Default: name
**useKey** | use the key instead of value for saving and reading data. Default: false

<a name="form-views"></a>
## Form views

For each page your form supports [Create](#form-create-page), [Update](#form-update-page) and [Preview](#form-preview-page) you should provide a [view file](#introduction) with the corresponding name - **create.htm**, **update.htm** and **preview.htm**.

The form behavior adds two methods to the controller class: `formRender` and `formRenderPreview`. These methods render the form controls configured with the YAML file described above.

<a name="form-create-view"></a>
### Create view

The **create.htm** view represents the Create page that allows users to create new records. A typical Create page contains breadcrumbs, the form itself, and the form buttons. The **data-request** attribute should refer to the `onSave` AJAX handler provided by the form behavior. Below is a contents of the typical create.htm form.

    <?= Form::open(['class'=>'layout']) ?>

        <div class="layout-row">
            <?= $this->formRender() ?>
        </div>

        <div class="form-buttons">
            <div class="loading-indicator-container">
                <button
                    type="button"
                    data-request="onSave"
                    data-request-data="close:true"
                    data-hotkey="ctrl+enter, cmd+enter"
                    data-load-indicator="Creating Category..."
                    class="btn btn-default">
                    Create and Close
                </button>
                <span class="btn-text">
                    or <a href="<?= Backend::url('acme/blog/categories') ?>">Cancel</a>
                </span>
            </div>
        </div>

    <?= Form::close() ?>

<a name="form-update-view"></a>
### Update view

The **update.htm** view represents the Update page that allows users to update or delete existing records. A typical Update page contains breadcrumbs, the form itself, and the form buttons. The Update page is very similar to the Create page, but usually has the Delete button. The **data-request** attribute should refer to the `onSave` AJAX handler provided by the form behavior. Below is a contents of the typical update.htm form.

    <?= Form::open(['class'=>'layout']) ?>

        <div class="layout-row">
            <?= $this->formRender() ?>
        </div>

        <div class="form-buttons">
            <div class="loading-indicator-container">
                <button
                    type="button"
                    data-request="onSave"
                    data-request-data="close:true"
                    data-hotkey="ctrl+enter, cmd+enter"
                    data-load-indicator="Saving Category..."
                    class="btn btn-default">
                    Save and Close
                </button>
                <button
                    type="button"
                    class="oc-icon-trash-o btn-icon danger pull-right"
                    data-request="onDelete"
                    data-load-indicator="Deleting Category..."
                    data-request-confirm="Do you really want to delete this category?">
                </button>
                <span class="btn-text">
                    or <a href="<?= Backend::url('acme/blog/categories') ?>">Cancel</a>
                </span>
            </div>
        </div>

    <?= Form::close() ?>

<a name="form-preview-view"></a>
### Preview view

The **preview.htm** view represents the Preview page that allows users to preview existing records in the read-only mode. A typical Preview page contains breadcrumbs and the form itself. Below is a contents of the typical preview.htm form.

    <div class="form-preview">
        <?= $this->formRenderPreview() ?>
    </div>

<a name="field-conditions"></a>
## Applying conditions to fields

Sometimes you may want to manipulate the value or appearance of a form field under certain conditions, for example, you may want to hide an input if a checkbox is ticked. There are a few ways you can do this, either by using the trigger API or field dependencies. The input preset converter is primarily used to converting field values. These options are described in more detail below.

<a name="field-input-preset"></a>
### Input preset converter

The input preset converter is defined with the `preset` [form field option](#form-field-options) and allows you to convert text entered into an element to a URL, slug or file name value in another input element.

In this example we will automatically fill out the `url` field value when a user enters text in the `title` field. If the text **Hello world** is typed in for the Title, the URL will follow suit with the converted value of **/hello-world**. This behavior will only occur when the destination field (`url`) is empty and untouched.

    title:
        label: Title

    url:
        label: URL
        preset:
            field: title
            type: url

Alternatively, the `preset` value can also be a string that refers to the **field** only, the `type` option will then default to **slug**.

    slug:
        label: Slug
        preset: title

The following options are available for the `preset` option:

Option | Description
------------- | -------------
**field** | defines the other field name to source the value from.
**type** | specifies the conversion type. See below for supported values.
**prefixInput** | optional, prefixes the converted value with the value found in the supplied input element using a CSS selector.

Following are the supported types:

Type | Description
------------- | -------------
**exact** | copies the exact value
**slug** | formats the copied value as a slug
**url** | same as slug but prefixed with a /
**camel** | formats the copied value with camelCase
**file** | formats the copied value as a file name with whitespace replaced with dashes

<a name="field-trigger-events"></a>
### Trigger events

Trigger events are defined with the `trigger` [form field option](#form-field-options) and is a simple browser based solution that uses JavaScript. It allows you to change elements attributes such as visibility or value, based on another elements' state. Here is a sample definition:

    is_delayed:
        label: Send later
        comment: Place a tick in this box if you want to send this message at a later time.
        type: checkbox

    send_at:
        label: Send date
        type: datepicker
        cssClass: field-indent
        trigger:
            action: show
            field: is_delayed
            condition: checked

In the above example the `send_at` form field will only be shown if the `is_delayed` field is checked. In other words, the field will show (action) if the other form input (field) is checked (condition). The `trigger` definition specifies these options:

Option | Description
------------- | -------------
**action** | defines the action applied to this field when the condition is met. Supported values: show, hide, enable, disable, empty.
**field** | defines the other field name that will trigger the action. Normally the field name refers to a field in the same level form. For example, if this field is in a [repeater widget](#widget-repeater), only fields in that same [repeater widget](#widget-repeater) will be checked. However, if the field name is preceded by a caret symbol `^` like: `^parent_field`, it will refer to a [repeater widget](#widget-repeater) or form one level higher than the field itself. Additionally, if more than one caret `^` is used, it will refer that many levels higher: `^^grand_parent_field`, `^^^grand_grand_parent_field`, etc.
**condition** | determines the condition the specified field should satisfy for the condition to be considered "true". Supported values: checked, unchecked, value[somevalue].

<a name="field-dependencies"></a>
### Field dependencies

Form fields can declare dependencies on other fields by defining the `dependsOn` [form field option](#form-field-options) which provides a more robust server side solution for updating fields when their dependencies are modified. When the fields that are declared as dependencies change, the defining field will update using the AJAX framework. This provides an opportunity to interact with the field's properties using the `filterFields` methods or changing available options to be provided to the field. Examples below:

    country:
        label: Country
        type: dropdown

    state:
        label: State
        type: dropdown
        dependsOn: country

In the above example the `state` form field will refresh when the `country` field has a changed value. When this occurs, the current form data will be filled in the model so the dropdown options can use it.

    public function getCountryOptions()
    {
        return ['au' => 'Australia', 'ca' => 'Canada'];
    }

    public function getStateOptions()
    {
        if ($this->country == 'au') {
            return ['act' => 'Capital Territory', 'qld' => 'Queensland', ...];
        }
        elseif ($this->country == 'ca') {
            return ['bc' => 'British Columbia', 'on' => 'Ontario', ...];
        }
    }

This example is useful for manipulating the model values, but it does not have access to the form field definitions. You can filter the form fields by defining a `filterFields` method inside the model, described in the [Filtering form fields](#filter-form-fields) section. An example is provided below:

    dnsprovider:
        label: DNS Provider
        type: dropdown

    registrar:
        label: Registrar
        type: dropdown

    specificfields[for][provider1]:
        label: Provider 1 ID
        type: text
        hidden: true
        dependsOn:
            - dnsprovider
            - registrar

    specificfields[for][provider2]:
        label: Provider 2 ID
        type: text
        hidden: true
        dependsOn:
            - dnsprovider
            - registrar

And the logic for the filterFields method would be as follows:

    public function filterFields($fields, $context = null)
    {
        $displayedVendors = strtolower($this->dnsprovider->name . $this->registrar->name);
        if (str_contains($displayedVendors, 'provider1')) {
            $fields->{'specificfields[for][provider1]'}->hidden = false;
        }
        if (str_contains($displayedVendors, 'provider2')) {
            $fields->{'specificfields[for][provider2]'}->hidden = false;
        }
    }

In the above example, both the `provider1` and `provider2` fields will automatically refresh whenever either the `dnsprovider` or `registrar` fields are modified. When this occurs, the full form cycle will be processed, which means that any logic defined in `filterFields` methods would be run again, allowing you to filter which fields get displayed dynamically.

<a name="prevent-field-submission"></a>
### Preventing a field from being submitted

Sometimes you may need to prevent a field from being submitted. In order to do that, just add an underscore (\_) before the name of the field in the form configuration file. Form fields beginning with an underscore are purged automatically and no longer saved to the model.

    address:
        label: Title
        type: text

    _map:
        label: Point your address on the map
        type: mapviewer

<a name="extend-form-behavior"></a>
## Extending form behavior

Sometimes you may wish to modify the default form behavior and there are several ways you can do this.

<a name="overriding-action"></a>
### Overriding controller action

You can use your own logic for the `create`, `update` or `preview` action method in the controller, then optionally call the Form behavior parent method.

    public function update($recordId, $context = null)
    {
        //
        // Do any custom code here
        //

        // Call the FormController behavior update() method
        return $this->asExtension('FormController')->update($recordId, $context);
    }

<a name="overriding-redirect"></a>
### Overriding controller redirect

You can specify the URL to redirect to after the model is saved by overriding the `formGetRedirectUrl` method. This method returns the location to redirect to with relative URLs being treated as backend URLs.

    public function formGetRedirectUrl($context = null, $model = null)
    {
        return 'https://octobercms.com';
    }

<a name="extend-model-query"></a>
### Extending model query

The lookup query for the form [database model](../database/model) can be extended by overriding the `formExtendQuery` method inside the controller class. This example will ensure that soft deleted records can still be found and updated, by applying the **withTrashed** scope to the query:

    public function formExtendQuery($query)
    {
        $query->withTrashed();
    }

<a name="extend-form-fields"></a>
### Extending form fields

You can extend the fields of another controller from outside by calling the `extendFormFields` static method on the controller class. This method can take three arguments, **$form** will represent the Form widget object, **$model** represents the model used by the form and **$context** is a string containing the form context. Take this controller for example:

    class Categories extends \Backend\Classes\Controller
    {
        public $implement = ['Backend.Behaviors.FormController'];

        public $formConfig = 'form_config.yaml';
    }

Using the `extendFormFields` method you can add extra fields to any form rendered by this controller. Since this has the potential to affect all forms used by this controller, it is a good idea to check the **$model** is of the correct type. Here is an example:

    Categories::extendFormFields(function($form, $model, $context)
    {
        if (!$model instanceof MyModel) {
            return;
        }

        $form->addFields([
            'my_field' => [
                'label'   => 'My Field',
                'comment' => 'This is a custom field I have added.',
            ],
        ]);

    });

You can also extend the form fields internally by overriding the `formExtendFields` method inside the controller class. This will only affect the form used by the `FormController` behavior.

    class Categories extends \Backend\Classes\Controller
    {
        [...]

        public function formExtendFields($form)
        {
            $form->addFields([...]);
        }
    }

The following methods are available on the $form object.

Method | Description
------------- | -------------
**addFields** | adds new fields to the outside area
**addTabFields** | adds new fields to the tabbed area
**addSecondaryTabFields** | adds new fields to the secondary tabbed area
**removeField** | remove a field from any areas

Each method takes an array of fields similar to the [form field configuration](#form-fields).

<a name="filter-form-fields"></a>
### Filtering form fields

You can filter the form field definitions by overriding the `filterFields` method inside the Model used. This allows you to manipulate visibility and other field properties based on the model data. The method takes two arguments **$fields** will represent an object of the fields already defined by the [field configuration](#form-fields) and **$context** represents the active form context.

    public function filterFields($fields, $context = null)
    {
        if ($this->source_type == 'http') {
            $fields->source_url->hidden = false;
            $fields->git_branch->hidden = true;
        }
        elseif ($this->source_type == 'git') {
            $fields->source_url->hidden = false;
            $fields->git_branch->hidden = false;
        }
        else {
            $fields->source_url->hidden = true;
            $fields->git_branch->hidden = true;
        }
    }

The above example will set the `hidden` flag on certain fields by checking the value of the Model attribute `source_type`. This logic will be applied when the form first loads and also when updated by a [defined field dependency](#field-dependencies).

<a name="validate-form-fields"></a>
## Validating form fields

To validate the fields of your form you can make use of the [Validation](../database/traits#validation) trait in your model.
