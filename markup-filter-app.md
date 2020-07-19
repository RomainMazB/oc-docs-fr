# |app

Le filtre `|app` renvoie une adresse relative du chemin public du site Web. Le résultat est une URL absolue, comprenant le nom du domaine et le protocole vers l'emplacement spécifié dans le paramètre du filtre. Le filtre peut être appliqué à n'importe quel chemin.

    <link rel="icon" href="{{ '/favicon.ico'|app }}" />

Si l'adresse du site Web est **https://octobercms.com**, l'exemple ci-dessus affichera ce qui suit:

    <link rel="icon" href="https://octobercms.com/favicon.ico" />

Il peut également être utilisé pour les URL statiques:

     <a href="{{ '/about-us'|app }}">
         À propos de nous
     </a>

Ce qui précède produirait:

    <a href="https://octobercms.com/about-us">
        À propos de nous
    </a>

> **Remarque**: Le filtre `|page` est recommandé pour créer des liens vers d'autres pages.
