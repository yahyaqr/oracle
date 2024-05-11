## Typing Component Props

This is intended as a basic orientation and reference for React developers familiarizing with TypeScript.

### Basic Prop Types Examples

A list of TypeScript types you will likely use in a React+TypeScript app:

```tsx
type AppProps = {  
	message: string;  
	count: number;  
	disabled: boolean;
	
	/** array of a type! */  
	names: string[];  
	
	/** string literals to specify exact string values,
	with a union type to join them together */  
	status: "waiting" | "success";  
	
	/** an object with known properties
	(but could have more at runtime) */  
	obj: {  
		id: string;  
		title: string;  
	};  
	
	/** array of objects! (common) */  
	objArr: {  
		id: string;  
		title: string;  
	}[];
	
	/** any non-primitive value - can't access any properties
	(NOT COMMON but useful as placeholder) */  
	obj2: object;  
	
	/** an interface with no required properties -
	(NOT COMMON, except for things like `React.Component<{}, State>`) */  
	obj3: {};  
	
	/** a dict object with any number of properties of the same type */  
	dict1: {  
		[key: string]: MyTypeHere;  
	};  

	// equivalent to dict1  
	dict2: Record<string, MyTypeHere>; 
	
	/** function that doesn't take or return anything (VERY COMMON) */  
	onClick: () => void;
	
	/** function with named prop (VERY COMMON) */  
	onChange: (id: number) => void;  
	
	/** function type syntax that takes an event (VERY COMMON) */  
	onChange: (event: React.ChangeEvent<HTMLInputElement>) => void;  
	
	/** alternative function type syntax that takes an event (VERY COMMON) */  
	onClick(event: React.MouseEvent<HTMLButtonElement>): void;  
	
	/** any function as long as you don't invoke it (not recommended) */  
	onSomething: Function;  
	
	/** an optional prop (VERY COMMON!) */  
	optional?: OptionalType;  
	
	/** when passing down the state setter function returned by `useState`
	to a child component. `number` is an example,
	swap out with whatever the type of your state */  
	setState: React.Dispatch<React.SetStateAction<number>>;  
};
```

#### `object` as the non-primitive type

`object` is a common source of misunderstanding in TypeScript. It does not mean "any object" but rather "any non-primitive type", which means it represents anything that is not `number`, `string`, `boolean`, `symbol`, `null` or `undefined`.

Typing "any non-primitive value" is most likely not something that you should do much in React, which means you will probably not use `object` much.

#### Empty interface, `{}` and `Object`

