# react-typescript

## [Steve kinney FEM ](https://stevekinney.github.io/react-and-typescript/)

```ts
// ts does it's best to infer types for you

////////////////////////////////////////////////
/// Fundamentals
////////////////////////////////////////////////

// key can be any string, string values
type ItemHash = {
  [key: string]: string;
};
// alt
Record<string, Item>;

// typing component children
// child could be component(s), html elements, string, etc...
 React.ReactNode // works for all options

// typing css
type BoxProps = { children: React.ReactNode; style?: React.CSSProperties };
```
