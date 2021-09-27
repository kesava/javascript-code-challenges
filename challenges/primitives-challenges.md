<div align="right">
  <a href="/README.md#this-is-a-collection-of-modern-interview-code-challenges-on-javascript-suitable-for" id="home">Home</a>
</div>

## JavaScript interview code challenges on Primitives - challenges

1. [Write a program to reverse a string](#Q4)
1. [Write a program to reverse a string by words. Also show the reverse of each words in place](#Q5)
1. [Write a code to replace all the spaces of the string with underscores](#Q7)
1. [Write a function to truncate a string to a certain number of letters](#Q10)
1. [Write a code to truncate a string to a certain number of words](#Q11)
1. [Write a function to chop a string into chunks of a given length and return it as array](#Q15)
1. [Write a code to remove all the vowels from a given string](#Q16)

---

#### Q4
### Write a program to reverse a string


```js
const str = "JavaScript is awesome";
str.split("").reverse().join("");        // "emosewa si tpircSavaJ"
```

###### Notes
The string can be tested if it is __palindrome__, by comparing the actual string with the reversed string

###### References
- https://www.freecodecamp.org/news/how-to-reverse-a-string-in-javascript-in-3-different-ways-75e4763c68cb/

<br />


#### Q5
### Write a program to reverse a string by words. Also show the reverse of each words in place

- The string can be reversed by words, by splitting the string with spaces and joining them back after reverse
- If the the letters in each word have to be reversed, the string reversal procedure has to be followed after breaking the string with spaces

```js
const str = "JavaScript is awesome";
str.split(" ").reverse().join(" ");                                             // "awesome is JavaScript"
```

```js
const str = "JavaScript is awesome";
str.split(" ").map(val => val.split("").reverse().join("")).join(" ");          // "tpircSavaJ si emosewa"
```

<br />

#### Q7
### Write a code to replace all the spaces of the string with underscores

- The string can be split using the space character and can be joined back with underscore to replace all the spaces with strings
- `replaceAll` is the inbuilt String function on prototype which can be used to replace a string with another string

```js
str.split(" ").join("_");
```

```js
str.replaceAll(" ", "_");
```

###### Notes
`replace` is also an inbuilt String function on prototype which can be used to replace the 1st occurence of the string with another string

###### References
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replaceAll

<br />

#### Q10
### Write a function to truncate a string to a certain number of letters

```js
const truncate = (str, maxLen) => {
  if (maxLen >= str.length) {
    return str;
  }
  return `${str.slice(0, maxLen).trim()}...`;
}

truncate('Hello world', 6) // Hello... (not Hello ...)
```

<br />

#### Q11
### Write a code to truncate a string to a certain number of words

- The string can be broken in to words array and then `slice` method of array can be used to get the number of words which will then be joined back

```js
const str = 'JavaScript is simple but not easy to master';
const wordLimit = 3;

str.split(' ').slice(0, wordLimit).join(' ');               // "JavaScript is simple"
```


#### Q15
### Write a function to chop a string into chunks of a given length and return it as array
```js
// Example
stringChop('JavaScript');               // ["JavaScript"]
stringChop('JavaScript', 2);            // ["Ja", "va", "Sc", "ri", "pt"]
stringChop('JavaScript', 3);            // ["Jav", "aSc", "rip", "t"]
```

I'll attempt a solution using generators.
```js
function* take(input, n) {
  if (n <= 0) {
    yield input;
  } else {
    for(let start = 0, end = n; start < input.length; start = end, end = n + start) {
      yield input.slice(start, end);
    }
  }
}
const stringChop = (str, n=0) => {
  const output = [];
  for (const value of take(str.split(""), n)) {
    output.push(value.join(""));
  }
  return output;
}
```

<br />

#### Q16
### Write a code to remove all the vowels from a given string

Here is a Haskell like solution.

```js
const isVowel = c => (c === 'a') || (c === 'e') || (c === 'i') || (c === 'o') || (c === 'u');
const notVowel = c => !isVowel(c);

const stripVowels = str => str.split('').filter(notVowel).join('');
```


<br />


[[↑] Back to top](#home)
