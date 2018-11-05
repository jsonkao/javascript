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



# Random Notes

**Garbage collection:** JS uses mark-and-sweep. From roots and branch out, marks all refs. Everything unmarked is removed.
