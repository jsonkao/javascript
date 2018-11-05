### Table of Contents

| [JavaScript](#javascript) |
|-----------------|
| [Type Conversion](#type-conversion) |
| [Comparsion](#comparison) |
| [Symbols](#symbols) |
| [Functions](#functions) |
| [`this`](#this) |
| [Objects](#objects) |

| [Data Structures](#data-structures) |
|-----------------|
| [Using Arrays](#using-arrays) |

| [Algorithms](#algorithms) |
|-----------------|
| [Rubin-Karp substring search](#rubin-karp-substring-search) |

| [Problems](#problems) |
|-----------------|
| [Strings](#strings) |
| [Linked Lists](#linked-lists)

[**Random Notes**](#random-notes)

---

# JavaScript

## Type Conversion

**String():** null => “null”. Can never convert Symbols

**Number():** Automatic in mathematical expressions, except “+” (if any operands are strings, becomes string concat)
- undefined => NaN, null => 0, true/false, 
- string => whitespace removed. empty string => 0
- 1 / 0 = Infinity

**Boolean():** intuitively empty => 0, an empty string, null, undefined and NaN

## Comparison

**String comparison:** compared in dictionary order (letter by letter) _by Unicode value_
**When different types,** converted to Number (including “==”)
=> 0 == false because Number(false) = 0. “===” does not type convert

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

- Object keys are Symbols or strings, or coerced into strings

_Left off at http://javascript.info/object-toprimitive_

---

# Data Structures

## Using Arrays

For LinkedLists, Stacks, Queues, use native array methods:
- Add to the end of an Array: `let newLength = fruits.push('Orange')`
- Remove from the end of an Array: `let last = fruits.pop(); // remove Orange (from the end)`
- Remove from the front of an Array `let first = fruits.shift(); // remove Apple from the front`

More array methods:
- Remove n items by index position: `let removedItem(s) = fruits.splice(pos, n); // this is how to remove an item`

### Sets

`new Set([ ... ])` stores unique values.

---

# Algorithms

## Rubin-Karp: search substring of length `s` in string of length `b`

Hashes windows of length `s`. Exploits the fact that `two strings equal --> hashes are equal`. Strongly reduces string matching.

Problems come from collisions, which can be reduced with a rolling hash function, which computes a hash based on the hash of a previous value in constant time.

Our hash function treats a string in some base >= the number of possible letters, say 128.
```js
// String: 'doe '
let code = c => c.charCodeAt(0);

hash('doe') = code('d') * 128^2 + code('o;) * 128 + code('e') * 1
hash('oe ') = (hash('doe') - code('d') * 128^2) * 128 + code(' ')
```

---

# Problems

## Strings

### Check if s2 is a rotation of s1 (e.g. 'waterbottle' and 'erbottlewat') with one call to `isSubstring`

`isSubstring(s2, s1 + s1)`

## Linked Lists

### Check for intersection in singly LL's

1. Two pointers that run through their entire list. If tails are not equal, no intersection.
2. Now that we have lengths, move pointers back to list starts, and advance the longer list's pointer by the difference in lengths
3. Traverse together until they equal

### Return start of a circular loop

Is there a circular loop? Use Fast (2 steps)/Slow (1 step). If a circular loop exists, they must collide.

If there is a circular loop, start Fast/Slow at beginning of the LL.
1. After k steps, when Slow enters the loop, Fast has travelled 2k, so it is k into the loop. So Fast is N - k behind Slow, where N = size of the loop.
2. Each Slow step, Fast gets 1 step closer to slow. So they collide after N - k steps, and Slow is N - k nodes into the loop. 
3. Both Slow and the start of the LL are k nodes from the loop. So move Fast to the start of the loop and step both pointers one node at a time, until they collide.

Implementation:
1. Have a Fast and Slow.
2. When they collide, move Fast to LLHead.
3. Move Slow and Fast at a rate of 1 step until they collide. Return the new collision point.

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
