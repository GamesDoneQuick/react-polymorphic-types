# react-polymorphic-types

Zero-runtime polymorphic component definitions for React

[![npm](https://img.shields.io/npm/v/react-polymorphic-types)](https://www.npmjs.com/package/react-polymorphic-types)
[![npm bundle size](https://img.shields.io/bundlephobia/min/react-polymorphic-types)](https://bundlephobia.com/result?p=react-polymorphic-types)

## Motivation

Being a successor to [react-polymorphic-box](https://github.com/kripod/react-polymorphic-box), this project offers more accurate typings with less overhead.

## Features

- Automatic code completion, based on the value of the `as` prop
- Static type checking against the associated component’s inferred props
- HTML element name validation

## Usage

A `Heading` component can demonstrate the effectiveness of polymorphism:

```tsx
<Heading color="rebeccapurple">Heading</Heading>
<Heading as="h3">Subheading</Heading>
```

Custom components like the previous one may utilize the package as shown below.

```tsx
import type { PolymorphicPropsWithoutRef } from "react-polymorphic-types";

// An HTML tag or a different React component can be rendered by default
export const HeadingDefaultElement = "h2";

// Component-specific props should be specified separately
export type HeadingOwnProps = {
  color?: string;
};

// Extend own props with others inherited from the underlying element type
// Own props take precedence over the inherited ones
export type HeadingProps<
  T extends React.ElementType = typeof HeadingDefaultElement
> = PolymorphicPropsWithoutRef<HeadingOwnProps, T>;

export function Heading<
  T extends React.ElementType = typeof HeadingDefaultElement
>({ as, color, style, ...restProps }: HeadingProps<T>) {
  const Element: React.ElementType = as || ButtonDefaultElement;
  return <Element style={{ color, ...style }} {...restProps} />;
}
```

### With [`React.forwardRef`](https://reactjs.org/docs/react-api.html#reactforwardref)

```tsx
import * as React from "react";
import type { PolymorphicForwardRefExoticComponent } from "react-polymorphic-types";
import { Heading, HeadingDefaultElement, HeadingOwnProps } from "./Heading";

export const RefForwardingHeading: PolymorphicForwardRefExoticComponent<
  HeadingOwnProps,
  HeadingDefaultElement
> = React.forwardRef(Heading);
```

### With [`React.memo`](https://reactjs.org/docs/react-api.html#reactmemo)

```tsx
import * as React from "react";
import type { PolymorphicMemoExoticComponent } from "react-polymorphic-types";
import { Heading, HeadingDefaultElement, HeadingOwnProps } from "./Heading";

export const MemoizedHeading: PolymorphicMemoExoticComponent<
  HeadingOwnProps,
  HeadingDefaultElement
> = React.memo(Heading);
```

### With [`React.lazy`](https://reactjs.org/docs/react-api.html#reactlazy)

```tsx
import * as React from "react";
import type { PolymorphicLazyExoticComponent } from "react-polymorphic-types";
import type { HeadingDefaultElement, HeadingOwnProps } from "./Heading";

export const LazyHeading: PolymorphicLazyExoticComponent<
  HeadingOwnProps,
  HeadingDefaultElement
> = React.lazy(async () => {
  const { Heading } = await import("./Heading");
  return { default: Heading };
});
```