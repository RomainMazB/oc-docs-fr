# {% styles %}

La balise `{% styles %}` rend les liens CSS vers les fichiers de feuilles de style injectés par l'application. La balise est généralement définie dans la section HEAD d'une page ou d'une maquette:

     <head>
         ...
         {% styles %}
     </head>

> **Remarque**: Cette balise ne doit apparaître qu'une seule fois dans un cycle de page donné pour éviter les références dupliquées.

## Injection des feuilles de style

Les liens vers les fichiers des feuilles de style peuvent être injectés par du code PHP soit par des [composants](../plugin/components#component-assets) ou par des [pages](../cms/pages#injecting-assets).

    function onStart()
    {
        $this->addCss('assets/css/hello.css');
    }

Vous pouvez également injecter un balisage brut dans la balise `{{% styles %}` en utilisant le [placeholder](../cms/layouts#placeholders) anonyme **styles**. Utilisez la balise `{% put %}` dans les pages ou les maquettes pour ajouter du contenu au placeholder:

    {% put styles %}
        <link href="/themes/demo/assets/css/page.css" rel="stylesheet" />
    {% endput %}
