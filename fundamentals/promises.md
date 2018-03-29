# Promises


## What is a promise?

Why is a JavaScript ES6 `promise` called a 'promise'? Here is a snippet from the *Oxford Dictionary of English* definition of 'promise':

> **promise** |ˈprɒmɪs|<br>
noun<br>
1 a declaration or assurance that one will do something or that a particular thing will happen

This pretty well sums up what a promise means in JavaScript: something that will be delivered in the future (if and when the promise is *fulfilled*).

Traditionally, *callbacks* are used as a way to receive the data that is delivered asynchronously (meaning that the data is not likely to be available at the time it is requested but can be expected some time later). Using callbacks can quickly become unwieldy when dealing with many asynchronous events (e.g., ajax calls), especially when they depend on each other (google for *callback hell*).

JavaScript ES6 introduces promises as a better alternative for callbacks when dealing with asynchronous events.

We can state a number of simple facts about ES6 promises:

- A promise is a JavaScript object (`typeof somePromise === 'object'`) that serves as a placeholder for a (future) value.
- Because a promise is an ordinary JavaScript object you can pass it around as an argument to a function, return it from a function, assign it to a variable, push it to an array, etc.
- You can receive the 'promised' value by calling the `.then()` method of the promise, passing it a function that will receive that value as its argument as soon as it is available.
- You can create a promise by calling the ES6 `Promise` constructor function with `new` (see Listing 1 below), then call `resolve()` when results are ready or `reject()` on detecting an error.
- Sometimes you can get a ready-made promise by calling an appropriate API or library function, like the `fetch()` Web API function in Listing 1.
- Internally, a promise can be in one of three states:
   - **pending**: the asynchronous result is still awaiting delivery
   - **fulfilled**: the asynchronous result has been delivered and is available (`resolve()` was called)
   - **rejected**: an error was encountered: the promise could not be fulfilled (`reject()` was called)
- A promise that is no longer pending because it was either fulfilled or rejected is said to be _settled_.
- A promise that is _settled_ has reached its final state. Its state and value can no longer be changed. It has become _immutable_. Subsequently calling `resolve()` or `reject()` does no longer affect the outcome of the promise.

## Example code

Listing 1 shows an example based on an asynchronous XMLHttpRequest that we will use throughout the rest of this discussion.

```js
'use strict';

function fetchJSON(url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open('GET', url);
    xhr.responseType = 'json';
    xhr.onreadystatechange = () => {
      if (xhr.readyState === 4) {
        if (xhr.status < 400) {
          resolve(xhr.response);
        } else {
          reject(new Error(xhr.statusText));
        }
      }
    };
    xhr.send();
  });
}

// alternative:
// const fetchJSON = url => fetch(url).then(res => res.json());

const url = 'http://api.nobelprize.org/v1/laureate.json?gender=female';

fetchJSON(url)
  .then(data => renderData(data))
  .catch(err => renderError(err));

function renderData(data) {
  console.log(data);
}

function renderError(err) {
  console.error(err.message);
}
```

Listing 1. Asynchronous `XMLHttpRequest` (and `fetch` alternative) using a promise.

The `fetchJSON()` function in Listing 1 returns a `promise` that resolves to a value converted from JSON data received from a remote API end point. The alternative version of `fetchJSON()` (commented out here) uses a more modern browser function that natively returns a promise.


## The .then() method

A promise exposes a `.then()` method through which you can obtain its fulfilled value or an error value in the case the promise was rejected:

```js
somePromise.then(onFulfilled, onRejected);
```

The `.then()` method takes as its parameters two **optional** functions, the first one dealing with the 'happy' scenario (the promise is fulfilled) and the second one dealing with the error case (the promise is rejected). If you are only interested in the success case you can leave out the second parameter:

```js
somePromise.then(data => {
  // ...
});
```

If you are only interested in the error case, you can pass `null` for the first argument:

```js
somePromise.then(null, err => {
  //...
});
```

or you can use another method available on a promise, `.catch()`, which is just a shorthand for calling `then()` with `null` as its first argument:

```js
somePromise
  .then(data => {
    // ...
  })
  .catch(err => {
    // ...
  });
```