An empty interface, `{}` and `Object` all represent "any non-nullish value"—not "an empty object" as you might think. [Using these types is a common source of misunderstanding and is not recommended](https://typescript-eslint.io/rules/no-empty-interface/).

```tsx
interface AnyNonNullishValue {} // equivalent to `type AnyNonNullishValue = {}` or `type AnyNonNullishValue = Object`  
  
let value: AnyNonNullishValue;  
  
// these are all fine, but might not be expected  
value = 1;  
value = "foo";  
value = () => alert("foo");  
value = {};  
value = { foo: "bar" };  
  
// these are errors  
value = undefined;  
value = null;
```

### Useful React Prop Type Examples

Relevant for components that accept other React components as props.

```tsx
export declare interface AppProps {
	// best, accepts everything React can render
	children?: React.ReactNode;   
	
	// A single React element
	childrenElement: React.tsx.Element;

	// to pass through style props
	style?: React.CSSProperties;   

	// form events! the generic parameter is the type of event.target 
	onChange?: React.FormEventHandler<HTMLInputElement>;

	// to impersonate all the props of a button element
	// and explicitly not forwarding its ref 
	props: Props & React.ComponentPropsWithoutRef<"button">;

	// to impersonate all the props of MyButtonForwardedRef
	// and explicitly forwarding its ref 
	props2: Props & React.ComponentPropsWithRef<MyButtonWithForwardRef>;
}
```

## Function Components

These can be written as normal functions that take a `props` argument and return a tsx element.

```tsx
// Declaring type of props - see "Typing Component Props" for more examples  
// use `interface` if exporting so that consumers can extend
type AppProps = {  
	message: string;  
};   
  
// Easiest way to declare a Function Component; return type is inferred.  
const App = ({ message }: AppProps) => <div>{message}</div>;  
  
// you can choose annotate the return type
// so an error is raised if you accidentally return some other type  
const App = ({ message }: AppProps): React.tsx.Element => <div>{message}</div>;  
  
// you can also inline the type declaration;
// Eliminates naming the prop types, but looks repetitive  
const App = ({ message }: { message: string }) => <div>{message}</div>;  
  
// Alternatively, you can use `React.FunctionComponent` (or `React.FC`), if you prefer.  
// With latest React types and TypeScript 5.1.
// it's mostly a stylistic choice, otherwise discouraged.  
const App: React.FunctionComponent<{ message: string }> = ({ message }) => (  
	<div>{message}</div>  
);
```

> Tip: You might use [Paul Shen's VS Code Extension](https://marketplace.visualstudio.com/items?itemName=paulshen.paul-typescript-toolkit) to automate the type destructure declaration (incl a [keyboard shortcut](https://twitter.com/_paulshen/status/1392915279466745857?s=20)).

## Hooks

Hooks are [supported in `@types/react` from v16.8 up](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/a05cc538a42243c632f054e42eab483ebf1560ab/types/react/index.d.ts#L800-L1031).

### useState

Type inference works very well for simple values:

```tsx
const [state, setState] = useState(false);
// `state` is inferred to be a boolean
// `setState` only takes booleans
```

See also the [Using Inferred Types](https://react-typescript-cheatsheet.netlify.app/docs/basic/troubleshooting/types/#using-inferred-types) section if you need to use a complex type that you've relied on inference for.

However, many hooks are initialized with null-ish default values, and you may wonder how to provide types. Explicitly declare the type, and use a union type:

```tsx
const [user, setUser] = useState<User | null>(null);

// later...
setUser(newUser);
```

You can also use type assertions if a state is initialized soon after setup and always has a value after:

```tsx
const [user, setUser] = useState<User>({} as User);

// later...
setUser(newUser);
```

This temporarily "lies" to the TypeScript compiler that `{}` is of type `User`. You should follow up by setting the `user` state — if you don't, the rest of your code may rely on the fact that `user` is of type `User` and that may lead to runtime errors.

##@# useCallback

You can type the `useCallback` just like any other function.

```tsx
const memoizedCallback = useCallback(
  (param1: string, param2: number) => {
    console.log(param1, param2)
    return { ok: true }
  },
  [...],
);
/**
 * VSCode will show the following type:
 * const memoizedCallback:
 *  (param1: string, param2: number) => { ok: boolean }
 */
```

Note that for React < 18, the function signature of `useCallback` typed arguments as `any[]` by default:

```tsx
function useCallback<T extends (...args: any[]) => any>(
  callback: T,
  deps: DependencyList
): T;
```

In React >= 18, the function signature of `useCallback` changed to the following:

```tsx
function useCallback<T extends Function>(callback: T, deps: DependencyList): T;
```

Therefore, the following code will yield "`Parameter 'e' implicitly has an 'any' type.`" error in React >= 18, but not <17.

```tsx
// @ts-expect-error Parameter 'e' implicitly has 'any' type.
useCallback((e) => {}, []);
// Explicit 'any' type.
useCallback((e: any) => {}, []);
```

### useReducer

You can use [Discriminated Unions](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes-func.html#discriminated-unions) for reducer actions. Don't forget to define the return type of reducer, otherwise TypeScript will infer it.

```tsx
import { useReducer } from "react";

const initialState = { count: 0 };

type ACTIONTYPE =
  | { type: "increment"; payload: number }
  | { type: "decrement"; payload: string };

function reducer(state: typeof initialState, action: ACTIONTYPE) {
  switch (action.type) {
    case "increment":
      return { count: state.count + action.payload };
    case "decrement":
      return { count: state.count - Number(action.payload) };
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: "decrement", payload: "5" })}>
        -
      </button>
      <button onClick={() => dispatch({ type: "increment", payload: 5 })}>
        +
      </button>
    </>
  );
}
```

### useEffect / useLayoutEffect

Both of `useEffect` and `useLayoutEffect` are used for performing **side effects** and return an optional cleanup function which means if they don't deal with returning values, no types are necessary. When using `useEffect`, take care not to return anything other than a function or `undefined`, otherwise both TypeScript and React will yell at you. This can be subtle when using arrow functions:

```tsx
function DelayedEffect(props: { timerMs: number }) {
  const { timerMs } = props;

  useEffect(
    () =>
      setTimeout(() => {
        /* do stuff */
      }, timerMs),
    [timerMs]
  );
  // bad example! setTimeout implicitly returns a number
  // because the arrow function body isn't wrapped in curly braces
  return null;
}
```

### useRef

In TypeScript, `useRef` returns a reference that is either [read-only](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/abd69803c1b710db58d511f4544ec1b70bc9077c/types/react/v16/index.d.ts#L1025-L1039) or [mutable](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/abd69803c1b710db58d511f4544ec1b70bc9077c/types/react/v16/index.d.ts#L1012-L1023), depends on whether your type argument fully covers the initial value or not. Choose one that suits your use case.

#### Option 1: DOM element ref

**[To access a DOM element](https://reactjs.org/docs/refs-and-the-dom.html):** provide only the element type as argument, and use `null` as initial value. In this case, the returned reference will have a read-only `.current` that is managed by React. TypeScript expects you to give this ref to an element's `ref` prop:

```tsx
function Foo() {
  // - If possible, prefer as specific as possible. For example, HTMLDivElement
  //   is better than HTMLElement and way better than Element.
  // - Technical-wise, this returns RefObject<HTMLDivElement>
  const divRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    // Note that ref.current may be null. This is expected, because you may
    // conditionally render the ref-ed element, or you may forget to assign it
    if (!divRef.current) throw Error("divRef is not assigned");

    // Now divRef.current is sure to be HTMLDivElement
    doSomethingWith(divRef.current);
  });

  // Give the ref to an element so React can manage it for you
  return <div ref={divRef}>etc</div>;
}
```

If you are sure that `divRef.current` will never be null, it is also possible to use the non-null assertion operator `!`:

```tsx
const divRef = useRef<HTMLDivElement>(null!);
// Later... No need to check if it is null
doSomethingWith(divRef.current);
```

Note that you are opting out of type safety here - you will have a runtime error if you forget to assign the ref to an element in the render, or if the ref-ed element is conditionally rendered.

> **Tip: Choosing which `HTMLElement` to use**
> 
> Refs demand specificity - it is not enough to just specify any old `HTMLElement`. If you don't know the name of the element type you need, you can check [lib.dom.ts](https://github.com/microsoft/TypeScript/blob/v3.9.5/lib/lib.dom.d.ts#L19224-L19343) or make an intentional type error and let the language service tell you:
> 
> ![[Pasted image 20240512000612.png]]

#### Option 2: Mutable value ref

**[To have a mutable value](https://reactjs.org/docs/hooks-faq.html#is-there-something-like-instance-variables):** provide the type you want, and make sure the initial value fully belongs to that type:

```tsx
function Foo() {
  // Technical-wise, this returns MutableRefObject<number | null>
  const intervalRef = useRef<number | null>(null);

  // You manage the ref yourself (that's why it's called MutableRefObject!)
  useEffect(() => {
    intervalRef.current = setInterval(...);
    return () => clearInterval(intervalRef.current);
  }, []);

  // The ref is not passed to any element's "ref" prop
  return <button onClick={/* clearInterval the ref */}>Cancel timer</button>;
}
```

### useImperativeHandle

Based on this [Stackoverflow answer](https://stackoverflow.com/a/69292925/5415299):

```tsx
// Countdown.tsx

// Define the handle types which will be passed to the forwardRef
export type CountdownHandle = {
  start: () => void;
};

type CountdownProps = {};

const Countdown = forwardRef<CountdownHandle, CountdownProps>((props, ref) => {
  useImperativeHandle(ref, () => ({
    // start() has type inference here
    start() {
      alert("Start");
    },
  }));

  return <div>Countdown</div>;
});
```

```tsx
// The component uses the Countdown component

import Countdown, { CountdownHandle } from "./Countdown.tsx";

function App() {
  const countdownEl = useRef<CountdownHandle>(null);

  useEffect(() => {
    if (countdownEl.current) {
      // start() has type inference here as well
      countdownEl.current.start();
    }
  }, []);

  return <Countdown ref={countdownEl} />;
}
```

### Custom Hooks

If you are returning an array in your Custom Hook, you will want to avoid type inference as TypeScript will infer a union type (when you actually want different types in each position of the array). Instead, use [TS 3.4 const assertions](https://devblogs.microsoft.com/typescript/announcing-typescript-3-4/#const-assertions):

```
import { useState } from "react";

export function useLoading() {
  const [isLoading, setState] = useState(false);
  const load = (aPromise: Promise<any>) => {
    setState(true);
    return aPromise.finally(() => setState(false));
  };
  return [isLoading, load] as const; // infers [boolean, typeof load] instead of (boolean | typeof load)[]
}
```

This way, when you destructure you actually get the right types based on destructure position.

## Class Components

Within TypeScript, `React.Component` is a generic type (aka `React.Component<PropType, StateType>`), so you want to provide it with (optional) prop and state type parameters:

```tsx
type MyProps = {
  // using `interface` is also ok
  message: string;
};
type MyState = {
  count: number; // like this
};
class App extends React.Component<MyProps, MyState> {
  state: MyState = {
    // optional second annotation for better type inference
    count: 0,
  };
  render() {
    return (
      <div>
        {this.props.message} {this.state.count}
      </div>
    );
  }
}
```


Don't forget that you can export/import/extend these types/interfaces for reuse.

>**Why annotate `state` twice?**
>
>It isn't strictly necessary to annotate the `state` class property, but it allows better type inference when accessing `this.state` and also initializing the state.
>
>This is because they work in two different ways, the 2nd generic type parameter will allow `this.setState()` to work correctly, because that method comes from the base class, but initializing `state` inside the component overrides the base implementation so you have to make sure that you tell the compiler that you're not actually doing anything different.

**Class Methods**: Do it like normal, but just remember any arguments for your functions also need to be typed:

```tsx
class App extends React.Component<{ message: string }, { count: number }> {
  state = { count: 0 };
  render() {
    return (
      <div onClick={() => this.increment(1)}>
        {this.props.message} {this.state.count}
      </div>
    );
  }
  increment = (amt: number) => {
    // like this
    this.setState((state) => ({
      count: state.count + amt,
    }));
  };
}
```

**Class Properties**: If you need to declare class properties for later use, just declare it like `state`, but without assignment:

```tsx
class App extends React.Component<{
  message: string;
}> {
  pointer: number; // like this
  componentDidMount() {
    this.pointer = 3;
  }
  render() {
    return (
      <div>
        {this.props.message} and {this.pointer}
      </div>
    );
  }
}
```

### Typing getDerivedStateFromProps

Before you start using `getDerivedStateFromProps`, please go through the [documentation](https://reactjs.org/docs/react-component.html#static-getderivedstatefromprops) and [You Probably Don't Need Derived State](https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html). Derived State can be implemented using hooks which can also help set up memoization.

Here are a few ways in which you can annotate `getDerivedStateFromProps`

1. If you have explicitly typed your derived state and want to make sure that the return value from `getDerivedStateFromProps` conforms to it.

```tsx
class Comp extends React.Component<Props, State> {
  static getDerivedStateFromProps(
    props: Props,
    state: State
  ): Partial<State> | null {
    //
  }
}
```

2. When you want the function's return value to determine your state.

```tsx
class Comp extends React.Component<
  Props,
  ReturnType<typeof Comp["getDerivedStateFromProps"]>
> {
  static getDerivedStateFromProps(props: Props) {}
}
```

3. When you want derived state with other state fields and memorization

```tsx
type CustomValue = any;
interface Props {
  propA: CustomValue;
}
interface DefinedState {
  otherStateField: string;
}
type State = DefinedState & ReturnType<typeof transformPropsToState>;
function transformPropsToState(props: Props) {
  return {
    savedPropA: props.propA, // save for memoization
    derivedState: props.propA,
  };
}
class Comp extends React.PureComponent<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = {
      otherStateField: "123",
      ...transformPropsToState(props),
    };
  }
  static getDerivedStateFromProps(props: Props, state: State) {
    if (isEqual(props.propA, state.savedPropA)) return null;
    return transformPropsToState(props);
  }
}
```

## Typing defaultProps

### You May Not Need `defaultProps`

As per [this tweet](https://twitter.com/dan_abramov/status/1133878326358171650), defaultProps will eventually be deprecated. You can check the discussions here:

- [Original tweet](https://twitter.com/hswolff/status/1133759319571345408)
- More info can also be found in [this article](https://medium.com/@matanbobi/react-defaultprops-is-dying-whos-the-contender-443c19d9e7f1)

The consensus is to use object default values.

Function Components:

```tsx
type GreetProps = { age?: number };

const Greet = ({ age = 21 }: GreetProps) => // etc
```

Class Components:

```tsx
type GreetProps = {
  age?: number;
};

class Greet extends React.Component<GreetProps> {
  render() {
    const { age = 21 } = this.props;
    /*...*/
  }
}

let el = <Greet age={3} />;
```

## Forms and Events

If performance is not an issue (and it usually isn't!), inlining handlers is easiest as you can just use [type inference and contextual typing](https://www.typescriptlang.org/docs/handbook/type-inference.html#contextual-typing):

```tsx
const el = (
  <button
    onClick={(event) => {
      /* event will be correctly typed automatically! */
    }}
  />
);
```

But if you need to define your event handler separately, IDE tooling really comes in handy here, as the @type definitions come with a wealth of typing. Type what you are looking for and usually the autocomplete will help you out. Here is what it looks like for an `onChange` for a form event:

```tsx
type State = {
  text: string;
};
class App extends React.Component<Props, State> {
  state = {
    text: "",
  };

  // typing on RIGHT hand side of =
  onChange = (e: React.FormEvent<HTMLInputElement>): void => {
    this.setState({ text: e.currentTarget.value });
  };
  render() {
    return (
      <div>
        <input type="text" value={this.state.text} onChange={this.onChange} />
      </div>
    );
  }
}
```

Instead of typing the arguments and return values with `React.FormEvent<>` and `void`, you may alternatively apply types to the event handler itself (_contributed by @TomasHubelbauer_):

```tsx
  // typing on LEFT hand side of =
  onChange: React.ChangeEventHandler<HTMLInputElement> = (e) => {
    this.setState({text: e.currentTarget.value})
  }
```

> **Why two ways to do the same thing?**
> 
> The first method uses an inferred method signature `(e: React.FormEvent<HTMLInputElement>): void` and the second method enforces a type of the delegate provided by `@types/react`. So `React.ChangeEventHandler<>` is simply a "blessed" typing by `@types/react`, whereas you can think of the inferred method as more... _artisanally hand-rolled_. Either way it's a good pattern to know. [See our Github PR for more](https://github.com/typescript-cheatsheets/react/pull/24).

**Typing onSubmit, with Uncontrolled components in a Form**

If you don't quite care about the type of the event, you can just use `React.SyntheticEvent`. If your target form has custom named inputs that you'd like to access, you can use a type assertion:

```tsx
<form
  ref={formRef}
  onSubmit={(e: React.SyntheticEvent) => {
    e.preventDefault();
    const target = e.target as typeof e.target & {
      email: { value: string };
      password: { value: string };
    };
    const email = target.email.value; // typechecks!
    const password = target.password.value; // typechecks!
    // etc...
  }}
>
  <div>
    <label>
      Email:
      <input type="email" name="email" />
    </label>
  </div>
  <div>
    <label>
      Password:
      <input type="password" name="password" />
    </label>
  </div>
  <div>
    <input type="submit" value="Log in" />
  </div>
</form>
```

Of course, if you're making any sort of significant form, [you should use Formik](https://jaredpalmer.com/formik) or [React Hook Form](https://react-hook-form.com/), which are written in TypeScript.

## Context

### Basic example

Here's a basic example of creating a context containing the active theme.

```tsx
import { createContext } from "react";

type ThemeContextType = "light" | "dark";

const ThemeContext = createContext<ThemeContextType>("light");
```

Wrap the components that need the context with a context provider:

```tsx
import { useState } from "react";

const App = () => {
  const [theme, setTheme] = useState<ThemeContextType>("light");

  return (
    <ThemeContext.Provider value={theme}>
      <MyComponent />
    </ThemeContext.Provider>
  );
};
```

Call `useContext` to read and subscribe to the context.

```tsx
import { useContext } from "react";

const MyComponent = () => {
  const theme = useContext(ThemeContext);

  return <p>The current theme is {theme}.</p>;
};
```

### Without default context value

If you don't have any meaningful default value, specify `null`:

```tsx
import { createContext } from "react";

interface CurrentUserContextType {
  username: string;
}

const CurrentUserContext = createContext<CurrentUserContextType | null>(null);
```

```tsx
const App = () => {
  const [currentUser, setCurrentUser] = useState<CurrentUserContextType>({
    username: "filiptammergard",
  });

  return (
    <CurrentUserContext.Provider value={currentUser}>
      <MyComponent />
    </CurrentUserContext.Provider>
  );
};
```

Now that the type of the context can be `null`, you'll notice that you'll get a `'currentUser' is possibly 'null'` TypeScript error if you try to access the `username` property. You can use optional chaining to access `username`:

```tsx
import { useContext } from "react";

const MyComponent = () => {
  const currentUser = useContext(CurrentUserContext);

  return <p>Name: {currentUser?.username}.</p>;
};
```

However, it would be preferable to not have to check for `null`, since we know that the context won't be `null`. One way to do that is to provide a custom hook to use the context, where an error is thrown if the context is not provided:

```tsx
import { createContext } from "react";

interface CurrentUserContextType {
  username: string;
}

const CurrentUserContext = createContext<CurrentUserContextType | null>(null);

const useCurrentUser = () => {
  const currentUserContext = useContext(CurrentUserContext);

  if (!currentUserContext) {
    throw new Error(
      "useCurrentUser has to be used within <CurrentUserContext.Provider>"
    );
  }

  return currentUserContext;
};
```

Using a runtime type check in this will has the benefit of printing a clear error message in the console when a provider is not wrapping the components properly. Now it's possible to access `currentUser.username` without checking for `null`:

```tsx
import { useContext } from "react";

const MyComponent = () => {
  const currentUser = useContext(CurrentUserContext);

  return <p>Name: {currentUser?.username}.</p>;
};
```

#### Type assertion as an alternative

Another way to avoid having to check for `null` is to use type assertion to tell TypeScript you know the context is not `null`:

```tsx
const CurrentUserContext = createContext<CurrentUserContextType>(
  {} as CurrentUserContextType
);
```

Another option is to use an empty object as default value and cast it to the expected context type:

```tsx
const CurrentUserContext = createContext<CurrentUserContextType>(null!);
```

You can also use non-null assertion to get the same result:

```
const CurrentUserContext = createContext<CurrentUserContextType>(null!);
```

When you don't know what to choose, prefer runtime checking and throwing over type asserting.

## forwardRef/createRef

Check the [Hooks section](https://github.com/typescript-cheatsheets/react/blob/main/README.md#hooks) for `useRef`.

`createRef`:

```ts
import { createRef, PureComponent } from "react";

class CssThemeProvider extends PureComponent<Props> {
  private rootRef = createRef<HTMLDivElement>(); // like this
  render() {
    return <div ref={this.rootRef}>{this.props.children}</div>;
  }
}
```

`forwardRef`:

```ts
import { forwardRef, ReactNode } from "react";

interface Props {
  children?: ReactNode;
  type: "submit" | "button";
}
export type Ref = HTMLButtonElement;

export const FancyButton = forwardRef<Ref, Props>((props, ref) => (
  <button ref={ref} className="MyClassName" type={props.type}>
    {props.children}
  </button>
));
```

**Side note: the `ref` you get from forwardRef is mutable so you can assign to it if needed**
 
This was done [on purpose](https://github.com/DefinitelyTyped/DefinitelyTyped/pull/43265/). You can make it immutable if you have to - assign `React.Ref` if you want to ensure nobody reassigns it:

```tsx
import { forwardRef, ReactNode, Ref } from "react";

interface Props {
  children?: ReactNode;
  type: "submit" | "button";
}

export const FancyButton = forwardRef(
  (
    props: Props,
    ref: Ref<HTMLButtonElement> // <-- here!
  ) => (
    <button ref={ref} className="MyClassName" type={props.type}>
      {props.children}
    </button>
  )
);
```

If you are grabbing the props of a component that forwards refs, use [`ComponentPropsWithRef`](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/a05cc538a42243c632f054e42eab483ebf1560ab/types/react/index.d.ts#L770).

### Generic forwardRefs

Read more context in [https://fettblog.eu/typescript-react-generic-forward-refs/](https://fettblog.eu/typescript-react-generic-forward-refs/):

#### Option 1 - Wrapper component

```tsx
type ClickableListProps<T> = {
  items: T[];
  onSelect: (item: T) => void;
  mRef?: React.Ref<HTMLUListElement> | null;
};

export function ClickableList<T>(props: ClickableListProps<T>) {
  return (
    <ul ref={props.mRef}>
      {props.items.map((item, i) => (
        <li key={i}>
          <button onClick={(el) => props.onSelect(item)}>Select</button>
          {item}
        </li>
      ))}
    </ul>
  );
}
```

#### Option 2 - Redeclare forwardRef

```tsx
// Redeclare forwardRef
declare module "react" {
  function forwardRef<T, P = {}>(
    render: (props: P, ref: React.Ref<T>) => React.ReactElement | null
  ): (props: P & React.RefAttributes<T>) => React.ReactElement | null;
}

// Just write your components like you're used to!
import { forwardRef, ForwardedRef } from "react";

interface ClickableListProps<T> {
  items: T[];
  onSelect: (item: T) => void;
}

function ClickableListInner<T>(
  props: ClickableListProps<T>,
  ref: ForwardedRef<HTMLUListElement>
) {
  return (
    <ul ref={ref}>
      {props.items.map((item, i) => (
        <li key={i}>
          <button onClick={(el) => props.onSelect(item)}>Select</button>
          {item}
        </li>
      ))}
    </ul>
  );
}

export const ClickableList = forwardRef(ClickableListInner);
```

#### Option 3 - Call signature

```tsx
// Add to `index.d.ts`
interface ForwardRefWithGenerics extends React.FC<WithForwardRefProps<Option>> {
  <T extends Option>(props: WithForwardRefProps<T>): ReturnType<
    React.FC<WithForwardRefProps<T>>
  >;
}

export const ClickableListWithForwardRef: ForwardRefWithGenerics =
  forwardRef(ClickableList);
```

## Portals

Using `ReactDOM.createPortal`:

```tsx
const modalRoot = document.getElementById("modal-root") as HTMLElement;
// assuming in your html file has a div with id 'modal-root';

export class Modal extends React.Component<{ children?: React.ReactNode }> {
  el: HTMLElement = document.createElement("div");

  componentDidMount() {
    modalRoot.appendChild(this.el);
  }

  componentWillUnmount() {
    modalRoot.removeChild(this.el);
  }

  render() {
    return ReactDOM.createPortal(this.props.children, this.el);
  }
}
```

**Using hooks**

Same as above but using hooks

```tsx
import { useEffect, useRef, ReactNode } from "react";
import { createPortal } from "react-dom";

const modalRoot = document.querySelector("#modal-root") as HTMLElement;

type ModalProps = {
  children: ReactNode;
};

function Modal({ children }: ModalProps) {
  // create div element only once using ref
  const elRef = useRef<HTMLDivElement | null>(null);
  if (!elRef.current) elRef.current = document.createElement("div");

  useEffect(() => {
    const el = elRef.current!; // non-null assertion because it will never be null
    modalRoot.appendChild(el);
    return () => {
      modalRoot.removeChild(el);
    };
  }, []);

  return createPortal(children, elRef.current);
}
```

Modal Component Usage Example:

```tsx
import { useState } from "react";

function App() {
  const [showModal, setShowModal] = useState(false);

  return (
    <div>
      // you can also put this in your static html file
      <div id="modal-root"></div>
      {showModal && (
        <Modal>
          <div
            style={{
              display: "grid",
              placeItems: "center",
              height: "100vh",
              width: "100vh",
              background: "rgba(0,0,0,0.1)",
              zIndex: 99,
            }}
          >
            I'm a modal!{" "}
            <button
              style={{ background: "papyawhip" }}
              onClick={() => setShowModal(false)}
            >
              close
            </button>
          </div>
        </Modal>
      )}
      <button onClick={() => setShowModal(true)}>show Modal</button>
      // rest of your app
    </div>
  );
}
```

This example is based on the [Event Bubbling Through Portal](https://reactjs.org/docs/portals.html#event-bubbling-through-portals) example of React docs.

## Error Boundaries

### Option 1: Using react-error-boundary

[React-error-boundary](https://github.com/bvaughn/react-error-boundary) - is a lightweight package ready to use for this scenario with TS support built-in. This approach also lets you avoid class components that are not that popular anymore.

### Option 2: Writing your custom error boundary component

If you don't want to add a new npm package for this, you can also write your own `ErrorBoundary` component.

```tsx
import React, { Component, ErrorInfo, ReactNode } from "react";

interface Props {
  children?: ReactNode;
}

interface State {
  hasError: boolean;
}

class ErrorBoundary extends Component<Props, State> {
  public state: State = {
    hasError: false
  };

  public static getDerivedStateFromError(_: Error): State {
    // Update state so the next render will show the fallback UI.
    return { hasError: true };
  }

  public componentDidCatch(error: Error, errorInfo: ErrorInfo) {
    console.error("Uncaught error:", error, errorInfo);
  }

  public render() {
    if (this.state.hasError) {
      return <h1>Sorry.. there was an error</h1>;
    }

    return this.props.children;
  }
}

export default ErrorBoundary;

```