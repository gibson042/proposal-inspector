# Proposal: Inspector

Champions:
* @JakobJingleheimer

Authors:
* @JakobJingleheimer

## [Stage](https://tc39.github.io/process-document/)

**Current**: 1 (30 May 2025)

## The Problem

Detailed inspection information in a human-friendly way, such as when comparing (diffing) values.

In the context of [Comparisons](https://github.com/JakobJingleheimer/proposal-modes), merely knowing `A` is unexpected is almost useless when you can't see what `A` and `B` are.

<details>
<summary>examples (current native capability)</summary>

Annoying:
```js
if (A !== B) throw new Error('A does not equal B');
// Error: A does not equal B
```

Better
```js
if (A !== B) throw new Error(`${A} does not equal ${B}`);
// Error: 1 does not equal 2
```

But brittle
```js
if (A !== B) throw new Error(`${A} does not equal ${B}`);
// Error: [object Object] does not equal [object Object]
```
</details>

<details>
<summary>examples (the general desire)</summary>

```js
const result = compare(A, B);

{
  pretty: `{
    …
  ✓     qux: 'a',
  ✗     qux: 'b',
    …
  }`,
  paths: ['foo.bar.qux'],
  expected: 'a',
  actual: 'b',
}
```
</details>

### Reasons to provide natively:

* Very expensive for userland to compose (the vast majority of test runner execution time)
  * Pretty printing
* Special access (requires [Modes](https://github.com/JakobJingleheimer/proposal-modes) - will not be available in "production"):
  * Proxies
  * Constituents of expressions (compiler)
  * Constituents of structure (diff)
  * Internal slots
* Broadly applicable: can be used by more than [Comparisons]() (e.g. Pattern Matching could leverage this; it could be used entirely on its own).

## Ecosystem today

Currently, there are several libraries in userland that provide this functionality in varying ways.

Runtimes (can offer special access):

* [Bun `inspect`](https://bun.sh/docs/api/utils#bun-inspect)
  * Does not offer special access
* [Node.js `util.inspect`](https://nodejs.org/api/util.html#utilinspectobject-options) (also used by Deno)
  * Offers special access (ex `showProxy`)

Libraries (cannot offer special access):

* [chai assertion-error](https://github.com/chaijs/assertion-error)
* [jest-diff](https://github.com/jestjs/jest/tree/main/packages/jest-diff)
* [object-inspect](https://github.com/inspect-js/object-inspect)

## Prior to stage 2

* Consider customisation/extensibility (public symbols?)
  * This is potentially especially important for frameworks that may want to utilise Inspector under the hood and want to tweak only a very small piece.
* Determine what information to provide, how, and under what cicumstances.
* [Modes](https://github.com/JakobJingleheimer/proposal-modes) as a requirement to offer "special access".
