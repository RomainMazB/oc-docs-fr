# {% component %}

La balise `{% component %}` analysera le contenu de balisage par défaut pour un [composant du CMS](../cms/components) et l'affichera sur la page. Tous les composants ne fournissent pas de balisage par défaut, la documentation du plugin vous guidera pour une utilisation correcte.

    {% component "blogPosts" %}

Cela rendra le partiel du composant nomé **default.htm** et est essentiellement un alias dans l'exemple suivant:

    {% partial "blogPosts::default" %}

<a name="variables"> </a>

## Variables

Certains composants prennent en charge [le passage des variables](../cms/components#component-variables) au moment du rendu:

    {% component "blogPosts" postsPerPage="5" %}

<a name="customizing-components"> </a>

## Personnalisation des composants

Dans la plupart des cas, la balise `{% component %}` n'est pas nécessaire et le balisage est fourni comme exemple d'utilisation pour l'API du composant. Les composants sont destinés à être personnalisés, cela peut être fait de deux manières:

1. [Déplacement du balisage par défaut vers un partiel](../cms/components#moving-default-markup)
1. [Remplacemant des partiels d'un composant](../cms/components#overriding-partials)
