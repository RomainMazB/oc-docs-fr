# AJAX Event Handlers

- [Écouteurs AJAX](#ajax-handlers)
    - [Appeler un écouteur AJAX](#calling-handlers)
- [Rediriger dans un écouteur AJAX](#redirects-in-handlers)
- [Retourner des données depuis un écouteur AJAX](#returning-data-from-handlers)
- [Émettre une erreur dans un écouteur AJAX](#throw-ajax-exception)
- [Exécuter du code avant les écouteurs](#before-handler)

<a name="ajax-handlers"></a>
## Écouteurs AJAX

Les Écouteurs AJAX sont des fonctions PHP qui peuvent être définies dans la [section PHP](../cms/themes#php-section) d'une page, d'un layout, ou dans un [composant](../cms/components). Le nom de l'écouteur doit suivre la syntaxe suivante: 'onQuelqueChose'. Tous les écouteurs supportent la [mise à jour de partiels](../ajax/update-partials) en tant que requête AJAX. @TODO: vérifier la dernière phrase

    function onSubmitContactForm()
    {
        // ...
    }

Si deux écouteurs avec le même nom sont définis simultanément dans une page et un layout, l'écouteur de la page sera exécuté. Les écouteurs définis dans les [composants](../cms/components) ont la plus petite priorité.

<a name="calling-handlers"></a>
### Appeler un écouteur

Toute requête AJAX doit spécifier un nom d'écouteur, que vous utilisiez l'[API des attributs de données](../ajax/attributes-api), ou l'[API JavaScript(../ajax/javascript-api). Lorsqu'une requête est faite, le serveur va chercher tous les écouteurs enregistrés et prendre le premier qu'il trouve.

    <!-- Attributes API -->
    <button data-request="onSubmitContactForm">Go</button>

    <!-- JavaScript API -->
    <script> $.request('onSubmitContactForm') </script>

Si deux composants ont enregistré un écouteur portant le même nom, il est conseillé de préfixer l'écouteur avec [l'alias](../cms/components#aliases) du composant. Si un composant utilise un alias **moncomposant**, l'écouteur peut être ciblé avec `moncomposant::onQuelquechose`.

    <button data-request="mycomponent::onSubmitContactForm">Go</button>

Vous pouvez utiliser la variable référence [`__SELF__`](https://octobercms.com/docs/plugin/components#referencing-self) à la place d'un alias hardcodé dans le cas ou l'utilisateur change l'alias du composant utilisé sur la page.

    <form data-request="{{ __SELF__ }}::onCalculate" data-request-update="'{{ __SELF__ }}::calcresult': '#result'">

#### Écouteur générique

Parfois vous pourriez avoir besoin d'effectuer une requête AJAX dans le seul but de mettre à jour du contenu sur la page, sans nécessairement exécuter du code. Vous pouvez utiliser l'écouteur `onAjax` dans ce but. Cet écouteur est disponible partout, sans besoin d'écrire aucun code.

    <button data-request="onAjax">Do nothing</button>

<a name="redirects-in-handlers"></a>
## Rediriger dans un écouteur AJAX

Si vous avez besoin de rediriger le navigateur à une autre adresse, retournez l'objet `Redirect` depuis l'écouteur AJAX. Le framework redirigeras le navigateur dès que la réponse sera retourné par le serveur :

    function onRedirectMe()
    {
        return Redirect::to('https://google.fr');
    }

<a name="returning-data-from-handlers"></a>
## Retourner des données depuis un écouteur AJAX

Dans des cas précis, vous pourriez vouloir retourner des données structurées depuis vos écouteurs AJAX. Si un écouteur AJAX retourne un tableau, vous pourrez y accéder dans la méthode liée à l'évènement `success` :

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
## Exécuter du code avant les écouteurs

Vous pourriez vouloir exécuter du code avant l'exécution d'un écouteur. En définissant la méthode `onInit` dans [le cycle de vie d'exécution de la page](../cms/layouts#dynamic-pages), cela vous permet d'exécuter du code avant l'exécution de tous les écouteurs AJAX.

    function onInit()
    {
        // Depuis la section PHP d'une page ou d'un layout
    }

Vous pouvez aussi définir ceci dans la fonction `init` d'une [classe de composant](../plugin/components#page-cycle-init), ou d'une classe de [widget backend](../backend/widgets).

    function init()
    {
        // Depuis la classe d'un composant ou d'un widget
    }
