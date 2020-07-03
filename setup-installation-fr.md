# Installation

- [Configuration minimale requise](#system-requirements)
- [L'installeur (l'assistant d'installation)](#wizard-installation)
  - [Problèmes d’installation courants](#troubleshoot-installation)
- [Installation via la ligne de commande](#command-line-installation)
- [Taches post-installation](#post-install-steps)
  - [Suppression des fichiers d’installation](#delete-install-files)
  - [Revoir la configuration](#config-review)
  - [Configuration du planificateur](#crontab-setup)
  - [Configuration des tâches en file d’attente](#queue-setup)

Octobre peut être de deux façons, soit à l’aide de [l’installateur (l'assistant d'installation)](#wizard-installation) ou [via la ligne de commande](../console/commands#console-install). Avant de procéder, vous devez vérifier que votre serveur/hebergeur remplit les conditions minimales requises du système.

<a name="system-requirements"></a>

## Configuration minimale requise

Pour faire tourner Octobre CMS, votre serveur/hébergeur devrait supporter
les exigences ci-dessous:

1. Version du PHP 7.2 ou supérieure
1. Extension PHP PDO (et driver approprié pour la base de données à laquelle vous souhaitez vous connecter)
1. Extension PHP cURL
1. Extension PHP OpenSSL
1. Extension PHP mbstring
1. Extension PHP ZipArchive
1. Extension PHP GD
1. Extension PHP SimpleXML

Certaines distributions d’OS peuvent necessiter l'installation manuelle de certaines extensions requises,

Si vous utilisez Ubuntu, l'execution de la commande suivante installera toutes les extensions requises:

```bash
sudo apt-get update &&
sudo apt-get install php php-ctype php-curl php-xml php-fileinfo php-gd php-json php-mbstring php-mysql php-sqlite3 php-zip
```

<!-- TODO revoir cette phrase -->

Si vous utilisez le moteur de base de données SQL Server, vous devrez installer les fonctions d'agrégation définies par l'utilisateur [concaténation de groupe](https://groupconcat.codeplex.com/)

<a name="wizard-installation"></a>

## L'installeur (l'assistant d'installation)

L’installeur est un moyen recommandé pour installer Octobre. Il est plus simple que l’installation à via la ligne de commande et ne nécessite pas de compétences particulières.

1. Préparez un répertoire vide sur votre serveur. Il peut s’agir d’un sous-répertoire, d’une racine de domaine ou d’un sous-domaine.
2. [Téléchargez l'archive du fichier de l’installateur](http://octobercms.com/download).
3. Déballez l’archive de l’installateur dans le répertoire préparé auparavent.
4. Accordez des autorisations d’écriture sur le répertoire d’installation et tous ses sous-répertoires et fichiers.
5. Accédez au script install.php dans votre navigateur Web.
6. Suivez les instructions d’installation.

![image](https://github.com/octobercms/docs/blob/master/images/wizard-installer.png?raw=true) {.img-responsive .frame}

<a name="troubleshoot-installation"></a>

### Problèmes d’installation courants

1. **Une erreur 500 s’affiche lors du téléchargement des fichiers de l’application**: vous devrez peut-être augmenter ou désactiver la limite du délai d’attente sur votre serveur Web. Par exemple, le FastCGI d’Apache a parfois l’option `-idle-timeout` définie sur 30 secondes.

1. **Un écran vide s’affiche lors de l’ouverture de l’application**: vérifiez les autorisations définies pour les fichiers et les dossiers du repertoire `/storage`, elles doivent être configurés en écriture pour le serveur Web

1. **Un code d’erreur "liveConnection" s’affiche**: l’installateur testera une connexion au serveur d’installation à l’aide du port 80. Vérifiez que votre serveur web peut créer des connexions sortantes sur le port 80 via PHP. Contactez votre fournisseur d’hébergement, se problème souvent causé par les paramètres du pare-feu du serveur.

1. **La zone back-end affiche "Page introuvable" (404)**: Si l’application ne trouve pas la base de données, une page 404 s’affichera pour le back-end. Essayez d’activer le mode débogage pour afficher le message d’erreur sous-jacent.

> **Remarque:** Un journal d’installation détaillé peut être trouvé dans le fichier `install_files/install.log`

<a name="command-line-installation"></a>

## Installation via la ligne de commande

Si vous vous sentez plus à l’aise avec la ligne de commande ou si vous souhaitez utiliser Composer, il existe un processus d’installation par CLI sur la [page de l’interface console](../console/commands#console-install).

<a name="post-install-steps"></a>

## Taches post-installation

Certaines choses devront être configurer une fois l’installation terminée.

<a name="delete-install-files"></a>

### Suppression des fichiers d’installation

Si vous avez utilisé [l’installeur (l'assistant de l'installation)](#wizard-installation), vous devez supprimer les fichiers d’installation pour des raisons de sécurité. Le programme d'installation d'Octobre tentera de nettoyer après lui-même, mais vous devez toujours vérifier qu'ils ont bien été supprimés:

    install_files/      <== Dossier d'installation
    install.php         <== Script d'Installation

<a name="config-review"></a>

### Revoir/Examiner la configuration

Les fichiers de configuration sont stockés dans le répertoire **config** de l’application. Bien que chaque fichier contient une description pour chaque paramètre, il est important de [revoir les options de configuration communes](../setup/configuration) disponibles pour votre situation.

Par exemple, pour les environnements de production, vous pouvez activer la [protection CSRF](../setup/configuration#csrf-protection) alors qu'en développement, vous aimeriez activer [les mises à jour de pointe](../setup/configuration#edge-updates).<!-- TODO Traduction correcte de 'bleeding edge updates' -->

Bien que la plupart des configurations sont facultatives, nous recommandons fortement de désactiver le [mode débogage](../setup/configuration#debug-mode) pour les environnements de production. Vous pouvez également utiliser un [dossier public](../setup/configuration#public-folder) pour obtenir une sécurité supplémentaire.

<a name="crontab-setup"></a>

### Configuration du planificateur

Pour que les _tâches planifiées_ fonctionnent correctement, vous devez ajouter l’entrée Cron suivante à votre serveur. L’édition du crontab est généralement effectuée avec la commande `crontab -e`

    * * * * * php /path/to/artisan schedule:run >> /dev/null 2>&1

Assurez-vous de remplacer **/path/to/artisan** par le chemin absolu vers le fichier _artisan_ dans le répertoire racine d’Octobre. Ce Cron appellera le planificateur de commandes chaque minute. Octobre évalue ensuite toutes les tâches planifiées et exécute les tâches qui sont dues.

> **Remarque:** Si vous ajoutez ceci à `/etc/cron.d` vous devez spécifier un utilisateur immédiatement après `* * * * *`.

<a name="queue-setup"></a>

### Configuration des tâches en file d’attente

Vous pouvez éventuellement configurer une file d’attente externe pour le traitement des _tâches en file d’attente_, par défaut, celles-ci seront traitées de manière asynchrone par la plate-forme. Ce comportement peut être modifié en définissant le paramètre `default` dans le fichier `config/queue.php`

Si vous décidez d’utiliser le pilote de la file d’attente de la `database`, il est préférable d’ajouter une entrée Crontab pour la commande `php artisan queue:work --once` afin de traiter le premier travail disponible dans la file d’attente.
