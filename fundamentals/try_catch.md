# try...catch

From MDN:

> The `try...catch` statement marks a block of statements to try, and specifies a response, should an exception be thrown.

Let's first talk about exceptions. Exceptions are used in programming to signal that an unexpected situation has arisen or an unexpected result was received, usually because of some error. In that situation, a program can throw an exception. In JavaScript this can be done as follows:

> `throw` _expression_;

By convention, the _expression_ is expected to be a standard JavaScript [Error](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error) object. You can state the reason for the exception by passing a message string as argument to the constructor of the `Error` object, as in this example.

```js
throw new Error('No response from the network.');
```

When you throw an error, further execution of the current code stops immediately. The JavaScript engine will start looking for code that is specifically written to handle the error (see `try..catch` below). If there is no such code then the JavaScript engine will report it as an unhandled exception. When encountering an unhandled exception in the browser, JavaScript will clear the call stack end wait for a next event to occur. In Node the JavaScript program will be terminated abnormally.

## Exception handling with try...catch

If you anticipate that your code may be subjected to exceptions (either because you throw exceptions yourself or exceptions thrown by Web API or library functions*) you can handle exceptions by wrapping your code in a `try...catch` block.

Example: The `JSON.parse()` method may throw an exception if the string passed to `.parse()` is not valid JSON.

```js
try {
  const obj = JSON.parse('this is invalid JSON');
  console.log(obj);
}
catch (err) {
  console.error('An error occurred: ' + err.message);
}

// console output:
// An error occurred: Unexpected token h in JSON at position 1
```

Note that the `catch` block receives the `Error` object that was thrown.

\* Note: You should be able to find out whether exceptions are thrown in a Web API (e.g. `JSON.parse()`) or library functions by inspecting the corresponding documentation.

## try...catch with async and await

The `try...catch` block is also important when working with `async` and `await`. Where the default method of working with promises involves a `.catch()` method to deal with errors, there is no such method when using `async` and `await`. Rejected promises when using `async` and `await` are thrown as exceptions.

This is an example taken from the [fundamental on promises](./promises.md):

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

The `fetchJSON()` function returns a promise. Should that promise be rejected an exception is thrown and caught by the `catch` block.

## When to throw exceptions

As a rule of thumb you should only throw exceptions yourself if the situation or result is truly unexpected. For instance, if you ask a user to enter a number and the user types something that is not a number you should **not** throw an exception. This is because it is in the normal course of events that you can expect a user to type something other than a number. In this case you should deal with it in the usual fashion without throwing an exception.

Examples of situations where an exception is appropriate are:

- The network is unexpectedly down.
- A database connection was dropped.

And so on.

## Further reading on MDN

[try...catch](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch)