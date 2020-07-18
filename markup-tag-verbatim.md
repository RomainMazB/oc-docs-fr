# {% verbatim %}

La balise `{% verbatim %}` marque des sections entières comme étant du texte brut qui ne doit pas être analysé.

     {% verbatim %} <p>Bonjour, {{ name }}</p> {% endverbatim %}

Ce qui précède sera rendu dans le navigateur exactement comme:

     <p>Bonjour, {{ name }}</p>

Par exemple, AngularJS utilise la même syntaxe de template, ce qui vous permettra de décider quelles variables utiliser pour chacune.

     <p>Bonjour {{ name }}, ceci est analysé par Twig </p>

     {% verbatim%}
         <p>Bonjour {{ name }}, ceci est analysé par AngularJS</p>
     {% endverbatim%}
