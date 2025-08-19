## for see Query [Click here](README.md)   

# Basic DOM (Document Object Model) — Cheat Sheet

**Updated:** 2025-08-19

---

## About this file

A focused, clean, and easy-to-read reference covering **DOM fundamentals**, short **regex primer**, **14-digit phone validation** (HTML + JS), and a compact **API basics** section. Duplicates removed and small clarifications added where helpful.

---

## 1. What is the DOM?

* **DOM** = Document Object Model.
* Browser converts HTML into a **tree-like object structure (nodes)** that JavaScript can read and modify.
* The DOM is the bridge between HTML/CSS and JavaScript.

**Simple HTML example:**

```html
<!DOCTYPE html>
<html>
  <head>
    <title>DOM Example</title>
  </head>
  <body>
    <h1 id="title">Hello World</h1>
    <button onclick="changeText()">Click Me</button>
  </body>
</html>
```

---

---

## 2. Querying / Accessing elements

```javascript
document.getElementById('id');            // single element
document.getElementsByClassName('cls');   // HTMLCollection (live)
document.getElementsByTagName('p');      // HTMLCollection (live)
document.querySelector('.c');            // first match (CSS selector)
document.querySelectorAll('.c');         // NodeList (static)
```

**Notes:**

* `querySelector` & `querySelectorAll` support any CSS selector and are usually recommended for consistency.
* `getElementsBy...` return **live** collections; `querySelectorAll` returns a static NodeList.

---

## 3. Reading & changing content

```html
<p id="demo">Old</p>
```

```javascript
const p = document.getElementById('demo');
p.innerHTML = '<strong>New</strong>'; // allows HTML
p.textContent = 'Plain text';          // safer for text-only
```

**When to use which:** prefer `textContent` unless you deliberately need to insert HTML (beware XSS).

---

## 4. Attributes, classes & styles

**Attributes:**

```javascript
const el = document.querySelector('#title');
el.getAttribute('id');
el.setAttribute('data-id','123');
el.removeAttribute('data-id');
```

**Classes:**

```javascript
el.classList.add('active');
el.classList.remove('active');
el.classList.toggle('open');
el.classList.contains('active');
```

**Styles (prefer classes instead of inline styles):**

```javascript
el.style.color = 'red';           // inline override
// Better: add/remove a CSS class
el.classList.add('red-text');
```

---

## 5. Creating, inserting & removing elements

```javascript
// Create
const newP = document.createElement('p');
newP.textContent = 'I am new!';

// Insert
document.body.appendChild(newP);            // at the end
// or
const container = document.getElementById('container');
container.insertBefore(newP, container.firstChild);

// Remove
newP.remove();                              // modern method
// or
container.removeChild(container.lastChild);
```

---

## 6. Events & event handling

**Inline (quick, not recommended for larger apps):**

```html
<button onclick="sayHello()">Click</button>
```

\*\*Recommended: \*\***`addEventListener`**

```javascript
const btn = document.querySelector('#myBtn');
btn.addEventListener('click', () => {
  console.log('Clicked');
});

// Example: event handler with event object
document.addEventListener('keydown', function (e) {
  console.log(e.key);
});
```

**Event delegation (good for many dynamic items):**

```javascript
// instead of adding listeners to every list item,
// add one listener to the parent:
const list = document.querySelector('#list');
list.addEventListener('click', (e) => {
  const li = e.target.closest('li');
  if (!li) return;
  // handle click for li
});
```

---

## 7. Mini practice projects (copy-paste-ready)

### 1) Counter

```html
<h1 id="count">0</h1>
<button id="inc">+</button>
<button id="dec">-</button>
<script>
let count = 0;
document.getElementById('inc').addEventListener('click', () => {
  count++;
  document.getElementById('count').textContent = count;
});
document.getElementById('dec').addEventListener('click', () => {
  count--;
  document.getElementById('count').textContent = count;
});
</script>
```

### 2) Dark Mode Toggle

