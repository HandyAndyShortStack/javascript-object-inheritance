# Andy's Object Inheritance Pattern for JavaScript
Ever since I began coding javascript, I have had a difficult time achieving clean object inheritance.  My requirements:
* inherited initialization functions with arguments
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
    value: 'parent prototype value'
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
    value: 'child prototype value'
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

## Notable Features
* this pattern separates constructor and prototype definition
* you can choose where or whether to run parent constructor functions by using `Function.prototype.apply`
* arguments to constructor functions should be named and contained in `options` objects
* prototypical inheritance is not achieved by instantiating parent constructors
  * prototype objects' prototypes are set up using `Object.create` to point directly to the parent constructor's prototype
  * prototype defintitions get good results, but need to be described with a verbose properties descriptor object
(due to the way `Object.create` is implemented)
  * `writable`, `enumerable`, and `configurable` will default to `false` when defining a property within
a prototype's properties defifition object
* using `Object.create` to build prototype objects breaks the standard implementation of `Object.prototype.constructor`,
so it is necessary to patch up the `constructor` property of each prototype object manually
