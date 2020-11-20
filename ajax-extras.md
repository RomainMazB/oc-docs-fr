# AJAX Extra features

- [Indicateur de chargement](#loader-stripe)
- [Validation de formulaire](#ajax-validation)
    - [Émettre une erreur de validation](#throw-validation-exception)
    - [Afficher les messages d'erreurs](#error-messages)
    - [Afficher les erreurs avec les champs](#field-errors)
- [Bouton de chargement](#loader-button)
- [Message Flash](#ajax-flash)
- [Exemples d'utilisation](#usage-example)

Lorsque vous utiliser le framework AJAX, vous devez définir le suffixe d'option **extras** qui intègre les feuilles de styles et scripts additionnels. Ces fonctionnalités sont très utiles lorsque vous utilisez AJAX sur la partie frontend du site.

    {% framework extras %}

<a name="loader-stripe"></a>
## Indicateur de chargement

La première fonctionnalité que vous devriez remarquer est l'indicateur de chargement affiché en haut de la page lorsqu'une requête AJAX est en attente. L'indicateur observent les [évènement globaux](../ajax/javascript-api#global-events) utilisé par le framework AJAX.
The first feature you should notice is a loading indicator that is displayed on the top of the page when an AJAX request runs. The indicator hooks in to [global events used by the AJAX framework.

Lorsqu'une requête AJAX est envoyé, l'évènement `ajaxPromise` est émis, ce qui affiche l'indicateur de chargement et met le curseur de la souris en état de chargement. Les évènements `ajaxFail` et `ajaxDone` sont utilisés pour détecter la fin de la requête, c'est à ce moment que l'indicateur disparaît.

<a name="ajax-validation"></a>
## Validation de formulaire

Vous devez définir l'attribut `data-request-validate` sur un formulaire pour activer la fonctionnalité de validation.

    <form
        data-request="onSubmit"
        data-request-validate>
        <!-- ... -->
    </form>

<a name="throw-validation-exception"></a>
### Émettre une erreur de validation

Dans la méthode PHP qui reçois la requête AJAX, vous devez émettre une [exception de validation](../services/error-log#validation-exception) en utilisant la classe `ValidationException` pour déterminer qu'un champs est invalide, dans laquelle le premier argument est un tableau. Le tableau doit utiliser le nom des champs en tant que clé, et les messages d'errurs pour valeurs.

    function onSubmit()
    {
        throw new ValidationException(['name' => 'Vous devez saisir un nom!']);
    }

> **Remarque** : Vous pouvez aussi passer une instance du [service de validation](../services/validation) comme premier paramètre de l'exception.

<a name="error-messages"></a>
### Afficher les messages d'erreurs

Dans un formulaire, vous pouvez afficher le premier message d'erreur en utilisant l'attribut ` data-validate-error` sur un élément conteneur. Le contenu de l'élément sera rempli par le message d'erreur et l'élément sera rendu visible.

    <div data-validate-error></div>
    
Pour afficher plusieurs messages d'erreur, intégrez un élément avec l'attribut `data-message`. Dans cette exemple, le paragraphe sera dupliqué et remplis à chaque message d'erreur retourné.

    <div class="alert alert-danger" data-validate-error>
        <p data-message></p>
    </div>

Pour ajouter des classes CSS lorsqu'une erreur de validation est retournée par AJAX, écoutez les évènements JavaScript `ajaxInvalidField` et `ajaxPromise`.

    $(window).on('ajaxInvalidField', function(event, fieldElement, fieldName, errorMsg, isFirst) {
        $(fieldElement).closest('.form-group').addClass('has-error');
    });
    
    $(document).on('ajaxPromise', '[data-request]', function() {
        $(this).closest('form').find('.form-group.has-error').removeClass('has-error');
    });

<a name="field-errors"></a>
### Afficher les messages d'erreur avec les champs

Vous pouvez aussi afficher des messages d'erreur individuel pour chaque champs en créant un élément qui utilise l'attribut `data-validate-for` et en renseignant le nom du champs comme valeur.

    <!-- Champs de saisie -->
    <input name="phone" />

    <!-- Message de validation du champs de saisie -->
    <div data-validate-for="phone"></div>

Si l'élément est laissé vide, il sera remplis par le message d'erreur retourné par le serveur. Sinon vous pouvez définir directement un texte de votre choix qui sera affiché.

    <div data-validate-for="phone">
        Oops.. Le numéro de téléphone est incorrect!
    </div>

<a name="loader-button"></a>
## Bouton de chargement

Lorsque n'importe quel élément a l'attribut `data-attach-loading`, la classe CSS `oc-loading` lui sera ajouté durant la requête AJAX. Cette classe ajoutes un *logo de chargement* et verrouille le bouton à l'aide du pseudo élément CSS `:after`.

    <form data-request="onSubmit">
        <button data-attach-loading>
            Envoyer
        </button>
    </form>

    <a
        href="#"
        data-request="onDoSomething"
        data-attach-loading>
        Faire quelque chose
    </a>

<a name="ajax-flash"></a>
## Messages Flash

Définissez l'attribut `data-request-flash` sur un formulaire pour activer l'utilisation de messages Flash au succès des requêtes AJAX. 

    <form
        data-request="onSuccess"
        data-request-flash>
        <!-- ... -->
    </form>

Combiné à l'utilisation de la façade `Flash` dans la méthode PHP, un message flash apparaîtras lorsque la requête est terminée.

    function onSuccess()
    {
        Flash::success('Vous avez réussi!');
    }

Pour conserver un aspect similaire sur tout le site pour vos messages flash, vous pouvez afficher un [message flash standard](../markup/tag-flash) en plaçant simplement ce code sur votre page, ou layout.

    {% flash %}
        <p
            data-control="flash-message"
            class="flash-message fade {{ type }}"
            data-interval="5">
            {{ message }}
        </p>
    {% endflash %}

<a name="usage-example"></a>
## Exemples d'utilisation

Ci-dessous un exemple complet de validation de formulaire. Il appelle la méthode `onDoSomething` qui active l'état de chargement du bouton, effectue la validation des champs du formulaire, puis retourne et affiche un message flash.

    <form
        data-request="onDoSomething"
        data-request-validate
        data-request-flash>

        <div>
            <input name="name" />
            <span data-validate-for="name"></span>
        </div>

        <div>
            <input name="email" />
            <span data-validate-for="email"></span>
        </div>

        <button data-attach-loading>
            Submit
        </button>

        <div class="alert alert-danger" data-validate-error>
            <p data-message></p>
        </div>

    </form>

La méthode PHP regarde les données `POST` transmise par le navigateur et vérifies les règles du validateur. Si la validation échoues, une exception `ValidationException` est émise, sinon un message `Flash::success` est retourné. 

    function onDoSomething()
    {
        $data = post();

        $rules = [
            'name' => 'required',
            'email' => 'required|email',
        ];

        $validation = Validator::make($data, $rules);

        if ($validation->fails()) {
            throw new ValidationException($validation);
        }

        Flash::success('Jobs done!');
    }
