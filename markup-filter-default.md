# |default

Le filtre `|default` renvoie la valeur passée comme premier argument si la valeur filtrée n'est pas définie ou vide, sinon la valeur filtrée est retournée.

    {{ variable|default('La variable n'est pas définie') }}

    {{ variable.toto|default('La propriété toto de la variable n'est pas définie') }}

     {{ variable['toto']|default('La clé toto dans la variable n'est pas définie') }}

     {{ ''|default('La variable est vide') }}

Lorsque vous utilisez le filtre `default` sur une expression qui utilise des variables dans certains appels de méthode, veillez à utiliser le filtre `default` chaque fois qu'une variable peut être indéfinie:

    {{ variable.methode(toto|default('titi'))|default('titi') }}
