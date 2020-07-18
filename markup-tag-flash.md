# {% flash %}

Les balises `{% flash %}` et `{% endflash %}` rendront tous les messages flash stockés dans la session utilisateur, définis par la classe PHP `Flash`. La variable `message` à l'intérieur contiendra le texte du message flash et le balisage à l'intérieur se répétera pour plusieurs messages flash.

    <ul>
        {% flash %}
            <li>{{ message }}</li>
        {% endflash %}
    </ul>

Vous pouvez utiliser la variable `type` qui représente le type du message flash, **success**, **error**, **info** ou **warning**.

    {% flash %}
        <div class="alert alert-{{ type }}">
            {{ message }}
        </div>
    {% endflash %}

Vous pouvez également spécifier le `type` pour filtrer les messages flash d'un type donné. L'exemple suivant n'affichera que les messages **success**, s'il y a un message **error**, il ne sera pas affiché.

     {% flash success %}
         <div class = "alert alert-success">{{message}}</div>
     {% endflash%}

## Définition des messages flash

Les messages Flash peuvent être définis par les [components](../cms/components) ou à l'intérieur de la [section PHP](../cms/themes#php-section) de la page ou de la maquette avec la classe `Flash`.

    <?php

    function onSave()
    {
        // Définit un message réussi
         Flash::success('Paramètres enregistrés avec succès!');

         // Définit un message d'erreur
         Flash::error('Erreur d'enregistrement des paramètres');

         // Définit un message d'avertissement
         Flash::warning('Il y avait un problème mais pas de soucis');

         // Définit un message informatif
         Flash::info('Juste un avertissement concernant les paramètres');
    }
