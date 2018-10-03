# Basic DOM manipulation

The Document Object Model (DOM) is a construct through which web browsers make the HTML structure of a web page (= 'document') available to JavaScript files loaded into that page. We can access the DOM through a global object called `document`.

Consider this HTML:

```html
<body>
  <div id="root"></div>
</body>
```

A common method to access an HTML element through the DOM is by giving it an ID, and then using the `document` method `getElementById()`.

```js
const rootDiv = document.getElementById('root');
```

Now, we have a *reference* to the HTML element in our JavaScript code. We can, for instance, set the text content through the `innerText` property.

```js
rootDiv.innerText = 'Hello, world!';
```

As a result, the HTML structure now looks like this:

```html
<body>
  <div id="root">Hello, world!</div>
</body>
```

> See the result of what this code changes in this [CodePen](https://codepen.io/remarcmij/pen/VEerRP) and play with it yourself.

_Note that we are not modifying the HTML file (e.g., `index.html`) itself. We are just modifying an in-memory representation of the HTML as was initially loaded through the HTML file._

We can also create elements:

```js
// Create a button element and label it Click Me!
const myButton = document.createElement('button');
myButton.innerText = 'Click Me!';
```

This creates a button element and sets its label (i.e. `innerText`), but this in itself is not enough. At this point the button is created but not yet added to the DOM: it is still somewhere "hanging up in the air". We need to attach it to some parent element that is already part of the DOM. This is done by calling `appendChild()` on that parent element, passing it a reference to the newly created element.

```js
rootDiv.appendChild(myButton);
```

As a result, the HTML structure now looks like this:

```html
<body>
  <div id="root">
    <button>Click Me!</button>
  </div>
</body>
```

> See the result of what this code changes in this [CodePen](https://codepen.io/remarcmij/pen/bmEaVm) and play with it yourself.

We can set attributes on elements:

```js
myButton.setAttribute('class', 'my-button');
```

Resulting HTML:

```html
<body>
  <div id="root">
    <button class="my-button">Click Me!</button>
  </div>
</body>
```

> See the result of what this code changes in this [CodePen](https://codepen.io/remarcmij/pen/wYMmpP) and play with it yourself.

We can add event listeners to elements to respond to user interaction. In this example we are adding an event listener to the button that responds to the `click` event. This event is triggered whenever the user clicks on the button. 

```js
 myButton.addEventListener('click', function () {
  const para = document.createElement('p');
  para.innerText = 'You clicked me!';
  rootDiv.appendChild(para);
});
```

Adding an event listener does not change the HTML structure. However the event listener may cause changes to the DOM (as is illustrated in the above example) when the event is triggered.

> See the result of what this code changes in this [CodePen](https://codepen.io/remarcmij/pen/MPKVEP) and play with it yourself.

