<div align="right">
  <a href="/README.md#this-is-a-collection-of-modern-interview-code-challenges-on-javascript-suitable-for" id="home">Home</a>
</div>

## JavaScript interview code challenges on Functions - challenges

1. [Design a Calulator interface for 2 number inputs which can perform sum, difference, product and dividend whenever invoked on the same interface](#Q1)
1. [Design a private counter function which exposes increment and retrive functionalities](#Q2)
1. [Write a polyfill for bind function](#Q3)
1. [Write a function which helps to achieve multiply(a)(b) and returns product of a and b](#Q5)
1. [Create a function which takes another function as an argument and makes it eligible for currying or partial application](#Q6)
1. [Design a function which helps to do debouncing](#Q7)
1. [Design a function which helps to do throttling](#Q8)
1. [Design an interface which limits the number of function calls by executing the function once for a given count of calls](#Q9)
1. [Write a singleton function to create an object only once](#Q10)
1. [Design a function with toggle functionality for given list of inputs where toggle function accepts list of values to be toggled upon](#Q11)
1. [Create a range function which returns an array for the provided inputs as start and end](#Q12)
1. [Write a function which takes a function as an argument to achieve memoization](#Q13)
1. [Create a single function which can perform sum(a, b, c), sum(a, b)(c), sum(a)(b, c) and sum(a)(b)(c) and returns sum of a, b and c](#Q14)
1. [Design a function which can keep recieving the arguments on each function call and returns the sum when no argument is passed](#Q15)
1. [Create an interface for a function such that whenever a function is triggered the system should log the time. Do not modify the function code](#Q16)
1. [Create an interface exposing subscribe and publish functionality, which allows publishing data which in turn invokes all the subscribers with the data](#Q17)

---

#### Q1
### Design a Calulator interface for 2 number inputs which can perform sum, difference, product and dividend whenever invoked on the same interface
```js
function Calculator(num1, num2) {
  const supportedMethods = {
    sum() {
      return num1 + num2;
    },
    difference() {
      return num1 - num2;
    },
    product() {
      return num1 * num2;
    },
    dividend() {
      return Math.floor(num1 / num2);
    }
  }
  
  // We want to throw an error message for any other method invoked on Calculator.
  const handler = {
    get: function(target, prop, recvr) {
      // First we check if the prop exists 
      if (Reflect.has(supportedMethods, prop)) {
        // HERE comes the tricky part. Proxy supports invoking an object member, but not a method.
        // So we have to define a new function, and define a proxy around it so, you can invoke a function on it.
        const F = function(...args) {
          return Reflect.apply(Reflect.get(supportedMethods, prop), undefined, args);
        }
        return new Proxy(F, {
          apply: function(target) {
            return target.apply();
          }
        })
        
      } else {
        throw `The invoked method - "${prop}" is not supported. The only supported methods are: ${Reflect.ownKeys(supportedMethods).join(", ")}.`
      }
    }
  }
  return new Proxy(supportedMethods, handler);
}
```
Examples
```js
const calc12And5 = Calculator(12, 5);
calc12And5.sum();                       // 17
calc12And5.difference();                // 7
calc12And5.product();                   // 60
calc12And5.dividend();                  // 2

calc12And5.square();
// The invoked method - "square" is not supported. The only supported methods are: sum, difference, product, dividend.
```

###### Notes
* The function closes over the arguments `num1` and `num2`. 
* This is a pretty advanced usecase of `Proxy` handler.

<br />

#### Q2
### Design a private counter function which exposes increment and retrive functionalities

```js
function privateCounter(){
    let count = 0;
    return {
        increment(val = 1){
            count += val;
        },
        retrieve(){
            return count;
        }
    }
}

// driver code
const counter = privateCounter();
counter.increment();
counter.increment();
counter.retrieve();             // 2
counter.increment(5);
counter.increment();
counter.retrieve();             // 8
```

###### Notes
The function closes over `count` and makes it inaccessible outside the function.

<br />

#### Q3
### Write a polyfill for bind function

- The `bind` method creates a new function that, when called, has its this keyword set to the provided context
We are going to write `bind` in terms of `apply`.
```js
if(!Function.prototype.bind){
    Function.prototype.bind = function(context){
        var fn = this;

        return function(){
            var allArgs = Array.prototype.slice.call(arguments);
            fn.apply(context, allArgs);
        };
    }
}
```

But `bind` takes in both `context` as well as `args`. So we are going to modify it slightly.
```js
if(!Function.prototype.bind){
    Function.prototype.bind = function(context){
        var fn = this;
        var bindArgs = Array.prototype.slice.call(arguments, 1); // exclude context and get the rest of the args for bind

        return function(){
            var allArgs = bindArgs.concat(Array.prototype.slice.call(arguments)); 
            fn.apply(context, allArgs);
        };
    }
}
```


###### Notes
This is a simple polyfill for bind without handling corner cases. It does not work when using the new operator

###### References
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind

<br />


#### Q5
### Write a function which helps to achieve multiply(a)(b) and returns product of a and b
```js
// Example
multiply(2)(4);                 // 8
multiply(5)(3);                 // 15
```

- The implementation of this can be achieved by calling a function which returns a function

```js
function multiply(num1){
    return function (num2){
        return num1 * num2;
    }
}
```

###### References
- https://medium.com/javascript-scene/curry-and-function-composition-2c208d774983

<br />

#### Q6
### Create a function which takes another function as an argument and makes it eligible for currying or partial application

- Function can take another function as argument and accept the arguments on the returned functions till the expected arguments are reached
- The arguments can be 1 or multiple, and the actual function will be called once the count of expected arguments are reached

```js
function currize(fn) {
    return function curry(...args) {
        // fn.length would be compile time length of input fn
        // args.length would be the run time length of the curried fn
        if (fn.length <= args.length) {
            return fn.apply(this, args);
        } else {
            return function (...args2) {
                // call recursively till we fully construct the original list of parameters of the input fn
                return curry.apply(this, args.concat(args2));
            };
        }
    };
}

// driver code
let sum = currize(function (a, b, c, d) {
    return a + b + c + d;
});

sum(1)(2)(3)(4);                    // called like curried function
sum(1,2)(3,4);                      // called like partial application
```

###### References
- https://javascript.info/currying-partials#advanced-curry-implementation

<br />

#### Q7
### Design a function which helps to do debouncing

- The `debounce` function forces a function to wait a certain amount of time before running again
- The function is built to limit the number of function calls to improve the performance
- Debounce function design can take function (to be debounced), delay and the optional context

```js
function debounce(fn, delay, context) {
  let timer;
  
  return function(...args) {
    if (timer) clearTimeout(timer);
    context = this ? this : context;
    timer = setTimeout(() => {
      fn.apply(context, args);
    }, delay);
  }
}
```

###### Notes
* This is a trailing debounce, the function is executed at the end of the delay period.
* Context is replaced while debounce function call in presence of a context. If not, context set during the `debounce` function call is used.

###### References
- https://www.youtube.com/watch?v=Zo-6_qx8uxg

<br />

#### Q8
### Design a function which helps to do throttling

- The `throttling` function forces a function to run once in an amount of time for one or multiple calls
- The function is built to limit the number of function calls to improve the performance
- Throttling function design can take function (to be throttled), delay and the optional context

```js
function throttle(fn, delay, context){
    let timer;
    let lastArgs;

    return function(...args){
        lastArgs = args;
        context = this ?? context;
        
        if(timer) return;
        
        timer = setTimeout(()=>{
            fn.apply(context, lastArgs);
            clearTimeout(timer);
        }
        , delay);
    };
}
```

###### Notes
Last arguments to the throttled function is saved so that most recent arguments will be used for throttled function execution (unlike debouncing where it is taken care due to its way of execution)

###### References
- https://www.youtube.com/watch?v=81NGEXAaa3Y

<br />

#### Q9
### Design an interface which limits the number of function calls by executing the function once for a given count of calls

- function forces a function run to for specific number of times in a given number of execution calls
- The function is built to limit the number of times a function is called
- Throttling function design can take function (to be throttled), delay and the optional context

```js
function sampler(fn, count, context){
    let counter = 0;

    return function(...args){
        lastArgs = args;
        context = this ?? context;
        counter++;
        if (counter < count) {
          return;
        } else {
          fn.apply(context, args);
          counter = 0;
        }
    };
}
```

```
// driver
const f = sampler(() => { console.log("hello")}, 5, window);
f()
f()
f()
f()
f() // only this one should print "hello"
```

###### Notes
Sampling is different from throttling in the way that sampling limits the execution by executing function once in a given number of calls where as throttling limits the execution by executing function once in a given amount of time

###### References
- https://rxjs.dev/api/operators/sample

<br />

#### Q10
### Write a singleton function to create an object only once

- Singleton is a design pattern which restricts the creation of only one object from a given interface
- When requested multiple times, same object is returned

```js
const SingletonMaker = function(obj) {
  let instance;
  
  function createInstance() {
    if (instance) {
      return instance;
    } else {
      instance = obj;
    }
  }
  
  return {
    getInstance() {
      createInstance();
      return instance;
    }
  }
}

const oSi = SingletonMaker({a: 1});
oSi.getInstance() === oSi.getInstance() // true
```

###### References
- https://www.dofactory.com/javascript/design-patterns/singleton

<br />

#### Q11
### Design a function with toggle functionality for given list of inputs where toggle function accepts list of values to be toggled upon
```js
// Example
const s1 = stateMachineMaker("on", "off");
s1.toggle() // "on"
s1.toggle() // "off"
s1.toggle() // "on"

const s2 = stateMachineMaker("red", "yellow", "green");
s2.toggle() // "red"
s2.toggle() // "yellow"
s2.toggle() // "green"
s2.toggle() // "red"
```

- Toggle functionality can be obtained by returning the next value cyclically on each call to the function
- The toggle function will return another function which maintains the closure over the values with which it was initialized

```js
const stateMachineMaker = function(...args) {
  let possibleStates = args;
  let current = -1;
  return {
    toggle() {
      current = (current + 1) % possibleStates.length;
      return possibleStates[current];
    }
  }
}
```

###### References
- https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/scope-closures/apB.md#closure-part-2

<br />

#### Q12
### Create a range function which returns an array for the provided inputs as start and end
```js
// Example
range(3, 6)     // [3, 4, 5, 6]
range(3)(5)     // [3, 4, 5]
range(3)(0)     // []
```

- Range functionality can be obtained by returning the an array from start to end both inclusive
- In case if 2nd argument is not passed, function will return another function which calls itself with once both the values are obtained

```js
function range(start, end) {
    if (end === undefined) {
        return function (end) {
            return range(start, end);
        };
    }
    const size = end - start;
    return [...Array(size).keys()].map(i => i + start);
  ;
}
```

###### References
- https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/get-started/apB.md#appendix-b-practice-practice-practice

<br />

#### Q13
### Write a function which takes a function as an argument to achieve memoization

- Memoization is an optimization technique used primarily to speed up the programs by storing the results of expensive function calls and returning the cached result when the same inputs occur again
- Function can be designed to use a cache storage (using `map` or `object`) which stores the values of function output against the input

```js
function memoize(fn) {
    const cache = new Map();
    return function () {
        const key = JSON.stringify(arguments);
        
        // if the caculations have already been done for inputs, return the value from cache
        if (cache.has(key)) {
            return cache.get(key);
        } else {
            // call the function with arguments and store the result in cache before returning
            cache.set(key, fn(...arguments));
            return cache.get(key);
        }
    };
}

// driver code
let factorial = memoize(function fact(value) {
    return value > 1 ? value * fact(value - 1) : 1;
});

factorial(5);                   // 120 (output is calculated by calling the function)
factorial(5);                   // 120 (output is returned from the cache which was stored from previous calculations)
```

###### Notes
Stringification of arguments done in order for the function to work for multiple arguments

###### References
- https://codeburst.io/understanding-memoization-in-3-minutes-2e58daf33a19

<br />

#### Q14
### Create a single function which can perform sum(a, b, c), sum(a, b)(c), sum(a)(b, c) and sum(a)(b)(c) and returns sum of a, b and c
```js
// Example
sum(2)(4)(6);            // 12
sum(3, 2)(5);            // 10
sum(4)(-10, -6);         // -12
sum(6, -3, 1);           // 4
```

- Sum functionality can be obtained by returning the sum when all the arguments are present
- The cases when only 1 or 2 arguments are passed need to be managed and handled

```js
function sum(a, b, c){
    if(a !== undefined && b !== undefined && c !== undefined){
        return a + b + c;
    }
    if(a !== undefined && b !== undefined){
        return function(c){
            return sum(a, b, c);
        }
    }
    return function(b, c){
        if(b !== undefined && c !== undefined){
            return sum(a, b, c);
        }
        return function(c){
            return sum(a, b, c);
        }
    }
}
```

```js
const countOfValues = 3;

function sum() {
    const args = arguments;

    if (args.length === countOfValues) {
        return Array.prototype.reduce.call(args, (a, b) => a + b);
    }

    return function () {
        return sum(...args, ...arguments);
    };
}
```

###### Notes
2nd approach is generic technique and can be used customized for any number of values

<br />

#### Q15
### Design a function which can keep recieving the arguments on each function call and returns the sum when no argument is passed

- The function can be designed to return another function which maintains the closure over the previous sum value
- The check for breaking condition can be added using the argument check for `undefined`
- 3rd solution uses the property on function to store the total which will be updated on each call hence the same function can be returned

```js
// Example
sum(2)(4)(6)(1)();          // 13
sum(2)(4)();                // 6
sum(3)();                   // 3
```

- Sum functionality can be obtained by returning the recursively calling till the 2nd parameter value is undefined

```js
function sum(a) {
    return function (b) {
        if (b === undefined) {
            return a;
        }
        return sum(a + b);
    };
}
```

```js
const sum = a => b => b === undefined ? a : sum(a + b);
```

```js
function sum(a) {
    if (typeof a === 'undefined') {
        return sum.total;
    }
    sum.total = (sum.total ?? 0) + a;
    return sum;
}
```

###### Notes
In the code value is checked if it is undefined reason being 0 is a falsy value and `b ? a : sum(a + b)` code fails when one of the argument is 0. Example: `sum(4)(3)(0)(2)()`

###### References
- https://www.youtube.com/watch?v=D5ENjfSkHY4

<br />

#### Q16
### Create an interface for a function such that whenever a function is triggered the system should log the time. Do not modify the function code

- Function call can be handled using Proxy in JavaScript
- `apply` keyword in proxy can be used to achieve the functionality without modifying the existing function code

```js
function generateSecretObject(key, value) {
    return { [key]: value };
}

generateSecretObject = new Proxy(generateSecretObject, {
    apply(target, context, args) {
        console.log(`${target.name} function is accessed at ${new Date()}`);
        return target.apply(context, args);
    },
});

// driver code
const user = {
    username: "0001",
    generateSecretObject
};
generateSecretObject("username", "Password");               // "generateSecretObject function is accessed at {time}"
```

###### Notes
This technique is helpful in logging or managing the data being passed to & returned from function without modifying the actual function code especially when function is a part of library or framework

<br />

#### Q17
### Create an interface exposing subscribe and publish functionality, which allows publishing data which in turn invokes all the subscribers with the data

- A simple module with publish and subscribe function can be exposed to achieve such functionality
- List of subscribers can be maintained in an array and can be invoked in loop on each publish

```js
function pubSub() {
    const subscribers = [];

    function publish(data) {
        subscribers.forEach(subscriber => subscriber(data));
    }

    function subscribe(fn) {
        subscribers.push(fn);
    }

    return {
        publish,
        subscribe,
    };
}

// driver code
const pubSubObj = pubSub();
pubSubObj.subscribe(data => {
    console.log('Subscriber 1: ' + data);
});
pubSubObj.subscribe(data => {
    console.log('Subscriber 2: ' + data);
});

// all subscribers will be called with the data on publish
pubSubObj.publish('Value is 10');
```

###### Notes
This is a well known JavaScript pattern called as __Publish/Subscribe Pattern__

###### References
- https://jsmanifest.com/the-publish-subscribe-pattern-in-javascript/

<br />

[[↑] Back to top](#home)
