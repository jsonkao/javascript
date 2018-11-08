### Table of Contents

| [Advanced JavaScript](#advanced-javasciript) |
|-----------------|
| [Closures](#closures) |
| [`var`](#var) |
| [Scheduling](#scheduling) |

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

| [Data Structures](#data-structures) |
|-----------------|
| [Using Arrays](#using-arrays) |
| [Heaps](#heaps) |
| [Graphs](#graphs) |

| [Algorithms](#algorithms) |
|-----------------|
| [Sorting](#sorting) |
| [Rubin-Karp substring search](#rubin-karp-substring-search) |

[**Random Notes**](#random-notes)

---

# Advanced JavaScript

## Closures

### Lexical Environment

Every _running function_ (so each instance), code block, and script has an associated LE, which stores local variables and a reference to outer LE.

Variables are first searched in the current LE, then outer, outer, etc. LE's don't store old variable values, so when accessing outer values, we use most recent values.

**One call-one LE:** a new LE is created every time a function runs

A closure is function that has `[[Environment]]`, so it remembers its outer variables and can access them.

### `var`

`var` has no block scope, only function-wide or global, so they are visible through blocks (including for loop declarations).

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

# Data Structures

## Using Arrays

_For storing ordered collections_

For LinkedLists, Stacks, Queues, use native array methods:
- Add to the end of an Array: `let newLength = fruits.push('Orange')`
- Add to the front of an Array: `fruits.unshift('Apple')`
- Remove from the end of an Array: `let last = fruits.pop(); // remove Orange (from the end)`
- Remove from the front of an Array `let first = fruits.shift(); // remove Apple from the front`

More array methods:
- Remove n items by index position: `let removedItem(s) = fruits.splice(pos, n); // this is how to remove an item`

### Sets

`new Set([ ... ])` stores unique values.

## Graphs

A graph is a set of vertices and a set of edges. Implementations are:
1. each Vertex stores an adjacency list
2. a map of { Vertex: adjacency list }

### Topological sort

Some ordering of vertices in a graph where if there is a path v_i to v_j, v_i appears before v_j in the ordering.

1. We store an indegree on each vertex, which represents how many vertices point to it.
2. We maintain a queue of vertices that have indegree = 0.
3. For every vertex in the queue, decrement its neighbors' indegrees. Add neighbors

```
topsort() throws CycleFoundException {
  Queue<Vertex> q = new Queue<Vertex>( );
  int counter = 0;

  for each Vertex v
    if( v.indegree == 0 )
      q.enqueue( v );

  while( !q.isEmpty( ) ) {

    Vertex v = q.dequeue( ); 
    v.topNum = ++counter; // Assign next number

    for each Vertex w adjacent to v
      if( --w.indegree == 0 ) 
        q.enqueue( w );
  }
  if ( counter != NUM_VERTICES )
    throw new CycleFoundException( );
}
```

### Djiktra's algorithm

```
Starting at a vertex s,
for each Vertex v
	v.dist = Infinity
	v.known = false
s.dist = 0
while (there are unknown vertices) {
	Vertex v = the unknown vertex with the smallest distance
	v.known = true
	for each adjacent Vertex w to v:
		if (!w.known) {
			d = distance from w to v
			if (v.dist + d < w.dist)
				w.dist = v.dist + d
		}
}
```

## Heaps

Heaps are complete binary trees where the parent must always be (isMax ? > : <) than its children.
- **insert:** Add to end of binary tree, then percolate up
- **remove:** Put last element in root's spot, then percolate down. If max heap, switch with larger child. If min heap, switch with smaller child.

---

# Algorithms

## Sorting

**HeapSort:** Add elements of array into a max heap is `O(N)`. Swap last element of heap with deleteMax(), then last element down to a valid index (before deleteMax’s spot). We are adding the maxes from right to left, so we get an ascending array `O(NlogN)`. 

**Insertion sort:** For p from 1 to N-1, make sure array from to 0 to p is sorted. Basically depends on the p-1 case. So basically bubbling the newly absorbed element leftwards.

**Merge sort:** sort left, sort right, merge the two. Merging has three counters: left, right, current. Inc them as appropriate while comparing left and right to determine which one goes into current. It's a good idea to Insertion sort arrays with N ≤ 20.

**Quicksort:** pivot = median(first, middle, last element). partition with counters i from 0 and j from last - 1 (backwards). Move i forward until element ≥ pivot. Move j back until element ≤ pivot. Switch i and j. Return quicksort(left) + pivot + quicksort(right).

## Rubin-Karp: search substring of length `s` in string of length `b`

Hashes windows of length `s`. Exploits the fact that `two strings equal --> hashes are equal`. Strongly reduces string matching.

Problems come from collisions, which can be reduced with a rolling hash function, which computes a hash based on the hash of a previous value in constant time.

Our hash function treats a string in some base >= the number of possible letters, say 128.
```js
// String: 'doe '
let code = c => c.charCodeAt(0);

hash('doe') = code('d') * 128^2 + code('o') * 128 + code('e') * 1
hash('oe ') = (hash('doe') - code('d') * 128^2) * 128 + code(' ')
```

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
