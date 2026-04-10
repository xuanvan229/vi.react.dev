---
title: Sử dụng TypeScript
re: https://github.com/reactjs/react.dev/issues/5960
---

<Intro>

TypeScript là một cách phổ biến để thêm định nghĩa kiểu vào các codebase JavaScript. TypeScript [hỗ trợ JSX](/learn/writing-markup-with-jsx) ngay từ đầu và bạn có thể nhận được hỗ trợ React Web đầy đủ bằng cách thêm [`@types/react`](https://www.npmjs.com/package/@types/react) và [`@types/react-dom`](https://www.npmjs.com/package/@types/react-dom) vào dự án của bạn.

</Intro>

<YouWillLearn>

* [TypeScript với React Components](/learn/typescript#typescript-with-react-components)
* [Ví dụ về typing với Hooks](/learn/typescript#example-hooks)
* [Các kiểu phổ biến từ `@types/react`](/learn/typescript#useful-types)
* [Các tài nguyên học thêm](/learn/typescript#further-learning)

</YouWillLearn>

## Cài đặt {/*installation*/}

Tất cả các [React framework cấp độ sản xuất](/learn/creating-a-react-app#full-stack-frameworks) đều hỗ trợ sử dụng TypeScript. Làm theo hướng dẫn cài đặt dành riêng cho từng framework:

- [Next.js](https://nextjs.org/docs/app/building-your-application/configuring/typescript)
- [Remix](https://remix.run/docs/en/1.19.2/guides/typescript)
- [Gatsby](https://www.gatsbyjs.com/docs/how-to/custom-configuration/typescript/)
- [Expo](https://docs.expo.dev/guides/typescript/)

### Thêm TypeScript vào một dự án React hiện có {/*adding-typescript-to-an-existing-react-project*/}

Để cài đặt phiên bản mới nhất của các định nghĩa kiểu của React:

<TerminalBlock>
npm install --save-dev @types/react @types/react-dom
</TerminalBlock>

Các tùy chọn compiler sau cần được đặt trong `tsconfig.json` của bạn:

1. `dom` phải được bao gồm trong [`lib`](https://www.typescriptlang.org/tsconfig/#lib) (Lưu ý: Nếu không có tùy chọn `lib` nào được chỉ định, `dom` được bao gồm theo mặc định).
2. [`jsx`](https://www.typescriptlang.org/tsconfig/#jsx) phải được đặt thành một trong các tùy chọn hợp lệ. `preserve` nên đủ cho hầu hết các ứng dụng.
  Nếu bạn đang xuất bản một thư viện, hãy tham khảo [tài liệu `jsx`](https://www.typescriptlang.org/tsconfig/#jsx) để biết nên chọn giá trị nào.

## TypeScript với React Components {/*typescript-with-react-components*/}

<Note>

Mỗi file chứa JSX phải sử dụng phần mở rộng file `.tsx`. Đây là phần mở rộng dành riêng cho TypeScript cho TypeScript biết file này chứa JSX.

</Note>

Viết TypeScript với React rất giống với viết JavaScript với React. Sự khác biệt chính khi làm việc với một component là bạn có thể cung cấp kiểu cho props của component. Các kiểu này có thể được sử dụng để kiểm tra tính đúng đắn và cung cấp tài liệu nội tuyến trong các trình soạn thảo.

Lấy [component `MyButton`](/learn#components) từ hướng dẫn [Quick Start](/learn), chúng ta có thể thêm một kiểu mô tả `title` cho button:

<Sandpack>

```tsx src/App.tsx active
function MyButton({ title }: { title: string }) {
  return (
    <button>{title}</button>
  );
}

export default function MyApp() {
  return (
    <div>
      <h1>Welcome to my app</h1>
      <MyButton title="I'm a button" />
    </div>
  );
}
```

```js src/App.js hidden
import AppTSX from "./App.tsx";
export default App = AppTSX;
```
</Sandpack>

 <Note>

Các sandbox này có thể xử lý code TypeScript, nhưng chúng không chạy type-checker. Điều này có nghĩa là bạn có thể sửa đổi các sandbox TypeScript để học, nhưng bạn sẽ không nhận được bất kỳ lỗi kiểu hoặc cảnh báo nào. Để có type-checking, bạn có thể sử dụng [TypeScript Playground](https://www.typescriptlang.org/play) hoặc sử dụng một sandbox trực tuyến đầy đủ tính năng hơn.

</Note>

Cú pháp nội tuyến này là cách đơn giản nhất để cung cấp kiểu cho một component, mặc dù khi bạn bắt đầu có nhiều trường để mô tả, nó có thể trở nên khó quản lý. Thay vào đó, bạn có thể sử dụng `interface` hoặc `type` để mô tả props của component:

<Sandpack>

```tsx src/App.tsx active
interface MyButtonProps {
  /** The text to display inside the button */
  title: string;
  /** Whether the button can be interacted with */
  disabled: boolean;
}

function MyButton({ title, disabled }: MyButtonProps) {
  return (
    <button disabled={disabled}>{title}</button>
  );
}

export default function MyApp() {
  return (
    <div>
      <h1>Welcome to my app</h1>
      <MyButton title="I'm a disabled button" disabled={true}/>
    </div>
  );
}
```

```js src/App.js hidden
import AppTSX from "./App.tsx";
export default App = AppTSX;
```

</Sandpack>

Kiểu mô tả props của component có thể đơn giản hoặc phức tạp tùy theo nhu cầu của bạn, mặc dù chúng nên là kiểu object được mô tả bằng `type` hoặc `interface`. Bạn có thể tìm hiểu về cách TypeScript mô tả các object trong [Object Types](https://www.typescriptlang.org/docs/handbook/2/objects.html) nhưng bạn cũng có thể quan tâm đến việc sử dụng [Union Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#union-types) để mô tả một prop có thể là một trong vài kiểu khác nhau và hướng dẫn [Creating Types from Types](https://www.typescriptlang.org/docs/handbook/2/types-from-types.html) cho các trường hợp sử dụng nâng cao hơn.


## Ví dụ về Hooks {/*example-hooks*/}

Các định nghĩa kiểu từ `@types/react` bao gồm các kiểu cho các Hook tích hợp, vì vậy bạn có thể sử dụng chúng trong các component của mình mà không cần bất kỳ thiết lập bổ sung nào. Chúng được xây dựng để tính đến code bạn viết trong component, vì vậy bạn sẽ nhận được [kiểu được suy luận](https://www.typescriptlang.org/docs/handbook/type-inference.html) nhiều lần và lý tưởng là không cần xử lý chi tiết nhỏ của việc cung cấp các kiểu.

Tuy nhiên, chúng ta có thể xem xét một vài ví dụ về cách cung cấp kiểu cho các Hook.

### `useState` {/*typing-usestate*/}

[`useState` Hook](/reference/react/useState) sẽ tái sử dụng giá trị được truyền vào làm state ban đầu để xác định kiểu của giá trị nên là gì. Ví dụ:

```ts
// Suy luận kiểu là "boolean"
const [enabled, setEnabled] = useState(false);
```

Điều này sẽ gán kiểu `boolean` cho `enabled`, và `setEnabled` sẽ là một hàm chấp nhận đối số `boolean`, hoặc một hàm trả về `boolean`. Nếu bạn muốn cung cấp kiểu rõ ràng cho state, bạn có thể làm như vậy bằng cách cung cấp đối số kiểu cho lời gọi `useState`:

```ts
// Đặt rõ ràng kiểu là "boolean"
const [enabled, setEnabled] = useState<boolean>(false);
```

Điều này không hữu ích lắm trong trường hợp này, nhưng một trường hợp phổ biến mà bạn có thể muốn cung cấp kiểu là khi bạn có union type. Ví dụ, `status` ở đây có thể là một trong vài chuỗi khác nhau:

```ts
type Status = "idle" | "loading" | "success" | "error";

const [status, setStatus] = useState<Status>("idle");
```

Hoặc, như được khuyến nghị trong [Nguyên tắc cấu trúc state](/learn/choosing-the-state-structure#principles-for-structuring-state), bạn có thể nhóm state liên quan thành một object và mô tả các khả năng khác nhau qua kiểu object:

```ts
type RequestState =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success', data: any }
  | { status: 'error', error: Error };

const [requestState, setRequestState] = useState<RequestState>({ status: 'idle' });
```

### `useReducer` {/*typing-usereducer*/}

[`useReducer` Hook](/reference/react/useReducer) là một Hook phức tạp hơn nhận một hàm reducer và một state ban đầu. Các kiểu cho hàm reducer được suy luận từ state ban đầu. Bạn có thể tùy chọn cung cấp đối số kiểu cho lời gọi `useReducer` để cung cấp kiểu cho state, nhưng thường tốt hơn là đặt kiểu trên state ban đầu thay thế:

<Sandpack>

```tsx src/App.tsx active
import {useReducer} from 'react';

interface State {
   count: number
};

type CounterAction =
  | { type: "reset" }
  | { type: "setCount"; value: State["count"] }

const initialState: State = { count: 0 };

function stateReducer(state: State, action: CounterAction): State {
  switch (action.type) {
    case "reset":
      return initialState;
    case "setCount":
      return { ...state, count: action.value };
    default:
      throw new Error("Unknown action");
  }
}

export default function App() {
  const [state, dispatch] = useReducer(stateReducer, initialState);

  const addFive = () => dispatch({ type: "setCount", value: state.count + 5 });
  const reset = () => dispatch({ type: "reset" });

  return (
    <div>
      <h1>Welcome to my counter</h1>

      <p>Count: {state.count}</p>
      <button onClick={addFive}>Add 5</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}

```

```js src/App.js hidden
import AppTSX from "./App.tsx";
export default App = AppTSX;
```

</Sandpack>


Chúng ta đang sử dụng TypeScript ở một vài nơi quan trọng:

 - `interface State` mô tả hình dạng của state của reducer.
 - `type CounterAction` mô tả các action khác nhau có thể được dispatch đến reducer.
 - `const initialState: State` cung cấp kiểu cho state ban đầu, và cũng là kiểu được sử dụng bởi `useReducer` theo mặc định.
 - `stateReducer(state: State, action: CounterAction): State` đặt các kiểu cho đối số và giá trị trả về của hàm reducer.

Một cách thay thế rõ ràng hơn để đặt kiểu trên `initialState` là cung cấp đối số kiểu cho `useReducer`:

```ts
import { stateReducer, State } from './your-reducer-implementation';

const initialState = { count: 0 };

export default function App() {
  const [state, dispatch] = useReducer<State>(stateReducer, initialState);
}
```

### `useContext` {/*typing-usecontext*/}

[`useContext` Hook](/reference/react/useContext) là kỹ thuật để truyền dữ liệu xuống cây component mà không cần truyền props qua các component. Nó được sử dụng bằng cách tạo một provider component và thường bằng cách tạo một Hook để tiêu thụ giá trị trong một component con.

Kiểu của giá trị được cung cấp bởi context được suy luận từ giá trị được truyền vào lời gọi `createContext`:

<Sandpack>

```tsx src/App.tsx active
import { createContext, useContext, useState } from 'react';

type Theme = "light" | "dark" | "system";
const ThemeContext = createContext<Theme>("system");

const useGetTheme = () => useContext(ThemeContext);

export default function MyApp() {
  const [theme, setTheme] = useState<Theme>('light');

  return (
    <ThemeContext value={theme}>
      <MyComponent />
    </ThemeContext>
  )
}

function MyComponent() {
  const theme = useGetTheme();

  return (
    <div>
      <p>Current theme: {theme}</p>
    </div>
  )
}
```

```js src/App.js hidden
import AppTSX from "./App.tsx";
export default App = AppTSX;
```

</Sandpack>

Kỹ thuật này hoạt động khi bạn có giá trị mặc định có ý nghĩa - nhưng đôi khi có những trường hợp bạn không có, và trong những trường hợp đó `null` có thể cảm thấy hợp lý như giá trị mặc định. Tuy nhiên, để cho phép hệ thống kiểu hiểu code của bạn, bạn cần đặt rõ ràng `ContextShape | null` trên `createContext`.

Điều này gây ra vấn đề là bạn cần loại bỏ `| null` trong kiểu cho các consumer context. Khuyến nghị của chúng tôi là để Hook thực hiện kiểm tra runtime sự tồn tại của nó và ném lỗi khi không có:

```js {5, 16-20}
import { createContext, useContext, useState, useMemo } from 'react';

// This is a simpler example, but you can imagine a more complex object here
type ComplexObject = {
  kind: string
};

// The context is created with `| null` in the type, to accurately reflect the default value.
const Context = createContext<ComplexObject | null>(null);

// The `| null` will be removed via the check in the Hook.
const useGetComplexObject = () => {
  const object = useContext(Context);
  if (!object) { throw new Error("useGetComplexObject must be used within a Provider") }
  return object;
}

export default function MyApp() {
  const object = useMemo(() => ({ kind: "complex" }), []);

  return (
    <Context value={object}>
      <MyComponent />
    </Context>
  )
}

function MyComponent() {
  const object = useGetComplexObject();

  return (
    <div>
      <p>Current object: {object.kind}</p>
    </div>
  )
}
```

### `useMemo` {/*typing-usememo*/}

<Note>

[React Compiler](/learn/react-compiler) tự động ghi nhớ các giá trị và hàm, giảm nhu cầu gọi `useMemo` thủ công. Bạn có thể sử dụng compiler để xử lý memoization tự động.

</Note>

[`useMemo`](/reference/react/useMemo) Hook sẽ tạo/truy cập lại một giá trị đã được ghi nhớ từ một lời gọi hàm, chỉ chạy lại hàm khi các dependency được truyền vào tham số thứ 2 thay đổi. Kết quả của việc gọi Hook được suy luận từ giá trị trả về của hàm trong tham số đầu tiên. Bạn có thể rõ ràng hơn bằng cách cung cấp đối số kiểu cho Hook.

```ts
// Kiểu của visibleTodos được suy luận từ giá trị trả về của filterTodos
const visibleTodos = useMemo(() => filterTodos(todos, tab), [todos, tab]);
```


### `useCallback` {/*typing-usecallback*/}

<Note>

[React Compiler](/learn/react-compiler) tự động ghi nhớ các giá trị và hàm, giảm nhu cầu gọi `useCallback` thủ công. Bạn có thể sử dụng compiler để xử lý memoization tự động.

</Note>

[`useCallback`](/reference/react/useCallback) cung cấp một tham chiếu ổn định đến một hàm miễn là các dependency được truyền vào tham số thứ hai là giống nhau. Giống như `useMemo`, kiểu của hàm được suy luận từ giá trị trả về của hàm trong tham số đầu tiên, và bạn có thể rõ ràng hơn bằng cách cung cấp đối số kiểu cho Hook.


```ts
const handleClick = useCallback(() => {
  // ...
}, [todos]);
```

Khi làm việc trong chế độ strict của TypeScript, `useCallback` yêu cầu thêm kiểu cho các tham số trong callback của bạn. Điều này là vì kiểu của callback được suy luận từ giá trị trả về của hàm, và không có tham số, kiểu không thể được hiểu đầy đủ.

Tùy thuộc vào sở thích về phong cách code của bạn, bạn có thể sử dụng các hàm `*EventHandler` từ các kiểu React để cung cấp kiểu cho event handler cùng lúc với việc định nghĩa callback:

```ts
import { useState, useCallback } from 'react';

export default function Form() {
  const [value, setValue] = useState("Change me");

  const handleChange = useCallback<React.ChangeEventHandler<HTMLInputElement>>((event) => {
    setValue(event.currentTarget.value);
  }, [setValue])

  return (
    <>
      <input value={value} onChange={handleChange} />
      <p>Value: {value}</p>
    </>
  );
}
```

## Các kiểu hữu ích {/*useful-types*/}

Có một tập hợp khá rộng các kiểu đến từ package `@types/react`, đáng đọc khi bạn cảm thấy thoải mái với cách React và TypeScript tương tác. Bạn có thể tìm thấy chúng [trong thư mục React trong DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/react/index.d.ts). Chúng ta sẽ đề cập một vài kiểu phổ biến hơn ở đây.

### DOM Events {/*typing-dom-events*/}

Khi làm việc với các DOM event trong React, kiểu của event thường có thể được suy luận từ event handler. Tuy nhiên, khi bạn muốn trích xuất một hàm để truyền vào event handler, bạn sẽ cần đặt rõ ràng kiểu của event.

<Sandpack>

```tsx src/App.tsx active
import { useState } from 'react';

export default function Form() {
  const [value, setValue] = useState("Change me");

  function handleChange(event: React.ChangeEvent<HTMLInputElement>) {
    setValue(event.currentTarget.value);
  }

  return (
    <>
      <input value={value} onChange={handleChange} />
      <p>Value: {value}</p>
    </>
  );
}
```

```js src/App.js hidden
import AppTSX from "./App.tsx";
export default App = AppTSX;
```

</Sandpack>

Có nhiều loại event được cung cấp trong các kiểu React - danh sách đầy đủ có thể được tìm thấy [ở đây](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/b580df54c0819ec9df62b0835a315dd48b8594a9/types/react/index.d.ts#L1247C1-L1373) dựa trên [các event phổ biến nhất từ DOM](https://developer.mozilla.org/en-US/docs/Web/Events).

Khi xác định kiểu bạn đang tìm kiếm, trước tiên bạn có thể xem thông tin hover cho event handler bạn đang sử dụng, điều này sẽ hiển thị kiểu của event.

Nếu bạn cần sử dụng một event không có trong danh sách này, bạn có thể sử dụng kiểu `React.SyntheticEvent`, đây là kiểu cơ sở cho tất cả các event.

### Children {/*typing-children*/}

Có hai con đường phổ biến để mô tả các children của một component. Đầu tiên là sử dụng kiểu `React.ReactNode`, đây là union của tất cả các kiểu có thể được truyền vào làm children trong JSX:

```ts
interface ModalRendererProps {
  title: string;
  children: React.ReactNode;
}
```

Đây là một định nghĩa rất rộng về children. Thứ hai là sử dụng kiểu `React.ReactElement`, chỉ là các JSX element và không phải các primitive JavaScript như chuỗi hoặc số:

```ts
interface ModalRendererProps {
  title: string;
  children: React.ReactElement;
}
```

Lưu ý, bạn không thể sử dụng TypeScript để mô tả rằng children là một kiểu nhất định của các JSX element, vì vậy bạn không thể sử dụng hệ thống kiểu để mô tả một component chỉ chấp nhận children `<li>`.

Bạn có thể thấy ví dụ về cả `React.ReactNode` và `React.ReactElement` với type-checker trong [TypeScript playground này](https://www.typescriptlang.org/play?#code/JYWwDg9gTgLgBAJQKYEMDG8BmUIjgIilQ3wChSB6CxYmAOmXRgDkIATJOdNJMGAZzgwAFpxAR+8YADswAVwGkZMJFEzpOjDKw4AFHGEEBvUnDhphwADZsi0gFw0mDWjqQBuUgF9yaCNMlENzgAXjgACjADfkctFnYkfQhDAEpQgD44AB42YAA3dKMo5P46C2tbJGkvLIpcgt9-QLi3AEEwMFCItJDMrPTTbIQ3dKywdIB5aU4kKyQQKpha8drhhIGzLLWODbNs3b3s8YAxKBQAcwXpAThMaGWDvbH0gFloGbmrgQfBzYpd1YjQZbEYARkB6zMwO2SHSAAlZlYIBCdtCRkZpHIrFYahQYQD8UYYFA5EhcfjyGYqHAXnJAsIUHlOOUbHYhMIIHJzsI0Qk4P9SLUBuRqXEXEwAKKfRZcNA8PiCfxWACecAAUgBlAAacFm80W-CU11U6h4TgwUv11yShjgJjMLMqDnN9Dilq+nh8pD8AXgCHdMrCkWisVoAet0R6fXqhWKhjKllZVVxMcavpd4Zg7U6Qaj+2hmdG4zeRF10uu-Aeq0LBfLMEe-V+T2L7zLVu+FBWLdLeq+lc7DYFf39deFVOotMCACNOCh1dq219a+30uC8YWoZsRyuEdjkevR8uvoVMdjyTWt4WiSSydXD4NqZP4AymeZE072ZzuUeZQKheQgA).

### Style Props {/*typing-style-props*/}

Khi sử dụng inline styles trong React, bạn có thể sử dụng `React.CSSProperties` để mô tả object được truyền vào prop `style`. Kiểu này là union của tất cả các thuộc tính CSS có thể có, và là cách tốt để đảm bảo bạn đang truyền các thuộc tính CSS hợp lệ vào prop `style`, và để nhận auto-complete trong trình soạn thảo của bạn.

```ts
interface MyComponentProps {
  style: React.CSSProperties;
}
```

## Học thêm {/*further-learning*/}

Hướng dẫn này đã đề cập đến những kiến thức cơ bản về việc sử dụng TypeScript với React, nhưng còn nhiều điều cần học hơn.
Các trang API riêng lẻ trên tài liệu có thể chứa tài liệu chuyên sâu hơn về cách sử dụng chúng với TypeScript.

Chúng tôi khuyến nghị các tài nguyên sau:

 - [The TypeScript handbook](https://www.typescriptlang.org/docs/handbook/) là tài liệu chính thức của TypeScript, và đề cập đến hầu hết các tính năng ngôn ngữ chính.

 - [The TypeScript release notes](https://devblogs.microsoft.com/typescript/) đề cập đến các tính năng mới chuyên sâu.

 - [React TypeScript Cheatsheet](https://react-typescript-cheatsheet.netlify.app/) là một cheatsheet được cộng đồng duy trì để sử dụng TypeScript với React, đề cập đến nhiều trường hợp edge case hữu ích và cung cấp độ rộng hơn tài liệu này.

 - [TypeScript Community Discord](https://discord.com/invite/typescript) là nơi tuyệt vời để đặt câu hỏi và nhận giúp đỡ về các vấn đề TypeScript và React.
