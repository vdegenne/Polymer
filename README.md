# my-polymer-docs

## vocabulary

in this personal documentation, I use the following as the vocabulary :

```html
<element-one> <!-- `upward` or `higher` or `parent` element -->
  <element-two></element-two> <!-- `downward` or `lower` or `child` element -->
</element-one>
```

- TWDB : Two-way Data Binding

## statements

### two-way data binding theory

Let's consider the following :

```html
<element-one> 
  <element-two some-data="{{someData}}"></element-two>
</element-one>
```
`ElementTwo.someData` needs to set `notify` to `true` so both `ElementOne.someData` and `ElementTwo.someData` share the same variable *(1)*; That's a Two-way Data Binding.

- if `ElementOne.someData` AND/OR `ElementTwo.someData` is/are `readyOnly`, the shared variable *(1)* used in the TWDB will still be modified even if both elements use some mutation functions (set, push, ...) but the notifications and the observers won't get called.

for instance, 
```javascript
// in ElementOne
this.set('someData', 1);
/* Now ElementOne.someData and ElementTwo.someData are equal to 1
   But the mutation function has no effects because of the `readOnly` set to `true` */
```
- so it is recommended not to use `readOnly` UNLESS `ElementOne` only needs to read `someData` and not modify any of its value.

- If `ElementOne` needs to invoke some observers inside `ElementTwo`, for instance :

```javascript
this.set('anObject.prop1', 1);
/* We expect observers in ElementTwo on someData to be triggered */
```
Then it is recommended to initialize the shared variable (1) in the properties static function of both elements.

```javascript
static get properties () {
  return {
    ...
    anObject: {
      type: Object,
      value: function () { return {} }
    },
    ...
  }
}
```

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
