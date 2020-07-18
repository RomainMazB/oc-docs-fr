# {% if %}

Les balises `{% if %}` et `{% endif %}` représenteront une expression et sont comparables aux instructions if du PHP. Dans sa forme la plus simple, vous pouvez l'utiliser pour tester si une expression est évaluée à `true`:

    {% if online == false %}
        <p>Le site Web est en mode maintenance.</p>
    {% endif %}

Vous pouvez également tester si un tableau n'est pas vide:

    {% if users %}
        <ul>
            {% for user in users %}
                <li>{{ user.username }}</li>
            {% endfor %}
        </ul>
    {% endif %}

> **Remarque**: Si vous voulez tester si la variable `users` est définie, utilisez `{% if users is defined %}` à la place.

Vous pouvez également utiliser `not` pour vérifier les valeurs évaluées à `false`:

    {% if not user.subscribed %}
        <p>Vous n'êtes pas abonné à notre liste de diffusion.</p>
    {% endif %}

Pour plusieurs expressions, `{% elseif %}` et `{% else %}` peuvent être utilisés:

    {% if kenny.sick %}
        Kenny est malade.
    {% elseif kenny.dead %}
        Tu as tué Kenny! Enfoiré!!!
    {% else %}
        Kenny a l'air bien pour l'instant.
    {% endif %}

## Les règles d'expression

Les règles pour déterminer si une expression est vraie ou fausse sont les mêmes qu'en PHP, voici les règles des cas marginaux:

| Valeur                    | Évaluation booléenne |
| ------------------------- | -------------------- |
| _chaîne vide_             | faux                 |
| _zéro numérique_          | faux                 |
| _caractères d'espacement_ | vrai                 |
| _tableau vide_            | faux                 |
| _null_                    | faux                 |
| _tableau non vide_        | vrai                 |
| _objet_                   | vrai                 |
