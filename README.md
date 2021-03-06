### Table of Contents

| [Promises, async/await](#promises-async-await) |
|-----------------|
| [Callbacks](#callbacks) |

| [Advanced Functions](#advanced-functions) |
|-----------------|
| [Closures](#closures) |
| [`var`](#var) |
| [Scheduling](#scheduling) |
| [Throttling and debouncing](#throttling-and-debouncing) |
| [Decorators](#decorators) |

| [Basic JavaScript](#basic-javascript) |
|-----------------|
| [Type Conversion](#type-conversion) |
| [Comparsion](#comparison) |
| [Symbols](#symbols) |
| [Functions](#functions) |
| [`this`](#this) |
| [Objects](#objects) |
| [Maps](#maps) |
| [Sets](#sets) |
| [Primitives](#primitives) |
| [Data Types](#data-types) |

[**Random Notes**](#random-notes)

---

# Promises, async/await

## Callbacks

Some `loadScript` function can be asynchronous (not run now, but later). If we want to use its contents, we can add a callback, and then call `script.onload = () => callback(script)`. But easily turns into "callback hell".

## Promise

Promise:
1. Takes code that takes time. The rest of our code waits for it, and wants its result once its ready.
2. When the code produces the promised result, the Promise makes that result available to all the subscribed code.

```
let promise = new Promise(function(resolve, reject) {
  resolve("done");

  reject(new Error("…")); // ignored
  setTimeout(() => resolve("…")); // ignored
});

promise.then(
	function(result) {},
	function(resolve) {}
);

```
**Resolve** changes the Promise's internal state to fulfilled, and passes result to `.then`. **Reject** runs when promise rejected (`.catch(f) = .then(null, f);`).

Handlers are always asynchronous: code after .then/.catch always end up running before subscribers because of JS's internal execution queue.

### Promise chaining

We can chain `.then`'s because a `.then` handler returns a Promise. When a `then` returns, it resolves that Promise with the return value. If `then` returns a Promise, we wait until it settles.

## async/await

`async` before a function makes it always return a function (`return 1` is `return Promise.resolve(1)`). `async` allows `await` to be used.

`await` before a promise makes JS wait until that promise settles, then return result or throw error.
```
async function f1() {
	let a = await p1; // wait 2 seconds
	let b = await p2; // wait another 2 seconds
	return a + b
}

async function f2() {
	return await p1 + await p2; // wait 2 seconds total (running in parallel);
}
```

---

# Advanced Functions

## Closures

### Lexical Environment

Every _running function_ (so each instance), code block, and script has an associated LE, which stores local variables and a reference to outer LE.

Variables are first searched in the current LE, then outer, outer, etc. LE's don't store old variable values, so when accessing outer values, we use most recent values.

**One call-one LE:** a new LE is created every time a function runs

A closure is function that has `[[Environment]]`, so it remembers its outer variables and can access them.

### `var`

`var` has no block scope, only function-wide or global, so they are visible through blocks (including for loop declarations).

```
(function () {
	console.log(salary); // logs undefined because declaration is hoisted
  var salary = "5000$";
})();
```

## Scheduling

### Recursive setTimeout

We can call a `setTimeout` inside another, which will run something regularly. We can also use this to, for instance, request something every 5 seconds, but if the server is overloaded, increase it by a factor of 2 every time.

**Recursive setTimeout guarantees a delay between the executions, setInterval does not.**
Set Interval:
![](https://raw.githubusercontent.com/iliakan/javascript-tutorial-en/master/1-js/06-advanced-functions/08-settimeout-setinterval/setinterval-interval.png)

Set Timeout:
![](https://raw.githubusercontent.com/iliakan/javascript-tutorial-en/master/1-js/06-advanced-functions/08-settimeout-setinterval/settimeout-interval.png)

The func's execution time consumes a part of the interval. If execution time > interval, func runs again immediately.

### `setTimeout(func, 0)`

Zero-timeout scheduling is used to schedule the call "as soon as possible, but after the current code is complete". Even if it was `setTimeout(func, 100)` and the current code took > 100ms, func would still wait.

## Throttling and Debouncing

Throttling enforces a maximum number of times a function can be called over time. As in "execute this function at most once every 100 milliseconds." Debouncing enforces that a function not be called again until a certain amount of time has passed without it being called. As in "execute this function only if 100 milliseconds have passed without it being called."

```js
const debounce = (fn, delay) => {
	let timerId;
	return function(...args) {
		clearInterval(timerId);
		timerId = setTimeout(() => fn.apply(this, args), delay);
	};
};

const throttle = (fn, delay) => {
	let inThrottle = false;
	return function() {
		if (!inThrottle) {
			fn.apply(this, arguments);
			inThrottle = true;
			setTimeout(() => inThrottle = false, delay);
		}
	}
};

function throttle2(fn, wait) {
  let isThrottled = false,
    lastArgs = null;
  return function wrapper() {
    if (isThrottled) {
      lastArgs = arguments;
    } else {
      fn.apply(this, arguments);
      isThrottled = setTimeout( () => {
        isThrottled = false;
        if (lastArgs) {
          wrapper.apply(this, lastArgs);
          lastArgs = null;
        }
      }, wait);
    }
  }
}
		
	f() -> wait for a period of time before it can be called again
```

## Decorators

Wraps a function, for instance, to cache the value.
```
function slow(x) { slow stuff; }

function cachingDecorator(func) {
	// cache logic...
	return function(x) {
		// cache logic...
		return slow(x);
	}
}

slow = cachingDecorator(slow);
```

To set context, use `func.call(context, ...args)`. Or `func.apply(context, [args])`.

---

# Basic JavaScript

## Type Conversion

**String():** null => “null”. Can never convert Symbols

**Number():** Automatic in mathematical expressions, except “+” (if any operands are strings, becomes string concat)
- undefined => NaN, null => 0, true/false, 
- string => whitespace removed. empty string => 0
- 1 / 0 = Infinity

**Boolean():** intuitively empty => 0, an empty string, null, undefined and NaN

## Comparison

**String comparison:** compared in dictionary order (letter by letter) _by Unicode value_.

**When different types compared with `<=`, `>=`,** converted to Number.

Same with equality of **only strings, numbers, and booleans**. `null` coerces into `undefined`.

When something is `NaN`, any comparison intuitively yields false.

## Symbols

`Symbol(desc)` are unique. Can be hidden properties of objects.

Global registry, `Symbol.for(“desc”)` gets (or creates) that symbol to be reused.

`Symbol.keyFor(symbol in global registry)` gets description of that symbol. Must be in global registry.

## Functions

**Function declarations** `function foo(a)` defined first. Visible everywhere in code (or block, which also includes if/else).

**Function expressions** `foo = function(a)` are created when they’re reached.

## `this`

_(defined at run-time)_

In object methods, has value "before the dot": `obj.method()`’s `this` is `obj`.

If no object, a function's `this` is undefined (in non-use strict, it's `window`)

If we separate “dot” and method (i.e. `(hi = user.hi)()` instead of `(user.hi)()`), `this` is removed.

Arrow method takes `this` from outer function, if any.

## Objects

_For storing keyed collections_

- Object keys are Symbols or strings, or coerced into strings

_Left off at http://javascript.info/object-toprimitive_

## Maps

_For storing keyed collections with keys of any type_

```
new Map() creates the map
map.set(key, value)
map.get(key)
map.has(key)
map.delete(key)
map.clear()
map.size
```

## Sets

_For a collection of unique values_

```
new Set(iterable) – creates the set, optionally from an array of values (any iterable will do).
```

Methods: add, delete, has, clear

Also a size property.

## Primitives

When using object methods, primitives are wrapped in an object, the method is applied, and then the object is destroyed. This ensures primitives are lightweight, but we can still use object properties.
```js
let str = "Hello";

str.test = 5; // creates a test property in the temporary wrapper object

alert(str.test); // error because object was destroyed, and test doesn't exist anymore
```

## Data Types

**Numbers**
- 52 of 64 bits can be used to store digits
- Loss of precision: we can't easily express a fraction in binary. We could use `+(0.1.toFixed(2))`. And when large numbers need more than 52 bits, least significant digits are chopped off.

**Strings*
- Looped with `for..of`
- Common methods: `includes`, `indexOf`,

---

# Random Notes

### Garbage collection

JS uses mark-and-sweep. From roots and branch out, marks all refs. Everything unmarked is removed.

### Primitives vs. objects

The following pattern holds for `Boolean` and `Number`.
```
console.log(typeof 'foo'); // Logs "string"
console.log(typeof String('foo')); // Logs "string"
console.log(typeof new String('foo'));  // Logs "object"
```
