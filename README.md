# my-polymer-docs

- in a two-way data binding, the properties of the upward element (which includes the other element for the binding) used in the binding shouldn't be set to "readOnly" because the child of the binding needs to modify its value.

- When using two-way data binding with objects (e.g. `<app-data my-object="{{myObject}}"></app-data>`) Polymer binds one object to the other into the same instance. It means, when you modify one object, both objects of the binding are modified. This is powerful, both elements are sharing the same object and Polymer does not try to serialize and pass any data into the element's attributes.

- in a two-way data binding, when the lower element (the child) has an observer on the shared data, it is a good practice in both elements of the bindings to initialize the data 

- Polymer creates the shadow-root from the <template> in the <dom-module> only when the instance is attached to the dom. because the lifecycle starts when the instance is attached.
  That means connectedCallback function won't be called before attached.

- _flushProperties() called on a Polymer.Element extended Instance will force the Polymer lifecycle if the Element wasn't attached to the dom. If the Element has a Polymer <template>, _flushProperties() won't create the template and any further attachment of the Element to the dom won't trigger the creation of the Polymer <template> as well. It is then not recommended to use this function, unless the Element is only a data-structure that the application is using to store data.

- It is not recommended to reflectToAttribute an object-type or Array of object-type properties, for Polymer serialize and can generate long string of text pasted in the dom (and there is no use).

- It is possible to use Array of objects in a two-way data binding because polymer binds the data using references.

- When using Array of objects in a two-way data binding, we can use paths in template. e.g.
```
<b>{{theArray.0.property}}</b>
```

- To change and notify the change of an object property (if the object is a property of the Polymer element, or if the object is in an array and the array is a property of the Polymer element) or the value of an array, use the function **set**, for instance :

```javascript
this.set('theObject.property', 14); // if the object is a property
this.set('intArray.0', 14); // if the array is a property
this.set('objArray.0.property', 14); // if the object is in an array and the array a property
```

note : it is important to note that using **set** or **notifyPath** will only affect templates where the placeholders match the changes and related observers such as

```javascript
static get observers () {
  return ['_udpateElement(objArray.0.property)'];
}
```
