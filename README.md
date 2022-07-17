# react-typescript

# [Steve kinney FEM ](https://stevekinney.github.io/react-and-typescript/)

- Theme - ts does it's best to infer types for you
- Equivalent dictionaries

```ts
type ItemHash = {
  [key: string]: Item;
};
Record<string, Item>;
```

- Functions

```ts
type ContrivedExampleProps = {
  onHover: () => void;
  onChange: (id: number) => void;
};
```

- CSSProperties

```ts
type BoxProps = { children: React.ReactNode; style?: React.CSSProperties };
const Box = ({ children, style = {} }: BoxProps) => {
  return (
    <section style={{ padding: "1em", border: "5px solid purple", ...style }}>
      {children}
    </section>
  );
};
```

- Input handler inference

```ts
// typically
const changeCount = (e: ChangeEvent<HTMLInputElement>) => {
  setCount(+event.target.value);
};

// when directly on change handler, you can actually omit ChangeEvent<HTMLInputElement>
<input
  onChange={(e) => {
    setCount(+event.target.value);
  }}
/>;
```

- useReducer hook, improvement

```ts
// This is better rather than payload?: number;
type BasicCounterAction = {
  type: "INCREMENT" | "DECREMENT";
};
type SetCounterAction = {
  type: "SET";
  payload: number;
};
const reducer = (
  state: CounterState,
  action: BasicCounterAction | SetCounterAction
) => {
  switch (action.type) {
    case "INCREMENT":
      return { value: state.value + 1 };
    case "DECREMENT":
      return { value: state.value - 1 };
    case "SET":
      return { value: action.payload };
  }
};
```

- TS `as` keyword, ex. `{} as RGBContextType`
  - tell TS that you know better
  - ex. string as one of these types

## Context - skipped

