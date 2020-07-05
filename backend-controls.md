# Contrôles Back-end

- [Tableau de score](#scoreboards)

- [Indicateurs](#indicators)

- [Graphique en fromage](#pie-chart)

- [Graphique en barres](#bar-chart)

L'interface utilisateur back-end intègres un nom d'afficheur HTML que vous pouvez utiliser sur vos pages.

<a name="scoreboards"></a>

## Tableau de scores

L'afficheur tableau de scores est généralement utilisé avant une liste pour afficher une vue d'ensemble des données les plus importantes. L'afficheur peut être formé de n'importe quel graphique et indicateur.
Exemple d'un tableau de score affiché avant une liste:
```html
<div class="scoreboard">

<div data-control="toolbar">

<div class="scoreboard-item control-chart" data-control="chart-pie">

<ul>

<li data-color="#95b753">Published <span>84</span></li>

<li data-color="#e5a91a">Drafts <span>12</span></li>

<li data-color="#cc3300">Deleted <span>18</span></li>

</ul>

</div>

<div class="scoreboard-item control-chart" data-control="chart-bar">

<ul>

<li data-color="#95b753">Published <span>84</span></li>

<li data-color="#e5a91a">Drafts <span>12</span></li>

<li data-color="#cc3300">Deleted <span>18</span></li>

</ul>

</div>

<div class="scoreboard-item title-value">

<h4>Weight</h4>

<p>100</p>

<p class="description">unit: kg</p>

</div>

</div>

</div>

<?= $this->listRender() ?>
```
![image](https://github.com/octobercms/docs/blob/master/images/list-scoreboard.png?raw=true) {.img-responsive .frame}

A noter que vous pouvez utiliser la class **scoreboard-item** pour vos éléments du tableau de scores.

<a name="indicators"></a>

## Indicateur

Les indicateurs sont de simples éléments de rapport avec un titre, une valeur et une description. Vous pouvez utiliser les classes `positive` et `negative` sur la valeur de l'élément. Les classes d'icône de la [police Autumn](http://daftspunk.github.io/Font-Autumn/) vous permet d'ajouter une icône avant la valeur.
```html
<div class="scoreboard-item title-value">

<h4>Weight</h4>

<p>100</p>

<p class="description">unit: kg</p>

</div>

<div class="scoreboard-item title-value">

<h4>Comments</h4>

<p class="positive">44</p>

<p class="description">previous month: 32</p>

</div>

<div class="scoreboard-item title-value">

<h4>Length</h4>

<p class="negative">31</p>

<p class="description">previous: 42</p>

</div>

<div class="scoreboard-item title-value">

<h4>Latest commenter</h4>

<p class="oc-icon-star">John Smith</p>

<p class="description">registered: yes</p>

</div>

<div class="scoreboard-item title-value" data-control="goal-meter" data-value="88">

<h4>goal meter</h4>

<p>88%</p>

<p class="description">37 posts remain</p>

</div>
```
![image](https://github.com/octobercms/docs/blob/master/images/name-title-indicators.png?raw=true) {.img-responsive .frame}

> **Note:** l'exemple est donné dans un contexte où il est intégré à un d'un tableau de scores. Si vous utilisez un indicateur dans un partiel de [widget de rapport](widgets#report-widgets), la class **scoreboard-item** ne devrait pas être utilisée.

<a name="pie-chart"></a>

## Graphique en quartiers
Le graphique en quartiers affiches des informations sous la forme d'un cercle. Exemple de syntaxe :
```html
<div

class="control-chart centered wrap-legend"

data-control="chart-pie"

data-size="200"

data-center-text="100">

<ul>

<li>Label 1 <span>100</span></li>

<li>Label 2 <span>100</span></li>

<li>Label 3 <span>100</span></li>

</ul>

</div>
```
![image](https://github.com/octobercms/docs/blob/master/images/traffic-sources.png?raw=true) {.img-responsive .frame}

<a name="bar-chart"></a>

## Graphique en barres

L'exemple suivant montre la syntaxe d'un graphique en barres. La class **wrap-legend** est optionnelle, elle contrôle l'apparence de la légende. Les attributs **data-height** et **data-full-width** sont eux aussi optionnels.
```html
<div

class="control-chart wrap-legend"

data-control="chart-bar"

data-height="100"

data-full-width="1">

<ul>

<li>Label 1 <span>100</span></li>

<li>Label 2 <span>100</span></li>

<li>Label 3 <span>100</span></li>

</ul>

</div>
```
![image](https://github.com/octobercms/docs/blob/master/images/bar-chart.png?raw=true) {.img-responsive .frame}