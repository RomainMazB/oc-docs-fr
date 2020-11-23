# Contrôleurs et AJAX

- [Introduction](#introduction)
    - [Définition de la class du contrôleur](#class-definition)
    - [Propriétés du contrôleur](#controller-properties)
- [Actions, vues et routage](#actions-views-routing)
- [Passer des données aux vues](#passing-data-to-views)
- [Enregistrer le contexte de navigation](#navigation-context)
- [Utiliser un gestionnaire AJAX depuis le back-end](#ajax)
    - [Les gestionnaires AJAX](#ajax-handlers)
    - [Émettre des requêtes AJAX](#triggering-ajax-requests)
- [Middleware du contrôleur](#controller-middleware)

<a name="introduction"></a>
## Introduction
Le back-end d'October implémente la structure MVC.
Les contrôleurs gèrent les pages et intègres des fonctionnalités tels que l'affichage de listes et de formulaires.
Cet article décrit comment développer des contrôleurs back-end et configurer des comportements.

Chaque contrôleur est représenté par un script PHP établi dans le sous-dossier `controllers` d'un dossier de plugin.
Les vues d'un contrôleur sont des fichiers avec l'extension `.htm` contenu dans le dossier `views` du contrôleur.
Ce dossier peut aussi contenir certains fichiers de configuration. Un exemple de la structure du dossier d'un contrôleur :

    plugins/
      acme/
        blog/
          controllers/
            users/                <=== Dossier des vues du contrôleur
              _partial.htm        <=== Fichier de partiel du contrôleur
              config_form.yaml    <=== Fichier de configuration du contrôleur
              index.htm           <=== Fichier d'une vue du contrôleur
            Users.php             <=== Class du contrôleur
          Plugin.php

<a name="class-definition"></a>
### Définition de la class du contrôleur

La class d'un contrôleur doit étendre la class `\Backend\Classes\Controller`.

Comme toutes les autres class du plugin, celle du contrôleur doit
résider dans le même namespace que celui du [plugin](../plugin/registration#namespaces).
La représentation la plus simple d'un contrôleur utilisé à l'intérieur d'un Plugin ressemble à ça : 

    namespace Acme\Blog\Controllers;

    class Posts extends \Backend\Classes\Controller {

        public function index()    // <=== Méthode d'une action
        {

        }
        
    }

Habituellement, chaque contrôleur définis les fonctionnalités pour ne travailler qu'avec
un seul type de données - comme les articles de blog ou les catégories.
Tous les comportements décrits ci-dessous suivent cette convention.

<a name="controller-properties"></a>
### Propriétés du contrôleur
La class de base d'un contrôleur back-end définie un certain nombre de propriétés
qui permette de configurer l'apparence de la page et de gérer sa sécurité : 

| Property | Description |
------------- | -------------
**$fatalError** | Permet de stocker une erreur fatale qui serais affichée dans la vue dans le cas où une erreur surviendrais lors d'une action. 
**$user** | Contiens les informations sous forme d'objet de l'utilisateur actuellement connecté.
**$suppressView** | Permet de prévenir l'affichage d'une vue. Peut être modifié depuis une action du contrôleur ou son constructeur.
**$params** | Un tableau des paramètres du système de routage.
**$action** | Le nom de la méthode de l'action en cours d'exécution.
**$publicActions** | Défini un tableau des actions possibles par un utilisateur non-connecté. Peut être sur-écrit dans la définition de la class.
**$requiredPermissions** | Autorisations requises pour voir cette page. Peut être configuré depuis la définition de class ou le constructeur. Voir [Utilisateurs et permissions](users) pour les détails.
**$pageTitle** | Défini le titre de la page. Peut être renseigné depuis la méthode du contrôleur.
**$bodyClass** | Class CSS appliquée sur la balise `body` utilisée pour modifier l'apparence de la page. Peut être modifié depuis une action du contrôleur ou son constructeur.
**$guarded** | Défini un tableau des méthodes qui ne peuvent pas être appelées en tant qu'action. Peut être étendu depuis le constructeur.
**$layout** | Défini un layout customisé à utiliser par les vues du contrôleur (voir [maquettes](#layouts) ci-dessous).

<a name="actions-views-routing"></a>
## Actions, vues et routage
Les méthodes publiques du contrôleur, appelée **actions** sont jumelées
avec leur **fichiers de vue** qui représente la page correspondante à l'action. 
Les fichiers de vue back-end utilise la syntaxe PHP.
Exemple de contenu d'un fichier `index.htm` correspondant à la méthode de l'action **index** :

    <h1>Bonjour le monde</h1>

L'URL de cette page est construite depuis le nom de l'auteur du plugin,
le nom du plugin, le nom du contrôleur et enfin le nom de l'action :

    backend/[nom de l'auteur]/[nom du plugin]/[nom du contrôleur]/[nom de l'action]

Ce qui résulte à l'adresse URL suivante :

    http://example.com/backend/acme/blog/utilisateurs/index

<a name="passing-data-to-views"></a>
## Passer des données aux vues

Utiliser la propriété du contrôleur `$vars` pour passer des données directement à la vue :

    $this->vars['maVariable'] = 'valeur';

Les variables passées avec la propriété `$vars` peuvent désormais être affichées directement dans la vue :

    <p>La valeur de la variable maVariable est <?= $maVariable ?></p>

<a name="navigation-context"></a>
## Enregistrer le contexte de navigation
Les plugins peuvent enregistrer dans le backend des menus
et sous-menus dans le [fichier d'enregistrement du plugin](../plugin/registration#navigation-menus).

Le contexte de navigation défini quel menu et sous-menu sont actifs pour la page actuelle.
Vous pouvez le définir à l'aide de la class `BackendMenu` :

    BackendMenu::setContext('Acme.Blog', 'blog', 'categories');

Le premier paramètre définir les noms de l'auteur et du plugin.
Le second défini le code du menu.
Et le troisième - optionnel - défini le code du sous-menu.
Habituellement, vous appellerez la méthode `BackendMenu::setContext` depuis le constructeur du contrôleur.

    namespace Acme\Blog\Controllers;

    class Categories extends \Backend\Classes\Controller {

        public function __construct()
        {
            parent::__construct();
    
            BackendMenu::setContext('Acme.Blog', 'blog', 'categories');
        }

Vous pouvez définir le titre d'une page back-end avec la propriété `$pageTitle` du la class du contrôleur
(A noter que les comportements natifs formulaire et liste peuvent le faire pour vous) :

    $this->pageTitle = 'Catégories du blog';

<a name="ajax"></a>
## Utiliser un gestionnaire AJAX depuis le back-end
Le framework AJAX utilisé en back-end intègre la même [librairie AJAX](../ajax/introduction) que sur les pages front-end.
La librairie est chargée automatiquement dans le back-end

<a name="ajax-handlers"></a>
### Les gestionnaires AJAX
Les gestionnaires AJAX back-end peuvent être défini dans la class du contrôleur ou dans un les [widgets](widgets).
Dans la class du contrôleur, les gestionnaires AJAX sont déifnis comme des méthodes publiques
dont le nom est précédé de la chaîne de caractères `on`: **onCreerUnDevis**, **onCreerUneFacture**, etc.

Les gestionnaires AJAX back-end peuvent retourner un tableau de données,
émettre des erreurs ou rediriger vers une autre page (voir [Les gestionnaires d'évènement AJAX](../ajax/handlers)).

Vous pouvez utiliser `$this->vars` pour définir des données
et la méthode `makePartial` du contrôleur pour générer un partiel
et retourner son contenu en tant que donnée de la réponse

    public function onCreerUnDevis()
    {
        if (Request::input('uneVariable') != 'uneValeur') {
            throw new ApplicationException('Valeur incorrecte');
        }

        $this->vars['foo'] = 'bar';

        return [
            'contenuPartiel' => $this->makePartial('un-partiel')
        ];
    }

<a name="triggering-ajax-requests"></a>
### Émettre des requêtes AJAX
Les requêtes AJAX peuvent être émises avec l'API des attributs de données ou l'API JavaScript

Se référer à [la librairie AJAX](../ajax/introduction) pour les détails.
L'exemple suivant montre comment émettre une requête avec un bouton dans le back-end.

    <button
        type="button"
        data-request="onCreerUnDevis"
        class="btn btn-default">
        Sauvegarder le devis
    </button>

> **Remarque** : Vous pouvez viser précisément le gestionnaire AJAX d'un widget grâce au prefix `widget::onAction`.
> Voir l'article sur les [gestionnaires AJAX des widgets](../backend/widgets#generic-ajax-handlers) pour plus de précisions.

<a name="controller-middleware"></a>
## Middleware du contrôleur
Vous pouvez définir un middleware dans vos contrôleurs back-end,
ce qui vous fournis une mécanique simple pour effectuer des changements de la réponse HTTP à la requête.
Par exemple, vous pourriez vouloir spécifier un header HTTP pour certaines actions dans votre contrôleur,
ou rediriger l'utilisateur s'il ne répond pas à certains critères.

Les middlewares du contrôleur sont exécutés après que la requête ait été acceptée par October CMS mais avant qu'elle ne soit retournée au navigateur.

Pour définir un middleware dans votre contrôleur, vous pouvez le spécifier dans son constructeur en appelant la méthode `middleware()`.

    public function __construct()
    {
        parent::__construct();
    
        $this->middleware(function ($requete, $reponse) {
            // Fonctionnalité du middleware
        });
    }

La méthode `middleware()` requiert un callback avec deux arguments,
`$requete` fournissant les [informations de la requête](../services/request-input#request-information)
et `$reponse` fournissant l'[objet de la réponse](../services/response-view#basic-responses).
Le callback doit retourner l'objet `$reponse` avec vos modifications.

Par exemple, pour ajouter un header `Header-test` à votre réponse, vous pourriez faire comme-ceci :

    public function __construct()
    {
        parent::__construct();
    
        $this->middleware(function ($requete, $reponse) {
            $reponse->headers->set('Header-test', 'Test');
            return $reponse;
        });
    }

Vous pouvez aussi contrôler quelle action utiliser votre middleware à l'aide des modificateurs `only()` et `except()`.
Par exemple, pour restreindre le middleware à n'être utilisé
que lorsque la méthode `index` est appelée, vous pourriez faire comme-ceci :

    public function __construct()
    {
        parent::__construct();
    
        $this->middleware(function ($requete, $reponse) {
            // Fonctionnalité du middleware
        })->only('index');
    }

Ou au contraire, vous pouvez définir que le middleware sera utilisé partout, sauf sur l'action index : 

    public function __construct()
    {
        parent::__construct();
    
        $this->middleware(function ($requete, $reponse) {
            // Fonctionnalité du middleware
        })->except('index');
    }