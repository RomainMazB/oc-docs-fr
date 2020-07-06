# AJAX Event Handlers

- [Écouteurs AJAX](#ajax-handlers)
    - [Appeler un gestionnaire AJAX](#calling-handlers)
- [Rediriger dans un gestionnaire AJAX](#redirects-in-handlers)
- [Retourner des données depuis un gestionnaire AJAX](#returning-data-from-handlers)
- [Émettre une erreur dans un gestionnaire AJAX](#throw-ajax-exception)
- [Exécuter du code avant les gestionnaires](#before-handler)

<a name="ajax-handlers"></a>
## Écouteurs AJAX

Les gestionnaires AJAX sont des fonctions PHP qui peuvent être définies dans la [section PHP](../cms/themes#php-section) d'une page, d'un layout, ou dans un [composant](../cms/components). Le nom du gestionnaire doit suivre la syntaxe suivante: 'onQuelqueChose'. Tous les gestionnaires supportent la [mise à jour de partiels](../ajax/update-partials) en tant que requête AJAX. @TODO: vérifier la dernière phrase

    function onSubmitContactForm()
    {
        // ...
    }

Si deux gestionnaires avec le même nom sont définis simultanément dans une page et un layout, le gestionnaire de la page sera exécuté. Les gestionnaires définis dans les [composants](../cms/components) ont la plus petite priorité.

<a name="calling-handlers"></a>
### Appeler un gestionnaire

Toute requête AJAX doit spécifier un nom d'un gestionnaire, que vous utilisiez l'[API des attributs de données](../ajax/attributes-api), ou l'[API JavaScript(../ajax/javascript-api). Lorsqu'une requête est faite, le serveur va chercher tous les gestionnaires enregistrés et prendre le premier qu'il trouve.

    <!-- Attributes API -->
    <button data-request="onSubmitContactForm">Go</button>

    <!-- JavaScript API -->
    <script> $.request('onSubmitContactForm') </script>

Si deux composants ont enregistré un gestionnaire portant le même nom, il est conseillé de préfixer le gestionnaire avec [l'alias](../cms/components#aliases) du composant. Si un composant utilise un alias **moncomposant**, le gestionnaire peut être ciblé avec `moncomposant::onQuelquechose`.

    <button data-request="mycomponent::onSubmitContactForm">Go</button>

Vous pouvez utiliser la variable référence [`__SELF__`](https://octobercms.com/docs/plugin/components#referencing-self) à la place d'un alias codé en dur dans le cas ou l'utilisateur change l'alias du composant utilisé sur la page.

    <form data-request="{{ __SELF__ }}::onCalculate" data-request-update="'{{ __SELF__ }}::calcresult': '#result'">

#### Gestionnaire générique

Parfois vous pourriez avoir besoin d'effectuer une requête AJAX dans le seul but de mettre à jour du contenu sur la page, sans nécessairement exécuter du code. Vous pouvez utiliser le gestionnaire `onAjax` dans ce but. Cet gestionnaire est disponible partout, sans besoin d'écrire aucun code.

    <button data-request="onAjax">Do nothing</button>

<a name="redirects-in-handlers"></a>
## Rediriger dans un gestionnaire AJAX

Si vous avez besoin de rediriger le navigateur à une autre adresse, retournez l'objet `Redirect` depuis le gestionnaire AJAX. Le framework redirigera le navigateur dès que la réponse sera retourné par le serveur :

    function onRedirectMe()
    {
        return Redirect::to('https://google.fr');
    }

<a name="returning-data-from-handlers"></a>
## Retourner des données depuis un gestionnaire AJAX

Dans des cas précis, vous pourriez vouloir retourner des données structurées depuis vos gestionnaires AJAX. Si un gestionnaire AJAX retourne un tableau, vous pourrez y accéder dans la méthode liée à l'évènement `success` :

    function onFetchDataFromServer()
    {
        /* Du code exécuté par le serveur */

        return [
            'totalUsers' => 1000,
            'totalProjects' => 937
        ];
    }

Les données peuvent être récupérées avec l'API des attributs de données :

    <form data-request="onHandleForm" data-request-success="console.log(data)">

La même chose avec l'API Javascript :

    <form
        onsubmit="$(this).request('onHandleForm', {
            success: function(data) {
                console.log(data);
            }
        }); return false;">

<a name="throw-ajax-exception"></a>
## Émettre une erreur AJAX

Vous pouvez émettre une [erreur AJAX](../services/error-log#ajax-exception) en utilisant la classe `AjaxException` pour traiter la réponse comme une erreur tout en gardant la possibilité de retourner des données comme habituellement. Renseignez simplement le contenu de la réponse en tant que premier argument de l'exception.

    throw new AjaxException([
        'error' => 'Pas assez de question',
        'questionsNeeded' => 2
    ]);

> **Note**: Lorsque vous retourner ce type d'erreur, les [partiels seront mis à jours](../ajax/update-partials) comme habituellement.

<a name="before-handler"></a>
## Exécuter du code avant les gestionnaires

Vous pourriez vouloir exécuter du code avant l'exécution d'un gestionnaire. En définissant la méthode `onInit` dans [le cycle de vie d'exécution de la page](../cms/layouts#dynamic-pages), cela vous permet d'exécuter du code avant l'exécution de tous les gestionnaires AJAX.

    function onInit()
    {
        // Depuis la section PHP d'une page ou d'un layout
    }

Vous pouvez aussi définir ceci dans la fonction `init` d'une [classe de composant](../plugin/components#page-cycle-init), ou d'une classe de [widget backend](../backend/widgets).

    function init()
    {
        // Depuis la classe d'un composant ou d'un widget
    }
