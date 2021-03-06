# When and why would I want to use SFC declaration

## Ok way

Normally, when defining a react component in typescript, we only specify the `props` and leave the typescript to figure out the component/function type. E.g.

```typescript
// interface.ts
import { ReactNode, CSSProperties } from 'react';

export interface IContainerProps {
  children: ReactNode;
  style?: CSSProperties;
}
```

```typescript
// container.tsx - only define props
import React from 'react';
import { IContainerProps } from './interface.ts'
export default function Container(props: IContainerProps) {
  const { children, style = {}} = props;
  return (
    <div style={style ? style : {}}>
      {children}
    </div>
  );
}
```

`Container` would then be inferred with the type `function Container(props: IContainerProps): JSX.Element`

### ReactElement

```typescript
interface ReactElement<P = any, T extends string | JSXElementConstructor<any> = string | JSXElementConstructor<any>> {
  type: T;
  props: P;
  key: Key | null;
}
```

`<div> abc </div>` is a ReactElement as it is indeed equivalent to:

```javascript
{
	$$typeof: Symbol(react.element),
	props: { children: 'abc' },
	type: 'div',
	...
}
```

### ReactNode

```typescript
type ReactNode = ReactChild | ReactFragment | ReactPortal | boolean | null | undefined;

type ReactChild = ReactElement | ReactText;
type ReactText = string | number
type ReactFragment = {} | ReactNodeArray
interface ReactNodeArray extends Array<ReactNode> {}
```

## Better way

Notice that we have to define `children` prop in `IContainerProps`, we can optimize this with `SFC`

```typescript
import React, { SFC } from 'react';
import { IContainerProps } from './interface';

const Container: SFC<IContainerProps> = (props) => {
  const { children, style = {} } = props;
  return (
    <div style={style ? style : {}}>
      {children}
    </div>
  );
}

export default Container;
```

```typescript
import { CSSProperties } from 'react';

export interface IContainerProps {
  style?: CSSProperties;
}
```

It works as `SFC` (which is basically `FunctionComponent`) has defined the `children` with generic type.

```typescript
type SFC<P = {}> = FunctionComponent<P>;

interface FunctionComponent<P = {}> {
  (props: PropsWithChildren<P>, context?: any): ReactElement | null;
  propTypes?: WeakValidationMap<P>;
  contextTypes?: ValidationMap<any>;
  defaultProps?: Partial<P>;
  displayName?: string;
}
```

```typescript
type PropsWithChildren<P> = P & { children?: ReactNode };
```

Note here `type` can be used to generate a new `type definition`

## Notice

* If you want to follow the latest news/articles for the series of my blogs, Please [「Watch」](https://github.com/n0ruSh/blogs/)to Subscribe.
