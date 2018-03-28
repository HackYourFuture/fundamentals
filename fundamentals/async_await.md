# async & await

## Revisiting promises

The example shown in Listing 1 below was used in the fundamental about [promises](./promises.md) to illustrate the chaining of promises with a final `.catch()` at the end of the chain to handle errors.

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
```

Listing 1. Chained promises.

In this example the `fetchJSON()` function fetches data from a remote API and returns a promise. When the promise returned by the first call to `fetchJSON()` resolves, the returned data is rendered to the DOM by means of the `renderData()` function. Subsequently, a second call is made to `fetchJSON` to fetch other data. When the promise of that second call is fulfilled, the supplemental data is rendered to the DOM.

Because there is a time delay between the arrival of the first data set and the arrival of the second data set, a user looking at the web page will see the page being filled up in two separate updates. It may be a better user experience if the page updates are all done in one go. We could modify the code of Listing 1 to postpone rendering the first data set until the second data set (`otherData`) is received. This is illustrated in Figure 2.

```js
function fetchAndRender(url, otherUrl) {
  fetchJSON(url)
    .then(data => {
      return fetchJSON(otherUrl)
        .then(otherData => {
          renderData(data);
          renderOtherData(otherData);
        });
    })
    .catch(err => {
      renderError(err);
    });
}
```

Listing 2. Nested promises.

Now, when the promise returned by the second call to `fetchJSON()` is fulfilled, the `.then()` method directly called on that second promise is used to render both the data from the first `fetchJSON()` call (still accessible through a _closure_) and the `otherData` from the second promise.

We must still return the result of the inner promise chain to ensure that any promise rejection in that inner chain is caught by the terminating `.catch()` of the outer chain. That result will in this example be a promise that is fulfilled to the value `undefined` or a rejected promise in case of an error.

To achieve the goal from this example of rendering in one go, there is yet another option available to us using promises, provided that the second call to `fetchJSON()` is not dependent on data from the first call. In this example this is indeed the case. This makes it possible to use the `Promise.all()` method, as shown in Listing 3.

```js
function fetchAndRender(url, otherUrl) {
  Promise.all([fetchJSON(url), fetchJSON(otherUrl)])
    .then(([data, otherData]) => {
      renderData(data);
      renderOtherData(otherData);
    })
    .catch(err => {
      renderError(err);
    });
}
```

Listing 3. Running promises in parallel using Promise.all().

The promises from the two calls to `fetchJSON()` now run in parallel. `Promise.all()` returns a new promise that is resolved if all the promises passed in the array argument are resolved or is rejected as soon as any one of the promises is rejected. Its fulfilled value is an array of the fulfilled values of the individual promises, in the same order. Because the promises run in parallel the browser can send out two simultaneous XMLHttpRequests, thereby improving overall performance.


### The async/await alternative

The keywords `async` and `await` were introduced in ECMAScript 2017 as a new way to 'consume' promises. It obviates the need to use `.then()` and `.catch()` and makes it possible to write code that uses promises in a "synchronous" fashion.

Referring back to the code snippet of Listing 1, we can now rewrite this code as shown in Listing 4 below:

```js
async function fetchAndRender(url, otherUrl) {
  try {
    const data = await fetchJSON(url);
    renderData(data);
    const otherData = await fetchJSON(otherUrl);
    renderOtherData(otherData);
  }
  catch (err) {
    renderError(err);
  }
}
```

Listing 4. Reimplementation of Listing 1 using async/await

The `await` keyword causes the code execution to be suspended in a non-blocking manner until the promise returned by `fetchJSON()` is resolved. Once the promise is resolved the awaited expression returns the fulfilled value of the promise and execution resumes at the point where it was left off.

> If you forget to use the `await` keyword you will get the promise itself rather than its fulfilled value. And of course, there will be no 'waiting'.

You can only use the `await` keyword in functions marked with the keyword `async`. The return value (if any) of that function is also a promise.

Note that you must use a `try`-`catch` block to deal with errors. Please refer to the [try...catch](./try_catch.md) fundamental for more information.

Referring back to the nested promises of Listing 2, we can now with `async`/`await` achieve our objective of rendering in one go simply by moving the call to `renderData()` after the second `fetchJSON()` call, as shown in Listing 5. What can be easier than that?

```js
async function fetchAndRender(url, otherUrl) {
  try {
    const data = await fetchJSON(url);
    const otherData = await fetchJSON(otherUrl);
    renderData(data);
    renderOtherData(otherData);
  }
  catch (err) {
    renderError(err);
  }
}
```

Listing 5. Reimplementation of Listing 2 using async/await

With `async`/`await` we can also take advantage of running promises in parallel. In this case we must use the same `Promise.all()` method as shown in Listing 3. The version with `async`/`await` is shown in Listing 6 below.

```js
async function fetchAndRender(url, otherUrl) {
  try {
    const [data, otherData] = await Promise.all([fetchJSON(url), fetchJSON(otherUrl)]);
    renderData(data);
    renderOtherData(otherData);
  }
  catch (err) {
    renderError(err);
  }
}
```

Listing 6. Reimplementation of Listing 3 using async/await

More info on MDN:

- [async function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
- [await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await)

Wes Bos video on `async`/`await`: https://youtu.be/9YkUCxvaLEk

