# str()

Les fonctions préfixée de `str_` exécutent des tâches utiles lors du traitement des chaînes. Le helper correspond directement à la classe PHP `Str` et à ses méthodes. Par exemple:

    {{ str_camel() }}

est l'équivalent en PHP de ce qui suit:

    <?= Str::camel() ?>

> **Remarque**: Les méthodes en _camelCase_ (dans la classe PHP `Str`) doivent être converties en _snake_case_ (pour Twig).

## str_limit()

Limitez le nombre de caractères dans une chaîne.

    {{ str_limit ('Le renard brun rapide ...', 100) }}

Pour ajouter un suffixe lorsque la limite est appliquée, passez-le comme troisième argument. La valeur par défaut est `...`.

    {{ str_limit('Le renard brun rapide ...', 100, '... En savoir plus!') }}

## str_words()

Limitez le nombre de mots dans une chaîne.

    {{ str_words('Le renard brun rapide ...', 100) }}

Pour ajouter un suffixe lorsque la limite est appliquée, passez-le comme troisième argument. La valeur par défaut est `...`.

    {{ str_words('Le renard brun rapide ...', 100, '... En savoir plus!') }}

## str_camel()

Convertissez une valeur en _camelCase_.

    // Rendra: bonjourLeMonde
    {{ str_camel('bonjour le monde') }}

## str_studly()

Convertissez une valeur en _StudlyCase_.

    // Rebdra: BonjourLeMonde
    {{ str_studly ('bonjour le monde') }}

## str_snake()

Convertissez une valeur en _snake_case_.

    // Rendra: bonjour_le_monde
    {{ str_snake ('bonjour le monde') }}

Le deuxième argument peut fournir un délimiteur.

    // Rendra: bonjour---le---monde
    {{ str_snake ('bonjour le monde', '---') }}

## str_plural()

Obtenez le pluriel d'un mot anglais.

    // Rendra: chickens
    {{ str_plural('chicken') }}
