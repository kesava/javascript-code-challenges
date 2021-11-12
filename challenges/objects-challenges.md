<div align="right">
  <a href="/README.md#this-is-a-collection-of-modern-interview-code-challenges-on-javascript-suitable-for" id="home">Home</a>
</div>

## JavaScript interview code challenges on Objects - challenges

1. [Display all the keys and values of a nested object](#Q1)
1. [Write a program which can empty a given object](#Q2)
1. [Show how a deep copy of an object can be done](#Q3)
1. [Create an array of pair of values (key, value) from an object and store it in a map. Consider the object is not nested](#Q4)
1. [Create an object with a property 'marks' which cannot be set to a value less than 0](#Q5)
1. [Create an object which has a property 'userid' which can only be set once and will be a read only property](#Q6)
1. [Design a function which takes an array as input and returns a function 'next', calling which fetches a value one by one](#Q7)
1. [Create an object 'obj' with functions assigned to keys. Show how can we achieve 'obj.func1().func2().func3()' considering func1, func2, func3 are object keys](#Q8)
1. [Create an object with property counter which keeps incrementing on every access](#Q9)
1. [Create an object and make it behave like an array which allows push and pop operations on items](#Q10)
1. [Write a function which can be used to deeply compare 2 nested objects](#Q11)
1. [Write a program to make all the properties of an object ready only but allow the addition of new properties](#Q13)
1. [Write a program which can return a boolean if value is present in the range with given start and end values in an object](#Q14)
1. [Write a function which accepts a collection of values & an iteratee as arguments and returns a grouped object](#Q16)
1. [Design a utility on an array of objects where the access can be made to the object using index (as usual) and also from primary key of the object](#Q18)
1. [Provide an object on which a value can be set to nested property even if it does not exist](#Q21)
1. [Provide a path lookup for nested objects](#Q22)

---

#### Q1
### Display all the keys and values of a nested object

- `typeof` operator on value gives the type of value
- Recursive solution can be used to iterate over all the nested objects

```js
function kvPrint(obj){
    let strList = [];
    for(let key in obj) {
        if (typeof obj[key] !== "object") {
            strList.push(`[${key} : ${obj[key]}]`);
        }
        else {
          if (Array.isArray(obj[key])) {
            strList.push(`[${key} : [${obj[key].join(", ")}]]`);
          } else {
            strList.push(`[${key} : ${kvPrint(obj[key])}]`);
          }
        }
    }
  return strList.join(", ");
}

kvPrint({
  a: 1,
  b: {
    c: 2
  },
  d: [1,2],
});
// '[a : 1], [b : [c : 2]], [d : [1, 2]]
```

<br />

#### Q2
### Write a program which can empty a given object

- Object can be emptied by removing all the keys present on it
- Alternatively, a new object can be created and the prototype of the new object can be set as prototype of old object

```js
function cleanObj(obj) {
  for(let key in obj) {
    Reflect.deleteProperty(obj, key);
  }
  return obj;
}
```

```js
cleanObj({
  a: 1,
  b: {
    c: 2
  },
  d: [1,2]
});

// {};
```

###### Notes
'obj' is considered to be the object to be emptied

<br />

#### Q3
### Show how a deep copy of an object can be done

- Deep copy is done by copying all the properties of the object to another object

```js
const shallowCopy1 = obj => Object.assign({}, obj);
const shallowCopy2 = obj => ({...obj});
const shallowCopy3 = obj => JSON.parse(JSON.stringify(obj)); // Not really deep if your obj has function members too
```

```js
// Mutually recursive functions to deepCopy
function cloneArray(arr) {
  let retArr = [];
  for(let mem in arr) {
    if (Array.isArray(arr[mem])) {
      retArr.push(cloneArray(arr[mem]));
    } else if (typeof arr[mem] === 'object') {
      retArr.push(deepCopy(arr[mem]));
    } else {
      retArr.push(arr[mem]);
    }
  }
  return retArr;
}
function deepCopy(obj) {
  let retObj = {};
  for(let key in obj) {
    if (typeof obj[key] === 'object') {
      if (Array.isArray(obj[key])) {
        retObj[key] = cloneArray(obj[key]);
      } else {
        retObj[key] = deepCopy(obj[key]);
      }
    } else {
      retObj[key] = obj[key];
    }
  }
  return retObj;
}
```

```js
// driver code
const obj = {
  a: 1,
  b: { 
    c : 2
  },
  d: [3, 4],
  e: [[5], [6]],
  f: () => {},
};
let s1 = shallowCopy1(obj);
let s2 = shallowCopy2(obj);
let s3 = shallowCopy3(obj);
let d1 = deepCopy(obj);

obj.a = 2
obj.b.c = 5
obj.d[1] = 9
obj.e[0][0] = 7

console.log({ 
  obj, 
  s1,
  s2,
  s3,
  d1
});

//   obj: {
//     a: 2,
//     b: { c: 5 },
//     d: [ 3, 9 ],
//     e: [ [ 7 ], [ 6 ] ],
//     f: ƒ f()
//   },
//   s1: {
//     a: 1,
//     b: { c: 5 },
//     d: [ 3, 9 ],
//     e: [ [ 7 ], [ 6 ] ],
//     f: ƒ f()
//   },
//   s2: {
//     a: 1,
//     b: { c: 5 },
//     d: [ 3, 9 ],
//     e: [ [ 7 ], [ 6 ] ],
//     f: ƒ f()
//   },
//   s3: {
//     a: 1,
//     b: { c: 2 },
//     d: [ 3, 4 ],
//     e: [ [ 5 ], [ 6 ] ]
//   },
//   d1: {
//     a: 1,
//     b: { c: 2 },
//     d: [ 3, 4 ],
//     e: [ [ 5 ], [ 6 ] ],
//     f: ƒ f()
//   }
```

###### Notes
`shallowCopy3` provided does deep copy of a nested object also but this technique results in loss of data

###### References
- https://www.digitalocean.com/community/tutorials/copying-objects-in-javascript

<br />

#### Q4
### Create an array of pair of values (key, value) from an object and store it in a map. Consider the object is not nested

- As the object is not nested, the key-value pairs can be obtained directly by using Object.entries
- Map can be initialized with key-value pairs

```js
function mapify(obj) {
  const entries = Object.entries(obj);
  const map = new Map()
  for(let idx in entries) {
    map.set(entries[idx][0], entries[idx][1]);
  }
  return map;
}

// driver code
const obj = {
  a: 1,
  b: {
    c: 2
  },
  d: [3, 4]
}

mapify(obj) 
// Map(3) {
//   'a' => 1,
//   'b' => { c: 2 },
//   'd' => [ 3, 4 ],
// }
```

###### References
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries

<br />

#### Q5
### Create an object with a property 'marks' which cannot be set to a value less than 0

- `getter` and `setter` on the properties of object can be used to control the read and write behavior

```js
const obj = { marks: 0 };

Object.defineProperty(obj, 'marks', {
    set(value) {
        if (value < 0) {
            throw new Error("Marks cant be less than zero");
        } else {
          marks = value;
        }
    },
    get() {
        return marks;
    }
});

obj.marks = -2 // Error: Marks cant be less than zero
obj.marks = 2 // { marks: 2]
```

###### References
- https://javascript.info/property-accessors

<br />

#### Q6
### Create an object which has a property 'userid' which can only be set once and will be a read only property

- Property accessor `writable` to `false` sets the property to be read only

```js
const object1 = {};
function defineSetOnce(obj, key, val) {
  Object.defineProperty(obj, key, {
    value: val,
    writable: false,
  });
  return obj;
}

// driver code
const o1 = defineSetOnce(object1, 'k1', 9);
o1.k1 = 22;
o1.k1 // 9
o1.k1 = 2
o1.k1 // 9
```

###### Notes
`obj.k1` is a ready only property and does not allow overwriting

<br />

#### Q7
### Design a function which takes an array as input and returns a function 'next', calling which fetches a value one by one

- The function returned `next` will return an object which contains value and done properties

```js
function makeIterator(array) {
  let index = 0;
  return {
    next() {
      if (array.length === index) {
        return {
          done: true,
        }
      } else {
        return {
          done: false,
          value: array[index++],
        }
      }
    }
  }
}

// driver code
const it = makeIterator([3, 4, 5]);
it.next()
it.next()
it.next()
it.next()
it.next()

// { done: false, value: 3 }
// { done: false, value: 4 }
// { done: false, value: 5 }
// { done: true }
// { done: true }
```

###### References
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/iterator

<br />

#### Q8
### Create an object 'obj' with functions assigned to keys. Show how can we achieve 'obj.func1().func2().func3()' considering func1, func2, func3 are object keys

- For achieving chaining functionality, each function can return the calling context itself so that context is retained

```js
function nameMaker(fname, lname) {
  return {
    fname,
    lname,
    fullname: '',
    fullName(continental=false) {
      if (continental) {
        this.fullname = `${this.lname}, ${this.fname}`;
      } else {
        this.fullname = `${this.fname} ${this.lname}`;
      }
      return this;
    },
    upperCase() {
      this.fname = this.fname.toUpperCase();
      this.lname = this.lname.toUpperCase();
      return this;
    },
    snakeCase() {
      this.fname = this.fname ? `${this.fname[0].toUpperCase()}${this.fname.slice(1)}` : '';
      this.lname = this.lname ? `${this.lname[0].toUpperCase()}${this.lname.slice(1)}` : '';
      return this;
    },
    tee() {
      console.log({ fname: this.fname, lname: this.lname, fullname: this.fullname });
      return this;
    }
  }
}

// driver code
nameMaker("john", "appleseed").tee().snakeCase().tee().upperCase().tee();

// { fname: 'john', lname: 'appleseed', fullname: '' }
// { fname: 'John', lname: 'Appleseed', fullname: '' }
// { fname: 'JOHN', lname: 'APPLESEED', fullname: '' }
```

###### Notes
Order of calling the functions does not matter as all the functions are returning object itself

###### References
- https://medium.com/technofunnel/javascript-function-chaining-8b2fbef76f7f

<br />

#### Q9
### Create an object with property counter which keeps incrementing on every access
```js
let o1 = makeObjCounter({ a: 1, b: 2}, 'b');
let o2 = makeObjCounter({ a: 1, b: 2}, 'b');

o1.b
o2.b
o1.b
o2.b
o1.b

// { key: 'b', count: 1 }
// { key: 'b', count: 1 }
// { key: 'b', count: 2 }
// { key: 'b', count: 2 }
// { key: 'b', count: 3 }
```

- The access to the property of the object can be configured through `Proxy`

```js
function makeObjCounter(obj, key) {
  const counterTable = new Map();
  const lookupKey = Symbol(key);
  let count = counterTable.get(lookupKey) || 0;
  
  const handler = {
    get: function(target, prop, recvr) {
      if (prop === key) {
        counterTable.set(lookupKey, ++count);
        console.log({ key, count });
        return target[key];
      }
    }
  }
  return new Proxy(obj, handler);
}
```

###### Notes
Symbol is used to maintain the private variable in the object.

<br />

#### Q10
### Create an object and make it behave like an array which allows push and pop operations on items

- Object does not have by default a property named 'length' and hence we can define it on object which helps to track the length
- 'push' and 'pop' functions can be added to the object which internally calls the Array methods `push` and `pop` by passing the object context

```js
function makeArrayLikeObj() {
    const retObj = {};
    let idx = 0;
    const handler = {
      get: function(target, prop, recvr) {
        if (prop === 'length') {
          return idx;
        } else {
          return target[prop];
        }
      }
    };
    return new Proxy({
      push(elem) {
        retObj[idx++] = elem;
        return this;
      },
      pop() {
        const temp = retObj[String(--idx)];
        Reflect.deleteProperty(retObj, String(idx));
        return temp;
      },
     *[Symbol.iterator]() {
       let index = 0;
        while (index < Object.keys(retObj).length) {
          yield retObj[String(index)];
          index++;
        }
      },
    }, handler);
}


// driver code
const arr = makeArrayLikeObj();
arr.push(2).push(8).push(80)
arr.length // 3
arr.pop() // pops 80
arr.length // 2
for(let i of arr) {
  console.log(i) // 2, 8
} 
[...arr] // [2, 8]                             
```

###### Notes
* This array like object supports - `.length`, `.push()`, and `.pop()` methods. It also behaves like a spread operator collection.

<br />

#### Q11
### Write a function which can be used to deeply compare 2 nested objects
```js
// Example
const obj1 = {
    name: 'John',
    details: {
        x: 1,
        y: 2,
    },
};

const obj2 = {
    name: 'John',
    details: {
        y: 2,
        x: 1,
    },
};

deepEqual(obj1, obj2);              // true
```

- The objects can be deeply compared by checking the key value pairs recursively

```js
function deepEqual(object1, object2) {
    const keys1 = Object.keys(object1);
    const keys2 = Object.keys(object2);

    if (keys1.length !== keys2.length) {
        return false;
    }

    for (const key of keys1) {
        const val1 = object1[key];
        const val2 = object2[key];
        const areObjects = val1 != null && typeof val1 === 'object' && val1 != null && typeof val2 === 'object';
        if ((areObjects && !deepEqual(val1, val2)) || (!areObjects && val1 !== val2)) {
            return false;
        }
    }

    return true;
}
```

###### Notes
Stringification of both objects and comparision will also work, but fails on keys order mismatch

###### References
- https://dmitripavlutin.com/how-to-compare-objects-in-javascript/

<br />

#### Q13
### Write a program to make all the properties of an object ready only but allow the addition of new properties

- The exisiting properties of the object can be made read only with `set` keyword using Proxy

```js
function makeObjReadOnly(obj) {
  return new Proxy(obj, {
    get: function(target, prop) {
      return target[prop];
    },
    set: function(target, prop, value) {
      if (Reflect.ownKeys(target).includes(prop)) {
       throw new Error("Object properties are read-only");
      } else {
        target[prop] = value;
      }
    }
  });
}
const o1 = { a: 1, b: 2};

const o2 = makeObjReadOnly(o1);
console.log({ o1, o2 });
// o2.a = 3;
o2.c = 3
console.log({ o1, o2 });
```

###### Notes
`if` condition takes care whether the property is new or existing to handle the read only scenario

###### References
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries

<br />

#### Q14
### Write a program which can return a boolean if value is present in the range with given start and end values in an object

- The object `in` can be trapped using Proxy trap `has`, to check if the value is in the range or not

```js
function makeRangeCheckWithoutArray(start, end) { // start <= end
  const handler = {
    has: function(target, prop) {
      if ((prop >= start) && (prop <= end)) {
        return true;
      } else {
        return false;
      }
    }
  }
  return new Proxy({
    start,
    end
  }, handler);
}

const r1 = makeRangeCheckWithoutArray(4, 10);
5 in r1 // true
15 in r1 // false
```

<br />

#### Q16
### Write a function which accepts a collection of values & an iteratee as arguments and returns a grouped object
```js
// Example
groupBy([6.1, 4.2, 6.3], Math.floor);               // { 6: [6.1, 6.3], 4: [4.2] }
groupBy(['one', 'two', 'three'], x => x.length');         // { 3: ['one', 'two'], 5: ['three'] }
```

- An empty object can be created and used to push the values of array to respective property of the iteratee output

```js
function groupBy(collection, predicate) {
  const output = {};
  let tmp;
  if (typeof predicate !== 'function') return {};
  for(let val of collection) {
    tmp = predicate.apply(null, [val]);
    output[tmp] ? (output[tmp].push(val)) : (output[tmp] = [val]);
  }
  return output;
}
```

###### References
- https://lodash.com/docs/4.17.15#groupBy

<br />

#### Q18
### Design a utility on an array of objects where the access can be made to the object using index (as usual) and also from primary key of the object
```js
// Example
const coll = [
  {name: 'John', id: '1'},
  {name: 'Paul', id: '2'},
  {name: 'George', id: '3'},
  {name: 'Ringo', id: '4'}
];
const o1 = makeSearchableByPrimaryKey(coll, 'name');
o1['Paul'] // { name: 'Paul', id: '2' }
o1[3] // { name: 'Ringo', id: '4' }
o1[5] // undefined
```

- The access to the index happens for arrays by default and the Proxy can be setup to enable the fetching of object using primary key (any other key can also be coded)

```js
function makeSearchableByPrimaryKey(arr, pKey) {
  const handler = {
    get: function(target, prop) {
      if (prop in target) {
        return target[prop];
      } else if (typeof prop === 'string') {
        return arr.find(x => x[pKey] === prop);
      }
    }
  }
  return new Proxy(arr, handler);
}
```

<br />

#### Q21
### Provide an object on which a value can be set to nested property even if it does not exist. 

- The nested object can be accessed only if all the nested properties are defined on the object
- A proxy can designed to create such nested object properties on demand whenever such non existent property is requested and attempted to set with value
- `get` trap of proxy can be used to create the objects dynamically and set the value

```js
function setNestedObject(obj) {
  const handler = {
    get: function(target, prop) {
      if (prop in target) {
        return target[prop];
      } else {
        target[prop] = setNestedObject({});
        return target[prop];
      }
    }
  }
  return new Proxy(obj, handler);
}

// driver code
const obj = {a: 1};
const o1 = setNestedObject(obj);
o1.b.c = 3

o1.b.c // 3
```

<br />

#### Q22
### Provide a path lookup to a nested object. 

- The nested lookup works by splitting the lookup path and recursing on the lookup.

```js
function makeLookupObj(obj) {
  const handler = {
    get: function(target, prop) {
      const lookups = prop.split('.');
      if (lookups.length === 1) {
        return target[lookups[0]];
      } else {
        console.log({ lookups, d: target[lookups[0]], e: lookups.slice(1).join(".") })
        if (typeof target[lookups[0]] === 'object') {
          return makeLookupObj(target[lookups[0]])[lookups.slice(1).join(".")]
        } else if ((target[lookups[0]] !== undefined) && lookups.length === 1) {
          return target[lookups[0]];
        } else {
          return undefined;
        }
      }
    }
  }
  return new Proxy(obj, handler);
}


// driver code
const obj = {
  a: 1,
  b: {
    c: {
      d: 1
    }
  },
  e: 5,
}

const o1 = makeLookupObj(obj);
o1['b.c'] // {d: 1}
o1['b.c.d'] // 1
o1['b.c.d.e'] // undefined
```

<br />

[[↑] Back to top](#home)