```html
<button id="toggleDark">🌙 Toggle Dark Mode</button>
<style>
  .dark { background: #111; color: #efefef; }
</style>
<script>
const toggle = document.getElementById('toggleDark');
toggle.addEventListener('click', () => document.body.classList.toggle('dark'));
</script>
```

### 3) Show / Hide Password

```html
<input type="password" id="pwd">
<button id="showPwd">Show</button>
<script>
const pwd = document.getElementById('pwd');
const showBtn = document.getElementById('showPwd');
showBtn.addEventListener('click', () => {
  pwd.type = pwd.type === 'password' ? 'text' : 'password';
  showBtn.textContent = pwd.type === 'password' ? 'Show' : 'Hide';
});
</script>
```

---

## 8. Regex primer (essentials)

* `\d` → digit (0–9)
* `\w` → word char (A–Z, a–z, 0–9, \_)
* `\s` → whitespace
* `.` → any char (except newline)
* Quantifiers: `+` (1+), `*` (0+), `?` (0 or 1), `{n}` (exact), `{n,m}` (range)
* Anchors: `^` (start), `$` (end)

**Examples:**

* Exactly 14 digits: `^\d{14}$`
* 7 to 14 digits: `^\d{7,14}$`
* Literal plus sign: to match `+` use `\+` in regex.

---

## 9. Phone number validation — *Exactly 14 digits* (HTML + JS)

Use this when you want the input to contain **only digits** and **exactly 14 of them**.

### HTML (pattern + maxlength)

```html
<form id="phoneForm">
  <label for="phone">Phone Number (14 digits only)</label>
  <input id="phone" name="phone" type="tel"
         pattern="^\d{14}$" maxlength="14" required
         placeholder="12345678901234">
  <button type="submit">Submit</button>
</form>
```


* `pattern` enforces the browser-side validation on form submit.
* `maxlength` prevents typing more than 14 characters.

### JavaScript (immediate validation & normalized check)

```html
<input id="phoneInput" type="text" placeholder="Enter 14 digits">
<button id="checkBtn">Check</button>
<p id="phoneMsg"></p>

<script>
const regex14 = /^\d{14}$/;
let regex = /^0\d{13}$/;  //another - Must start with 0 and total 14 digits
const input = document.getElementById('phoneInput');
const msg = document.getElementById('phoneMsg');

function validate14(val) {
  return regex14.test(val);
}

// live feedback (optional)
input.addEventListener('input', () => {
  const cleaned = input.value.replace(/\D/g, ''); // allow only digits visually
  if (input.value !== cleaned) input.value = cleaned;
  msg.textContent = cleaned.length === 14 ? '✅ 14 digits' : `⚠️ ${cleaned.length} digits`;
});

// on demand check
document.getElementById('checkBtn').addEventListener('click', () => {
  if (validate14(input.value)) {
    alert('Valid 14-digit phone number: ' + input.value);
  } else {
    alert('Invalid: enter exactly 14 digits');
  }
});
</script>
```

**Server-side note:** Always validate on the backend too (regex `^\\d{14}$` or stronger) before saving.

---

## 10. API basics (compact)

* **API** = Application Programming Interface — rules for two systems to communicate.
* **Endpoint** = URL you call (e.g. `https://api.example.com/users`).
* **HTTP methods**: `GET`, `POST`, `PUT`/`PATCH`, `DELETE`.
* **Request**: URL, method, headers, body. **Response**: status code + body (usually JSON).

**Simple ************************************************************************`fetch`************************************************************************ example (GET / POST):**

```javascript
// GET
fetch('https://jsonplaceholder.typicode.com/users')
  .then(res => res.json())
  .then(data => console.log(data));

// POST
fetch('https://jsonplaceholder.typicode.com/users', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ name: 'John Doe' })
})
  .then(res => res.json())
  .then(data => console.log(data));
```

**Quick tips:** always handle errors, use proper auth (API keys, tokens, OAuth), and prefer HTTPS.

---


