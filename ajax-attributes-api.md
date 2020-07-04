# AJAX - L'API des atributs de données

- [L'API des attributs de données](#data-attributes)
- [Exemples d'utilisation](#data-attribute-examples)

<a name="data-attributes"></a>
## L'API des attributs de données
L'API des attributs de données vous permets d'émettre des requêtes AJAX sans aucun JavaScript. Dans beaucoup de cas,
cette interface est beaucoup moins verbeuse que l'interface JavaScript - vous écrivez moins de code pour un résultat
identique. Les attributs de données supportés sont :

Attribut | Description
------------- | -------------
**data-request** | Détermine le nom de la méthode AJAX.
**data-request-confirm** | Détermine le message de confirmation. La confirmation est affichée avant l'envoi de la requête. Si l'utilisateur clique sur le bouton "Annuler", la requête n'est pas envoyée.
**data-request-redirect** | Détermine l'URL de redirection après que la requête AJAX ai réussie.
**data-request-url** | Détermine l'URL vers laquelle la requête doit être envoyée. Par défaut: `window.location.href`
**data-request-update** | Détermine une liste de partiels et d'éléments de la page (à l'aide de sélecteur CSS) à mettre à jour. Le format est le suivant: `partiel: sélecteur, partiel: sélecteur`. L'utilisation des guillements peut être nécessaire dans certains cas, par exemple: `'my-partial': '#myelement'`. Si le nom du sélecteur est précédé par un arobase `@`, le contenu retourné par le serveur sera ajouté avant tout contenu du sélecteur, au lieu de remplacer le contenu existant. Si le nom du sélecteur se termine par le symbole `^`, alors le contenu sera ajouté à la fin.
**data-request-ajax-global** | `false` par défaut. Le mettre à `true` active les [évènements ajax](http://api.jquery.com/category/ajax/global-ajax-event-handlers/) globalement : `ajaxStart`, `ajaxStop`, `ajaxComplete`, `ajaxError`, `ajaxSuccess` et `ajaxSend`.
**data-request-data** | Détermine des données `POST` additionnelles qui seront envoyé au serveur. Le format est le suivant: `variable: valeur, variable: valeur`. Utilisation des guillemets si nécessaire: `variable: 'une chaîne de caractères'`. L'attribut peut être utilisé sur l'élément déclencheur, par exemple sur le bouton qui as aussi l'attribut `data-request`, sur l'élément parent le plus proche de l'élément déclencheur, ou sur l'élément `form` parent. Le framework fusionnes automatiquement ces paramètres avec ceux de `data-request-data`. Si un paramètre portant le même nom est déterminé sur plusieurs éléments, le framework suivra cette ordre de priorité: l'élément déclencheur avec l'attribut `data-request-data`, l'élément parent le plus proche avec l'attribut `data-request-data`, les données du formulaire `form` parent.
**data-request-before-update** | Détermine du code JavaScript a exécuter avant la mise à jour du contenu. Dans le code JavaScript, vous avez accès aux données suivantes: `this` (la page ayant émis la requête), l'object `context`, l'objet `data` contenant les données retournées par le serveur, la chaîne de caractère `textStatus`, et l'objet de la requête AJAX `jqXHR`.
**data-request-success** | Détermine du code JavaScript a exécuter après le succès de la requête. Dans le code JavaScript, vous avez accès aux données suivantes: `this` (la page ayant émis la requête), l'object `context`, l'objet `data` contenant les données retournées par le serveur, la chaîne de caractère `textStatus`, et l'objet de la requête AJAX `jqXHR`.
**data-request-error** | Détermine du code JavaScript si le serveur retourne une erreur. Dans le code JavaScript, vous avez accès aux données suivantes: `this` (la page ayant émis la requête), l'object `context`, la chaîne de caractère `textStatus`, et l'objet de la requête AJAX `jqXHR`.
**data-request-complete** | Détermine du code JavaScript a exécuter à la réponse du serveur, peut importe le status (échec ou réussite). Dans le code JavaScript, vous avez accès aux données suivantes: `this` (la page ayant émis la requête), l'object `context`, la chaîne de caractère `textStatus`, et l'objet de la requête AJAX `jqXHR`.
**data-request-loading** | Détermine le sélecteur CSS d'un élément devant être affiché pendant l'exécution de la requête. Vous pouvez utiliser cette option pour afficher un statut de chargement AJAX pendant la requête. Cette fonctionnalité utilise les méthodes jQuery `show()` et `hide()` pour déterminer la visibilité de l'élément.
**data-request-form** | Détermine explicitement un élément de formulaire à utiliser en tant que source de données pour la requête. Si non précisé, l'élément `form` le plus proche de l'élément déclencheur est utilisé, valable si l'élément lui-même est un formulaire.
**data-request-flash** | Quand spécifié, cette option indique au serveur la possibilité de retourner un message flash de tout type. Cette option est aussi utilisée par les [fonctionnalités supplémentaires](../ajax/extras#ajax-flash).
**data-request-files** | Quand spécifié, la requête acceptes l'envoie de fichier, cela requiert le support par le navigateur de l'interface `FormData`.
**data-track-input** | Peut être appliquée sur un champs texte, numérique ou mot de passe ayant aussi l'attribut `data-request`. Quand spécifié, le champs envoies automatiquement une requête AJAX request lorsque l'utilisateur y saisi quelquechose. Le paramètre optionnel `value` peut définir un délai interval en millisecondes avant que le framework émettes les requêtes.

Lorsque l'attribut `data-request` est spécifié sur un élément, l'élément déclenche une requête AJAX lorsque l'utilisateur interagis avec lui. Selon le type d'élément la requête est envoyée aux évènements suivants :

Element | Event
------------- | -------------
**Formulaires** | Lorsque le formulaire est envoyé.
**Liens, boutons** | Lorsque l'élément est cliqué.
**Champs texte, numérique et mot de passe** | Lorsque sa valeur change et seulement lorsque délai défini par `data-track-input` est dépassé.
**Liste de sélection, cases à cocher (checkbox et radio)** | Lorsque l'élément est coché.

<a name="data-attribute-examples"></a>
## Exemples d'utilisation

Appelles la méthode `onCalculate` lorsque le formulaire est envoyé. Met à jour le contenu de l'élément dont l'id est
"result" avec le partiel **calcresult** :

    <form data-request="onCalculate" data-request-update="calcresult: '#result'">

Demande une confirmation lorsque le bouton "Supprimer" est cliqué, avant d'envoyer la requête

    <form ... >
        ...
        <button data-request="onDelete" data-request-confirm="Êtes vous sûr(e)?">Supprimer</button>

Redirige vers une autre page après le succès de la requête :

    <form data-request="onLogin" data-request-redirect="/admin">

Affiche un popup après le succès de la requête :

    <form data-request="onLogin" data-request-success="alert('Yay!')">

Envoie un paramètre `mode` avec la valeur `update`:

    <form data-request="onUpdate" data-request-data="mode: 'update'">

Envoie un paramètre `id` avec la valeur `7` à travers plusieurs éléments :

    <div data-request-data="id: 7">
        <button data-request="onDelete">Supprimer</button>
        <button data-request="onSave">Mettre à jour</button>
    </div>

Insérer des [fichiers à uploader](../services/request-input#files) avec une requête :

    <form data-request="onSubmit" data-request-files>
        <input type="file" name="photo" accept="image/*" />
        <button type="submit">Envoyer</button>
    </form>
