# this.environment

Vous pouvez accéder à l'objet d'environnement actuel via `this.environment`.
Celui-ci renvoie une chaîne faisant référence à la [configuration de l'environnement actuelle](../setup/configuration#environment-config).

## Exemple

L'exemple suivant affichera une bannière si le site Web s'exécute dans l'environnement de test :

     {% if this.environment == 'test'%}

         <div class="banner">Environnement de test</div>

    {% endif %}
