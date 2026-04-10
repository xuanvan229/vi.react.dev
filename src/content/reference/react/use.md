---
title: use
---

<Intro>

`use` là một React API cho phép bạn đọc giá trị của một resource như [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) hoặc [context](/learn/passing-data-deeply-with-context).

```js
const value = use(resource);
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `use(resource)` {/*use*/}

Gọi `use` trong component của bạn để đọc giá trị của một resource như [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) hoặc [context](/learn/passing-data-deeply-with-context).

```jsx
import { use } from 'react';

function MessageComponent({ messagePromise }) {
  const message = use(messagePromise);
  const theme = use(ThemeContext);
  // ...
```

Không giống như React Hooks, `use` có thể được gọi bên trong các vòng lặp và câu lệnh điều kiện như `if`. Giống như React Hooks, hàm gọi `use` phải là một Component hoặc Hook.

Khi được gọi với Promise, API `use` tích hợp với [`Suspense`](/reference/react/Suspense) và [Error Boundaries](/reference/react/Component#catching-rendering-errors-with-an-error-boundary). Component gọi `use` *suspend* trong khi Promise được truyền cho `use` đang pending. Nếu component gọi `use` được bọc trong một Suspense boundary, fallback sẽ được hiển thị. Khi Promise được resolve, Suspense fallback được thay thế bởi các component đã render sử dụng dữ liệu được trả về bởi API `use`. Nếu Promise được truyền cho `use` bị reject, fallback của Error Boundary gần nhất sẽ được hiển thị.

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `resource`: đây là nguồn dữ liệu bạn muốn đọc giá trị. Một resource có thể là [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) hoặc một [context](/learn/passing-data-deeply-with-context).

#### Giá trị trả về {/*returns*/}

API `use` trả về giá trị được đọc từ resource như giá trị đã được resolve của [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) hoặc [context](/learn/passing-data-deeply-with-context).

#### Lưu ý {/*caveats*/}

* API `use` phải được gọi bên trong một Component hoặc một hàm Hook.
* Khi fetch dữ liệu trong [Server Component](/reference/rsc/server-components), hãy ưu tiên `async` và `await` hơn `use`. `async` và `await` tiếp tục rendering từ điểm `await` được gọi, trong khi `use` re-render component sau khi dữ liệu được resolve.
* Ưu tiên tạo Promise trong [Server Components](/reference/rsc/server-components) và truyền chúng cho [Client Components](/reference/rsc/use-client) hơn là tạo Promise trong Client Components. Các Promise được tạo trong Client Components được tạo lại trên mỗi lần render. Các Promise được truyền từ Server Component đến Client Component ổn định qua các lần re-render. [Xem ví dụ này](#streaming-data-from-server-to-client).

---

## Cách sử dụng {/*usage*/}

### Đọc context với `use` {/*reading-context-with-use*/}

Khi một [context](/learn/passing-data-deeply-with-context) được truyền cho `use`, nó hoạt động tương tự như [`useContext`](/reference/react/useContext). Trong khi `useContext` phải được gọi ở cấp cao nhất của component, `use` có thể được gọi bên trong các điều kiện như `if` và vòng lặp như `for`. `use` được ưu tiên hơn `useContext` vì nó linh hoạt hơn.

```js [[2, 4, "theme"], [1, 4, "ThemeContext"]]
import { use } from 'react';

function Button() {
  const theme = use(ThemeContext);
  // ...
```

`use` trả về <CodeStep step={2}>giá trị context</CodeStep> cho <CodeStep step={1}>context</CodeStep> bạn đã truyền. Để xác định giá trị context, React tìm kiếm cây component và tìm **context provider gần nhất ở phía trên** cho context cụ thể đó.

Để truyền context cho một `Button`, hãy bọc nó hoặc một trong các component cha của nó vào context provider tương ứng.

```js [[1, 3, "ThemeContext"], [2, 3, "\"dark\""], [1, 5, "ThemeContext"]]
function MyPage() {
  return (
    <ThemeContext value="dark">
      <Form />
    </ThemeContext>
  );
}

function Form() {
  // ... render các nút bên trong ...
}
```

Không quan trọng có bao nhiêu lớp component giữa provider và `Button`. Khi một `Button` *ở bất cứ đâu* bên trong `Form` gọi `use(ThemeContext)`, nó sẽ nhận `"dark"` như giá trị.

Không giống như [`useContext`](/reference/react/useContext), <CodeStep step={2}>`use`</CodeStep> có thể được gọi trong các điều kiện và vòng lặp như <CodeStep step={1}>`if`</CodeStep>.

```js [[1, 2, "if"], [2, 3, "use"]]
function HorizontalRule({ show }) {
  if (show) {
    const theme = use(ThemeContext);
    return <hr className={theme} />;
  }
  return false;
}
```

<CodeStep step={2}>`use`</CodeStep> được gọi từ bên trong câu lệnh <CodeStep step={1}>`if`</CodeStep>, cho phép bạn đọc giá trị từ Context một cách có điều kiện.

<Pitfall>

Giống như `useContext`, `use(context)` luôn tìm kiếm context provider gần nhất *ở phía trên* component gọi nó. Nó tìm kiếm lên trên và **không** xem xét các context provider trong component mà bạn đang gọi `use(context)`.

</Pitfall>

<Sandpack>

```js
import { createContext, use } from 'react';

const ThemeContext = createContext(null);

export default function MyApp() {
  return (
    <ThemeContext value="dark">
      <Form />
    </ThemeContext>
  )
}

function Form() {
  return (
    <Panel title="Welcome">
      <Button show={true}>Sign up</Button>
      <Button show={false}>Log in</Button>
    </Panel>
  );
}

function Panel({ title, children }) {
  const theme = use(ThemeContext);
  const className = 'panel-' + theme;
  return (
    <section className={className}>
      <h1>{title}</h1>
      {children}
    </section>
  )
}

function Button({ show, children }) {
  if (show) {
    const theme = use(ThemeContext);
    const className = 'button-' + theme;
    return (
      <button className={className}>
        {children}
      </button>
    );
  }
  return false
}
```

```css
.panel-light,
.panel-dark {
  border: 1px solid black;
  border-radius: 4px;
  padding: 20px;
}
.panel-light {
  color: #222;
  background: #fff;
}

.panel-dark {
  color: #fff;
  background: rgb(23, 32, 42);
}

.button-light,
.button-dark {
  border: 1px solid #777;
  padding: 5px;
  margin-right: 10px;
  margin-top: 10px;
}

.button-dark {
  background: #222;
  color: #fff;
}

.button-light {
  background: #fff;
  color: #222;
}
```

</Sandpack>

### Streaming dữ liệu từ server đến client {/*streaming-data-from-server-to-client*/}

Dữ liệu có thể được stream từ server đến client bằng cách truyền Promise như một prop từ <CodeStep step={1}>Server Component</CodeStep> đến <CodeStep step={2}>Client Component</CodeStep>.

```js [[1, 4, "App"], [2, 2, "Message"], [3, 7, "Suspense"], [4, 8, "messagePromise", 30], [4, 5, "messagePromise"]]
import { fetchMessage } from './lib.js';
import { Message } from './message.js';

export default function App() {
  const messagePromise = fetchMessage();
  return (
    <Suspense fallback={<p>waiting for message...</p>}>
      <Message messagePromise={messagePromise} />
    </Suspense>
  );
}
```

<CodeStep step={2}>Client Component</CodeStep> sau đó lấy <CodeStep step={4}>Promise nhận được như một prop</CodeStep> và truyền nó cho API <CodeStep step={5}>`use`</CodeStep>. Điều này cho phép <CodeStep step={2}>Client Component</CodeStep> đọc giá trị từ <CodeStep step={4}>Promise</CodeStep> ban đầu được tạo bởi Server Component.

```js [[2, 6, "Message"], [4, 6, "messagePromise"], [4, 7, "messagePromise"], [5, 7, "use"]]
// message.js
'use client';

import { use } from 'react';

export function Message({ messagePromise }) {
  const messageContent = use(messagePromise);
  return <p>Here is the message: {messageContent}</p>;
}
```
Vì <CodeStep step={2}>`Message`</CodeStep> được bọc trong <CodeStep step={3}>[`Suspense`](/reference/react/Suspense)</CodeStep>, fallback sẽ được hiển thị cho đến khi Promise được resolve. Khi Promise được resolve, giá trị sẽ được đọc bởi API <CodeStep step={5}>`use`</CodeStep> và component <CodeStep step={2}>`Message`</CodeStep> sẽ thay thế Suspense fallback.

<Sandpack>

```js src/message.js active
"use client";

import { use, Suspense } from "react";

function Message({ messagePromise }) {
  const messageContent = use(messagePromise);
  return <p>Here is the message: {messageContent}</p>;
}

export function MessageContainer({ messagePromise }) {
  return (
    <Suspense fallback={<p>⌛Downloading message...</p>}>
      <Message messagePromise={messagePromise} />
    </Suspense>
  );
}
```

```js src/App.js hidden
import { useState } from "react";
import { MessageContainer } from "./message.js";

function fetchMessage() {
  return new Promise((resolve) => setTimeout(resolve, 1000, "⚛️"));
}

export default function App() {
  const [messagePromise, setMessagePromise] = useState(null);
  const [show, setShow] = useState(false);
  function download() {
    setMessagePromise(fetchMessage());
    setShow(true);
  }

  if (show) {
    return <MessageContainer messagePromise={messagePromise} />;
  } else {
    return <button onClick={download}>Download message</button>;
  }
}
```

```js src/index.js hidden
import React, { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

// TODO: update this example to use
// the Codesandbox Server Component
// demo environment once it is created
import App from './App';

const root = createRoot(document.getElementById('root'));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

</Sandpack>

<Note>

Khi truyền Promise từ Server Component đến Client Component, giá trị resolved của nó phải có thể serialize để truyền giữa server và client. Các kiểu dữ liệu như hàm không thể serialize và không thể là giá trị resolved của Promise như vậy.

</Note>


<DeepDive>

#### Tôi có nên resolve Promise trong Server hay Client Component không? {/*resolve-promise-in-server-or-client-component*/}

Một Promise có thể được truyền từ Server Component đến Client Component và được resolve trong Client Component với API `use`. Bạn cũng có thể resolve Promise trong Server Component với `await` và truyền dữ liệu cần thiết cho Client Component như một prop.

```js
export default async function App() {
  const messageContent = await fetchMessage();
  return <Message messageContent={messageContent} />
}
```

Nhưng sử dụng `await` trong [Server Component](/reference/rsc/server-components) sẽ chặn việc render của nó cho đến khi câu lệnh `await` hoàn thành. Truyền Promise từ Server Component đến Client Component ngăn Promise chặn việc render của Server Component.

</DeepDive>

### Xử lý các Promise bị reject {/*dealing-with-rejected-promises*/}

Trong một số trường hợp, Promise được truyền cho `use` có thể bị reject. Bạn có thể xử lý các Promise bị reject bằng cách:

1. [Hiển thị lỗi cho người dùng với Error Boundary.](#displaying-an-error-to-users-with-error-boundary)
2. [Cung cấp một giá trị thay thế với `Promise.catch`](#providing-an-alternative-value-with-promise-catch)

<Pitfall>
`use` không thể được gọi trong khối try-catch. Thay vì khối try-catch [hãy bọc component của bạn trong Error Boundary](#displaying-an-error-to-users-with-error-boundary), hoặc [cung cấp một giá trị thay thế để sử dụng với method `.catch` của Promise](#providing-an-alternative-value-with-promise-catch).
</Pitfall>

#### Hiển thị lỗi cho người dùng với Error Boundary {/*displaying-an-error-to-users-with-error-boundary*/}

Nếu bạn muốn hiển thị lỗi cho người dùng khi Promise bị reject, bạn có thể sử dụng [Error Boundary](/reference/react/Component#catching-rendering-errors-with-an-error-boundary). Để sử dụng Error Boundary, hãy bọc component nơi bạn đang gọi API `use` trong một Error Boundary. Nếu Promise được truyền cho `use` bị reject, fallback cho Error Boundary sẽ được hiển thị.

<Sandpack>

```js src/message.js active
"use client";

import { use, Suspense } from "react";
import { ErrorBoundary } from "react-error-boundary";

export function MessageContainer({ messagePromise }) {
  return (
    <ErrorBoundary fallback={<p>⚠️Something went wrong</p>}>
      <Suspense fallback={<p>⌛Downloading message...</p>}>
        <Message messagePromise={messagePromise} />
      </Suspense>
    </ErrorBoundary>
  );
}

function Message({ messagePromise }) {
  const content = use(messagePromise);
  return <p>Here is the message: {content}</p>;
}
```

```js src/App.js hidden
import { useState } from "react";
import { MessageContainer } from "./message.js";

function fetchMessage() {
  return new Promise((resolve, reject) => setTimeout(reject, 1000));
}

export default function App() {
  const [messagePromise, setMessagePromise] = useState(null);
  const [show, setShow] = useState(false);
  function download() {
    setMessagePromise(fetchMessage());
    setShow(true);
  }

  if (show) {
    return <MessageContainer messagePromise={messagePromise} />;
  } else {
    return <button onClick={download}>Download message</button>;
  }
}
```

```js src/index.js hidden
import React, { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

// TODO: update this example to use
// the Codesandbox Server Component
// demo environment once it is created
import App from './App';

const root = createRoot(document.getElementById('root'));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

```json package.json hidden
{
  "dependencies": {
    "react": "19.0.0",
    "react-dom": "19.0.0",
    "react-scripts": "^5.0.0",
    "react-error-boundary": "4.0.3"
  },
  "main": "/index.js"
}
```
</Sandpack>

#### Cung cấp giá trị thay thế với `Promise.catch` {/*providing-an-alternative-value-with-promise-catch*/}

Nếu bạn muốn cung cấp một giá trị thay thế khi Promise được truyền cho `use` bị reject, bạn có thể sử dụng method <CodeStep step={1}>[`catch`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch)</CodeStep> của Promise.

```js [[1, 6, "catch"],[2, 7, "return"]]
import { Message } from './message.js';

export default function App() {
  const messagePromise = new Promise((resolve, reject) => {
    reject();
  }).catch(() => {
    return "no new message found.";
  });

  return (
    <Suspense fallback={<p>waiting for message...</p>}>
      <Message messagePromise={messagePromise} />
    </Suspense>
  );
}
```

Để sử dụng method <CodeStep step={1}>`catch`</CodeStep> của Promise, hãy gọi <CodeStep step={1}>`catch`</CodeStep> trên object Promise. <CodeStep step={1}>`catch`</CodeStep> nhận một đối số duy nhất: một hàm nhận thông báo lỗi như đối số. Bất cứ điều gì được <CodeStep step={2}>trả về</CodeStep> bởi hàm được truyền cho <CodeStep step={1}>`catch`</CodeStep> sẽ được sử dụng như giá trị đã resolved của Promise.

---

## Xử lý sự cố {/*troubleshooting*/}

### "Suspense Exception: This is not a real error!" {/*suspense-exception-error*/}

Bạn đang gọi `use` bên ngoài một hàm React Component hoặc Hook, hoặc gọi `use` trong khối try–catch. Nếu bạn đang gọi `use` bên trong một khối try–catch, hãy bọc component của bạn trong một Error Boundary, hoặc gọi `catch` của Promise để bắt lỗi và resolve Promise với một giá trị khác. [Xem các ví dụ này](#dealing-with-rejected-promises).

Nếu bạn đang gọi `use` bên ngoài một React Component hoặc hàm Hook, hãy chuyển lời gọi `use` đến một React Component hoặc hàm Hook.

```jsx
function MessageComponent({messagePromise}) {
  function download() {
    // ❌ hàm gọi `use` không phải là Component hoặc Hook
    const message = use(messagePromise);
    // ...
```

Thay vào đó, hãy gọi `use` bên ngoài bất kỳ closures nào của component, nơi hàm gọi `use` là một Component hoặc Hook.

```jsx
function MessageComponent({messagePromise}) {
  // ✅ `use` đang được gọi từ một component.
  const message = use(messagePromise);
  // ...
```
