# Stackoverflow questions personnelles


```this``` and ```this.root``` might be confusing.
[How to querySelector elements of an element's DOM using Polymer](http://stackoverflow.com/questions/30638632/how-to-queryselector-elements-of-an-elements-dom-using-polymer/30639528#30639528)



# Google’s Polymer 1.0 brings reuse and better branding to Web development

http://www.cio.com/article/2927587/web-development/google-polymer-brings-reuse-and-better-branding-to-web-development.html

<q>The Web’s explosive growth and competition between browser makers drove Web standards bodies such as W3C to ignore a component-based service-oriented architecture (SOA) model. Polymer aims to reverse this trend by allowing Web developers to build functional and design elements that fit a familiar SOA-like architecture in which components called elements provide services to other components through clearly defined interfaces.</q>


## About Local DOM, shadow DOM and shady DOM

[Local DOM Basics and API (polymer-project.org)](https://www.polymer-project.org/1.0/docs/devguide/local-dom.html#dom-distribution)

</q>In shadow DOM, the browser maintains separate light DOM and shadow DOM trees, and creates a merged view (the composed tree) for rendering purposes. addChild adds a node to an element’s light DOM.

In shady DOM, Polymer maintains its own light DOM and shady DOM trees. The document’s DOM tree is effectively the composed tree.<q>


## Polymer.dom API

Let say we have this element's template
```html
<dom-module id="my-element">
	<template>
		<p>first paragraph</p>
		<content></content>
	</template>
</dom-module>
```
and we call like
```html
<my-element>
	<p>second paragraph</p>
</my-element>
```
The second paragraph is a light DOM, and will be appended where the ```<content>``` lives in the template. When the element is rendered in the document's DOM, you can't really distinguished where is the shadow and where is the light DOM because Polymer is not using shadow DOM but shady DOM mechanism instead. Polymer is just keeping internally the references to the different parts of the DOM and updating them as fit.

In order to gain performance and consistency, it is recommended to use the DOM manipulation of Polymer directly.
For instance :

```javascript
Polymer.dom(this).appendChild(child); // will append child to the LIGHT DOM !
Polymer.dom(this.root).appendChild(child); // will append child to the local DOM
```
[a more accurate list of the different functions](https://www.polymer-project.org/1.0/docs/migration.html#dom-apis)

En réalité dans la partie impérative de Polymer, **this** fait référence au DOM de l'élément et **this.root** fait référence au fragment de ce DOM (voir DOCUMENT_FRAGMENT_NODE). Autrement dit ce dernier fait référence au véritable DOM du document et en l'utilisant avec ```Polymer.dom``` on peut alors manipuler et pointer des éléments du DOM complet. **this** donne en fait l'illusion d'avoir accès direct au DOM généré dans le DOM global. Mais si on l'utilise avec Polymer.dom, on remarque qu'on manipule seulement le light DOM (tout ce qui est dans ```<content></content>```). En dehors de Polymer.dom, **this** fait référence à l'élement Polymer, son local DOM, ses attributs, etc...

```javascript
this.$$(selector); // pour sélectionner des éléments du local DOM.
Polymer.dom(this.root).querySelector(selector); // si l'on souhaite sélectionner des élements du DOM réel.
Polymer.dom(this).appendChild()

Polymer.dom(this.root).querySelector('.myhello')



# Gérer les événements dans l'application

Une des forces de javascript et du DOM réside dans la possibilité de déléguer des tâches aux élements parents en déclenchant des évenements personalisés qui remonte d'élément en élément par effet bubbling. C'est un peu compliqué à mettre en place mais avec Polymer, le mécanisme est simplifié.

Une des méthodes consisterait à créer un Polymer *x-updater* qu'on insérerait dans les Polymers qui ont besoin d'être "réveillés" (en utilisant le clonage de nodes, voir [cloning nodes](https://github.com/vdegenne/javascript#cloning-nodes)) au lancement de l'application, et en utilisant l'attribut **listeners** du prototype des Polymers ciblés. Les *x-updater* doivent donc avoir une classe commune pour pouvoir les cibler et exécuter le fire dans toutes les parties de l'application où ils se trouvent.
Cependant cette méthode est un peu astucieuse à mettre en place (voir exemples/demos/x-shaker).

Une deuxième méthode consisterait à regrouper toutes les tâches spécifiques de l'applicaiton dans l'élément racine de l'application (e.g. *x-app*) et à les exécuter en fonction des écoutes événementielles enregistrées. Par effet bubbling on peut intercepter tous les événements déclenchés par l'interaction de l'utilisateur avec l'interface. C'est une méthode plus agréable à mettre en place et plus facile à maintenir (voir exemples/demos/managing-tasks)