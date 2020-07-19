# form()

Les fonctions préfixées par `form_` exécutent des tâches utiles lors du traitement des formulaires. Le helper est directement mappé sur la classe PHP `Form` et ses méthodes. Par exemple:

    {{ form_close() }}

est l'équivalent en PHP de:

    <?= Form::close() ?>

**Remarque**: Les méthodes en _camelCase_ (dans la classe PHP `Form`) doivent être converties en _snake_case_ (pour Twig).

## form_open()

Produit une balise d'ouverture FORM standard (non AJAX).

    {{ form_open() }}

Les attributs peuvent être passés dans le premier argument.

    {{ form_open({ classe: 'form-horizontal' }) }}

L'exemple ci-dessus produirait le code suivant:

    <form class="form-horizontal">

Il existe des options spéciales qui peuvent également être utilisées à côté des attributs.

    {{ form_open({ request: 'onUpdate' }) }}

La fonction prend en charge les options suivantes:

| Option      | La description                                                                                                                                                                                                       |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **method**  | Méthode du Request. Correspond à l'attribut **method** de la balise FORM. Par exemple: POST, GET, PUT, DELETE                                                                                                        |
| **request** | Un nom de gestionnaire à exécuter coté serveur lorsque le formulaire est publié. Voir l'article [Manipulation des formulaires](../cms/pages#handling-forms) pour plus de détails sur les gestionnaires d'événements. |
| **url**     | Spécifie l'URL sur laquelle le formulaire poste. Correspond à l'attribut **action** de la balise FORM.                                                                                                               |
| **files**   | Détermine si le formulaire soumettra des fichiers. Valeurs acceptées: **true** et **false**.                                                                                                                         |
| **model**   | Un objet du modèle pour la liaison du formulaire au modèle.                                                                                                                                                          |

## form_ajax()

Génère une balise d'ouverture FORM gérée par AJAX. Le premier paramètre de la fonction `form_ajax()` est le nom du gestionnaire AJAX. Le gestionnaire peut être défini dans la [section du code PHP](../cms/themes#php-section) dans la maquette ou la page, il peut aussi être défini dans un composant. Vous pouvez trouver plus d'informations sur AJAX dans l'article [Framework AJAX](../ajax/introduction).

    {{ form_ajax('onUpdate') }}

Les attributs peuvent être passés dans le deuxième argument.

    {{ form_ajax('onSave', { class: 'form-horizontal' }) }}

L'exemple ci-dessus produirait le code suivant:

    <form data-request="onSave" class="form-horizontal">

Certaines options spéciales peuvent également être utilisées avec les attributs.

    {{ form_ajax('onDelete', { data: { id: 2 }, confirm: 'Really delete this record?' }) }}

    {{ form_ajax('onRefresh', { update: { statistics: '#statsPanel' } }) }}

> **Remarque:** Lorsque vous essayez de référencer l'alias d'un composant avec `__SELF__` comme argument de `form_ajax ()`, vous devez d'abord créer la chaîne que vous souhaitez utiliser en dehors de l'appel lui-même. Exemple:

    {% set targetPartial = "'" ~ __SELF__ ~ "::statistics': '#statsPanel'" %}
    {{ form_ajax('onUpdate', { update: targetPartial }) }}

La fonction prend en charge les options suivantes:

| Option       | La description                                                                                        |
| ------------ | ----------------------------------------------------------------------------------------------------- |
| **success**  | Chaîne JavaScript à exécuter en cas de succès.                                                        |
| **error**    | Chaîne JavaScript à exécuter en cas d'échec du résultat.                                              |
| **confirm**  | Un message de confirmation à afficher avant d'envoyer la requête.                                     |
| **redirect** | En cas de succès, redirigez vers une URL.                                                             |
| **update**   | Un tableau de partiels à mettre à jour en cas de succès au format suivant: { 'partial': '#element' }. |
| **data**     | Données supplémentaires à inclure dans le request au format suivant: { 'mavariable': 'mavaleur' }.    |

## form_close()

Produit une balise de fermeture FORM standard. Cette balise est généralement disponible pour assurer une cohérence d'utilisation.

    {{ form_close () }}

L'exemple ci-dessus produirait:

    </form>

## Passage d'attributs à l'élément généré

Vous pouvez passer des attributs supplémentaires à la méthode `Form::open()` en passant un tableau de noms d'attributs et de valeurs à rendre sur l'élément `<form>` final généré .

    <?= Form::open(['id' => 'exemple', 'class' => 'quelque-chose']) ?>
        // ..
    <?= Form::close() ?>

L'exemple ci-dessus produirait ce qui suit:

    <form method="POST" action="" accept-charset="UTF-8" id="exemple" class="quelque-chose">
        // ..
    </form>
