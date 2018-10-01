# Basic DOM manipulations

Using JavaScript we can access and manipulate the Document Object Model (DOM). We access the DOM through a global object called `document`.

HTML
```html
<body>
  <div id="hello"></div>
</body>
```

A common method to access the DOM is by giving a HTML element an ID, and then using the `document` method `getElementById()`

```js
const x = document.getElementById('hello');
```

Now we have stored a *reference* of how that HTML element is accessed through the DOM object. We can use this to manipulate the element.

```js
x.innerHTML = 'hello';
```
See the result of what this code changes [here](https://jsfiddle.net/supercor/ud5ym96k/3/) and play with it yourself


We can also create elements
```js
const a = document.createElement('li');
x.appendChild(a);
```
See the result of what this code changes [here](https://jsfiddle.net/supercor/ud5ym96k/5/) and play with it yourself

We can set attributes on elements
 ```js
 a.setAttribute('id', 'hackyourfuture');
 ```
See the result of what this code changes [here](https://jsfiddle.net/supercor/ud5ym96k/8/) and play with it yourself

 We can add event listeners to elements:

 ```js
 turnLeftButton.addEventListener('click', function () {
    turn('left');
});
 ```
 See the result of what this code changes [here:](https://jsfiddle.net/supercor/ud5ym96k/9/) and play with it yourself

