# {% for %}

Les balises `{% for %}` et `{% endfor %}` feront une boucle sur chaque valeur d'une collection. Une collection peut être un tableau ou un objet implémentant l'interface `Traversable`.

    <ul>
        {% for user in users %}
            <li>{{ user.username }}</li>
        {% endfor%}
    </ul>

Vous pouvez également accéder aux clés et aux valeurs :

    <ul>
        {% for key, user in users%}
            <li>{{key}}: {{user.username}}</li>
        {% endfor%}
    </ul>

Si la collection est vide, vous pouvez rendre un bloc de remplacement en utilisant else :

    <ul>
        {% for user in users %}
            <li>{{ user.username }}</li>
        {% else %}
            <li><em>Aucun utilisateur trouvé</em></li>
        {% endfor %}
    </ul>

## Boucler une collection

Si vous avez besoin d'itérer sur une collection de nombres, vous pouvez utiliser l'opérateur `..`:

    {% for i in 0..10 %}
        - {{ i }}
    {% endfor %}

L'extrait du code ci-dessus afficherait tous les nombres de 0 à 10.

Il peut également être utile avec des lettres :

    {% for lettre in 'a'..'z' %}
        - {{ lettre }}
    {% endfor %}

L'opérateur `..` peut prendre n'importe quelle expression des deux côtés :

    {% for lettre in 'a'|upper..'z'|upper %}
        - {{ lettre }}
    {% endfor %}

## Ajout d'une condition

Contrairement à PHP, il n'y a pas de fonction pour `break` ou `continue` dans une boucle, mais vous pouvez toujours filtrer la collection.
L'exemple suivant ignore tous les `utilisateurs` qui ne sont pas actifs :

    <ul>
        {% for user in users if user.active %}
            <li>{{ user.username }}</li>
        {% endfor %}
    </ul>

## La variable de boucle

À l'intérieur d'un bloc de boucle `for`, vous pouvez accéder à certaines variables spéciales :

| Variable         | Description                                                                     |
| ---------------- | ------------------------------------------------------------------------------- |
| `loop.index`     | L'itération actuelle de la boucle. *(l'index commence à 1)*                     |
| `loop.index0`    | L'itération actuelle de la boucle. *(l'index commence à 0)*                     |
| `loop.revindex`  | Le nombre d'itérations à partir de la fin de la boucle *(l'index commence à 1)* |
| `loop.revindex0` | Le nombre d'itérations à partir de la fin de la boucle *(l'index commence à 0)* |
| `loop.first`     | Vrai si première itération                                                      |
| `loop.last`      | Vrai si dernière itération                                                      |
| `loop.length`    | Le nombre d'élément dans la collection                                          |
| `loop.parent`    | Le contexte parent                                                              |

    {% for user in users%}
        {{loop.index}} - {{user.username}}
    {% endfor%}
