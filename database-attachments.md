# Fichiers joints

- [Fichiers joints](#file-attachments)
    - [Joindre un nouveau fichier manuellement](#creating-attachments)
    - [Afficher un fichier joint](#viewing-attachments)
    - [Exemple d'utilisation](#attachments-usage-example)
    - [Exemple de validation](#attachments-validation-example)

<a name="file-attachments"></a>

## Fichiers joints

Les modèles supporte l'attachement de fichier basé sur un système interne
de [relation polymorphique](../database/relations#polymorphic-relations). Les relations `$attachOne` ou `$attachMany`
sont conçus de façon à lié un fichier à un enregistrement en base de données appelé "attachement". Dans la plupart des
cas le modèle `System\Models\File` est utilisé pour s'assurer de cette bonne liaison, la liaison de ce modèle stocke
cette relation sur la table `system_files` avec une relation polymorphique.

Dans les exemples ci-dessous, le modèle sera lié à un fichier joint "avatar" et plusieurs photos.

L'attachement d'un seul fichier joint :

    public $attachOne = [
        'avatar' => 'System\Models\File'
    ];

L'attachement de plusieurs fichiers dans une seule relation :

    public $attachMany = [
        'photos' => 'System\Models\File'
    ];

> **Note** : Dans les exemples ci-dessus, le nom de la relation sera utilisé comme n'importe quel champs lors de l'utilisation du widget `fileupload` dans les formulaires.
> Lors de la création d'une relation polymorphique avec entre votre modèle et le modèle `System\Models\File`,
> si une autre colonne portant le même nom que la relation est présent sur la table de votre modèle, un résultat inattendu peut se produire.

Il est possible de créer des fichiers joints "protégés", ceux-ci seront chargés dans le dossier `uploads/protected` de
votre application qui n'est pas accessible de façon publique depuis un navigateur. Un fichier protégé est créé lorsque
l'argument `public` de la relation est défini comme `false`

    public $attachOne = [
        'avatar' => ['System\Models\File', 'public' => false]
    ];

<a name="creating-attachments"></a>

### Joindre un nouveau fichier manuellement

Pour une relation unique `$attachOne`, vous pouvez créer manuellement la jonction à l'aide du nom de la relation, Pour
valeur, vous pouvez utiliser la méthode `Input::file`, qui sers à lire les données depuis un fichier envoyé via un
formulaire.

    $model->avatar = Input::file('file_input');

Vous pouvez aussi, si le fichier est déjà présent sur le serveur, lié directement le fichier en saisissant son chemin
complet.

    $model->avatar = '/chemin/complet/vers/le/fichier.jpg';

Parfois il peut aussi être nécessaire de créer un modèle `File` directement depuis les données binaire.

    $fichier = (new System\Models\File)->fromData('Un fichier texte', 'mon-fichier-texte.txt');

Pour les attachments multiples `$attachMany`, vous pouvez utiliser la fonction `create` sur la relation.

    $model->photos()->create(['data' => Input::file('file_input')]);

Il est aussi possible de préparer le modèle `File`, et de l'associer plus tard. Noté que dans ce cas,
l'attribut `is_public` doit obligatoirement être renseigné.

    $fichier = new System\Models\File;
    $fichier->data = Input::file('file_input');
    $fichier->is_public = true;
    $fichier->save();

    $model->avatar()->add($fichier);

Il est aussi possible de créer le fichier depuis une URL. Afin d'utiliser cette méthode, l'extension PHP cURL doit être
installé sur la machine.

    $fichier = new System\Models\File;
    $fichier->fromUrl('https://example.com/uploads/chemin/public/du/fichier.jpg');

    $utilisateur->avatar()->add($fichier);

Vous pouvez aussi changer le nom du fichier téléchargé, en utilisant le second paramètre de la méthode.

    $fichier->fromUrl('https://example.com/uploads/chemin/public/du/fichier.jpg', 'mon-avatar.jpg');

<a name="viewing-attachments"></a>
### Afficher un fichier joint

La méthode `getPath` retourne l'URL complète d'un fichier public. L'exemple suivant afficher quelque-chose
comme `exemple.com/uploads/public/chemin/du/fichier.jpg`

    echo $model->avatar->getPath();

Utilisation sur un attachement multiple à l'aide d'une boucle

    foreach ($model->photos as $photo) {
        echo $photo->getPath();
    }

La méthode `getLocalPath` retourne le chemin absolu sur le système de fichier.

    echo $model->avatar->getLocalPath();

Vous pouvez afficher directement le contenu du fichier, à l'aide de la méthode `output`. Cette méthode ajoutes les
headers nécessaire au téléchargement de fichier.

    echo $model->avatar->output();

Vous pouvez redimensionner les images directement à l'aide de la méthode `getThumb`. La méthode utilise 3 paramètres :
largeur, hauteur et des options, les options suivantes sont possibles :

Option | Description
------------- | -------------
**mode** | auto, exact, portrait, landscape, crop, fit. Par défaut : auto
**quality** | 0 - 100. Par défaut : 90
**interlace** | booléen: false (default), true
**extension** | auto, jpg, png, webp, gif. Par défaut : jpg

Les paramètres de largeur et hauteur, respectivement `width` et `height` doivent être renseignés en tant que nombre ou avec la chaîne de caractères `auto` pour un redimensionnement automatique.

    echo $model->avatar->getThumb(100, 100, ['mode' => 'crop']);

Afficher une image sur une page :

    <img src="{{ model.avatar.getThumb(100, 100, {'mode':'exact', 'quality': 80, 'extension': 'webp'}) }}" alt="Description de l'image" />

#### Les différents modes de redimensionnement

L'option `mode` permet de spécifier la façon doit être redimensionné. Voici les différentes valeurs possibles :

* `auto` choisis le mode `portrait` ou `landscape` selon l'orientation de la photo
* `exact` redimensionne l'image à la largeur et hauteur précise défini en paramètre, sans tenir compte du ratio de la photo existante
* `portrait` redimensionne à la hauteur et adapte la largeur pour ne pas déformer l'image
* `landscape` redimensionne à la largeur et adapte la hauteur pour ne pas déformer l'image
* `crop`  découpe l'image après avoir réduit au maximum l'image
* `fit` intègre l'image dans les dimensions maximales données tout en préservant l'aspect de l'image

<a name="attachments-usage-example"></a>
### Exemple d'utilisation
Cette section montre des exemples d'utilisation complets du système de fichier-joint.
Depuis la définition de la relation jusqu'à l'affichage d'une image téléverser sur une page.

À l'intérieur d'un modèle, définissez la relation vers la classe, `System\Models\File` :

    class Post extends Model
    {
        public $attachOne = [
            'image_mise_en_avant' => 'System\Models\File'
        ];
    }

Créez un formulaire pour téléverser un fichier :

    <?= Form::open(['files' => true]) ?>

        <input name="fichier_joint" type="file">

        <button type="submit">Upload File</button>

    <?= Form::close() ?>

Procédez au téléversement du fichier sur le serveur et joignez-le au modèle :

    // Trouve le modèle Article
    $article = Article::find(1);

    // Sauvegarde l'image mise en avant du modèle Article
    if (Input::hasFile('fichier_joint')) {
        $article->image_mise_en_avant = Input::file('fichier_joint');
    }

Optionnellement, vous pouvez aussi utiliser [l'association différé](../database/relations#deferred-binding) pour différer la relation :

    // Trouve le modèle Article
    $article = Post::find(1);

    // Cherche les données transmisent depuis le champs 'fichier_joint' du HTML précédent
    $imageDeLArticle = Input::file('fichier_joint');

    // S'il existe, sauvegarde l'association différé en utilisant une clé de session
    if ($imageDeLArticle) {
        $article->image_mise_en_avant()->create(['data' => $imageDeLArticle], $sessionKey);
    }

Affichez l'image téléversée sur une page : 

    // Trouve le modèle Article
    $article = Post::find(1);

    // Recherche une image mise en avant, sinon utilise une par défaut
    if ($article->image_mise_en_avant) {
        $imageMiseEnAvant = $article->image_mise_en_avant->getPath();
    }
    else {
        $imageMiseEnAvant = 'http://placehold.it/220x300';
    }

    <img src="<?= $imageMiseEnAvant ?>" alt="Image mise en avant">

Si vous avez besoin de connaître le propriétaire d'un fichier, vous pouvez utiliser la propriété `attachment` du modèle `File` :

    public $morphTo = [
        'attachment' => []
    ];

Exemple :

    $utilisateur = $fichier->attachment;

Pour plus d'information, lisez la section sur les [relations polymorphiques](../database/relations#polymorphic-relations)

<a name="attachments-validation-example"></a>

### Exemple de validation

L'exemple ci-dessous utiliser la [validation de tableau](../services/validation#validating-arrays) pour valider la relation `$attachMany`

    use October\Rain\Database\Traits\Validation;
    use System\Models\File;
    use Model;
    
    class Galerie extends Model
    {
        use Validation;

        public $attachMany = [
            'photos' => File::class
        ];
    
        public $rules = [
            'photos'   => 'required',
            'photos.*' => 'image|max:1000|dimensions:min_width=100,min_height=100'
        ];
    }

Pour plus d'information sur la syntaxe `attribut.*` utilisée précédemment, lisez la section sur la [validation de tableau](../services/validation#validating-arrays).
