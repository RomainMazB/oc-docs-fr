# |page

Le filtre `|page` crée un lien vers une page en utilisant un nom de fichier de page sans extension comme paramètre. Par exemple, pour la page **about.htm**, vous pouvez utiliser le code suivant pour générer un lien vers celle-ci:

    <a href="{{ 'about'|page }}">À propos de nous</a>

N'oubliez pas que si vous faites référence à une page d'un sous-répertoire, vous devez spécifier le nom du sous-répertoire:

    <a href="{{ 'contacts/about'|page }}">À propos de nous</a>

> **Remarque**: La [Documentation des thèmes](../cms/themes#subdirectories) contient plus de détails sur l'utilisation des sous-répertoires.

Pour accéder au lien d'une certaine page depuis la section du code PHP, vous pouvez utiliser `$this->pageUrl('nom_de_la_page_sans-extension')`:

    ==
    <?php
    function onStart() {
        $this['newsPage'] = $this->pageUrl('blog/overview');
    }
    ?>
    ==
    {{ newsPage }}

Vous pouvez créer un lien vers la page en cours en filtrant une chaîne vide:

    <a href="{{ ''|page }}">Actualiser la page</a>

Pour obtenir le lien vers la page courante en PHP, vous pouvez utiliser `$this->pageUrl('')` avec une chaîne vide.

    ==
    <?php
    function onStart() {
        $this['currentUrl'] = $this->pageUrl('');
    }
    ?>
    ==
    {{ currentUrl }}

<a name="reverse-routing"> </a>

## Routage inversé

Lors d'un lien vers une page dont les paramètres URL sont définis, le filtre `|page` prend en charge le routage inversé en passant un tableau comme premier argument.

    url = "/blog/post/:post_id"
    ==
    [...]

Étant donné que le contenu ci-dessus se trouve dans un fichier de page CMS **post.htm**, vous pouvez créer un lien vers cette page en utilisant:

    <a href="{{ 'post'|page({ post_id: 10 }) }}">
        Article de blog n° 10
    </a>

Si l'adresse du site Web est **https://octobercms.com**, l'exemple ci-dessus rendra ce qui suit:

    <a href="https://octobercms.com/blog/post/10">
        Article de blog n° 10
    </a>

<a name="persistent-parameters"> </a>

## Paramètres d'URL persistants

Si un paramètre d'URL est déjà présent dans l'environnement, le filtre `|page` l'utilisera automatiquement.

    url = "/blog/post/:post_id"

    url = "/blog/post/edit/:post_id"

S'il y a deux pages, **post.htm** et **post-edit.htm**, avec les URLs définies ci-dessus, vous pouvez créer un lien vers l'une ou l'autre page sans avoir à définir le paramètre `post_id`.

    <a href="{{ 'post-edit'|page }}">
        Modifier cet article
    </a>

Lorsque le balisage ci-dessus apparaît sur la page **post.htm**, il affiche les éléments suivants:

    <a href="https://octobercms.com/blog/post/edit/10">
        Modifier cet article
    </a>

La valeur `post_id` de **10** est déjà connue et est persistée à travers les environnements. Vous pouvez désactiver cette fonctionnalité en passant le 2ème argument comme `false`:

    <a href="{{ 'post'|page(false) }}">
        Article de blog inconnu
    </a>

Ou en définissant une valeur différente:

    <a href="{{ 'post'|page({ post_id: 6 }) }}">
        Article du blog n° 6
    </a>
