# {% placeholder %}

La balise `{% placeholder %}` affichera un placeholder (une section réservée) qui est généralement [utilisé dans la maquette](../cms/layouts#placeholders).
Cette balise renverra tout le contenu du placeholder qui a été ajouté à l'aide de la balise `{% put %}`, ou tout contenu défini par défaut (facultatif).

    {% placeholder nom %}

Le contenu peut ensuite être injecté dans le placeholder dans n'importe quelle page ou partiel.

    {% put nom %}
        <p>Placez ce texte dans le placeholder "nom"</p>
    {% endput %}

<a name="default-placeholder-content"></a>
## Contenu par défaut du placeholder

Les placeholders peuvent avoir un contenu par défaut qui peut être remplacé ou complété par une page.
Si la balise `{% put %}` d'un placeholder avec un contenu par défaut n'est pas définie sur une page, le contenu du placeholder par défaut est affiché.

Exemple de définition d'un placeholder dans le modèle de la maquette :

    {% placeholder zonelateral default %}
        <p><a href="/contacts">Contactez-nous</a></p>
    {% endplaceholder %}

La page peut injecter plus de contenu dans le placeholder. La balise `{% default %}` spécifie un endroit où le contenu par défaut du placeholder doit être affiché.
Si la balise n'est pas utilisée, le contenu du placeholder est complètement remplacé.

    {% put zonelateral %}
        <p><a href="/services">Services</a></p>
        {% default %}
    {% endput %}

<a name="checking-placeholder-exits"></a>
## Vérification de l'existence d'un placeholder

Dans un modèle de maquette, vous pouvez vérifier si un contenu d'un placeholder existe en utilisant la fonction `placeholder()`.
Cela vous permet de générer un balisage différent selon que la page fournit ou non un contenu de placeholder. Exemple :

    {% if placeholder('menulateral') %}
        <!-- Balisage pour une page avec une zone latérale -->
        <div class="row">
            <div class="col-md-3">
                {% placeholder menulateral %}
            </div>
            <div class="col-md-9">
                {% page %}
            </div>
        </div>
    {% else %}
        <!-- Balisage pour une page sans zone latérale -->
        {% page %}
    {% endif %}

<a name="custom-placeholder-attributes"></a>
## Attributs personnalisés

La balise `placeholder` accepte deux attributs facultatifs, `title` et `type`.
- L'attribut `title` n'est pas utilisé par le CMS lui-même, mais pourrait être utilisé par d'autres plugins.
- L'attribut `type` gère le type du placeholder. Il existe deux types pris en charge pour le moment, **text** et **html**.

Le contenu des placeholders de type texte est échappé avant d'être affiché.
Les attributs `title` et `type` doivent être définis après le nom du placeholder et l'attribut `default`, s'il est présent.
Exemple :

    {% placeholder commande title="Informations de commande" type="text" %}

Exemple d'un placeholder avec un contenu par défaut et des attributs title et type.

    {% placeholder commande default title="Informations de commande" type="text" %}
        Il n'y a aucune information de commande pour ce produit.
    {% endplaceholder %}
