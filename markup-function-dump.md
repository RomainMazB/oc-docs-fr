# dump()

La fonction `dump()` affiche les informations sur une variable du modèle (maquette, page, ...). Ceci est utile lors du débogage d'un modèle qui ne se comporte pas comme prévu.

     {{ dump(user) }}

Vous pouvez inspecter plusieurs variables en les passant comme arguments supplémentaires:

    {{ dump(user, categories) }}

Si vous ne transmettez aucune valeur, toutes les variables du contexte actuel seront affichées:

    {{ dump() }}
