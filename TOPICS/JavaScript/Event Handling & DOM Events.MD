# JavaScript Event Handling & DOM Events — Complete Reference with Examples

Event handling is at the core of interactive web development. JavaScript provides powerful mechanisms to **listen for**, **respond to**, and **control** user and system events across the DOM.

---

## 🧠 Table of Contents

1. [What Are Events?](#what-are-events)
2. [Event Flow (Capturing → Target → Bubbling)](#event-flow-capturing--target--bubbling)
3. [Event Listeners](#event-listeners)
4. [Event Object](#event-object)
5. [Event Propagation](#event-propagation)
6. [Preventing Default Actions](#preventing-default-actions)
7. [Event Delegation](#event-delegation)
8. [Keyboard Events](#keyboard-events)
9. [Mouse Events](#mouse-events)
10. [Form Events](#form-events)
11. [Window & Document Events](#window--document-events)
12. [Custom Events](#custom-events)
13. [Async Events & Microtasks](#async-events--microtasks)
14. [Performance Tips & Best Practices](#performance-tips--best-practices)
15. [Summary Table](#summary-table)

---

## 🔹 What Are Events?

An **event** is a signal that something has happened — e.g., a user clicks a button, types a key, or the DOM finishes loading.

Common examples:

* `click`
* `submit`
* `keydown`
* `scroll`
* `resize`

Each event triggers a **callback function**, known as an **event handler**.

```js
button.addEventListener('click', () => {
  console.log('Button clicked!');
});
```

---

## 🔹 Event Flow (Capturing → Target → Bubbling)

When an event occurs, it passes through three phases:

1. **Capturing phase** – event travels from `window` → root → target element.
2. **Target phase** – event reaches the element that triggered it.
3. **Bubbling phase** – event bubbles back up from target → document.

### Example

```html
<div id="parent">
  <button id="child">Click Me</button>
</div>
```

```js
parent.addEventListener('click', () => console.log('Parent clicked'));
child.addEventListener('click', () => console.log('Child clicked'));
// Clicking button:
// Child clicked
// Parent clicked
```

> By default, events use the **bubbling phase**. You can opt into **capturing** using a third parameter:

```js
div.addEventListener('click', handler, true); // capture phase
```

---

## 🔹 Event Listeners

### Adding Listeners

```js
element.addEventListener('click', handler);
```

### Removing Listeners

```js
element.removeEventListener('click', handler);
```

### Inline HTML (not recommended)

```html
<button onclick="alert('Clicked!')">Click</button>
```

---

## 🔹 Event Object

The event object is automatically passed to the handler:

```js
document.addEventListener('click', (event) => {
  console.log('Target:', event.target);
  console.log('Type:', event.type);
});
```

| Property             | Description                           |
| -------------------- | ------------------------------------- |
| `type`               | Event type (`click`, `keydown`)       |
| `target`             | The original element clicked          |
| `currentTarget`      | Element the listener is bound to      |
| `bubbles`            | Boolean indicating bubbling           |
| `defaultPrevented`   | True if `preventDefault()` was called |
| `key`, `code`        | Keyboard input details                |
| `clientX`, `clientY` | Mouse coordinates                     |

---

## 🔹 Event Propagation

Control how events move through the DOM.

### Stop Bubbling

```js
element.addEventListener('click', (e) => {
  e.stopPropagation();
});
```

### Stop Capturing and Bubbling

```js
e.stopImmediatePropagation();
```

---

## 🔹 Preventing Default Actions

Prevents browser’s default behavior (like form submission or link navigation).

```js
link.addEventListener('click', (e) => {
  e.preventDefault(); // stops navigation
  console.log('Custom link handler');
});
```

---

## 🔹 Event Delegation

Use **one parent listener** to manage multiple child elements efficiently.

```js
document.querySelector('#list').addEventListener('click', (e) => {
  if (e.target.tagName === 'LI') {
    console.log('Clicked:', e.target.textContent);
  }
});
```

✅ Benefits:

* Fewer event listeners
* Handles dynamic elements automatically

---

## 🔹 Keyboard Events

| Event      | Description                     |
| ---------- | ------------------------------- |
| `keydown`  | Fires when key is pressed       |
| `keypress` | (Deprecated) similar to keydown |
| `keyup`    | Fires when key is released      |

Example:

```js
document.addEventListener('keydown', (e) => {
  if (e.key === 'Enter') console.log('Enter pressed');
});
```

---

## 🔹 Mouse Events

| Event        | Description                 |
| ------------ | --------------------------- |
| `click`      | Single click                |
| `dblclick`   | Double click                |
| `mousedown`  | Pressed down                |
| `mouseup`    | Released                    |
| `mousemove`  | Mouse moved                 |
| `mouseenter` | Entered element (no bubble) |
| `mouseleave` | Left element (no bubble)    |
| `mouseover`  | Entered element (bubbles)   |
| `mouseout`   | Left element (bubbles)      |

Example:

```js
box.addEventListener('mousemove', (e) => {
  console.log(`X: ${e.clientX}, Y: ${e.clientY}`);
});
```

---

## 🔹 Form Events

| Event    | Description           |
| -------- | --------------------- |
| `submit` | Form submitted        |
| `change` | Input value changed   |
| `input`  | Fires every keystroke |
| `focus`  | Element focused       |
| `blur`   | Element lost focus    |

Example:

```js
form.addEventListener('submit', (e) => {
  e.preventDefault();
  console.log('Form submitted!');
});
```

---

## 🔹 Window & Document Events

| Event              | Description                      |
| ------------------ | -------------------------------- |
| `load`             | Page fully loaded                |
| `resize`           | Window resized                   |
| `scroll`           | Page scrolled                    |
| `DOMContentLoaded` | HTML parsed (before images load) |
| `beforeunload`     | User leaving page                |

Example:

```js
window.addEventListener('resize', () => {
  console.log('Window resized!');
});
```

---

## 🔹 Custom Events

You can create and dispatch your own events.

### Create Custom Event

```js
const event = new CustomEvent('greet', {
  detail: { name: 'Alice' }
});

document.addEventListener('greet', (e) => {
  console.log(`Hello, ${e.detail.name}!`);
});

document.dispatchEvent(event);
```

---

## 🔹 Async Events & Microtasks

Event handlers can trigger **Promises or async functions**, but they execute **synchronously first**, then queue microtasks.

```js
document.addEventListener('click', async () => {
  console.log('Click start');
  await Promise.resolve();
  console.log('Click async done');
});
console.log('Outside');

// Output:
// Outside
// Click start
// Click async done
```

---

## 🔹 Performance Tips & Best Practices

✅ Use **event delegation** instead of many listeners.
✅ Remove listeners with `removeEventListener()` when no longer needed.
✅ Avoid inline event handlers (`onclick`).
✅ Use **passive listeners** for scroll events:

```js
window.addEventListener('scroll', handleScroll, { passive: true });
```

✅ Debounce or throttle high-frequency events like `resize` or `mousemove`.

Example debounce:

```js
function debounce(fn, delay) {
  let timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
}

window.addEventListener('resize', debounce(() => {
  console.log('Resized!');
}, 300));
```

---

## 🔹 Summary Table

| Concept          | Description                      | Example                               |
| ---------------- | -------------------------------- | ------------------------------------- |
| Event Flow       | Capturing → Target → Bubbling    | `addEventListener('click', fn, true)` |
| Event Object     | Contains details                 | `event.target`, `event.type`          |
| Stop Propagation | Prevent bubbling                 | `event.stopPropagation()`             |
| Prevent Default  | Stop browser action              | `event.preventDefault()`              |
| Delegation       | Parent handles child events      | `e.target.tagName`                    |
| Keyboard         | `keydown`, `keyup`               | Listen for keys                       |
| Mouse            | `click`, `mousemove`, `dblclick` | Mouse actions                         |
| Form             | `submit`, `change`, `input`      | Form interactions                     |
| Custom Events    | Developer-defined                | `new CustomEvent()`                   |
| Performance      | Optimize handlers                | Debounce, passive listeners           |

---

### ✅ In Short

* Events follow the **capturing → target → bubbling** model.
* Use **event delegation** for scalable performance.
* Always **prevent default** only when necessary.
* Use **passive** and **debounced** listeners for smooth performance.
* Combine with **async/await** for complex async UI workflows.

---

**Next Suggested Topic:** 🧩 *JavaScript Objects & Prototypes — Complete Reference with Examples*
