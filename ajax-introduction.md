# Débuter avec AJAX

- [Introduction](#introduction)

- [Intégrer le framework](#framework-script)

- [Comment fonctionne les requêtes AJAX](#how-ajax-works)

- [Exemples d'utilisation](#usage-example)

<a name="introduction"></a>

## Introduction

October intègre un framework qui apporte une suite complète de fonctionnalités AJAX qui vous permet de charger des données depuis le serveur sans rafraîchissement de la page. La même libraire peut être utilisée depuis depuis un [theme](../cms/themes) et n'importe où dans le [panneau d'administration](../backend/controllers-ajax#ajax).

Le framework AJAX existe en deux versions, vous pouvez autant utiliser [L'API JavaScript](../ajax/javascript-api) out [l'API des attributs de données](../ajax/attributes-api). L'API des attributs de données ne nécessite aucune connaissance JavaScript pour utiliser AJAX avec OctoberCMS.

<a name="framework-script"></a>

## Intégrer le framework
Le framework AJAX est optionnel dans votre [theme](../cms/themes), pour utiliser la librairie, vous devez l'intégrer en plaçant la syntaxe `{% framework %}` n'importe où dans votre [page](../cms/pages) ou [layout](../cms/layouts). Cela intègres la librairie JavaScript front-end. La librairie requiert jQuery, vous devez donc l'intégrer avant, par exemple :
```html
<script src="{{ 'assets/javascript/jquery.js'|theme }}"></script>

{% framework %}
```
La syntaxe `{% framework %}` supportes le paramètre **extras**. Si ce paramètre est mentionné, cela integreras les feuilles de styles et fichiers JavaScript pour les [fonctionnalités supplémentaires](../ajax/extras), comme la validation de formulaire ou l'indicateur de chargement.
```html
{% framework extras %}
```
<a name="how-ajax-works"></a>

## Comment fonctionnent les requêtes AJAX

Une page peut émettre une requête AJAX autant depuis L'API des attributs de données qu'en utilisant JavaScript. Chaque requête invoque un **écouteur d'événement** -- aussi appelé un [écouteur AJAX](../ajax/handlers) -- depuis le serveur et peut mettre à jour des éléments de la page en utilisant des partiels. Les requêtes AJAX fonctionnent à son plein potentiel lorsqu'il est utilisé dans un formulaire puis les données du formulaire sont automatiquement envoyé au serveur. Voici le fonctionnement d'une requête :

1. Le navigateur client émet une requête AJAX en fournissant le nom de l'écouteur et d'éventuels autres paramètres optionnels.
2. Le serveur retrouve [l'écouteur AJAX](../ajax/handlers) et l'exécute.
3. L'écouteur exécuté la logique nécessaire et met à jour l'environnement en insérant des variables de page.
4. Le serveur [généres les partiels](../ajax/update-partials) requis par le client avec l'option `update`.
5. Le serveur retourne la réponse, qui contient la syntaxe des partiels générés.
6. Le framework du côté du client met à jour les éléments de la page avec les données des partiels reçu du serveur.

> **Note**: Selon le contexte de la page, la vue d'[un partiel du CMS](../cms/partials) ou d'[un partiel back-end](../backend/views-partials) sera générée.

<a name="usage-example"></a>

## Exemple d'utilisation

Ci-dessous retrouvez un exemple utilisant l'API des attributs de données pour créer un formulaire utilisant AJAX. Le formulaire va émettre une requête AJAX vers l'écouteur **onTest** et demander à ce que le conteneur du résultat soit mis à jour avec la syntaxe de la partiel **monpartiel**.
```html
<!-- Formulaire utilisant AJAX -->

<form data-request="onTest" data-request-update="monpartiel: '#monDiv'">

<!-- Insérer deux valeurs -->

<input name="valeur1"> + <input name="valeur2">

<!-- Bouton d'action -->

<button type="submit">Calculer</button>

</form>

<!-- Result container -->

<div id="monDiv"></div>
```
> **Note**: Les données `valeur1` et `valeur2` sont automatiquement envoyé avec la requête AJAX.

Le partiel **monpartiel** contiens la syntaxe qui lis le résultat de la variable `resultat`.
```html
Le résultat est {{ resultat }}
```
La méthode de l'écouteur **onTest** accèdes au données du formulaire à l'aide de la [fonction d'aide](../services/helper#method-input) `input` et le résultat est passé à la variable de page `resultat`.
```html
function onTest()

{

    $this->page['resultat'] = input('valeur1') + input('valeur2');

}
```

L'exemple peut être lu ainsi: "Quand le formulaire est envoyé, émet une requête AJAX vers l'écouteur **onTest**. Quand l'écouteur as terminé, il génère le partiel **monpartiel** et injectes son contenu dans l'élément **#monDiv**".