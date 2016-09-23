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
