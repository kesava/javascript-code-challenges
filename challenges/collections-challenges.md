<div align="right">
  <a href="/README.md#this-is-a-collection-of-modern-interview-code-challenges-on-javascript-suitable-for" id="home">Home</a>
</div>

## JavaScript interview code challenges on Collections - challenges

1. [Write `for...in` and `for...of` loops for arrays and objects. Make an object iterable](#Q1)
1. [Create a Proxy object through which the array can be accessed as usual but also allow to access the values through negative indices](#Q23)

---

#### Q1
### Write `for...in` and `for...of` loops for arrays and objects. Make an object iterable.

- Function can take 2 arguments which concatenates arrays
- 2nd array parameter can be defaulted to 1st array if the value is not passed

```js
const arr = [4,5,6];
const obj = {
  a: 1,
  b: 2,
  c: 3,
}

function makeIterator(obj) {
    return Object.defineProperty(obj, Symbol.iterator, {
      enumerable: false,
      writable: false,
      configurable: true,
      value: function*() {
        for(let i of Object.entries(obj)) {
          yield i;
        }
      }
    });
}

for(let i in arr) {
  console.log(i) // '0', '1', '2'
}
for(let i of arr) {
  console.log(i) // 4, 5 6
}
for(let i in obj) {
  console.log(i) // 'a', 'b', 'c'
}
for(let i of makeIterator(obj)) {
  console.log(i) // [ 'a', 1 ], [ 'b', 2 ], [ 'c', 3 ]
}
```

<br />

#### Q23
### Create a Proxy object through which the array can be accessed as usual but also allow to access the values through negative indices
```js
// Example
let arr = [10, 20, 30];

arr[-1];            // 30
arr[-2];            // 20
```

- `get` trap of proxy can be used to map the negative index to the valid array position

```js
arr = new Proxy(arr, {
    get(target, handler) {
        if (handler < 0) return target[target.length + Number(handler)];
        else return target[handler];
    },
});
```

<br />

[[↑] Back to top](#home)
