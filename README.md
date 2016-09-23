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
