---
layout: page-api
title: QUnit.equiv()
excerpt: A recursive comparison of JavaScript values.
groups:
  - extension
redirect_from:
  - "/extension/QUnit.equiv/"
version_added: "1.0.0"
---

`QUnit.equiv( a, b, ... )`

Compare two or more values for equality in a recursive, deep manner.

| name | description |
|------|-------------|
| `a` (any) | First value to compare |
| `b` (any) | Second value to compare |
| `...` (any) | Additional values to compare |

This function is the underlying engine for [`assert.deepEqual()`](../assert/deepEqual.md) and related assertions. It performs a strict, recursive comparison of all own and inherited properties.

When called with an even number of arguments, each pair is compared. For example, `QUnit.equiv(A, B, C, D)` compares C to D, then B to C, then A to B. Passing 0 or 1 argument is treated as a trivial pass (returns `true`).

## Supported types

### Primitive values

A strict equality check is used for strings, numbers, booleans, symbols, `null`, and `undefined`. NaN is treated as equal to NaN.

### Boxed primitives

Boxed primitives (e.g. `new Number(42)`) are unwrapped before comparison, so `QUnit.equiv(42, new Number(42))` returns `true`.

### Objects

For plain objects, the comparison is recursive: all own and inherited properties are compared using the same deep-equality logic. Object identity is disregarded — two objects with the same properties but different references are considered equal.

For objects with custom class types, the constructor is compared. Two objects with different constructors are not considered equal.

### Arrays

Elements are compared element by element. Arrays of different lengths are not equal.

### Date

Dates are compared by their internal timestamp (`valueOf()`).

### RegExp

Regular expressions are compared by their source string and flags (including the sticky `y` flag and unicode `u` flag).

### Set and Map

Set and Map instances are compared by their contents. Elements are matched order-independently — two Sets with the same elements in a different order are still considered equal.

### Functions

Functions are compared by reference (strict equality).

### Recursion

Circular references are handled safely. If the same pair of values is encountered again during comparison, the comparison short-circuits and returns `true` to avoid infinite recursion.

## Examples

```js
// Primitive values
QUnit.equiv(42, 42); // true
QUnit.equiv('hello', 'hello'); // true
QUnit.equiv(NaN, NaN); // true
QUnit.equiv(null, undefined); // false

// Objects
QUnit.equiv({ a: 1, b: 2 }, { a: 1, b: 2 }); // true
QUnit.equiv({ a: 1 }, { a: 1, b: 2 }); // false

// Arrays
QUnit.equiv([1, 2, 3], [1, 2, 3]); // true
QUnit.equiv([1, 2, 3], [1, 2, 3, 4]); // false

// Multiple arguments
QUnit.equiv(A, B, C, D); // Compares C-D, then B-C, then A-B
```

## Usage with assert.deepEqual

This method is used internally by [`assert.deepEqual()`](../assert/deepEqual.md) and related assertions. It is exposed on the `QUnit` namespace to allow plugins and custom assertions to reuse the same deep-comparison logic.

```js
QUnit.test('custom assertion example', function (assert) {
  const actual = { nested: { value: 42 } };
  const expected = { nested: { value: 42 } };

  // Uses QUnit.equiv internally
  assert.deepEqual(actual, expected);

  // Can also be called directly
  assert.true(QUnit.equiv(actual, expected));
});
```
