# |raw

Les variables d'affichage dans octoberCMS sont automatiquement échappées, le filtre `|raw` marque la valeur comme étant "sûre" et ne sera pas échappée si `raw` est le dernier filtre appliqué.

    {# Cette variable ne sera pas échappée #}
    {{ variable|raw }}

Soyez prudent lorsque vous utilisez le filtre `raw` dans des expressions :

    {% set hello = '<strong>Hello</strong>' %}
    {% set hola = '<strong>Hola</strong>' %}

    {{ false ? '<strong>Hola</strong>' : hello|raw }}

    {# Le rendu ci-dessus ne sera pas le même que #}
    {{ false ? hola : hello|raw }}

    {# Le rendu correspondra au suivant #}
    {{ (false ? hola : hello)|raw }}
