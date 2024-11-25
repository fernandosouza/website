---
author: ["Fernando Souza"]
title: "Understanding Typescript Unknown Type"
# If I put this date in the future, the blog post does not show up.
date: "2024-11-24"
description: ""
summary: ""
tags: ["ypescript", "uknown type", "type narrowing"]
categories: ["ypescript"]
series: ["Typescript Guide"]
ShowToc: true
TocOpen: true
---

## Why do these types exist in TypeScript?

The answer lies in the need for flexibility when dealing with uncertain or mixed types.

The `any` type provides complete freedom, allowing developers to bypass TypeScript’s type-checking system. This freedom comes with a trade-off: the responsibility falls entirely on the developer to ensure the code is error-free.

In contrast, the `unknown` type introduces flexibility with safety. It forces developers to explicitly check and narrow the type before using the variable, ensuring that the code handles all cases appropriately. By doing so, `unknown` guides developers toward writing more robust and predictable code.

Lets take the use of the `fetch()` function as an example and see how we can handle different possible exceptions from it.

```ts
async function callAPI(abort: AbortController) {
  try {
    await fetch("https://example.com", {
      signal: abort.signal,
    });
  } catch (error: AbortSignal | TypeError) {
    // ...
  }
}

async function callAPI(abort: AbortController) {
  try {
    await fetch("https://example.com", {
      signal: abort.signal,
    });
  } catch (error: unknown) {
    // ...
  }
}
```

Instead of enumerating all the possible error types that might be caught in a catch block, we can simply declare the error parameter as `unknown`.

In the example with the `unknown` type, attempting to access any property of the error `parameter` will prompt TypeScript to require a type guard. Without it, the code will result in a type-checking error. This behavior is not enforced when the error `argument` is typed as any.

```ts
async function callAPI(abort: AbortController) {
  try {
    await fetch("https://example.com", {
      signal: abort.signal,
    });
  } catch (error: unknown) {
    error.aborted; // TypeScript will thrown an type-checking eror as aborted might not always be available.
  }
}

async function callAPI(abort: AbortController) {
  try {
    await fetch("https://example.com", {
      signal: abort.signal,
    });
  } catch (error: any) {
    error.aborted; // TypeScript won't care if the error does not contain the aborted property.
  }
}
```

## The any type

Defining a parameter as `any` means stating that it can be of any type, effectively disabling TypeScript's type-checking. This makes the code vulnerable, as there are no guarantees about the value's structure or behavior. While manual checks can still be implemented, using any removes the automatic safeguards provided by the type system, increasing the risk of errors when processing or accessing the value.

## The unknown type

`unknown` is a type that requires the developer to explicitly validate the type of a value before using it (learn more about type guards). This ensures greater safety and prevents errors when accessing properties or methods of values with an unknown type.

## Conclusion

The difference between any and unknown is key to writing safer TypeScript code. While any provides flexibility by bypassing type checks, it sacrifices safety. In contrast, unknown ensures type validation, promoting reliability when working with uncertain data like API responses or errors.

By requiring explicit checks, unknown balances flexibility with safety, helping prevent runtime issues. Use unknown to maintain strong type safety and reserve any for rare cases where unchecked flexibility is truly necessary.
