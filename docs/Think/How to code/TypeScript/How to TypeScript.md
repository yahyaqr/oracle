# TypeScript

TypeScript is a typed superset of JavaScript that combines the flexibility and general syntax of JavaScript with a type checker and syntax. It’s one of the fastest-growing and most popular languages on the web today.

Code written in TypeScript compiles to regular JavaScript. Anything you write in TypeScript can be run wherever it could be in JavaScript: web browsers, Node servers, and more.

# About TypeScript

[TypeScript](https://www.typescriptlang.org/) is a typed language that checks to see if JavaScript variables are of the correct data type before the code is run. It achieves this by using four tools that build on each other in the following order:

1. A language specification that describes JavaScript syntax as well as new TypeScript-specific syntax
2. A compiler program that takes in TypeScript (and therefore also JavaScript) syntax and converts it to the equivalent JavaScript
3. A type checker program that reads in TypeScript syntax, understands the types of values every entity is meant to be and notices any “type errors” (mismatches)
4. A language server that editors such as VS Code can run to provide build-time hints and refactoring tools

## History

TypeScript was released in October 2012 by Microsoft after two years of development. [Anders Hejlsberg](https://twitter.com/ahejlsberg), the original designer of C#, was the lead designer for the language.

## Installation

To download the latest stable build via npm, use the following command:

```sh
npm install -g typescript
```

To check the installed version, use the following command:

```sh
tsc -v
```

TypeScript was developed by Microsoft and is [available in VSCode](https://code.visualstudio.com/docs/typescript/typescript-tutorial) by default.

## Playground

There’s an interactive playground provided by the TypeScript team at [https://www.typescriptlang.org/play](https://www.typescriptlang.org/play).

It allows you to type in JavaScript or TypeScript code on the left and see the resultant JavaScript output or any TypeScript type errors.

# Arrays

In TypeScript, arrays are considered to be collections of single, “generic” types of values. All elements must be of the same type of data as prescribed in the array definition.

## Defining an array

Array types can be inferred during the initialization of a new array.

In this example, the `vowels` array is inferred to consist of elements of type `string`:

```ts
const vowels = ['a', 'e', 'i', 'o', 'u'];

vowels.push('y'); // Ok

vowels.push(7);
// Error: Argument of type 'number' is not assignable to parameter of type 'string'.
```

Arrays can also be defined with the “generic” type of its elements already preset in two ways:

```ts
const vowels: string[] = ['a', 'e', 'i', 'o', 'u'];

// alternate way using Array class
const altVowels: Array<string> = ['a', 'e', 'i', 'o', 'u'];
```

More than one type can be prescribed in the array definition with the “or” `|` operator:

```ts
const numbers: (string | number)[] = [1, '2', 3, 'four'];

// Alternative syntax
// const numbers: [string, number] = [1, "2", 3, "four"];
```

As long as each element in `numbers` is of type `string` or `number`, it is valid.

## Array Types

An array type consists of the type of values inside the array followed by square brackets `[]`.

Arrays without any initial contents may be declared as that type to tell TypeScript what will later go into them.

In this example, `dates` doesn’t initially include a value, so declaring it as `Date[]` tells TypeScript what’s allowed in it:

```ts
const dates: Date[] = [];

dates.push(new Date('2021-12-1994')); // Ok

dates.push(10241995);
// Error: Argument of type 'number' is not assignable to parameter of type 'Date'.
```

# Assignability

**Assignability** is how TypeScript can determine whether a value of a particular data type can be assigned to a variable of the same (or another) data type.

## When/Where Assignability Takes Place

TypeScript performs assignability checks whenever a value is being assigned into another location in the computer’s memory, such as:

- Assigning values to variables.
- Passing arguments to functions.

## Example: Assigning Matching Data Types

In this snippet of code, TypeScript sees that the `spooky` variable is initially assigned a boolean value, so it believes the `spooky` variable should always be of type `boolean`.

Assigning a variable of type boolean later on is allowed, as a type is assignable to itself, but assigning any other type, such as a `string`, is not allowed:

```ts
let spooky = true;

spooky = false; // Allowed

spooky = 'skeletons'; // Not allowed
```

Running the code above will cause the following error:

```sh
Error: Type 'string' is not assignable to type 'boolean'.
```

## Examples: Comparing Object Types

When comparing object types, TypeScript will ensure that all the required fields exist in the assigning object type. It will also ensure that all field that do exist on the types match up.

In the following example, we define an `interface` object called `Skeleton`, which will serve as a blueprint for the following:

- The names of `Skeleton` properties.
- The corresponding `: type` definitions.

the first `receiveSkeleton()` call works because its object argument contains both required properties -`spooky` and `scary`- and they are of the correct types.

```ts
interface Skeleton {
  spooky: boolean;
  scary: boolean;
}

function receiveSkeleton(skeleton: Skeleton) {
  console.log(skeleton.spooky ? 'Spooky ' : 'Not spooky...');
  console.log(skeleton.scary ? 'scary!' : 'Not scary...');
}

receiveSkeleton({ spooky: true, scary: false }); // Ok
```
The second example throws an `Error` because the `spooky` property is of type `string` instead of `boolean`:

```ts
receiveSkeleton({
  spooky: 'Very!',
  scary: true,
});
```

The error will look like this:

```sh
Error: Type 'string' is not assignable to type 'boolean'
```

This third and final example throws an `Error` because the object we passed to `receiveSkeleton` was missing a `scary` property.

```ts
receiveSkeleton({
  spooky: false,
});
```

The following error will occur, per the TypeScript checker:

```sh
Error: Argument of type '{ spooky: false; }'
is not assignable to parameter of type 'Skeleton'.
Property 'scary' is missing in type '{ spooky: false; }'
but required in type 'Skeleton'.
```

# Classes

With TypeScript, **`class`** objects can use type annotation to describe the relationship between class members and their static types. Classes are a core part of the JavaScript language that TypeScript respects. In addition to type annotations, TypeScript adds a few new syntaxes to help write classes in a type-safe manner.

## Members

Class member types work similarly to parameter and variable types.

- If an initial value exists, the member’s type is inferred to be that type.
- If no initial value exists, the member is implicitly of type `any` unless a type annotation exists on its name.

In the following `Box` class, `count` is inferred to be of type `number` because its initial value is `0`, while `value` is explicitly declared as type `string`.

```ts
class Box {
  count = 0;
  value: string;
  constructor(value: string) {
    this.value = value;
  }
}
```

Therefore, asking for the `count` of a `Box` instance is of type `number`, and the `value` is of type `string`:

```ts
const myBox = new Box('stuff');
myBox.count; // Type: number
myBox.value; // Type: string
```

## Member Visibility

JavaScript has a `#` syntax to mark a member as private, meaning it can only be accessed inside its class:

```ts
class Secret {
  #value: string;

  constructor(value: string) {
    this.#value = value;
  }

  getValue() {
    return this.#value;
  }
}

const mySecret = new Secret('shhh');

mySecret.getValue(); // Ok

mySecret.#value;

/* 
Error: Property '#value' is not accessible outside
class 'Secret' because it has a private identifier.
*/
```

## Public, Private, or Protected

Separately, TypeScript also supports adding `public`, `protected`, or `private` in front of a class member to indicate whether the member may be used outside that class.

- `public` (default): anybody may access that member, anywhere.
- `protected`: only the class or its derived classes may access that member.
- `private`: only the class itself may access that member.

The key difference between JavaScript’s `#` syntax and TypeScript’s privacy keywords are that:

- JavaScript’s `#` syntax changes the name of the member.
- TypeScript’s privacy keywords exist only in the type system; the member’s name is not changed.

In this example, the `Base` class sets up five variables:

- The first two, `first` and `second`, are publicly accessible.
- The third, `third`, is protected and only accessible in the class or a derived class.
- The fourth and fifth, `fourth` and `fifth`, are set to private with `private` and `#`, respectively.

```ts
class Base {
  // These two are functionally equivalent
  first = '';
  public second = '';

  protected third = '';

  private fourth = '';
  #fifth = '';
}
```

The `Derived` class uses the `extends` keyword to become a child of the `Base` class. In the example below, a `.test()` method is used to borrow from members of the parent `Base` class:

```ts
class Derived extends Base {
  test() {
    this.first; // Ok: public
    this.second; // Ok: public

    this.third; // Ok: protected

    this.fourth;
    /*
    Error: Property 'fourth' is private
    and only accessible within class 'Base'.
    */

    this.fifth;
    /*
    Error: Property '#fifth' is not accessible outside
    class 'Base' because it has a private identifier.
    */
  }
}
```

The members `first`, `second`, and even the protected `third`, are all accessible within the `Derived` class definition. With `fourth` and `fifth`, however, they are both private members of `Base`. Therefore, they are not accessible inside of `Derived`.

The example below shows how some members of `Dervied` can be invoked through an instance variable, `derived`, while others are not as accessible. While `third` can be used within the actual `Derived` class, as a protected member, it cannot be used outside of the class with an instance variable.

```ts
const derived = new Derived();

derived.first; // Ok: public
derived.second; // Ok: public

derived.third;
/*
Error: Property 'third' is protected
and only accessible within class 'Base'.
*/

derived.fourth;
/*
Error: Property 'fourth' is private
and only accessible within class 'Base'.
*/

derived.fifth;
/*
Error: Property '#fifth' is not accessible outside
class 'Base' because it has a private identifier.
*/
```

## Implementing Interfaces

Classes can use the TypeScript `interface` keyword to further define the “shape” of the class object. This means that instances of the class are assignable to a given interface type.

To use interfaces with classes, we use the `implements` keyword between the class name and name of the interface followed by the opening curly bracket `{`.

In this example, the `Speaker` interface has a single `speak()` method, so the `SpeechGiver` class may be marked as implementing it:

```ts
interface Speaker {
  speak(): string;
}

class SpeechGiver implements Speaker {
  constructor(speech: string) {
    this.speech = speech;
  }

  increaseEmphasis() {
    this.speech += '!';
  }

  speak() {
    return this.speech;
  }
}
```

Classes can implement more than one interface, as well, by a comma-separated (`,`) list of any number of interfaces.

Marking a class as implementing an interface doesn’t change anything about the class itself; it won’t change the types of any members or implicitly add them to the class. It’s purely a way to make sure the class implements the interface properly where it’s declared, rather than when instances of the class happen to not match up with an interface.

# Functions

With **functions**, TypeScript infers the types of its parameters as well as any return value.

## Function Parameters

The types of function parameters work similarly to variable declarations. If the parameter has a default value, it will take on the type of that value. Otherwise, we may declare the type of that parameter by adding a type annotation after its name.

Here, the `logAgeAndName()` function explicitly declares `age` to be of type `number`, while `name` is inferred to be of type `string` from its default value:

```ts
function logAgeAndName(age: number, name = '(anonymous)') {
  console.log(`${name}, age ${age}`);
}

logAgeAndName(16, 'Mulan'); // ok: Mulan, age 16

logAgeAndName(0); // also ok: (anonymous), age 0

logAgeAndName('Mulan', 16);
// Argument of type 'string' is not assignable to parameter of type 'number'
```

The last use of `logAgeAndName()` shows that the order of the parameters passed to the function matter. The first argument is expected to be of type `number` while the second is inferred to be of type `string`.

### Optional Parameters

Function parameters can be made optional by adding a `?` question mark after their name, before any type annotation. TypeScript will understand that they don’t need to be provided when the function is invoked. Additionally, their type is a union that includes `undefined`. This means that if a given function does not use the optional parameter, its value is set to `undefined`.

The following `logFavoriteNumberAndReason()` function indicates `favorite` as a required `number` and `reason` as an optional `string`, so the function must be called with at least one `number` parameter:

```ts
function logFavoriteNumberAndReason(favorite: number, reason?: string) {
  console.log(`Favorite: ${favorite}!`);

  if (reason) {
    console.log(`Because: ${reason}!`);
  }
}

logFavoriteNumberAndReason(7, 'an esoteric video game reference'); // Ok
logFavoriteNumberAndReason(9001); // Ok

logFavoriteNumberAndReason();
// Error: Expected 1-2 arguments, but got 0.
```

## Return Types

Most functions are written in a way that TypeScript can infer what value they return by looking at all the `return` statements in the function. Functions that don’t return a value are considered to have a return type of `void`.

In this example, the `getRandomFriend()` function is inferred to return a `string` type because all `return` statements have a value of type `string`, including the `fallback` parameter:

```ts
function getRandomFriend(fallback: string) {
  switch (Math.floor(Math.random() * 5)) {
    case 0:
      return 'Josh';
    case 1:
      return 'Sara';
    case 2:
      return 'Sonny';
    default:
      return fallback;
  }
}

const friend = getRandomFriend('Codey'); // Type: string
```

The return type of a function can be declared by adding a type annotation after the `)` right parenthesis following its list of parameters. Doing so can be useful in two situations:

- We can make sure the function returns that type.
- TypeScript will not attempt to infer the return type of a recursive function.

The following recursive `fibonacci()` function needs an explicit `: number` return type annotation for TypeScript to understand it. This returns type `number`:

```ts
function fibonacci(i: number): number {
  if (i <= 1) {
    return i;
  }

  return fibonacci(i - 1) + fibonacci(i - 2);
}
```

## Function Types

Function types may be represented in the type system. They look a lot like an arrow lambda, but with the return type instead of the function body.

This `withIncrementedValue()` takes in a `receiveNewValue` parameter function that itself takes in a `number` and returns nothing (`void`).

```ts
let value = 0;

function withIncrementedValue(receiveNewValue: (newValue: number) => void) {
  value += 1;
  receiveNewValue(value);
}

withIncrementedValue((receivedValue) => {
  console.log('Got', receivedValue);
});
```

Function parameter types may be inferred if their parent function is in a location with a known function type. In the prior example, the `receivedValue` parameter was inferred to be type `number`.

# Generics

In TypeScript, **generics** are used to assign multiple types to a function or variable without the value losing that specific type information upon return. The `any` keyword is similar in that it accommodates any and all types. However, it will not retain specific type information.

## Syntax

Generics are defined with `<` `>` brackets surrounding name(s) of the generic type(s), like `Array<T>` or `Map<Key, Value>`.

```ts
interface MyType<GenericValue> {
  value: GenericValue;
}

let myVar: MyType<string> = { value: 'Hello, World!' };
```

Generic types can be used with the following:

- [Classes](https://www.codecademy.com/resources/docs/typescript/classes)
- [Functions](https://www.codecademy.com/resources/docs/typescript/functions)
- [Interfaces](https://www.codecademy.com/resources/docs/typescript/interfaces)
- [Type aliases](https://www.codecademy.com/resources/docs/typescript/type-aliases)

## Example of Using Generics

In the following snippet, `Box<Value>` is a generic `interface` that with a generic `Value` type within. Next, two variables, `numberBox` and `stringBox`, are explicitly declared to be of type `Box<number>` and `Box<string>`, respectively:

```ts
interface Box<Value> {
  value: Value;
}

let numberBox: Box<number> = { value: 7 };
let stringBox: Box<string> = { value: 'Marathon' };
```
## Inferring Generic Types

Generics can be inferred from usage when not explicitly provided.

Here, `logAndReturnValue<Value>` is inferred first to have a generic `Value` of type `Date`:

```ts
function logAndReturnValue<Value>(value: Value): Value {

  console.log(value);
  return value;
}

const result = logAndReturnValue(new Date('2021-12-21')); // Type: Date
```

Here, the `KeyValueStore` class instance is inferred to have generic `<string, number>`, and its `makePair()`, therefore, returns type `[string, number]`:

```ts
class KeyValueStore<Key, Value> {
  #key: Key;
  #value: Value;

  constructor(key: Key, value: Value) {
    this.#key = key;
    this.#value = value;
  }

  makePair(): [Key, Value] {
    return [this.#key, this.#value];
  }
}

const store = new KeyValueStore('year', 2794);
const pair = store.makePair(); // Type: [string, number]
```

# Interfaces

Interfaces are used to “shape” an object by describing a certain set of members and/or type annotations.

## Syntax

Interfaces may be declared by:

1. Starting with the `interface` keyword.
2. Giving the interface a name.
3. Creating an object that contains a set of members and/or type annotations.

```ts
interface myType {
  memberOne: string;
  memberTwo: number;
};

let myVar: myType = {"My favorite number is ", 42 };
```

## `Dog` Interface Example

In this example, the `Dog` interface declares `fluffy` and `woof` members. Any value declared to be of type `Dog` is therefore known to have those members:

```ts
interface Dog {
  fluffy: boolean;
  woof(): string;
}

function interactWithDog(dog: Dog) {
  dog.woof();
  
  if (dog.fluffy) {
    console.log('What a floof!');
  }

  dog.bark();
  // Error: Property 'bark' does not exist on type 'Dog'.
}
```

Members that do not exist in the interface, such as `bark()`, cannot be accessed and will throw a type error

## Optional Members

Here, the `Pet` interface uses `?` to set `name` as an optional member. The only member that is required is `species`. Declaring an object of type `Pet` doesn’t need a `name` but does need a `species`:

```ts
interface Pet {
  name?: string;
  species: string;
}

let anonymous: Pet = { // Ok
  species: "Dog";
};

let named: Pet = {
  name: "Emerald",
  species: "Budgie",
};

let invalid: Pet = {
  name: "My Rock",
}
// Error: Property 'species' is missing in type
// '{ name: string; }' but required in type 'Pet'.
```

## Interface Extensions

Interfaces may be marked as _extending_ another interface. Doing so indicates that the derived child interface (the interface extending others) includes all members from the base parent interfaces (the interface being extended).

To mark an interface as extending other(s), add the `extends` keyword after its name followed by any number of interfaces to extend, with `,` commas between interface names.

In this example, the `Cat` interface is given a `.walk()` method by being an extension of the `Animal` interface. However, instance of type `Animal` don’t have access to members and methods defined in the `Cat` interface:

```ts
interface Animal {
  walk(): void;
}

interface Cat extends Animal {
  fluffy: boolean;
  purr(): string;
}

function workWithAnimals(animal: Animal, cat: Cat) {
  animal.walk(); // Ok: defined on Animal
  cat.walk(); // Ok: Cat extends Animal

  if (cat.fluffy) {
    // Ok: defined on Cat
    console.log('Floof!!');
    cat.purr(); // Ok: defined on Cat
  }

  animal.purr();
  // Error: Property 'purr' does not exist on type 'Animal'.
}
```

# Narrowing

**Narrowing** is a TypeScript process that refines a value of multiple types into a single, specific type.

[Union types](https://www.codecademy.com/resources/docs/typescript/unions) represent entities that may be one of two or more different types. Because they only allow access to members known to be on all possible types, we need to narrow down a value to a more specific type before using non-shared members.

## Where Narrowing Happens

TypeScript recognizes types as being narrowed when it sees branches of JavaScript control-flow logic, including:

- Conditional statements like `if/else`, ternaries, and switches.
- Looping statements, such as `for`- and `while`-loops.
- Truthiness checks for entities that don’t point to `false`-like values.

If an area of code can only be reached if a value is of a particular type, TypeScript will infer the value to be of that type in that control flow branch.

## Example: Narrowing with `if` Statement

In this example, our `logValueIfExists()` function accepts a `value` parameter with a union-type of `string | undefined`. If we want TypeScript to explicitly narrow our `value` down to a `string` type, we can use an `if` statement to act as a “type guard”.

```ts
function logValueIfExists(value: string | undefined) {
  if (typeof value === 'string') {
    console.log(`Yay! ${value}!`);
  }
}

logValueIfExists('narrowing');
// output: Yay! narrowing!
```

We invoked `logValueIfExists()` with a string variable, “narrowing”. Since the `value` is of type `string`, the code in the `if` statement will run and a new string will be logged to the console.

## Example: Narrowing With Truthiness

In the example below, we changed the `if` statement slightly to check if `value` has any value that is “truthy” and doesn’t evaluate to a falsey value such as `0`, `false`, or `undefined`.

```ts
function logValueIfExists(value: string | undefined) {
  value; // Type: string | undefined

  if (value) {
    console.log(`Yay! ${value}!`);
    value; // Type: string
    value.length; // Type: number
  }

  value.length;
  // Error: Object is possibly 'undefined'.
}

logValueIfExists('truthiness');
// output: Yay! truthiness!

let newValue;
logValueIfExists(newValue);
// newValue is "undefined"; nothing is logged.
```

Outside of the `if` statement, `value` is a union type (`string | undefined`) and is not narrowed, meaning that calling `value.length` won’t work. Inside the `if` statement, because `value` was narrowed to a `string` type, it now has a `length` property.

# Primitives

TypeScript supports primitives, which are the basic types in JavaScript that make up data other than functions and objects. TypeScript recognizes the following seven primitive types in JavaScript:

- `bigint`
- `boolean`
- `null`
- `number`
- `string`
- `symbol`
- `undefined`

If a value such as a parameter or variable is declared to be one of these, it is allowed to be assigned any of the literal values that are of that primitive type.

In this example, the variable `decoration` is initially declared to be type `string`. This means that `decoration` is allowed to be assigned, and reassigned, a plethora of string values:

```ts
let decoration: string;

decoration = 'Cobweb';
decoration = "Jack O'Lantern";
decoration = 'Lights';
```

In other words:

- A primitive is a basic type of data, such as `number` or `string`
- A literal is an example of a primitive with a value, such as `1337` or `"ghost"`

# Promises

In TypeScript, **promises** handle asynchronous operations, providing better control over the flow of code.

## Creating Promises

To create a new promise, use the `new` keyword followed by `Promise`. The `Promise` constructor accepts a function which should take two parameters:

- A function to resolve the promise.
- A function to reject the promise.

```ts
const myPromise = new Promise((resolve, reject) => {
  // Asynchronous code here
});
```

### Return Types

In TypeScript, add a type annotation to a `Promise` to indicate the type of value it resolves to. If not defined, it defaults to `any`. For example, to indicate a type of `string`:

```ts
const myPromise: Promise<string> = new Promise((resolve, reject) => {
  // This Promise resolves to a string
});
```

Here, `myPromise` is a promise that resolves to a string. If a different type is used to resolve the promise, an error will occur.

> **Note**: The type annotation for promises is used for the value the promise is resolved to, not the value it is rejected with. All rejected promises should be rejected with an `Error` to ensure consistency.

## Handling Promises

Once a promise is declared, use the `.then()` and `.catch()` methods to handle the success or failure of the asynchronous operation. The `.then()` method is called when the `Promise` is resolved, while the `.catch()` method is called when it is rejected.

```ts
myPromise
  .then((value) => {
    console.log('Promise resolved with value: ' + value);
  })
  .catch((error) => {
    console.error('Promise rejected with error: ' + error);
  });
```

## `async`/`await`

TypeScript also supports the `async`/`await` syntax, which is a more readable and an alternative way to handle promises.

```ts
async function myAsyncFunction() {
  try {
    const value = await myPromise;
    console.log('Promise resolved with value: ' + value);
  } catch (error) {
    console.error('Promise rejected with error: ' + error);
  }
}
```

The `async` keyword indicates that a function returns a `Promise`, and the `await` keyword is used to wait for the promise to be resolved. The `try`/`catch` block is used to handle rejected promises.

# Tuples

In TypeScript, tuple types are defined as array-like containers with a fixed sized and the types of each member element already set.

They have two distinct features:

- They know their size as well as exactly how many elements it contains.
- They already know the data type of each element.

## Syntax

Tuples can have a mix of different data types and look like an array consisting of types as values inside:

```ts
type myTuple: [string, number, boolean] = ["1", 2, true];
```

They consist of a left square bracket `[`, comma-separated list of types, and a right square bracket `]`. The literal values, “1”, 2, and `true`, must map to each element’s types, `string`, `number`, and `boolean`, in the correct order.

## Destructuring a Tuple

Like arrays and objects, tuples can be deconstructed into new variables that represents each element and their type. For example, here is a variable, `popStar`, with a given tuple type. One the other line, the `popStar` is destructured into four separate variables through its tuple type:

```ts
const popStar: [string, number, string, boolean] = [
  'Lil Nas X',
  22,
  'Old Town Road',
  true,
];

const [popStarName, age, popSingle, stillActive] = popStar;

console.log(`${popStarName} is ${age} years old.`);

console.log(
  `They are ${
    stillActive
      ? 'still active in the industry'
      : 'no longer active in the industry'
  }.`
);

console.log(`One of their most famous songs is ${popSingle}.`);
```

Destructuring tuple types allows to be able to better interpolate their values and the output will be:

```
Lil Nas X is 22 years old.
They are still active in the industry.
One of their most famous songs is Old Town Road
```

## Storing Returned Values in Tuples

Tuples are particularly useful for representing multiple return values from functions, or groups of values that are meant to be used together.

In this example, the `positiveDifference()` function is declared as returning an array consisting of a `string` and a `boolean` function:

```ts
function positiveDifference(left: number, right: number): [number, boolean] {
  return left > right ? [left - right, true] : [right - left, false];
}

const [difference, isPositive] = positiveDifference(7, 49);

difference; // Type: number, Value: 42
isPositive; // Type: boolean, Value: false
```

# Type Aliases

In TypeScript, **type aliases** create type definitions that can be reused throughout the code. This is unlike type unions and intersections, where the explicit type information is used repetitively.

## Syntax

Type aliases require the `type` keyword and a name. They can be created in two ways.

- They can be set as an object that describes the members and their respective types.

```ts
type myType = {

  memberOne: string;

  memberTwo: number;

}

let favoriteNum: myType = {"my favorite number is ", 42}

- They can also refer to other known types, like a union type.

type myType = string | number;

let favoriteNum: myType = '42';
```

Later on, whenever the name is used, TypeScript will assume this refers to the aliased value. Type aliases are similar to `interface`s in terms of syntax and behavior. However, a key difference is that an `interface` can have members added later on whereas type aliases can’t be changed after being defined.

## Type Aliases with Type Guards

In this example, the `StringsIsh` type alias can be used in place of the type union with several members. When applied to the `logAllStrings()` function:

```ts
type StringsIsh = string | string[] | null | undefined;

function logAllStrings(values: StringsIsh) {
  if (values === null || typeof values === 'undefined') return;

  if (typeof values === 'string') {
    console.log(values);
  } else {
    values.forEach(logAllStrings);
  }
}

logAllStrings('hello'); // Ok
logAllStrings(['hello', 'world']); // Also Ok
```

The first type guard checks to see if `values` matches either `null` or `undefined` in the `StringIsh` type alias. Next, the `if/else`-statement checks for a base case of `values` being of type `string`. Otherwise, it recursively invokes `logAllStrings` again.

## Type Aliases as Generics

Type aliases may be generic and may contain any type description, including:

- Primitives
- Literals
- Object types

The following `Result<T>` type may contain a value, `T`, or an object containing the value, `{ value: T }`:

```ts
type Result<T> = T | { value: T };

let value: Result<string> = 'hello'; // Ok

let other: Result<string> = { value: 'world' }; // Also Ok
```

# Type Annotations

TypeScript uses type annotation to explicitly type things such as variables, functions, and classes.

For the most part, TypeScript does a good job at inferring types from the codebase at build-time. However, some constructs in TypeScript, such as variables without an initial value or members of interfaces, don’t have an initially inferable type. This is where type annotations can be really useful.

## Syntax

Type annotations consist of a `:` colon followed by the name of a type. They exist after the name of the construct they’re declaring the type for.

```ts
// Class and interface members

class myClass {
  memberOne: string;
  memberTwo: string;
}

interface myInterface {
  memberOne: string;
  memberTwo: number;
  memberThree: boolean;
}

// Function Parameters

function myFunc(memberOne: string, memberTwo: string) {
  ...
}

// Function Returns
function myFunc(memberOne, memberTwo): string {
  return memberOne + memberTwo;
}

// Variables
let myVar: string = "Hello, World!";
```

## Type Annotations and `Date`s

In the following code snippet, the `date` variable doesn’t have an initial value, but with a `Date` type annotation. Later on, when `date` is assigned a value, it must receive a value of type `Date`. The first assignment of a new `Date` is the right type. However, the second assignment to a number, `10.31`, is not:

```ts
let date: Date;

date = new Date('2021-10-31'); // Ok

date = 10.31;
// Error: type 'number' is not assignable to type 'Date'.
```

## After TypeScript is Compiled

TypeScript type annotations don’t get compiled down to the output JavaScript, as they are a TypeScript syntax and not JavaScript. Removing comments from the above code would make it look roughly like this if run through the TypeScript compiler (`tsc`):

```ts
let date;
date = new Date('2021-10-31');
date = 10.31;
```

# Unions

Some values may be one of multiple possible types. TypeScript refers to these “either or” types as type unions. This refers to the set theory concept of being a “union” of multiple possible types.

## Syntax

A union type is written as a series of other types with a `|` vertical pipe between them.

```ts
let myVar: number | string = '42';
```

## Union Example

For example, the following `logEither()` function takes in a value that can be either a `number` or `string`:

```ts
function logEither(value: number | string) {
  console.log(value);
}

logEither('Durandal'); // Ok
logEither(7777777); // Also Ok

logEither(false);
// Error: Type 'boolean' is not assignable to type 'number | string'.
```

Optional values, including optional parameters and members, are implicitly type `| undefined`.

## Union type Members

When a value is a union type, TypeScript will only allow access to members that exist on all possible types.

In this example, we use a pair of ternary operators to create a `value`. This set the type of `value` to `boolean | string | string[]` and the only shared member is the `toString()` function. No others, such as `length`, are allowed since it can’t be applied to a `boolean`-type value:

```ts
// Type: boolean | string | string[]
const value =
  Math.random() > 0.5
    ? false
    : Math.random() > 0.5
    ? 'Leela'
    : ['Durandal', 'Thoth'];

value.toString(); // Ok: exists on all three

value.length;
/*
 * Error: Property 'length' does not exist
 * on type 'false | "Leela" | string[]'.
 * Property 'length' does not exist on type 'false'.
 */
```

See [[#Narrowing]] for how to narrow down union types into their more specific types.

# Variables

In TypeScript, besides being typed, variables are expected to follow the same rules and guidelines that they do with JavaScript.

## Syntax

TypeScript variables are generally inferred to be whatever basic type of value they are initially assigned with. Later in code, only values that are of that same basic type may be assigned to the variable. The term for whether a type is assignable to another type is assignability.

```ts
let myVar: string;

myVar = 'Hello'; // Ok
myVar = 'World!'; // Also Ok
myVar = 42; // Not Ok: Type 'number' is not assignable to type 'string'.
```

## Examples with Variables

In this snippet of code, TypeScript sees that the `spooky` variable is initially assigned a `boolean` value, so it believes the `spooky` variable should always be of type `boolean`. Assigning a variable of type `boolean` later on is allowed, as a type is assignable to itself:

```ts
let spooky = true;

spooky = false; // Ok
```

If a variable is assigned a different type of value, TypeScript will notice and emit a type checking complaint. Those type checking complaints can be surfaced as:

- Errors on the command-line.
- Syntax highlights in the code editor.

In this code snippet, the `scary` variable is initially assigned the value `"skeletons"`, which is a `string` type. Later, assigning the decimal number `10.31` is not allowed because a `number` type is not assignable to a variable of type `string`:

```ts
let scary = 'skeletons';

scary = 10.31;
// Error: Type 'number' is not assignable to type 'string'
```