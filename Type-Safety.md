# Type-Safety at compile-time and runtime
Type-safety is a property of programming languages that determines how variables are handled by the language at compile-time and runtime. 
A variable's type can be either infered by the language at compile & runtime, or it can be strongly defined.
JavaScript has the ability to infer weakly-typed variables, but this can result in type errors when a function expects a certain type but receives a different type instead. TypeScript is a superset of JavaScript that checks for type-safety at compile-time and ensures that types are strongly-defined before building the code into JS files.

By default, *TypeScript does not verify types at runtime* in order to avoid runtime overhead, in order to aggressively optimize runtime performance as a part of its design goals. TypeScript does, however, verifies types are strongly defined at compile time, when TS compiles its files into JS files.

This behavior does not provide any kind of safety mechanism against incorrect or invalid inputs, such as inputs from unhandled API endpoints.

# Type Guards
In built type guards, such as ```instanceof``` and ```typeof``` can be used to check variable types at runtime, before performing an action. Here's an example of how ```instanceof``` can be used to check the type of an input, at runtime:

```
class ClassOne {
    methodOne() {
        return true;
    }
}

class ClassTwo {
    methodTwo() {
        return true;
    }
}

function testType(instance: ClassOne | ClassTwo) {
    instance.methodOne(); // compiler will throw error
    instance.methodTwo(); // compiler will throw error

    if (instance instanceof ClassOne) {
        instance.methodOne();
    } else {
        instance.methodTwo();
    }
}
```


# Type-safe APIs & JSON Schemas
JSON Schemas can be used to enforce types and communication conventions during data interchanges. Tools like Swagger or GraphQL leverage JSON-like schemas to can facilitate the creation of type-safe API interfaces, and they are renowned for their robust typing capabilities.

# Validation libraries
Runtime validation libraries, such as ```zod``` or ```io-ts```, achieve runtime validation of types and properties. These are particularly useful when handling user inputs.

# Branded types
[**Branded types**](https://typescript.tv/best-practices/improve-your-type-safety-with-branded-types/) can help catch programming errors early in the development process by preventing values that don't meet certain criteria from being passed into functions or used in certain contexts. They are a form of defensive coding in TypeScript and can ensure type validation at runtime.

Here's an example of how to create and use Branded Types:
```
// PositiveNumber is a regular type connected with a custom "brand"
type PositiveNumber = number & { __brand: 'PositiveNumber' };

// The "divide" function requires "b" to be of type "PositiveNumber"
function divide(a: number, b: PositiveNumber) {
  return a / b;
}

// The assertion function returns a type predicate (is PositiveNumber)
// It converts any input into the "PositiveNumber" type if it passes the check
function assertPositiveNumber(x: unknown): asserts x is PositiveNumber {
  if (typeof x === 'number' && x < 0) {
    throw new Error('Number is not greater zero');
  }
}

const x = 10;
// After passing this check, "x" becomes "PositiveNumber"
assertPositiveNumber(x);
// A variable of type "PositiveNumber" can now be used with "divide"
divide(100, x); // OK!
```
