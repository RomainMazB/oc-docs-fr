# |md

Le filtre `|md` convertit la valeur en Markdown au format HTML.

    {{ '**Ce texte** est en gras.'|md }}

Ce qui précède produira le balisage suivant :

    <strong>Ce texte</strong> est en gras.