- [Video](https://frontendmasters.com/courses/react-typescript/context-api/)
- [Site](https://stevekinney.github.io/react-and-typescript/passing-dipatch)

<br/>

```ts
export interface ColorAdjustmentProps {
  // the component is any component with these props
  Adjustment: React.ComponentType<AdjustmentInputProps>;
}
// ColorAdjustmentProps is HOC
```

## Generics

```ts
function tap<T>(arr: T, fn: (arr: T) => void): T {
  fn(arr);
  return arr;
}
const arrayWithoutLast = tap([1, 2, 3, 4], function (array) {
  return array.pop();
});
```

## [Utility types](https://stevekinney.github.io/react-and-typescript/utility-types)

- **keyof**: keys of a type

```ts
type ObjectLiteralType = {
  first: 1;
  second: 2;
};
type Result = keyof ObjectLiteralType; // Inferred Type: "first" | "second"
```

- getting the type of a single key in an object

```ts
type Obj = {
  0: "a";
  1: "b";
  prop1: "d";
};

// Inferred Type: "c"
type Result0 = Obj["prop0"];

// Inferred Type: "a" | "b"
type Result1 = Obj[0 | 1];
```

- Getting type values

```ts
type Obj = {
  a: "A";
  b: "B";
  c: number;
};
type Values = Obj[keyof Obj]; // Inferred Type: number | "A" | "B"
```

- Union with Objects

```ts
type ObjectTypeA = {
  firstProp: number;
  sharedProp: string;
};

type ObjectTypeB = {
  secondProp: boolean;
  sharedProp: string;
};

type Union = ObjectTypeA | ObjectTypeB;
const u: Union = { sharedProp: "hey", firstProp: 11, secondProp: true };
// allows us to assume that u.sharedProp: string will always exist
```

- **Intersections**: only what appears in both

```ts
type A = "a" | "b" | "c";
type B = "b" | "c" | "d";

// Inferred Type: "b" | "c"
type Intersection = A & B;

// but for objects (above), it will combine them
```

- **Conditionals**: ternaries only

```ts
type Wrap<T> = T extends { length: number } ? [T] : T; //EX.1

type IsAssignableTo<A, B> = A extends B ? true : false; //EX.2

// Type `123` is assignable to type `number`
// Inferred Type: true
type Result1 = IsAssignableTo<123, number>;

// Type `number` is not assignable to type `123`
// Inferred Type: false
type Result2 = IsAssignableTo<number, 123>;
```

- **Exclude**: removes values from union, takes stuff out of a union

```ts
type Exclude<T, U> = T extends U ? never : T;

// Inferred Type: 1 | 3
type Result0 = Exclude<1 | 2 | 3, 2>;

// Inferred Type: "a" | "b"
type Result1 = Exclude<1 | "a" | 2 | "b", number>;

// Inferred Type: "a" | 2
type Result2 = Exclude<1 | "a" | 2 | "b", 1 | "b" | "c">;
```

- **Extract**: the opposite of exclude

```ts
type Extract<T, U> = T extends U ? T : never;

// Inferred Type: 1 | 2
type Result1 = Extract<1 | "a" | 2 | "b", number>;

// Inferred Type: 1 | "b"
type Result2 = Extract<1 | "a" | 2 | "b", 1 | "b" | "c">;
```

- Union iteration

```ts
// Inferred Type: { a: number; b: number; c: number; }
type Result = {
  [K in "a" | "b" | "c"]: number;
};

type Mask = {
  [K in keyof ObjectLiteralType]: boolean;
};
```

- **Pick**: pick out certain keys from an object type

```ts
// Inferred Type: { george: 2; ringo: 4; }
type Result = Pick<
  { john: 1; paul: 2; george: 3; ringo: 4 },
  "george" | "ringo"
>;
```

- **Omit**: opposite of pick, leave out particular properties

```ts
// Inferred Type: { john: 1; paul: 2; }
type Result = Omit<
  {
    john: 1;
    paul: 2;
    george: 3;
    ringo: 4;
  },
  "george" | "ringo"
>;
```

- String Manipulation Utilities

```ts
type UppercaseWes = Uppercase<"wes">;
type LowercaseWes = Lowercase<"Wes">;
type CapitalizeWes = Capitalize<"wes">;
type UncapitalizeWes = Uncapitalize<"Wes">;
```

## React specific types

- **React.HTMLProps\<HTMLXXXElement\>**: A type representing Props of specified native HTML element. Useful for extending HTML Elements

```ts
const Input = (props: <Props & React.HTMLProps<HTMLInputElement>) => {
  // our custom props + everything you can pass to the html
}

<Input about={...} accept={...} alt={...} ... />
```

- Props of a component

```ts
type MyComponentProps = React.ComponentProps<typeof MyComponent>;
```

- Template literals

```ts
type VerticalAlignment = "top" | "center" | "bottom";
type HorizonalAlignment = "left" | "center" | "right";
type Alignment = Exclude<
  `${VerticalAlignment}-${HorizonalAlignment}` | "center",
  "center-center"
>;
// if we wanted center rather than center-center
```

## [HOC with TS](https://stevekinney.github.io/react-and-typescript/higher-order-components)

- Create a HOC that accepts a character compatible component, C, that you can later instantiate with additional props

```ts
import { ComponentType, useState, useEffect, CSSProperties } from "react";
import * as React from "react";

export default function Application() {
  const CharacterInformationWithCharacter = withCharacter(CharacterInformation);
  return (
    <CharacterInformationWithCharacter style={{ backgroundColor: "pink" }} />
  );
}

export type CharacterType = {
  name: string;
  alignment: string;
  power: number;
};

const CharacterInformation = ({
  character: { name, alignment, power },
  style,
}: {
  character: CharacterType;
  style: CSSProperties;
}) => {
  return (
    <div style={style}>
      <h1>{name}</h1>
      <div>Alignment: {alignment}</div>
      <div>Power: {power}</div>
    </div>
  );
};

type WithCharacterProps = {
  character: CharacterType;
};
function withCharacter<T>(Component: ComponentType<T>) {
  type withoutCharacterProps = Omit<T, keyof WithCharacterProps>;
  return function (props: withoutCharacterProps) {
    const [character, setCharacter] = useState<CharacterType | null>(null);
    useEffect(() => {
      new Promise(function (resolve, _) {
        setTimeout(function () {
          resolve({
            name: "3-D Man",
            alignment: "neutral",
            power: 25,
          });
        }, 2000);
      }).then((c: CharacterType) => {
        setCharacter(c);
      });
    }, []);

    return character ? (
      <Component character={character} {...(props as T)} />
    ) : (
      <h1>Loading…</h1>
    );
  };
}
```
