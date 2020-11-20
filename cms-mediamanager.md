# Gestionnaire des Médias

- [Amazon S3](#amazon-s3)
- [Rackspace CDN](#rackspace-cdn)
- [Lecteurs audio et vidéo](#audio-and-video-players)
- [Autres options de configuration](#configuration-options)
- [Événements](#events)
- [Résolution des problèmes](#troubleshooting)

Par défaut, le Gestionnaire des Médias fonctionne avec le sous-répertoire storage/app/media du répertoire d'installation.
Pour utiliser Amazon S3 ou Rackspace CDN, vous devez mettre à jour la configuration du système.

> Vous devez installer le [Plugin Drivers](http://octobercms.com/plugin/october-drivers) avant de pouvoir utiliser les fonctionnalités d'Amazon S3 ou de Rackspace CDN.

Veuillez noter qu'après avoir modifié la configuration du Gestionnaire des Médias, vous devez réinitialiser son cache.
Vous pouvez le faire en appuyant sur le bouton **Actualiser** dans la barre d'outils du Gestionnaire des Médias.

<a name="amazon-s3"></a>
## Configuration de l'accès à Amazon S3

Pour utiliser Amazon S3 avec OctoberCMS, vous devez créer un compartiment S3, un dossier dans le compartiment et un utilisateur d'API.

Créez un compte Amazon AWS ou connectez-vous avec votre compte existant à AWS Console. Ouvrez le panneau de gestion S3.
Créez un nouveau compartiment et attribuez-lui un nom (le nom du compartiment fera partie des URLs de vos fichiers publics).

Créez un dossier **media** dans le compartiment. Le nom du dossier n'a pas d'importance. Ce dossier sera une racine de votre bibliothèque multimédia.

Par défaut, les fichiers des compartiments S3 ne sont pas accessibles directement. Pour rendre le compartiment public, revenez à la liste des compartiments et cliquez sur le compartiment.
Cliquez sur le bouton **Properties** dans la barre latérale droite. Développez l'onglet **Permissions**.
Cliquez sur le lien **Edit bucket policy**. Collez le code suivant dans la fenêtre contextuelle de la stratégie.
Remplacez le nom du compartiment par votre nom réel de compartiment :

    {
        "Version": "2008-10-17",
        "Id": "Policy1397632521960",
        "Statement": [
            {
                "Sid": "Stmt1397633323327",
                "Effect": "Allow",
                "Principal": {
                    "AWS": "*"
                },
                "Action": "s3:GetObject",
                "Resource": "arn:aws:s3:::NOM-DU-COMPARTIMENT/*"
            }
        ]
    }

Cliquez sur le bouton **Save** pour appliquer la politique. La stratégie donne un accès public en lecture seule à tous les dossiers et répertoires du compartiment.
Si vous allez utiliser le compartiment pour d'autres besoins,
il est possible de configurer un accès public à un dossier spécifique dans le compartiment,
il suffit de spécifier le nom du répertoire dans la valeur **Resource** :

    "arn:aws:s3:::NOM-DU-COMPARTIMENT/media/*"

Vous devez également créer un utilisateur d'API qu'OctoberCMS utilisera pour gérer les fichiers de compartiment.
Dans la console AWS, accédez à la section IAM. Accédez à l'onglet Utilisateurs et créez un nouvel utilisateur.
Le nom de l'utilisateur n'a pas d'importance. Assurez-vous que la case "Generate an access key for each user" est cochée lorsque vous créez un nouvel utilisateur.
Une fois qu'AWS a créé un utilisateur, il vous permet de voir les informations d'identification de sécurité, **Access Key ID** et **Secret Access Key** de l'utilisateur .
Copiez les clés et placez-les dans un fichier texte temporaire.

Revenez à la liste des utilisateurs et cliquez sur l'utilisateur que vous venez de créer. Dans la section **Permissions**,
cliquez sur le bouton **Attach Policy**. Sélectionnez la politique **AmazonS3FullAccess** dans la liste et cliquez sur le bouton **Attach Policy**.

Vous avez maintenant toutes les informations pour mettre à jour la configuration d'OctoberCMS. Ouvrez le script **config/filesystem.php** et recherchez la section **disks**.
Il contient déjà la configuration s3, vous devez remplacer les informations d'identification de l'API et les paramètres d'informations pour le compartiment :

| Paramètre  | Valeur                                                                          |
| ---------- | ------------------------------------------------------------------------------- |
| **key**    | la valeur **Access Key ID** de l'utilisateur que vous avez créé auparavant.     |
| **secret** | la valeur **Secret Access Key** de l'utilisateur que vous avez créé auparavant. |
| **bucket** | le nom de votre compartiment.                                                   |
| **region** | le code de région du compartiment, voir ci-dessous.                             |

Vous pouvez trouver la région du compartiment dans la console de gestion S3, dans les propriétés du compartiment. L'onglet 'Properties' affiche le nom de la région, par exemple Paris. La configuration du pilote S3 nécessite un code de compartiment. Utilisez ce tableau pour trouver le code de votre compartiment (vous pouvez également consulter la [documentation AWS](http://docs.aws.amazon.com/general/latest/gr/rande.html#s3_region)):

| Région                                                                  | Code            |
| ----------------------------------------------------------------------- | --------------- |
| <span class = "nowrap">**Est des États-Unis (Ohio)**</span>             | us-east-2       |
| <span class = "nowrap">**Est des États-Unis (Virginie du Nord)**</span> | us-east-1       |
| <span class = "nowrap">**Ouest américain (Californie du Nord)**</span>  | us-west-1       |
| <span class = "nowrap">**Ouest américain (Oregon)**</span>              | us-west-2       |
| <span class = "nowrap">**Asie-Pacifique (Hong Kong)**</span>            | ap-east-1       |
| <span class = "nowrap">**Asie-Pacifique (Mumbai)**</span>               | ap-sud-1        |
| <span class = "nowrap">**Asie-Pacifique (Osaka-Local)**</span>          | ap-nord-est-3   |
| <span class = "nowrap">**Asie-Pacifique (Séoul)**</span>                | ap-nord-est-2   |
| <span class = "nowrap">**Asie-Pacifique (Singapour)**</span>            | ap-sud-est-1    |
| <span class = "nowrap">**Asie-Pacifique (Sydney)**</span>               | ap-sud-est-2    |
| <span class = "nowrap">**Asie-Pacifique (Tokyo)**</span>                | ap-nord-est-1   |
| <span class = "nowrap">**Canada (Centre)**</span>                       | ca-central-1    |
| <span class = "nowrap">**Chine (Pékin)**</span>                         | cn-nord-1       |
| <span class = "nowrap">**Chine (Ningxia)**</span>                       | cn-nord-ouest-1 |
| <span class = "nowrap">**UE (Francfort)**</span>                        | eu-central-1    |
| <span class = "nowrap">**UE (Irlande)**</span>                          | eu-west-1       |
| <span class = "nowrap">**UE (Londres)**</span>                          | eu-west-2       |
| <span class = "nowrap">**UE (Paris)**</span>                            | eu-west-3       |
| <span class = "nowrap">**UE (Stockholm)**</span>                        | eu-north-1      |
| <span class = "nowrap">**Amérique du Sud (São Paulo)**</span>           | sa-east-1       |
| <span class = "nowrap">**Moyen-Orient (Bahreïn)**</span>                | moi-sud-1       |

Exemple de configuration après la mise à jour :

    'disks' => [
        ...
        's3' => [
            'driver' => 's3',
            'key'    => 'XXXXXXXXXXXXXXXXXXXX',
            'secret' => 'xxxXxXX+XxxxxXXxXxxxxxxXxxXXXXXXXxxxX9Xx',
            'region' => 'eu-west-3',
            'bucket' => 'my-bucket'
        ],
        ...
    ]

Enregistrez le script **config/filesystem.php** et ouvrez le script **config/cms.php**.
Trouvez la section **storage**. Dans le paramètre **media** mettez à jour **disk**, **folder** et **path** :

| Paramètre  | Valeur                                                             |
| ---------- | ------------------------------------------------------------------ |
| **disk**   | utilisez la valeur **s3**.                                         |
| **folder** | le nom du dossier que vous avez créé dans le compartiment S3.      |
| **path**   | le chemin public du dossier dans le compartiment, voir ci-dessous. |

Pour obtenir le chemin du dossier, ouvrez la console AWS et accédez à la section S3.
Accédez au compartiment et cliquez sur le dossier que vous avez créé auparavant.
Téléversez n'importe quel fichier dans le dossier et cliquez sur ce fichier.
Cliquez sur le bouton **Properties** dans la barre latérale droite. L'URL du fichier se trouve dans le paramètre **Link**.
Copiez l'URL et supprimez le nom du fichier et la barre oblique finale.

Exemple de configuration de stockage :

    'storage' => [
        ...
        'media' => [
            'disk'   => 's3',
            'folder' => 'media',
            'path' => 'https://s3-eu-west-3.amazonaws.com/votre-nom-du-compartiment/media'
        ]
    ]

Félicitations ! Vous êtes maintenant prêt à utiliser Amazon S3 avec OctoberCMS.
Notez que vous pouvez également configurer Amazon CloudFront CDN pour qu'il fonctionne avec votre compartiment.
Ce sujet n'est pas traité dans ce document, veuillez vous reporter à la [documentation CloudFront](http://aws.amazon.com/cloudfront/).
Après avoir configuré CloudFront, vous devrez mettre à jour le paramètre **path** dans la configuration du stockage.

<a name="rackspace-cdn"></a>
## Configuration de l'accès Rackspace CDN

Pour utiliser Rackspace CDN avec OctoberCMS,
vous devez créer un conteneur Rackspace CDN,
un dossier dans le conteneur et un utilisateur d'API.

Connectez-vous à la console de gestion Rackspace et accédez à la page "Storage / Files".
Créez un nouveau conteneur. Le nom du conteneur n'a pas d'importance,
il fera partie de vos URLs des fichiers publics.
Sélectionnez le type **Public (Enabled CDN)** pour le nouveau conteneur.

Créez un dossier **media** dans le conteneur. Le nom du dossier n'a pas d'importance.
Ce dossier sera une racine de votre bibliothèque multimédia.

Vous devez créer un utilisateur d'API qu'OctoberCMS utilisera pour gérer les fichiers dans le conteneur CDN.
Ouvrez la page "Account / User Management" dans la console Rackspace.
Cliquez sur le bouton **Create user**.
Saisissez le nom d'utilisateur (par exemple october.cdn.api), le mot de passe, la question de sécurité et la réponse.
Dans la section **Product Access**, sélectionnez **Custom** et dans la ligne CDN, sélectionnez **Admin**.
Utilisez le rôle **No Access** dans la section **Account** et utilisez **Technical Contact** dans la section **Contact Information**.
Enregistrez le compte utilisateur.
Après avoir enregistré le compte, vous verrez la section Détails de connexion avec la ligne **API Key** qui contient une valeur que vous devez utiliser dans les fichiers de configuration d'OctoberCMS.

Vous avez maintenant toutes les informations pour mettre à jour la configuration d'OctoberCMS.
Ouvrez le script **config/filesystem.php** et recherchez la section **disks**.
Il contient déjà la configuration de Rackspace, vous devez remplacer les informations d'identification de l'API et les paramètres d'informations du conteneur :

| Paramètre     | Valeur                                                                                                                                                                               |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **username**  | Nom d'utilisateur Rackspace (par exemple october.cdn.api).                                                                                                                           |
| **key**       | l'**API Key** de l'utilisateur que vous pouvez copier à partir de la page du profil utilisateur Rackspace.                                                                           |
| **container** | le nom du conteneur.                                                                                                                                                                 |
| **region**    | le code de région du compartiment, voir ci-dessous.                                                                                                                                  |
| **endpoint**  | laissez la valeur telle quelle.                                                                                                                                                      |
| **region**    | vous pouvez trouver la région dans la liste des conteneurs CDN dans le panneau de configuration de Rackspace. Le code est une valeur à 3 lettres, par exemple, **ORD** pour Chicago. |

Exemple de configuration après la mise à jour :

    'disks' => [
        ...
        'rackspace' => [
            'driver'    => 'rackspace',
            'username'  => 'october.api.cdn',
            'key'       => 'xx00000000xxxxxx0x0x0x000xx0x0x0',
            'container' => 'mon-conteneur',
            'endpoint'  => 'https://identity.api.rackspacecloud.com/v2.0/',
            'region'    => 'ORD'
        ],
        ...
    ]

Enregistrez le script **config/filesystem.php** et ouvrez le script **config/cms.php**.
Trouvez la section **storage**. Dans le paramètre **media** mettez à jour **disk**, **folder** et **path** :

| Paramètre  | Valeur                                                          |
| ---------- | --------------------------------------------------------------- |
| **disk**   | utilisez la valeur **rackspace**.                               |
| **folder** | le nom du dossier que vous avez créé dans le conteneur CDN.     |
| **path**   | le chemin public du dossier dans le conteneur, voir ci-dessous. |

Pour obtenir le chemin du dossier, accédez à la liste des conteneurs CDN dans la console Rackspace.
Cliquez sur le conteneur et ouvrez le dossier multimédia. Téléversez n'importe quel fichier.
Une fois le fichier téléversé, cliquez dessus. Le fichier s'ouvrira dans un nouvel onglet du navigateur.
Copiez l'URL du fichier et supprimez le nom du fichier et la barre oblique de fin.

Exemple de configuration de stockage :

    'storage' => [
        ...
        'media' => [
            'disk'   => 'rackspace',
            'folder' => 'media',
            'path' => 'https://xxxxxxxxx-xxxxxxxxx.r00.cf0.rackcdn.com/media'
        ]
    ]

Félicitations ! Vous êtes maintenant prêt à utiliser Rackspace CDN avec OctoberCMS.

<a name="audio-and-video-players"></a>
## Lecteurs audio et vidéo

Par défaut, le système utilise des balises audio et vidéo HTML5 pour rendre les fichiers audios et vidéos :

    <video src="video.mp4" controls></video>

ou

    <audio src="audio.mp3" controls></audio>

Ce comportement peut être remplacé. S'il existe des partiels CMS **oc-audio-player.htm** et **oc-video-player.htm**,
ils seront utilisés pour afficher le contenu audio et vidéo.
À l'intérieur des partiels, utilisez la variable **src** pour générer un lien vers le fichier source. Exemple :

    <video src="{{ src }}" width="320" height="200" controls preload></video>

Si vous ne souhaitez pas utiliser le lecteur HTML5, vous pouvez fournir tout autre balisage dans les partiels.
Il existe un [script tiers](https://html5media.info/) qui permet la prise en charge des balises vidéo et audio HTML5 pour les anciens navigateurs.

Lorsque les partiels sont écrits en Twig, vous pouvez automatiser l'ajout des sources vidéo alternatives en fonction d'une convention de dénomination.
Par exemple, s'il existe une convention selon laquelle il y a toujours une vidéo de résolution plus petite pour chaque vidéo pleine résolution
et que le fichier de résolution plus petit a l'extension "iphone.mp4", le balisage généré pourrait ressembler à ceci :

    <video controls>
        <source
            src="{{ src }}"
            media="only screen and (min-device-width: 568px)"></source>
        <source
            src="{{ src|replace({'.mp4': '.iphone.mp4'}) }}"
            media="only screen and (max-device-width: 568px)"></source>
    </video>

<a name="configuration-options"></a>
## Autres options de configuration

Il existe plusieurs options qui vous permettent d'affiner le réglage du Gestionnaire des Médias.
Tous peuvent être définis dans le script **config/cms.php**, dans la section **storage/media**, par exemple :

    'storage' => [
        ...

        'media' => [
            ...
            'ignore' => ['.svn', '.git', '.DS_Store']
        ]
    ],

| Paramètre           | Valeur                                                                                                                                                                                          |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **ignore**          | une liste de noms de fichiers et de répertoires à ignorer. La valeur par défaut est **['.svn', '.git', '.DS_Store']**.                                                                          |
| **ttl**             | spécifie la durée de vie du cache en minutes. La valeur par défaut est 10. Le cache est automatiquement invalidé lorsque des éléments de la bibliothèque sont ajoutés, mis à jour ou supprimés. |
| **imageExtensions** | extensions du fichier correspondant au type de document Image. La valeur par défaut est **['gif', 'png', 'jpg', 'jpeg', 'bmp']**.                                                               |
| **videoExtensions** | extensions du fichiers correspondant au type de document vidéo. La valeur par défaut est **['mp4', 'avi', 'mov', 'mpg']**.                                                                      |
| **audioExtensions** | extensions du fichier correspondant au type de document Audio. La valeur par défaut est **['mp3', 'wav', 'wma', 'm4a']**.                                                                       |

<a name="events"></a>
## Événements

Le Gestionnaire des Médias fournit quelques [événements](../services/events) que vous pouvez écouter afin d'améliorer l'extensibilité.

| Événement         | Description                             | Paramètres                                                                                  |
| ----------------- | --------------------------------------- | ------------------------------------------------------------------------------------------- |
| **folder.delete** | Appelé lorsqu'un dossier est supprimé   | `(string) $path`                                                                            |
| **file.delete**   | Appelé lorsqu'un fichier est supprimé   | `(string) $path`                                                                            |
| **folder.rename** | Appelé lorsqu'un dossier est renommé    | `(string) $originalPath`, `(string) $newPath`                                               |
| **file.rename**   | Appelé lorsqu'un fichier est renommé    | `(string) $originalPath`, `(string) $newPath`                                               |
| **folder.create** | Appelé lors de la création d'un dossier | `(string) $newFolderPath`                                                                   |
| **folder.move**   | Appelé lorsqu'un dossier est déplacé    | `(string) $path`, `(string) $dest`                                                          |
| **file.move**     | Appelé lorsqu'un fichier est déplacé    | `(string) $path`, `(string) $dest`                                                          |
| **file.upload**   | Appelé lorsqu'un fichier est téléversé  | `(string) $filePath`, `(\Symfony\Component\HttpFoundation\File\UploadedFile) $uploadedFile` |

**Pour vous connecter à ces événements, étendez directement la classe `Backend\Widgets\MediaManager`:**

    Backend\Widgets\MediaManager::extend(function($widget) {
        $widget->bindEvent('file.rename', function ($originalPath, $newPath) {
            // Mettre à jour les références personnalisées au chemin ici
        });
    });

**Ou écoutez globalement via la façade `Event` (chaque événement est préfixé par `media.` et sera passé l'objet instancié `Backend\Widgets\MediaManager` comme premier paramètre):**

    Event::listen('media.file.rename', function($widget, $originalPath, $newPath) {
        // Update custom references to path here
    });

<a name="troubleshooting"></a>
## Résolution des problèmes

Le problème le plus courant avec l'utilisation des services distants est le problème de connexion SSL.
Si vous obtenez des erreurs SSL, assurez-vous que votre serveur dispose de nouveaux certificats SSL d'autorités publiques de certification (CA).
