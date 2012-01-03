# Limbo

Limbo is a lightweight layer over javascript's built-in inheritance system that keeps all the good stuff and hides all the crap.

# what limbo doesn't do

- limit itself to Limbo-defined classes (you can inherit from Error or Boolean or even Function)
- mixins, interfaces, abstract static factory factories, and other bloat
- use Object.create (it even works in IE &lt; 8!)
- break `instanceof`
- have lots of magical object keys (the only special name is `init`)

# what you can do with limbo

- inheritable constructors (via the optional `init` method)
- closure-based "private" methods (see below)
- instantiate your objects without calling the constructor (absolutely necessary for inheritance)
- construct objects with variable arguments

# what limbo looks like

``` js
// adapted from coffeescript.org
var Animal = Limbo(function(animal) {
  animal.init = function(name) { this.name = name; };

  animal.move = function(meters) {
    console.log(this.name+" moved "+meters+"m.");
  }
});

var Snake = Limbo(Animal, function(snake, animal) {
  snake.move = function() {
    console.log("Slithering...");
    animal.move.call(this, 5);
  };
});

var Horse = Limbo(Animal, function(horse, animal) {
  horse.move = function() {
    console.log("Galloping...");
    animal.move.call(this, 45);
  };
});

var sam = Snake("Sammy the Python")
  , tom = Horse("Tommy the Palomino")
;

sam.move()
tom.move()
```

# how you use limbo

You can call `Limbo` in a few different ways:

``` js
// this defines a class that inherits directly from Object.
Limbo(function(proto, super, class, superclass) {
  // define private methods as regular functions that take
  // `self` (or `me`, or `it`, if you want to respect FutureReservedWords
  function myPrivateMethod(self, arg1, arg2) {
    // ...
  }

  proto.init = function() {
    myPrivateMethod(this, 1, 2)
  };

  // you can also return an object from this function, which will
  // be merged into the prototype.
  return { thing: 3 };
});

// this defines a class that inherits from MySuperclass
Limbo(MySuperclass, function(proto, super, class, superclass) {
  proto.init = function() {
    // call superclass methods with super.method.call(this, ...)
    //                           or super.method.apply(this, arguments)
    super.init.call(this);
  };
});

// for shorthand, you can pass an object in lieu of the function argument,
// but you lose the niceness of super and private methods.
Limbo({ init: function(a) { this.thing = a } });

MyClass = Limbo(function(p) { p.init = function() { console.log("init!") }; });
// instantiate objects by calling the class
MyClass() // => init!

// allocate blank objects with `new`
new MyClass // nothing logged
```