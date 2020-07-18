# {% page %}

La balise `{% page %}` rend le contenu d'une [page](../cms/pages) dans un modèle de maquette.

Voir [layouts](../cms/layouts#introduction) pour un exemple de base.

La balise `{% page %}` analyse le balisage brut d'un modèle de page. Un modèle de page peut à la fois injecter du contenu dans les placeholders et définir un balisage brut.

     description = "Exemple d'une maquette"
     ==
     <html>
         <head>
             {% placeholder head%}
         </head>
         <body>
             {% page %}
             ...

     description = "Exemple d'une page"
     ==
     {% put head %}
         <meta name="toto" content="titi">
     {% endput %}

     <p>Mon contenu.</p>

La page rendue avec le modèle entraînerait:

     <html>
         <head>
             <meta name="toto" content="titi">
         </head>
         <body>
             <p>Mon contenu.</p>
             ...
