# forEach, map, filter, reduce

The array methods **forEach**, **map**, **filter** and **reduce** are best understood by looking at how they might be implemented if you had to write them yourself in JavaScript. In the next few sections we will present simplified equivalents of the native implementations.

Common to these methods is that they all use a **for** loop internally to iterate through the elements of the target array. When working with arrays, many developers prefer these methods rather than using a **for** loop in their code. By virtue of their names they reveal more clearly what is intended than equivalent **for** loops, which must be examined more closely to determine what is going on.

## Array.forEach()

> [MDN definition](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach): The **forEach()** method executes a provided function once for each array element.

```js
const numbers = [1, 2, 3, 4];
let sum = 0;

numbers.forEach(x => {
  sum += x;
});

console.log(sum); // -> 10
```

> _See this in action in this [JSBin](https://jsbin.com/quforih/edit?js,console)._

### A custom forEach() implementation

```js
function forEach(arr, func) {
  for (let i = 0; i < arr.length; i++) {
    func(arr[i], i, arr);
  }
}

const numbers = [1, 2, 3, 4];
let sum = 0;

forEach(numbers, x => {
  sum += x;
});

console.log(sum); // -> 10
```

> _See this in action in this [JSBin](https://jsbin.com/bufamuz/edit?js,console)._

### Calling forEach() as a method on an array

To demonstrate how we can use dot notation directly on an array to call our custom `forEach()` function, similar to how the native, built-in `.forEach()` method works, we will add a new method `.myForEach()` to the native `Array` type that in turn calls our `forEach()` function.

> _Note that it is considered a bad practice to modify built-in JavaScript types like is done here. You should not do this in production code. We show it here for demonstration purposes only._

```js
function forEach(arr, func) {
  for (let i = 0; i < arr.length; i++) {
    func(arr[i], i, arr);
  }
}

Array.prototype.myForEach = function(func) {
  forEach(this, func);
};

const numbers = [1, 2, 3, 4];
let sum = 0;

numbers.myForEach(x => {
  sum += x;
});

console.log(sum); // -> 10
```

> _See this in action in this [JSBin](https://jsbin.com/pehexug/edit?js,console)._

### The callback function

For illustrative purposes we can add a `console.log` statement to the callback function and see what is passed as the second and third argument:

```js
const numbers = [1, 2, 3, 4];

numbers.forEach((elem, index, arr) => {
  console.log('elem: ' + elem + ', index: ' + index + ', arr: ' + arr);
});

/* output:
elem: 1, index: 0, arr: 1,2,3,4
elem: 2, index: 1, arr: 1,2,3,4
elem: 3, index: 2, arr: 1,2,3,4
elem: 4, index: 3, arr: 1,2,3,4
*/
```

> _See this in action in this [JSBin](https://jsbin.com/nohipew/edit?js,console)._

The first value is the value of the current element, the second value is the current loop index value and the array value is the array on which `.forEach()` is called.

As is common in JavaScript, you do not necessarily have to use all the parameters that are passed to the callback function. In fact, in many cases you will only need the first argument (the current array element).

Note that the callback functions for **map**, **filter** and **reduce**, as described below, receive the same three arguments, here named `elem`, `index` and `arr`.

## Array.map()

> [MDN definition](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map): The **map()** method creates a new array with the results of calling a provided function on every element in the calling array.

```js
const numbers = [1, 2, 3, 4];
const square = x => x * x;
const squaredNumbers = numbers.map(square);

console.log(squaredNumbers); // -> [ 1, 4, 9, 16 ]
```

> _See this in action in this [JSBin](https://jsbin.com/wewewej/edit?js,console)._

### A custom map() implementation

The **map()** function below initializes a new, empty array to which it pushes _transformed_ elements, one by one, as it iterates through the array argument `arr`, calling the `mapFn` function for each individual element. When the loop has been completed, the new array is returned. Note that the array `arr` itself remains unmodified.

```js
function map(arr, mapFn) {
  const result = [];
  for (let i = 0; i < arr.length; i++) {
    const mappedValue = mapFn(arr[i], i, arr);
    result.push(mappedValue);
  }
  return result;
}

const numbers = [1, 2, 3, 4];
const square = x => x * x;
const squaredNumbers = map(numbers, square);

console.log(squaredNumbers); // -> [1, 4, 9, 16]
```

> _See this in action in this [JSBin](https://jsbin.com/winudul/edit?js,console)._

To prevent unintended results it is essential that the function passed as the `mapFn` argument does not modify the original array. In computer science terms, this function should be _pure_, without side effects.

> - Read more: [What is a pure function?](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976)

## Array.filter()

> [MDN definition](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter): The **filter()** method creates a new array with all elements that pass the test implemented by the provided function.

```js
const numbers = [1, 2, 3, 4];
const isEven = x => x % 2 === 0;
const evenNumbers = numbers.filter(isEven);

console.log(evenNumbers); // -> [ 2, 4 ]
```

> _See this in action in this [JSBin](https://jsbin.com/zepemuc/edit?js,console)._

### A custom filter() implementation

This method works in a similar fashion as the `map()` method, but now elements are only pushed to the new array if the predicate function returns `true`. The new array will (potentially) have fewer elements than the original array, but the filtered elements are not changed in any way.

In the example below the predicate function test whether the current element is even by checking whether its value divided by two has a remainder of zero. The result of this comparison (`true` or `false`) is the return value of the predicate and determines whether the current element gets added to the new array or not.

```js
function filter(arr, predicateFn) {
  const result = [];
  for (let i = 0; i < arr.length; i++) {
    if (predicateFn(arr[i], i, arr)) {
      result.push(arr[i]);
    }
  }
  return result;
}

const numbers = [1, 2, 3, 4];
const isEven = x => x % 2 === 0;
const evenNumbers = filter(numbers, isEven);

console.log(evenNumbers); // -> [2, 4]
```

> _See this in action in this [JSBin](https://jsbin.com/nugibag/edit?js,console)._

<small>\*A _predicate_ is a function that returns a boolean, **true** or **false**, depending on the supplied arguments.</small>

## Array.reduce()

> [MDN definition](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce): The **reduce()** method executes a **reducer** function (that you provide) on each member of the array resulting in a single output value†.

<small>†Although reference is made to a 'single output value', this single value may well be an array or an object, as you will see later in the examples below.</small>

```js
const numbers = [1, 2, 3, 4];

const sum = (a, b) => a + b;
const total = numbers.reduce(sum, 0);

console.log(total); // -> 10
```

> _See this in action in this [JSBin](https://jsbin.com/sutamiy/edit?js,console)._

### A custom reduce() implementation

```js
function reduce(arr, reducerFn, initialValue) {
  let accumulator = initialValue;
  for (let i = 0; i < arr.length; i++) {
    accumulator = reducerFn(accumulator, arr[i], i, arr);
  }
  return accumulator;
}

const numbers = [1, 2, 3, 4];

const sum = (a, b) => a + b;
const total = reduce(numbers, sum, 0);

console.log(total); // -> 10
```

> _See this in action in this [JSBin](https://jsbin.com/jofojej/edit?js,console)._

The key to understanding the **reduce()** method is in the line:

```js
accumulator = reducerFn(accumulator, arr[i], i, arr);
```

In the case we don't need the current loop index and the subject array in the reducer function (which is often the case), we can simplify this to:

```js
accumulator = reducerFn(accumulator, arr[i]);
```

From this line we can define the reducer function as a function that takes an accumulator value and the current array element and returns a new accumulator value.

The **reduce()** method is the most flexible of the map/filter/reduce triplet. In fact, it is possible to rewrite **map()** and **filter** using **reduce()**.

### Using reduce() to filter

```js
const arr = [6, 3, 10, 1];

const evenNumbers = arr.reduce((acc, elem) => {
  if (elem % 2 === 0) {
    acc.push(elem);
  }
  return acc;
}, []);

console.log(evenNumbers); // -> [6, 10]
```

> _See this in action in this [JSBin](https://jsbin.com/pucaruv/edit?js,console)._

In this example our accumulator is an (initially empty) array. We push elements (in this case integer numbers) in the accumulator only when they are divisible by 2.

### Using reduce() to map

In this example an array of integer numbers is mapped to an array of their squares.

```js
const arr = [6, 3, 10, 1];

const squares = arr.reduce((acc, elem) => {
  acc.push(elem * elem);
  return acc;
}, []);

console.log(squares); // -> [36, 9, 100, 1]
```

> _See this in action in this [JSBin](https://jsbin.com/gatayet/edit?js,console)._

## Using reduce() to 'group by'

In this example our accumulator is not an array, but an (initially empty) object. It groups the array elements by gender.

```js
const arr = [
  { gender: 'F', name: 'Joyce' },
  { gender: 'M', name: 'Jim' },
  { gender: 'F', name: 'Lucy' },
  { gender: 'F', name: 'Janet' },
  { gender: 'M', name: 'Jack' },
  { gender: 'M', name: 'Ferdinand' },
];

const groupedNames = arr.reduce((acc, elem) => {
  if (acc[elem.gender]) {
    acc[elem.gender].push(elem);
  } else {
    acc[elem.gender] = [elem];
  }
  return acc;
}, {});

console.log(groupedNames);
```

Result:

```js
{
  F: [
    { gender: 'F', name: 'Joyce' },
    { gender: 'F', name: 'Lucy' },
    { gender: 'F', name: 'Janet' }
  ],
  M: [
    { gender: 'M', name: 'Jim' },
    { gender: 'M', name: 'Jack' },
    { gender: 'M', name: 'Ferdinand' }
  ]
}
```

> _See this in action in this [JSBin](https://jsbin.com/rufubu/edit?js,console)._

### Method chaining

The methods **map()** and **filter()** each return a new array. This makes it possible to chain these methods and create a 'pipeline' of operations, to be applied in sequence. The **reduce** method can return anything, including an array. If a **reduce** method returns something other than an array it can only be located at the end of an array method chain. The same applied to **forEach()**: it doesn't return anything. Therefore, it can only be placed at the end of a chain.

Let's take the last example, but now filtering out only those array elements for which the name starts with a 'J':

```js
const arr = [
  { gender: 'F', name: 'Joyce' },
  { gender: 'M', name: 'Jim' },
  { gender: 'F', name: 'Lucy' },
  { gender: 'F', name: 'Janet' },
  { gender: 'M', name: 'Jack' },
  { gender: 'M', name: 'Ferdinand' },
];

const groupedNames = arr
  .filter(elem => elem.name.startsWith('J'))
  .reduce((acc, elem) => {
    if (acc[elem.gender]) {
      acc[elem.gender].push(elem);
    } else {
      acc[elem.gender] = [elem];
    }
    return acc;
  }, {});

console.log(groupedNames);
```

Result:

```
{
  F: [
    { gender: 'F', name: 'Joyce' },
    { gender: 'F', name: 'Janet' }
  ],
  M: [
    { gender: 'M', name: 'Jim' },
    { gender: 'M', name: 'Jack' }
  ],
}
```

> _See this in action in this [JSBin](https://jsbin.com/yovodag/edit?js,console)._

## In summary

![image](assets/map-filter-reduce-emoji.png)

Credit: http://www.globalnerdy.com/2016/06/23/map-filter-and-reduce-explained-using-emoji/
