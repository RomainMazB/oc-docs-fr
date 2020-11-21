# Mettre à jour la page avec AJAX

- [Demander une mise à jour  depuis la vue](#pulling-updates)

- [Définition de la mise à jour](#update-definition)

- [Ajouter du contenu au début ou à la fin](#appending-prepending)

- [Envoyer une mise à jour depuis le contrôleur ou composant](#pushing-updates)

- [Passer des variables au partiel](#passing-variables)

Lorsqu'un gestionnaire s'exécute, il peut préparer des partiels qui mettrons à jour la page.
La mise à jour peut être demandé par la vue ou envoyer depuis le contrôleur ou composant et peut être dynamiquement généré avec des variables.

<a name="pulling-updates"></a>
## Demander une mise à jour depuis la vue

Le navigateur client peut demander une mise à jour depuis le serveur lorsqu'il émet une requête AJAX, c'est ce que l'on appellera une *demande de mise à jour*.
Le code suivant génères le partiel **monutilisateur** à l'intérieur de l'élément `#monDiv` après avoir appelé [le gestionnaire](../ajax/handlers) `onConnexion`.

    <div id="monDiv">{% partial 'monutilisateur' %}</div>

L'[API des attributs de données](../ajax/attributes-api) utilise l'attribut `data-request-update`.

    <!-- API des attributs de données -->

    <button
    
    data-request="onConnexion"
    
    data-request-update="monutilisateur: '#monDiv'">
    
        Go
    
    </button>

L'[API JavaScript](../ajax/javascript-api) utilise l'option de configuration `update`:

    // API JavaScript
    
    $.request('onConnexion', {
    
        update: { monutilisateur: '#monDiv' }
    
    })

<a name="update-definition"></a>
### Définition de la mise à jour

La définition de ce qui doit être mis à jour utilise une syntaxe similaire à un objet JSON dans laquelle :

- la partie gauche (la clé) est le **nom du partiel**
- la partie droite (la valeur) est l'**élément visé** à mettre à jour

Ci-dessous nous demandons à mettre à jour l'élément `#monDiv` avec le contenu du partiel **monpartiel**.

    monpartiel: '#monDiv'

Plusieurs partiels peuvent être mis à jour, dans ce cas il faut les séparer par des virgules.

    premierpartiel: '#monDiv', secondpartiel: '#autreDiv'

Si le nom du partiel contiens un slash ou un tiret, il faut l'entourer avec des 'apostrophes'.

    'dossier/monpartiel': '#monDiv', 'mon-partiell': '#autreDiv'

L'élément visé sera toujours à droite puisqu'il peut aussi être sélectionné à l'aide d'une fonction JavaScript.

    monpartiel: document.getElementById('monDiv')

<a name="appending-prepending"></a>
### Ajouter du contenu au début ou à la fin

Si le sélecteur est précédé du signe `@`, le contenu reçu du serveur sera ajouté avant le contenu existant de l'élément au lieu de le remplacer.

    'dossier/debut': '@#monDiv'

Si le sélecteur est précédé du signe `^`, le contenu retourné par le serveur sera ajouté après à la fin du contenu de l'élément.

    'dossier/fin': '^#monDiv'

<a name="pushing-updates"></a>
## Envoyer une mise à jour depuis le contrôleur ou composant

De la même manière, les [gestionnaires AJAX](../ajax/handlers) peuvent *envoyer des mises à jour* au client directement depuis le serveur.
Pour envoyer une mise à jour, le gestionnaire AJAX doit retourner un tableau dans lequel les clés sont les éléments HTML à mettre à jour (à l'aide d'un simple sélecteur CSS) et les valeurs sont les contenus à mettre à jour.

L'exemple suivant mettra à jour un l'élément portant l'id **monDiv** sur la page avec le contenu du partiel **monpartiel**.
le gestionnaire `onConnexion` appelle la méthode `renderPartial` pour générer le contenu d'un partiel en PHP.

    function onConnexion()
    {
        return [
        
            '#monDiv' => $this->renderPartial('monpartiel')
        
        ];
    }

> **Remarque** : Le premier caractère du nom de la clé doit être un `#` ou un `.` pour exécuter une mise à jour de contenu.

<a name="passing-variables"></a>
## Passer des variables au partiel

Selon le contexte d'exécution de [le gestionnaire d'événement](../ajax/handlers), passer des variables au partiel de fait de façons différentes:

- Utilisez `$this[]` dans la  [section PHP](../cms/themes#php-section) d'une page ou d'un layout.

- Utilisez `$this->page[]` dans un [composant de plugin](../plugin/components#ajax-handlers).

- Utilisez `$this->vars[]` dans la [partie back-end](../backend/controllers-ajax#ajax).

Les exemples suivants fournissent tous la variable **resultat** selon les contextes différents :

    // Dans la section PHP d'un layout ou d'une page
    
    $this['resultat'] = 'Hello world!';
    
    // Depuis un composant de plugin
    
    $this->page['resultat'] = 'Hello world!';
    
    // Depuis un contrôleur ou un widget du back-end
    
    $this->vars['resultat'] = 'Hello world!';
    
    Cette valeur peut être affiché directement avec Twig dans le partiel:
    
    <!-- Hello world! -->
    
    {{ resultat }}