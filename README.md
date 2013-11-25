# Andy's Object Inheritance Pattern for JavaScript
Ever since I began coding javascript, I have had a difficult time achieving clean object inheritance.  My requirements:
* inherited ititialization functions with arguments
* `prototype` and `constructor` properties pointed to their proper places
* easy control over the prototype chain
* control over the enumerability of prototype properties

My current pattern fulfills these requirements to my satisfaction:
## Definition

```javascript
function Parent(options) {
  this.parentConstructorProperty = options.parentConstructorProperty;
}
Parent.prototype = Object.create(null, {
  constructor: {
    value: Parent
  },
  parentPrototypeProperty: {
    value: 'parent prototype value',
    enumerable: false
  }
});

function Child(options) {
  Parent.apply(this, arguments);
  this.childConstructorProperty = options.childConstructorProperty;
}
Child.prototype = Object.create(Parent.prototype, {
  constructor: {
    value: Child
  },
  childPrototypeProperty: {
    value: 'child prototype value',
    enumerable: false
  }
});
```
## Instantiation

```javascript
var child = new Child({
  parentConstructorProperty: 'parent constructor value',
  childConstructorProperty: 'child constructor value'
});
```
## Testing

```javascript
child.parentConstructorProperty
//> 'parent constructor value'
child.childConstructorProperty
//> 'child constructor value'
child.parentPrototypeProperty
//> 'parent prototype value'
child.childPrototypeProperty
//> 'child prototype value'
child.constructor
//> [Function: Child]
Object.getPrototypeOf(child.constructor.prototype).constructor
//> [Function: Parent]
```

## 
