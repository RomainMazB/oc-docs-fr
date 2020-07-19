# html()

Les fonctions préfixées par `html_` effectuent des tâches utiles lors du traitement du balisage html. Le helper est directement mappé sur la classe PHP `Html` et ses méthodes. Par exemple:

     {{ html_strip() }}

est l'équivalent PHP de:

     <?= Html::strip() ?>

> **Remarque**: Les méthodes en _camelCase_ (dans la classe PHP `Html`) doivent être converties en _snake_case_ (pour Twig).

## html_strip()

Supprime les balises HTML d'une chaîne.

     {{ html_strip('<strong>Bonjour tout le monde</strong>') }}

## html_limit()

Limite le HTML avec une longueur spécifique et avec une gestion correcte des balises.

     {{ html_limit('<p>Contenu du post... </p>', 100) }}

Pour ajouter un suffixe lorsque la limite est appliquée, passez-le comme troisième argument. La valeur par défaut est `...`.

     {{ html_limit ('<p>Contenu du post ... </p>', 100, '... En savoir plus!') }}

## html_clean()

Nettoie le HTML pour empêcher la plupart des attaques XSS.

    {{ html_clean('<script>window.location = "https://google.com"</script>') }}

## html_email()

Obscurcit une adresse e-mail pour empêcher les robots-spam de la renifler.

    {{ html_email('a@b.c') }}

Par exemple:

    <a href="mailto: {{ html_email('a@b.c')|raw }}">Envoyez moi un email</a>

     <! - Ce qui précède affichera ->
    <a href="mailto: &#109;&#97;&#105;&#108;&#x74;o&#x3a;&#97;&#64;b.&#x63;">Envoyez moi un email</a>
