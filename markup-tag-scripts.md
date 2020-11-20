# {% scripts %}

La balise `{% scripts %}` insère des références de fichier JavaScript aux scripts injectés par l'application.
La balise est généralement définie avant la balise de fermeture `body` :

     <body>
         ...
         {% scripts %}
     </body>

> **Remarque** : Cette balise ne doit apparaître qu'une seule fois dans un cycle de page donné pour éviter les références dupliquées.

## Injection de scripts

Les liens vers les fichiers JavaScript peuvent être injectés par du code PHP soit par [les composants](../plugin/components#component-assets) ou par [les pages](../cms/pages#injecting-assets).

    function onStart()
    {
        $this->addJs('assets/js/app.js');
    }

Vous pouvez également injecter un balisage brut dans la balise `{% scripts %}` en utilisant le [placeholder](../cms/layouts#placeholders) anonyme **scripts**.
Utilisez la balise `{% put %}` dans les pages ou les maquettes pour ajouter du contenu au placeholder :

    {% put scripts %}
        <script type="text/javascript" src="/themes/demo/assets/js/menu.js"></script>
    {% endput %}
