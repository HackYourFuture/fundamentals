# async & await

The keywords `async` and `await` were introduced in ECMAScript 2017 as a new way to 'consume' promises. It obviates the need to use `.then()` and `.catch()` and makes it possible to write code that uses promises in a "synchronous" fashion.

Referring back to the code snippet of Listing 2, we can now rewrite this code as follows:

```js
async function fetchAndRender() {
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

fetchAndRender();
```

The `await` keyword causes the code execution to be suspended in a non-blocking manner until the promise returned by `fetchJSON()` is resolved. Once the promise is resolved the awaited expression returns the fulfilled value of the promise and execution resumes at the point where it was left off.

> If you forget to use the `await` keyword you will get the promise itself rather than its fulfilled value. And of course, there will be no 'waiting'.

You can only use the `await` keyword in functions marked with the keyword `async`. The return value (if any) of that function is also a promise.

Note that you must use a `try`-`catch` block to deal with errors. Please refer to the [try...catch](./try_catch.md) fundamental for more information.

More info on MDN:

- [async function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
- [await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await)

Wes Bos video on `async`/`await`: https://youtu.be/9YkUCxvaLEk

