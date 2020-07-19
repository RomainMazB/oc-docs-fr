# |theme

Le filtre `|theme` renvoie une adresse relative au chemin du thème actif du site Web. Le résultat est une URL absolue, y compris le domaine et le protocole, vers la ressource spécifiée dans le paramètre du filtre. Les ressources du thème résident généralement dans le sous-répertoire **assets** d'un répertoire de thème.

    <script type="text/javascript" src="{{ 'assets/js/menu.js'|theme }}"></script>

Si l'adresse du site Web est **https://octobercms.com** et que le thème actif est appelé `siteweb`, l'exemple ci-dessus produirait ce qui suit:

    <script type="text/javascript" src="https://october.com/themes/siteweb/assets/js/menu.js"></script>

<a name="combine-css-javascript"> </a>

## Combiner CSS et JavaScript

Le filtre peut également être utilisé pour combiner des ressources du même type en passant un tableau de fichiers.

    <link href="{{ [
        'assets/css/styles1.css',
        'assets/css/styles2.css'
    ]|theme }}" rel="stylesheet">

> **Remarque**: Vous pouvez activer la minification des ressources avec le paramètre `enableAssetMinify` dans le script `config/cms.php`. Par défaut, la minification est désactivée.

<a name="combiner-aliases"> </a>

### Alias ​​du combinateur

Le combinateur des ressources prend en charge les alias qui remplacent les chemins de fichiers, ceux-ci commenceront par le symbole `@`. Par exemple, les [ressources du framework AJAX](../ajax/introduction#framework-script) peuvent être inclus dans le combinateur:

    <script src="{{ [
        '@jquery',
        '@framework',
        '@framework.extras',
        'assets/javascript/app.js'
    ]|theme }}"></script>

Les alias suivants sont pris en charge:

| Alias ​​                | La description                                                                                       |
| ----------------------- | ---------------------------------------------------------------------------------------------------- |
| `@jquery`               | Référence à la bibliothèque jQuery (v3.4.0) utilisée dans l'interface d'administration. (JavaScript) |
| `@framework`            | Extra du framework AJAX, substitut pour la balise `{% framework %}`. (JavaScript)                    |
| `@framework.extras`     | Extra du framework AJAX, substitut pour la balise `{% framework extras %}`. (JavaScript, CSS)        |
| `@framework.extras.js`  | Extras du framework AJAX, (JavaScript)                                                               |
| `@framework.extras.css` | Extra au framework AJAX, (CSS)                                                                       |

Le même alias peut être utilisé pour JavaScript ou CSS, par exemple `@framework.extras`. Au moins une référence explicite avec une extension de fichier est nécessaire dans le tableau pour déterminer laquelle est utilisée.

<a name="external-combiner-paths"> </a>

### Chemins externes du combinateur

Dans certains cas, vous souhaiterez peut-être combiner un fichier en dehors du thème, ceci est réalisé en préfixant le chemin avec un symbole pour créer un chemin dynamique. Par exemple, un chemin commençant par `~/` créera un chemin relatif à l'application:

    <script src="{{ ['~/modules/system/assets/js/framework.js']|theme }}"></script>

Ces symboles sont pris en charge pour créer des chemins dynamiques:

| Symbole | La description                             |
| ------- | ------------------------------------------ |
| `$`     | Par rapport au répertoire des plugins      |
| `~`     | Par rapport au répertoire de l'application |
