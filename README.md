## pkfuncprogjs
###Chapter 4. Implementing Functional Programming Techniques in JavaScript
####Partial function application and currying
#####Function manipulation
######Function factories
basic style:
```
function bindFirstArg(func, a){
  return function(b){
    return func(a.b);
  }
}
```

bind first arg as itself
```
var makePowersOf = bindFirstArg(bindFirstArg, Math.pow);
var powersOfThree = makePowersOf(3);
console.log(powersOfThree(2)); // 9
console.log(powersOfThree(3)); // 27
```

same as
```
function bindFirstArg(
function bindFirstArg(func, a){
  return function(b){
    return func(a.b);
  }
}, a){ 
  return function(b){ //var powersOfThree = makePowersOf(3);
    return func(a.b);
  }
}
```


#####Partial application
Notice bindFirstArg() only works for functions that have exactly two arguments.  
Two ways to create it:  

As a stand-alone function, that is
```
var partial = function(func){...
```

As a polyfill, that is 

```
Function.prototype.partial = function(){...
```

######Partial application from the left
```
Function.prototype.partialApply = function(){
  var func = this;
  args = Array.prototype.slice.call(artguments);
  return function(){
    return func.apply(this,args.concat(
      Array.prototpye.slice.call(arguments)
    ));
  }
}
```
test function:
```
function nums2hex() {
  function componentToHex(component) {
    var hex = component.toString(16);
    if (hex.length == 1) {
      return "0" + hex;
    }
    else {
      return hex;
    }
  }
  return Array.prototype.map.call(arguments, componentToHex).join('');
}
```
Two ways. 1:direact call this function
```
console.log(nums2hex(100,200)); // '64c8'
```
2. using partialApply  
without param in first step
```
var part = nums2hex.partialApply();
console.log(part(123)); // '7b'
```
with param in first step, or passing params in second step
```
var part = nums2hex.partialApply(123);
console.log(part()); // '7b'
console.log(part(100)); // '7b64'
```

######Partial application from the right
change from
```
 return func.apply(this,args.concat(
      Array.prototpye.slice.call(arguments)
    ));
```
to
```
return func.apply(this,Array.prototpye.slice.call(arguments).concat(args));
```
#####Currying
Currying is the process of transforming a function with many arguments into a function with one argument that returns another function that takes more arguments as needed.  
A function with N arguments can be transformed into a function chain of N functions, each with only one argument.  

Ex: deal with multi-tier(parentheses) arguments
```
Function.prototype.curry = function (numArgs) {
  var func = this;
  numArgs = numArgs || func.length;

  // recursively acquire the arguments
  function subCurry(prev) {
    return function (arg) {   //returns a function with 1 more param, then recurse
      var args = prev.concat(arg);
      if (args.length < numArgs) {
        // recursive case: we still need more args
        return subCurry(args);
      }
      else {
        // base case: apply the function
        return func.apply(this, args);
      }
    };
  }
  return subCurry([]);    //must use array here
};
```
call
```
var hexs = nums2hex.curry(2);
console.log(hexs(11)(12));     // returns 0b0c
console.log(hexs(11));         // returns function
console.log(hexs(110)(12)(0)); // incorrect
```
####Function composition
#####Compose
###Chapter 7. Functional and Object-oriented Programming
####JavaScript's object-oriented implementation – using prototypes
A common mistake:
```
Foo.prototype = Object.create(Parent.prototype); // correct
Bar.prototype = Object.create(Parent); // incorrect, Bar class may not call Parent's method defined in prototype
```
####Mixing functional and object-oriented programming in JavaScript
#####Functional inheritance
######Strategy Pattern
######Mixins
mixins are implemented as objects with only methods.(we use object,not functions)
```
var small = {
  getPrice: function() {
    return this.basePrice;   
  },
  getDimensions: function() {
    return []
  }
}
```
classical addmixin
```
Shirt.prototype.addMixin = function (mixin) {
  for (var prop in mixin) {
    if (mixin.hasOwnProperty(prop)) {
      this.prototype[prop] = mixin[prop];
    }
  }
};
```
However this style is getting rewritten every time a mixin is added to it.
######Functional mixins
