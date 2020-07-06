# AJAX JavaScript

- [L'API JavaScript](#javascript-api)

- [Exemples d'utilisation](#javascript-examples)

- [Gestionnaire AJAX globaux](#global-events)

- [Exemples d'utilisation](#global-events-examples)

<a name="javascript-api"></a>

## L'API JavaScript
L'API JavaScript est plus puissante que l'API des attributs de données. La fonction `request` peut être utilisée sur n'importe quel élément à l'intérieur d'un formulaire, ou sur un formulaire. Lorsque la fonction est utilisé sur un élément à l'intérieur d'un formulaire, elle est renvoyé au formulaire.

The JavaScript API is more powerful than the data attributes API. The `request` method can be used with any element that is inside a form, or on a form element. When the method is used with an element inside a form, it is forwarded to the form.

La fonction `request` n'as qu'un seul argument obligatoire : le nom de l'écouteur AJAX. Exemple:

The `request` method has a single required argument - the AJAX handler name. Example:
```js
<form onsubmit="$(this).request('onLogin'); return false;">
```
...
Le second argument de la fonction `request` est l'objet options. Vous pouvez utiliser toutes les options compatible avec la [fonction AJAX de jQuery](http://api.jquery.com/jQuery.ajax/). Les options suivantes sont spécifiques au framework d'October:

Option | Description

------------- | -------------

**update** | un objet, défini une liste de partiels et d'éléments de page (sous forme de sélecteur CSS) à mettre à jour: `{'partiel': '#select'}`. Si le sélecteur est précédé du signe `@`, le contenu retourné par le serveur sera insérer avant le contenu actuel de l'élément au lieu de le remplacer.
**confirm** | une chaîne de caractères. Si défini, ce texte sera affiché avant l'envoi de la requête. Si l'utilisateur cliques sur le bouton Annuler, la requête ne sera pas envoyé.
**data** | un objet, détermine des données additionnelles à envoyer avec ceux du formulaire : `{variable : 'valeur'}`. Lorsque l'option `files` est définie à true, vous pouvez aussi intégrer des fichiers a charger dans cet objet en utilisant des [objets Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob). Pour spécifier le nom du fichier d'un objet `Blob`, définissez simplement la propriété `filename` de l'objet `Blob` lui-même.  (Ex. `var blob = new Blob(variable); blob.filename = 'test.txt'; var data = {'uploaded_file': blob};`)
**redirect** | une chaîne de caractères qui détermine l'URL vers laquelle rediriger le navigateur après le succès de la requête.
**beforeUpdate** | une fonction callback à exécuter avant de mettre à jour la page. La fonction prends trois paramètres : l'objet data retourné par le serveur, une chaîne de caractère correspondant à l'état de la requête et l'objet jqXHR. La variable `this` à l'intérieur de la fonction retourne le contenu de la requête - un objet avec deux paramètres: `handler` et `options` qui sont les paramètres initiaux de la requête.
**success** | une fonction callback à exécuter lorsque la requête as été exécutée avec succès. Si elle est définie, elle remplacera la fonctionnalité par défaut du framework : les partiels ne seront pas mis à jour et les évènements `beforeUpdate`,  `ajaxUpdate` et `ajaxUpdateComplete` ne seront pas émis. Vous pouvez toutefois appelé ces fonctionnalités en faisant un appel de la méthode `this.success(...)` à l'intérieur de votre fonction. La fonction reçois trois paramètres : l'objet data retourné par le serveur, une chaîne de caractère correspondant à l'état de la requête et l'objet jqXHR.
**error** | une fonction callback à exécuter lorsque la requête as échouée. Si cette option est définie, le message d'alerte par défaut ne sera pas affiché. La fonction reçois trois paramètres :  l'objet jqXHR, une chaîne de caractère correspondant à l'état de la requête et l'objet erreur - voir la [fonction jQuery AJAX](http://api.jquery.com/jQuery.ajax/).
**complete** | une fonction callback à exécuter lorsque la requête as été exécutée, peu importe son état de retour.
**form** | un élément `form` à utiliser en tant que source de données de la requête, défini en tant que sélecteur CSS ou d'élément DOM.
**flash** | quand défini à true, indique au serveur de vider et la possibilité de renvoyer un message "toast". Par défaut à false.
**files** | quand défini à true, la requête accepte les chargement de fichiers, cela requiert le support par le navigateur de l'interface `FormData`. Par défaut à false.
**loading** | optionnel: une chaîne de caractère ou un objet à afficher lorsque la requête est en cours. La chaîne de caractère doit être le sélecteur CSS d'un élément, l'objet doit supporter les fonctions `show()` et `hide()` pour gérer sa visibilité. Vous pouvez passer l'objet global `$.oc.stripeLoadIndicator` lorsque vous utilisé les [fonctionnalités supplémentaires](../ajax/extras).

Vous pouvez aussi certaines parties de la logique de la requête en passant les nouvelles méthodes comme options. Ces méthodes de la logique sont disponibles.

Méthodes | Description
------------- | -------------
**handleConfirmMessage(message)** | appelée lorsque vous demandez la confirmation de l'utilisateur.
**handleErrorMessage(message)** | appelée lorsqu'un message d'erreur doit apparaître.
**handleValidationMessage(message, fields)** | donnes le focus au premier champs invalide lorsque la validation est utilisée.
**handleFlashMessage(message, type)** | appelée lorsqu'un message flash est fournis en utilisant l'option **flash** (voir ci-dessus).
**handleRedirectResponse(url)** | appelée lorsque le navigateur doit redirigé vers une autre page.

<a name="javascript-examples"></a>
## Exemples d'utilisation

Demande la confirmation de l'utilisateur avant l'envoi de la requête onDelete:

    $('form').request('onDelete', {
        confirm: 'Êtes-vous sûr?',
        redirect: '/dashboard'
    })

Exécute le gestionnaire `onCalculate` et inject le partiel **calcresult** à l'intérieur de l'élément de la page avec la classe CSS **result**:

    $('form').request('onCalculate', {
        update: {calcresult: '.result'}
    })

Exécute le gestionnaire `onCalculate` avec des données additionnelles:

    $('form').request('onCalculate', {data: {value: 55}})

Exécute le gestionnaire `onCalculate` et exécutes un code avant que les éléments de la page ne soit mise à jour:

    $('form').request('onCalculate', {
        update: {calcresult: '.result'},
        beforeUpdate: function() { /* fait quelque-chose */ }
    })

Exécute le gestionnaire `onCalculate` et s’il retourne un succès, exécutes du code puis la méthode `success` par défaut:

    $('form').request('onCalculate', {success: function(data) {
        //... fait quelque-chose ...
        this.success(data);
    }})

Exécute une requête sans élément FORM:

    $.request('onCalculate', {
        success: function() {
            console.log('Fini!');
        }
    })

Exécute le gestionnaire `onCalculate` et s’il retourne un succès, exécutes du code après que la méthode `success` par défaut ai terminée:

    $('form').request('onCalculate', {success: function(data) {
        this.success(data).done(function() {
            //... fait quelque-chose après que la méthode success() ai été exécutée ...
        });
    }})

<a name="global-events"></a>
## Gestionnaire AJAX globaux

Le framework ajax émets certains évènements sur les éléments mis à jours, les éléments déclencheurs, le formulaire et l'objet window. Les évènements sont émis peu importe l'API utilisée - l'API des attributs de données ou l'API JavaScript.

Évènements | Description
------------- | -------------
**ajaxBeforeSend** | émis sur l'objet window avant que la requête ne soit envoyée
**ajaxBeforeUpdate** | émis sur l'objet `form` juste après que la requête ai été terminée mais avant la mise à jour de la page. La méthode prends 5 paramètres: l'objet de l'évènement, l'objet contexte, l'objets des données reçu du serveur, le status de la requête au format texte, l'objet jqXHR.
**ajaxUpdate** | émis sur un élément de la page après qu'il ai été mis à jour par le framework. La méthode prends 5 paramètres: l'objet de l'évènement, l'objet contexte, l'objets des données reçu du serveur, le status de la requête au format texte, l'objet jqXHR.
**ajaxUpdateComplete** | émis sur l'objet `window` après que tous les éléments ai été mis à jours par le framwork. La méthode prends 5 paramètres: l'objet de l'évènement, l'objet contexte, l'objets des données reçu du serveur, le status de la requête au format texte, l'objet jqXHR.
**ajaxSuccess** | émis sur l'objet `form` après que la requête ai été exécuté __avec succès__. La méthode prends 5 paramètres: l'objet de l'évènement, l'objet contexte, l'objets des données reçu du serveur, le status de la requête au format texte, l'objet jqXHR.
**ajaxError** | émis sur l'objet `form` si la requête as échouée. La méthode prends 5 paramètres: l'objet de l'évènement, l'objet contexte, le message d'erreur, le status de la requête au format texte, l'objet jqXHR.
**ajaxErrorMessage** | émis sur l'objet `window` si la requête as échouée. La méthode prends 2 paramètres: l'objet de l'évènement, le message d'erreur retourné par le serveur.
**ajaxConfirmMessage** | émis sur l'objet `window` lorsque l'option `confirm` est demandée. La méthode prends 2 paramètres: l'objet de l'évènement, le message de confirmation comme indiqué dans l'option `confirm`. Cela est utile pour implémenter une interface customisée de confirmation au lieu de celle fournie nativement par JavaScript. 

Ces évènements sont émis sur l'éléments déclencheur:

Évènements | Description
------------- | -------------
**ajaxSetup** | émis avant que la requête ne soit formée, permets aux options d'être modifiés via l'objet `context.options`.
**ajaxPromise** | émis juste avant que la requête AJAX ne soit émise.
**ajaxFail** | émis seulement si la requête échoue.
**ajaxDone** | émis seulement si la requête est réussie.
**ajaxAlways** | émis peu importe si la requête échoue ou est réussie.

<a name="global-events-examples"></a>
## Exemples d'utilisation

Exécute du code JavaScript lorsque l'évènement `ajaxUpdate` est émis sur un élément.

    $('.calcresult').on('ajaxUpdate', function() {
        console.log('Mis à jour!');
    })

Exécute une seule requête qui affiche un message Flash utilisant une logique customisée.

    $.request('onDoSomething', {
        flash: 1,
        handleFlashMessage: function(message, type) {
            $.oc.flashMsg({ text: message, class: type })
        }
    })

Applique une configuration globale à toutes les requêtes AJAX.

    $(document).on('ajaxSetup', function(event, context) {
        // Active la gestion de message Flash sur toutes les requêtes AJAX
        context.options.flash = true

        // Active l'indicateur de chargement StripeLoadIndicator sur toutes les requêtes AJAX
        context.options.loading = $.oc.stripeLoadIndicator

        // Gères les messages d'erreurs en émétant un message flash de type "erreur"
        context.options.handleErrorMessage = function(message) {
            $.oc.flashMsg({ text: message, class: 'error' })
        }

        // Gères les messages d'erreurs en émétant un message flash selon le type retourné
        context.options.handleFlashMessage = function(message, type) {
            $.oc.flashMsg({ text: message, class: type })
        }
    })
