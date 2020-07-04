# App configuration

- [Configuration du serveur Web](#webserver-configuration)
  - [Configuration Apache](#apache-configuration)
  - [Configuration Nginx](#nginx-configuration)
  - [Configuration Lighttpd](#lighttpd-configuration)
  - [Configuration IIS](#iis-configuration)
- [Configuration de l’application](#app-configuration)
  - [Mode débogage](#debug-mode)
  - [Mode sans échec](#safe-mode)
  - [Protection CSRF](#csrf-protection)
  - [Mises à jour de pointe](#edge-updates)
  - [Utilisation d’un dossier public](#public-folder)
  - [Utilisation d'un hébergement mutualisé](#shared-hosting)
- [Configuration de l’environnement](#environment-config)
  - [Définition d’un environnement de base](#base-environment)
  - [Environnement piloté par le domaine](#domain-environment)
  - [Conversion en configuration DotEnv](#dotenv-configuration)

Tous les fichiers de configuration d'October sont stockés dans le répertoire **config/**. Chaque option est documentée, alors n’hésitez pas à se référer aux fichiers et se familiariser avec les réglages possibles.

<a name="webserver-configuration"></a>

## Configuration du serveur Web

October possède une configuration de base qui doit être appliquée à votre serveur web. Les serveurs web communs et leur configuration peuvent être trouvés ci-dessous.

<a name="apache-configuration"></a>

### Configuration Apache

Si votre serveur Web exécute Apache, des exigences système supplémentaires sont nécessaires:

1. mod_rewrite doit être installés
1. L’option `AllowOverride` doit être activée

Dans certains cas, vous devrez peut-être décommenter cette ligne dans le fichier `.htaccess`:

    ##
    ## You may need to uncomment the following line for some hosting environments,
    ## if you have installed to a subdirectory, enter the name here also.
    ##
    # RewriteBase /

Si vous avez effectué votre installation dans un sous-répertoire, vous devez également ajouter le nom du sous-répertoire:

    RewriteBase /mysubdirectory/

<a name="nginx-configuration"></a>

### Configuration Nginx

De petites modifications sont nécessaires pour configurer votre site dans Nginx.

`nano /etc/nginx/sites-available/default`

Utilisez le code suivant dans la section **server**. Si vous avez installé octobre dans un sous-répertoire, remplacez les premières `/` des directives du chemin par le répertoire d'installation d'October:

    location / {
        # Let OctoberCMS handle everything by default.
        # The path not resolved by OctoberCMS router will return OctoberCMS's 404 page.
        # Everything that does not match with the whitelist below will fall into this.
        rewrite ^/.*$ /index.php last;
    }

    # Pass the PHP scripts to FastCGI server
    location ~ ^/index.php {
        # Write your FPM configuration here

    }

    # Whitelist
    ## Let October handle if static file not exists
    location ~ ^/favicon\.ico { try_files $uri /index.php; }
    location ~ ^/sitemap\.xml { try_files $uri /index.php; }
    location ~ ^/robots\.txt { try_files $uri /index.php; }
    location ~ ^/humans\.txt { try_files $uri /index.php; }

    ## Let nginx return 404 if static file not exists
    location ~ ^/storage/app/uploads/public { try_files $uri 404; }
    location ~ ^/storage/app/media { try_files $uri 404; }
    location ~ ^/storage/temp/public { try_files $uri 404; }

    location ~ ^/modules/.*/assets { try_files $uri 404; }
    location ~ ^/modules/.*/resources { try_files $uri 404; }
    location ~ ^/modules/.*/behaviors/.*/assets { try_files $uri 404; }
    location ~ ^/modules/.*/behaviors/.*/resources { try_files $uri 404; }
    location ~ ^/modules/.*/widgets/.*/assets { try_files $uri 404; }
    location ~ ^/modules/.*/widgets/.*/resources { try_files $uri 404; }
    location ~ ^/modules/.*/formwidgets/.*/assets { try_files $uri 404; }
    location ~ ^/modules/.*/formwidgets/.*/resources { try_files $uri 404; }
    location ~ ^/modules/.*/reportwidgets/.*/assets { try_files $uri 404; }
    location ~ ^/modules/.*/reportwidgets/.*/resources { try_files $uri 404; }

    location ~ ^/plugins/.*/.*/assets { try_files $uri 404; }
    location ~ ^/plugins/.*/.*/resources { try_files $uri 404; }
    location ~ ^/plugins/.*/.*/behaviors/.*/assets { try_files $uri 404; }
    location ~ ^/plugins/.*/.*/behaviors/.*/resources { try_files $uri 404; }
    location ~ ^/plugins/.*/.*/reportwidgets/.*/assets { try_files $uri 404; }
    location ~ ^/plugins/.*/.*/reportwidgets/.*/resources { try_files $uri 404; }
    location ~ ^/plugins/.*/.*/formwidgets/.*/assets { try_files $uri 404; }
    location ~ ^/plugins/.*/.*/formwidgets/.*/resources { try_files $uri 404; }
    location ~ ^/plugins/.*/.*/widgets/.*/assets { try_files $uri 404; }
    location ~ ^/plugins/.*/.*/widgets/.*/resources { try_files $uri 404; }

    location ~ ^/themes/.*/assets { try_files $uri 404; }
    location ~ ^/themes/.*/resources { try_files $uri 404; }

<a name="lighttpd-configuration"></a>

### Configuration Lighttpd

Si votre serveur Web exécute Lighttpd, vous pouvez utiliser la configuration suivante pour exécuter OctoberCMS. Ouvrez le fichier de configuration du site `sites.conf` avec votre éditeur préféré.

`nano /etc/lighttpd/conf-enabled/sites.conf`

Collez y le code suivant et modifiez **host address** et **server.document-root** pour qu’ils correspondent à votre application.

    $HTTP["host"] =~ "domain.example.com" {
        server.document-root = "/var/www/example/"

        url.rewrite-once = (
            "^/(plugins|modules/(system|backend|cms))/(([\w-]+/)+|/|)assets/([\w-]+/)+[-\w^&'@{}[\],$=!#().%+~/ ]+\.(jpg|jpeg|gif|png|svg|swf|avi|mpg|mpeg|mp3|flv|ico|css|js|woff|ttf)(\?.*|)$" => "$0",
            "^/(system|themes/[\w-]+)/assets/([\w-]+/)+[-\w^&'@{}[\],$=!#().%+~/ ]+\.(jpg|jpeg|gif|png|svg|swf|avi|mpg|mpeg|mp3|flv|ico|css|js|woff|ttf)(\?.*|)$" => "$0",
            "^/storage/app/uploads/public/[\w-]+/.*$" => "$0",
            "^/storage/temp/public/[\w-]+/.*$" => "$0",
            "^/(favicon\.ico|robots\.txt|sitemap\.xml)$" => "$0",
            "(.*)" => "/index.php$1"
        )
    }

<a name="iis-configuration"></a>

### Configuration IIS

Si votre serveur Web exécute Internet Information Services (IIS), vous pouvez utiliser la configuration ci-dessous dans le fichier **web.config** pour exécuter OctoberCMS.

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <clear />
                    <rule name="OctoberCMS to handle all non-whitelisted URLs" stopProcessing="true">
                       <match url="^(.*)$" ignoreCase="false" />
                       <conditions logicalGrouping="MatchAll">
                           <add input="{REQUEST_FILENAME}" matchType="IsFile" pattern="^/.well-known/*" negate="true" />
                           <add input="{REQUEST_FILENAME}" matchType="IsFile" pattern="^/storage/app/uploads/.*" negate="true" />
                           <add input="{REQUEST_FILENAME}" matchType="IsFile" pattern="^/storage/app/media/.*" negate="true" />
                           <add input="{REQUEST_FILENAME}" matchType="IsFile" pattern="^/storage/temp/public/.*" negate="true" />
                           <add input="{REQUEST_FILENAME}" matchType="IsFile" pattern="^/themes/.*/(assets|resources)/.*" negate="true" />
                           <add input="{REQUEST_FILENAME}" matchType="IsFile" pattern="^/plugins/.*/(assets|resources)/.*" negate="true" />
                           <add input="{REQUEST_FILENAME}" matchType="IsFile" pattern="^/modules/.*/(assets|resources)/.*" negate="true" />
                       </conditions>
                       <action type="Rewrite" url="index.php" appendQueryString="true" />
                   </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>

<a name="app-configuration"></a>

## Configuration de l’application

<a name="debug-mode"></a>

### Mode débogage

Le paramètre de débogage `debug` se trouvant dans le fichier de configuration `config/app.php` est activé par défaut.

Lorsque ce paramètre est activé, October affiche des messages d’erreur détaillés lorsqu’ils se produisent avec d’autres fonctionnalités de débogage. Bien qu’utile pendant le développement, le mode débogage doit toujours être désactivé dans un environnement de production. Cela empêche l’affichage d’informations potentiellement sensibles à l’utilisateur final.

Le mode débogage utilise les fonctionnalités suivantes lorsqu’il est activé:

1. Les [pages d’erreur détaillées](../cms/pages#error-page) s’affichent.
1. L’authentification non reussie d'un utilisateur fournit la raison de l'échec
1. Les [assets combinées](../markup/filter-theme) ne sont pas minifiés par défaut.
1. [Le mode sans échec](#safe-mode) est désactivé par défaut.

> **Important**: Définissez toujours le paramètre `app.debug` pour les environnements de production sur `false`.

<a name="safe-mode"></a>

### Mode sans échec

Le paramètre `enableSafeMode` du réglage du mode sans échec se trouve dans le fichier de configuration `config/cms.php`. La valeur par défaut est réglée sur `null`.

Si le mode sans échec est activé, la section code PHP est désactivée dans les modèles CMS pour des raisons de sécurité. S'il est réglé sur `null`, le mode sans échec est activé lorsque le [mode de débogage](#debug-mode) est désactivé.

<a name="safe-mode"></a>

### Protection CSRF

October fournit une méthode simple pour protéger votre application contre les attaques "cross-site request forgeries". Tout d’abord, un jeton aléatoire est placé dans la session de votre utilisateur. Ensuite, lorsqu’une [balise d’ouverture de formulaire est utilisée](../services/html#form-tokens), le jeton est ajouté à la page et renvoyé à chaque demande.

Bien que la protection CSRF soit activée par défaut, vous pouvez la désactiver avec le paramètre `enableCsrfProtection` dans fichier de configuration `config/cms.php`.

<a name="edge-updates"></a>

### Mises à jour de pointe

OctoberCMS et certains plugins de la marketplace implémenteront des changements en deux étapes afin d’assurer la stabilité globale et l’intégrité de la plate-forme. Cela signifie qu’ils ont une _build test_ en plus de la _build stable_ par défaut.

Vous pouvez demander à la plate-forme de préférer les builds de test à partir du marketplace en modifiant le paramètre `edgeUpdates` dans le fichier de configuration `config/cms.php`.

    /*
    |--------------------------------------------------------------------------
    | Bleeding edge updates
    |--------------------------------------------------------------------------
    |
    | If you are developing with October, it is important to have the latest
    | code base, set this value to 'true' to tell the platform to download
    | and use the development copies of core files and plugins.
    |
    */

    'edgeUpdates' => false,

> **Remarque** : Pour les développeurs de plugins, nous vous recommandons d’activer les **Test updates** pour vos plugins répertoriés sur le marketplace, via la page Paramètres plugin.

> **Remarque** : Si vous utilisez [Composer](../console/commands#console-install-composer) pour gérer les mises à jour, remplacez les exigences par défaut d’OctoberCMS dans votre fichier `composer.json` par les éléments suivants afin de télécharger les mises à jour directement à partir de la _branche develop_.

    "october/rain": "dev-develop as 1.0",
    "october/system": "dev-develop",
    "october/backend": "dev-develop",
    "october/cms": "dev-develop",
    "laravel/framework": "5.5.*@dev",

<a name="public-folder"></a>

### Utilisation d’un dossier public

Pour une sécurité ultime dans les environnements de production, vous pouvez configurer votre serveur Web pour utiliser un dossier **public/** pour vous assurer que seuls les fichiers publics peuvent être consultés. Tout d’abord, vous devrez créer un dossier public à l’aide de la commande.`october:mirror`

    php artisan october:mirror public/

Cela créera un nouveau répertoire appelé **public/** dans le répertoire de base du projet, à partir de là, vous devez modifier la configuration du serveur web pour utiliser ce nouveau chemin d’accès comme répertoire d’accueil, également connu sous le nom _wwwroot_.

> **Remarque**: La commande ci-dessus peut demander des privilèges Administrateur système ou _sudo_ pour s'executer. Il doit également être effectué après chaque mise à jour du système ou suite à l'installation d'un nouveau plugin.

<a name="shared-hosting"></a>

### Utilisation d'un hébergement mutualisé

Si vous partagez un serveur avec d'autres utilisateurs, vous devez agir comme si le site de votre voisin était compromis. Assurez-vous que tous les fichiers contenant des mots de passe (par exemple les fichiers de configuration du CMS comme `config/database.php`) ne peuvent pas être lus à partir d'autres comptes d'utilisateurs, même s'ils déterminent les chemins absolus de vos fichiers. Définir les autorisations de ces fichiers importants sur 600 (lecture et écriture uniquement pour le propriétaire et rien pour quiconque) est une bonne idée.

Vous pouvez configurer cette protection dans le fichier `config / cms.php` dans la section **Default permission mask**.

    /*
    |--------------------------------------------------------------------------
    | Default permission mask
    |--------------------------------------------------------------------------
    |
    | Specifies a default file and folder permission for newly created objects.
    |
    */

    'defaultMask' => ['file' => '644', 'folder' => '755'],

> **Remarque**: N'oubliez pas de vérifier manuellement si les fichiers sont déjà définis sur 644, vous pourriez avoir besoin d'accéder à votre cPanel et les définir.

<a name="environment-config"></a>

## Configuration de l’environnement

<a name="base-environment"></a>

### Définition d’un environnement de base

Il est souvent utile d’avoir des paramètres de configuration différentes en fonction de l’environnement dans lequel l’application s’exécute. Vous pouvez le faire en définissant la variable d’environnement `APP_ENV` qui, par défaut, définie sur **production**. Il existe deux façons courantes pour modifier cette valeur:

1.  Définissez la valeur `APP_ENV` directement dans votre serveur Web.

    Par exemple, dans Apache, cette ligne peut être ajoutée aux fichiers `.htaccess` ou `httpd.config`:

        SetEnv APP_ENV "dev"

1.  Créez un fichier .env dans le répertoire racine avec le contenu suivant:

        APP_ENV=dev

Dans les deux exemples ci-dessus, l’environnement est défini sur la nouvelle valeur `dev`. Les fichiers de configuration peuvent maintenant être créés dans le répertoire **config/dev** et remplaceront la configuration de base de l’application.

Par exemple, pour utiliser une base de données MySQL différente pour l’environnement `dev` seulement, créez un fichier appelé **config/dev/database.php** avec le contenu suivant:

    <?php

    return [
        'connections' => [
            'mysql' => [
                'host'     => 'localhost',
                'port'     => '',
                'database' => 'database',
                'username' => 'root',
                'password' => ''
            ]
        ]
    ];

<a name="domain-environment"></a>

### Environnement piloté par le domaine

October prend en charge l’utilisation d’un environnement détecté par un nom d’hôte spécifique. Vous pouvez placer ces noms d’hôte dans un fichier de configuration d’environnement, par exemple **config/environment.php**.

En utilisant le contenu ci-dessous dans le fichier **config/environment.php**, l’environnement sera défini sur `global` lorsque l'on accède à l’application via **global.website.tld**, et ainsi de suite pour les autres.

    <?php

    return [
        'hosts' => [
            'global.website.tld' => 'global',
            'local.website.tld' => 'local',
        ]
    ];

<a name="dotenv-configuration"></a>

### Conversion en configuration DotEnv

En tant qu’alternative à [la configuration de l’environnement de base](#base-environment), vous pouvez placer des valeurs courantes dans l’environnement au lieu d’utiliser des fichiers de configuration. La configuration est ensuite accessible à l’aide de la syntaxe [DotEnv](https://github.com/vlucas/phpdotenv). Exécutez la commande `october:env` pour déplacer les valeurs de configuration courantes vers l’environnement

    php artisan october:env

Cela créera un fichier **.env** dans le répertoire racine du projet et modifiera les fichiers de configuration pour utiliser la fonction d’assistance (helper) `env`. Le premier argument contient le nom clé trouvé dans l’environnement, le deuxième argument contient une valeur par défaut facultative.

    'debug' => env('APP_DEBUG', true),

Votre fichier `.env` ne doit pas être enregistrer dans le dépôt de votre application (git), puisque chaque développeur ou serveur utilisant votre application peut nécessiter une configuration d’environnement différente.

Il est également important que votre fichier `.env` ne soit pas être accessible au public en production. Pour ce faire, vous devez envisager d’utiliser un [dossier public](#public-folder).