Note that the `onFulfilled` and `onRejected` handler functions always execute asynchronously. When the promise is settled, the `onFulFilled` or `onRejected` handler is placed on the event/callback queue. They execute when the currently executing JavaScript code runs to completion, causing the [call stack](./event_loop.md#call-stack) to become empty and enabling the event loop to process the next event from the queue. This holds true even if the promise is immediately fulfilled or rejected, as in this example:

```js
Promise.resolve(42)
  .then(data => console.log(data));

console.log('after promise');

// console output:
// after promise
// 42
```

This example also shows how you can create a promise that is immediately resolved. There is no need to use `new` or to pass a `(resolve, reject) => {}` function to the `Promise` constructor. Similarly you can create a promise that is immediately rejected:

```js
Promise.reject(new Error('oops'))
  .catch(err => console.log(err.message));

console.log('after promise');

// console output:
// after promise
// oops
```

## Promise chaining

It is important to understand that the `.then()` method returns a new promise that resolves to the return value of `onFulfilled` (if specified) in case of the 'happy' scenario or the return value of `onRejected()` (if specified) in case of an error. If the return value of these functions is a plain JavaScript value, the new promise is immediately fulfilled with that value. If the return value is yet another promise then the outcome is determined by the inner promise, once settled. If the function does not return a value, the new promise is immediately fulfilled with the value `undefined`.

Because `.then()` (and `.catch`) return new promises, you can chain them together such that your code can be read as: do *this*, then do *that* and then *that*, etc.:

```js
function fetchAndRender(url, otherUrl) {
  fetchJSON(url)
    .then(data => {
      renderData(data);
      return fetchJSON(otherUrl);
    })
    .then(otherData => {
      renderOtherData(otherData);
    })
    .catch(err => {
      renderError(err);
    });
}

fetchAndRender();
```

Listing 2. Chaining of `then` and `catch`

Let's examine Listing 2 in a bit more detail. There two calls to `fetchJSON()`. Errors are handled in one place, by means of the `.catch()` method that terminates the promise "chain".

If you embed another promise inside the function that you pass to the `.then()` method (in Listing 2 this is done in the first `.then()`) you should return that promise as the function's return value. If you don't return the promise, there is no way for the `.catch()` at the end of the chain to "see" a `reject()` of the inner promise, leaving the rejection unhandled.

In case a promise in the chain is rejected due to some error, the promise chain will be traversed until an `onRejected` handler (e.g., in a terminating `.catch()` method) is found. All intermediate `onFulfilled` handlers (e.g. `.then()`) will be skipped.

Handling errors at the end of a promise chain is a major advantage over the repetition of error handling code in the case of callbacks.

Note however that a `.catch()` method does not necessarily have to be the last method in the chain. It can be used to handle errors midway. As mentioned previously, the `.catch()` method returns a new promise which can be used to provide some "fallback" value in case of errors.

In the example below a promise is created that is immediately rejected. The promise is subsequently "consumed" twice.

1. In the first case ('consumer 1'), the rejection is caught by a `.catch()` method and the rejection value `'bad'` is printed on the console.

2. In the second case ('consumer 2'), the rejection is also caught by a `.catch()` method, but now the catch handler completely ignores the rejection value and just returns the fallback value `'good.`. This becomes the fulfilled value of the promise returned by `.catch()`. The next `.then()` in the chain, completely oblivious that an error ever occurred, now prints the fulfilled value `'good'` on the console.

```js
const promise = Promise.reject('bad');

// consumer 1
promise
  .catch(console.log); // -> "bad"

// consumer 2
promise
  .catch(() => 'good')
  .then(console.log); // -> "good"
```

## Promise.all()

There may be situations where you want to execute multiple promises in parallel and wait until all promises are resolved. Of course, these promises must not be interdependent (i.e. a promise must not depend on the result of another promise running in parallel). The `Promise.all()` method accepts an array (or more precisely, an _iterable_) of promises. It return a new promise that is resolved when all promises in the array are resolved, or rejected as soon as one of the promises in the array is rejected.

The fulfilled value of the new promise is an array of fulfilled values of the individual promises passed to `Promise.all()`, in the same order.

More details on MDN: [Promise.all()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)

## Additional resources

Our previous students also enjoyed learning about promises at:

In text:

- http://javascript.info/promise-basics
- https://blog.cloudboost.io/explaining-basic-javascript-promises-in-jip-en-janneketaal-c98763c0abd6
- https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-promise-27fc71e77261

Video: The net Ninja: https://www.youtube.com/watch?v=yswb4SkDoj0

MDN:

- [MDN - Promise definition](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [MDN - Using Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)
- [Promises/A+ specification](https://promisesaplus.com/)
