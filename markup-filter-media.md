# |media

Le filter `|media` renvoie une adresse relative au chemin public de la [bibliothèque du Gestionnaire des Médias](../cms/mediamanager). Le résultat est une URL vers le fichier multimédia spécifié dans le paramètre du filtre.

    <img src="{{ 'banner.jpg'|media }}" />

Si l'adresse du Gestionnaire des Médias est **http://cdn.octobercms.com**, l'exemple ci-dessus produirait ce qui suit:

    <img src="http://cdn.octobercms.com/banner.jpg" />
