---
title: hydrateRoot
---

<Intro>

`hydrateRoot` cho phép bạn hiển thị các component React bên trong một DOM node của trình duyệt mà nội dung HTML đã được tạo trước đó bởi [`react-dom/server`.](/reference/react-dom/server)

```js
const root = hydrateRoot(domNode, reactNode, options?)
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `hydrateRoot(domNode, reactNode, options?)` {/*hydrateroot*/}

Gọi `hydrateRoot` để "gắn kết" React với HTML hiện có đã được render bởi React trong môi trường server.

```js
import { hydrateRoot } from 'react-dom/client';

const domNode = document.getElementById('root');
const root = hydrateRoot(domNode, reactNode);
```

React sẽ gắn kết vào HTML tồn tại bên trong `domNode`, và tiếp quản việc quản lý DOM bên trong nó. Một ứng dụng được xây dựng hoàn toàn bằng React thường chỉ có một lệnh gọi `hydrateRoot` với component root của nó.

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `domNode`: Một [phần tử DOM](https://developer.mozilla.org/en-US/docs/Web/API/Element) đã được render như phần tử root trên server.

* `reactNode`: "React node" được sử dụng để render HTML hiện có. Thường sẽ là một đoạn JSX như `<App />` đã được render với phương thức `ReactDOM Server` như `renderToPipeableStream(<App />)`.

* **tùy chọn** `options`: Một object với các tùy chọn cho React root này.

  * **tùy chọn** `onCaughtError`: Callback được gọi khi React bắt được lỗi trong Error Boundary. Được gọi với `error` được bắt bởi Error Boundary, và một object `errorInfo` chứa `componentStack`.
  * **tùy chọn** `onUncaughtError`: Callback được gọi khi một lỗi được ném ra và không được bắt bởi Error Boundary. Được gọi với `error` đã được ném và một object `errorInfo` chứa `componentStack`.
  * **tùy chọn** `onRecoverableError`: Callback được gọi khi React tự động phục hồi từ lỗi. Được gọi với `error` mà React ném ra, và một object `errorInfo` chứa `componentStack`. Một số lỗi có thể phục hồi có thể bao gồm nguyên nhân lỗi gốc dưới dạng `error.cause`.
  * **tùy chọn** `identifierPrefix`: Một tiền tố chuỗi mà React sử dụng cho các ID được tạo bởi [`useId`.](/reference/react/useId) Hữu ích để tránh xung đột khi sử dụng nhiều root trên cùng một trang. Phải là cùng tiền tố đã sử dụng trên server.


#### Giá trị trả về {/*returns*/}

`hydrateRoot` trả về một object với hai phương thức: [`render`](#root-render) và [`unmount`.](#root-unmount)

#### Lưu ý {/*caveats*/}

* `hydrateRoot()` yêu cầu nội dung được render phải giống hệt với nội dung được render từ server. Bạn nên coi sự không khớp là bug và sửa chúng.
* Trong chế độ development, React cảnh báo về sự không khớp trong quá trình hydration. Không có đảm bảo rằng sự khác biệt thuộc tính sẽ được vá trong trường hợp không khớp. Điều này quan trọng vì lý do hiệu suất vì trong hầu hết ứng dụng, sự không khớp là hiếm, và do đó việc xác nhận tất cả markup sẽ quá tốn kém.
* Bạn có thể chỉ có một lệnh gọi `hydrateRoot` trong ứng dụng. Nếu bạn sử dụng framework, nó có thể thực hiện lệnh gọi này cho bạn.
* Nếu ứng dụng của bạn được render phía client mà không có HTML đã render sẵn, việc sử dụng `hydrateRoot()` không được hỗ trợ. Hãy sử dụng [`createRoot()`](/reference/react-dom/client/createRoot) thay thế.

---

### `root.render(reactNode)` {/*root-render*/}

Gọi `root.render` để cập nhật một component React bên trong một React root đã được hydrate cho một phần tử DOM của trình duyệt.

```js
root.render(<App />);
```

React sẽ cập nhật `<App />` trong `root` đã được hydrate.

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*root-render-parameters*/}

* `reactNode`: Một "React node" mà bạn muốn cập nhật. Thường sẽ là một đoạn JSX như `<App />`, nhưng bạn cũng có thể truyền một phần tử React được tạo với [`createElement()`](/reference/react/createElement), một chuỗi, một số, `null`, hoặc `undefined`.


#### Giá trị trả về {/*root-render-returns*/}

`root.render` trả về `undefined`.

#### Lưu ý {/*root-render-caveats*/}

* Nếu bạn gọi `root.render` trước khi root hoàn thành hydration, React sẽ xóa nội dung HTML hiện có được render từ server và chuyển toàn bộ root sang render phía client.

---

### `root.unmount()` {/*root-unmount*/}

Gọi `root.unmount` để hủy cây đã render bên trong một React root.

```js
root.unmount();
```

Một ứng dụng được xây dựng hoàn toàn bằng React thường không có bất kỳ lệnh gọi `root.unmount` nào.

Điều này chủ yếu hữu ích nếu DOM node của React root (hoặc bất kỳ tổ tiên nào của nó) có thể bị xóa khỏi DOM bởi code khác. Ví dụ, hãy tưởng tượng một panel tab jQuery xóa các tab không hoạt động khỏi DOM. Nếu một tab bị xóa, mọi thứ bên trong nó (bao gồm cả các React root bên trong) cũng sẽ bị xóa khỏi DOM. Bạn cần thông báo cho React "dừng" quản lý nội dung của root đã bị xóa bằng cách gọi `root.unmount`. Nếu không, các component bên trong root đã bị xóa sẽ không dọn dẹp và giải phóng tài nguyên như subscription.

Gọi `root.unmount` sẽ unmount tất cả các component trong root và "tách" React khỏi DOM node root, bao gồm việc xóa bất kỳ event handler hoặc state nào trong cây.


#### Tham số {/*root-unmount-parameters*/}

`root.unmount` không nhận bất kỳ tham số nào.


#### Giá trị trả về {/*root-unmount-returns*/}

`root.unmount` trả về `undefined`.

#### Lưu ý {/*root-unmount-caveats*/}

* Gọi `root.unmount` sẽ unmount tất cả các component trong cây và "tách" React khỏi DOM node root.

* Sau khi bạn gọi `root.unmount`, bạn không thể gọi `root.render` lại trên root. Cố gắng gọi `root.render` trên một root đã unmount sẽ ném lỗi "Cannot update an unmounted root".

---

## Cách sử dụng {/*usage*/}

### Hydrate HTML được render từ server {/*hydrating-server-rendered-html*/}

Nếu HTML của ứng dụng được tạo bởi [`react-dom/server`](/reference/react-dom/client/createRoot), bạn cần *hydrate* nó trên client.

```js [[1, 3, "document.getElementById('root')"], [2, 3, "<App />"]]
import { hydrateRoot } from 'react-dom/client';

hydrateRoot(document.getElementById('root'), <App />);
```

Điều này sẽ hydrate HTML từ server bên trong <CodeStep step={1}>DOM node của trình duyệt</CodeStep> với <CodeStep step={2}>component React</CodeStep> cho ứng dụng của bạn. Thường thì bạn sẽ làm điều này một lần khi khởi động. Nếu bạn sử dụng framework, nó có thể làm điều này ngầm cho bạn.

Để hydrate ứng dụng, React sẽ "gắn kết" logic của các component vào HTML ban đầu được tạo từ server. Hydration biến bản HTML ban đầu từ server thành một ứng dụng tương tác đầy đủ chạy trong trình duyệt.

<Sandpack>

```html public/index.html
<!--
  HTML content inside <div id="root">...</div>
  was generated from App by react-dom/server.
-->
<div id="root"><h1>Hello, world!</h1><button>You clicked me <!-- -->0<!-- --> times</button></div>
```

```js src/index.js active
import './styles.css';
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(
  document.getElementById('root'),
  <App />
);
```

```js src/App.js
import { useState } from 'react';

export default function App() {
  return (
    <>
      <h1>Hello, world!</h1>
      <Counter />
    </>
  );
}

function Counter() {
  const [count, setCount] = useState(0);
  return (
    <button onClick={() => setCount(count + 1)}>
      You clicked me {count} times
    </button>
  );
}
```

</Sandpack>

Bạn không cần gọi `hydrateRoot` lại hoặc gọi nó ở nhiều nơi. Từ thời điểm này, React sẽ quản lý DOM của ứng dụng. Để cập nhật UI, các component của bạn sẽ [sử dụng state](/reference/react/useState) thay thế.

<Pitfall>

Cây React bạn truyền cho `hydrateRoot` cần tạo ra **cùng kết quả** như trên server.

Điều này quan trọng cho trải nghiệm người dùng. Người dùng sẽ dành một khoảng thời gian nhìn vào HTML từ server trước khi code JavaScript tải. Server rendering tạo ảo giác rằng ứng dụng tải nhanh hơn bằng cách hiển thị bản HTML của kết quả đầu ra. Đột ngột hiển thị nội dung khác sẽ phá vỡ ảo giác đó. Đây là lý do tại sao kết quả render từ server phải khớp với kết quả render ban đầu trên client.

Các nguyên nhân phổ biến nhất dẫn đến lỗi hydration bao gồm:

* Khoảng trắng thừa (như dấu xuống dòng) xung quanh HTML do React tạo bên trong root node.
* Sử dụng các kiểm tra như `typeof window !== 'undefined'` trong logic render.
* Sử dụng các API chỉ có trên trình duyệt như [`window.matchMedia`](https://developer.mozilla.org/en-US/docs/Web/API/Window/matchMedia) trong logic render.
* Render dữ liệu khác nhau trên server và client.

React phục hồi từ một số lỗi hydration, nhưng **bạn phải sửa chúng như các bug khác.** Trong trường hợp tốt nhất, chúng sẽ dẫn đến chậm hơn; trong trường hợp xấu nhất, các event handler có thể được gắn vào sai phần tử.

</Pitfall>

---

### Hydrate toàn bộ tài liệu {/*hydrating-an-entire-document*/}

Các ứng dụng được xây dựng hoàn toàn bằng React có thể render toàn bộ tài liệu dưới dạng JSX, bao gồm tag [`<html>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/html):

```js {3,13}
function App() {
  return (
    <html>
      <head>
        <meta charSet="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <link rel="stylesheet" href="/styles.css"></link>
        <title>My app</title>
      </head>
      <body>
        <Router />
      </body>
    </html>
  );
}
```

Để hydrate toàn bộ tài liệu, truyền biến toàn cục [`document`](https://developer.mozilla.org/en-US/docs/Web/API/Window/document) làm đối số đầu tiên cho `hydrateRoot`:

```js {4}
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(document, <App />);
```

---

### Tắt cảnh báo lỗi hydration không thể tránh {/*suppressing-unavoidable-hydration-mismatch-errors*/}

Nếu thuộc tính hoặc nội dung văn bản của một phần tử đơn lẻ không thể tránh khỏi sự khác biệt giữa server và client (ví dụ, timestamp), bạn có thể tắt cảnh báo không khớp hydration.

Để tắt cảnh báo hydration trên một phần tử, thêm `suppressHydrationWarning={true}`:

<Sandpack>

```html public/index.html
<!--
  HTML content inside <div id="root">...</div>
  was generated from App by react-dom/server.
-->
<div id="root"><h1>Current Date: <!-- -->01/01/2020</h1></div>
```

```js src/index.js
import './styles.css';
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(document.getElementById('root'), <App />);
```

```js src/App.js active
export default function App() {
  return (
    <h1 suppressHydrationWarning={true}>
      Current Date: {new Date().toLocaleDateString()}
    </h1>
  );
}
```

</Sandpack>

Điều này chỉ hoạt động ở một cấp độ, và được dự định là một giải pháp thoát hiểm. Đừng lạm dụng nó. React sẽ **không** cố gắng vá nội dung văn bản không khớp.

---

### Xử lý nội dung khác nhau giữa client và server {/*handling-different-client-and-server-content*/}

Nếu bạn cố tình cần render nội dung khác nhau trên server và client, bạn có thể thực hiện render hai lần. Các component render nội dung khác nhau trên client có thể đọc một [biến state](/reference/react/useState) như `isClient`, mà bạn có thể đặt thành `true` trong một [Effect](/reference/react/useEffect):

<Sandpack>

```html public/index.html
<!--
  HTML content inside <div id="root">...</div>
  was generated from App by react-dom/server.
-->
<div id="root"><h1>Is Server</h1></div>
```

```js src/index.js
import './styles.css';
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(document.getElementById('root'), <App />);
```

{/* kind of an edge case, seems fine to use this hack here */}
```js {expectedErrors: {'react-compiler': [7]}} src/App.js active
import { useState, useEffect } from "react";

export default function App() {
  const [isClient, setIsClient] = useState(false);

  useEffect(() => {
    setIsClient(true);
  }, []);

  return (
    <h1>
      {isClient ? 'Is Client' : 'Is Server'}
    </h1>
  );
}
```

</Sandpack>

Bằng cách này, lần render ban đầu sẽ render cùng nội dung với server, tránh sự không khớp, nhưng một lần render bổ sung sẽ xảy ra đồng bộ ngay sau hydration.

<Pitfall>

Cách tiếp cận này làm cho hydration chậm hơn vì các component phải render hai lần. Hãy lưu ý đến trải nghiệm người dùng trên các kết nối chậm. Code JavaScript có thể tải muộn hơn đáng kể so với render HTML ban đầu, vì vậy việc render UI khác ngay sau hydration cũng có thể gây khó chịu cho người dùng.

</Pitfall>

---

### Cập nhật component root đã được hydrate {/*updating-a-hydrated-root-component*/}

Sau khi root đã hoàn thành hydration, bạn có thể gọi [`root.render`](#root-render) để cập nhật component React root. **Khác với [`createRoot`](/reference/react-dom/client/createRoot), bạn thường không cần làm điều này vì nội dung ban đầu đã được render dưới dạng HTML.**

Nếu bạn gọi `root.render` tại một thời điểm nào đó sau hydration, và cấu trúc cây component khớp với những gì đã render trước đó, React sẽ [bảo toàn state.](/learn/preserving-and-resetting-state) Lưu ý rằng bạn có thể gõ vào input, nghĩa là các cập nhật từ các lệnh gọi `render` lặp lại mỗi giây trong ví dụ này không phá hủy dữ liệu:

<Sandpack>

```html public/index.html
<!--
  All HTML content inside <div id="root">...</div> was
  generated by rendering <App /> with react-dom/server.
-->
<div id="root"><h1>Hello, world! <!-- -->0</h1><input placeholder="Type something here"/></div>
```

```js src/index.js active
import { hydrateRoot } from 'react-dom/client';
import './styles.css';
import App from './App.js';

const root = hydrateRoot(
  document.getElementById('root'),
  <App counter={0} />
);

let i = 0;
setInterval(() => {
  root.render(<App counter={i} />);
  i++;
}, 1000);
```

```js src/App.js
export default function App({counter}) {
  return (
    <>
      <h1>Hello, world! {counter}</h1>
      <input placeholder="Type something here" />
    </>
  );
}
```

</Sandpack>

Việc gọi [`root.render`](#root-render) trên một root đã được hydrate là không phổ biến. Thường thì bạn sẽ [cập nhật state](/reference/react/useState) bên trong một trong các component thay thế.

### Ghi log lỗi trong production {/*error-logging-in-production*/}

Theo mặc định, React sẽ ghi log tất cả lỗi ra console. Để triển khai báo cáo lỗi riêng, bạn có thể cung cấp các tùy chọn xử lý lỗi tùy chọn cho root `onUncaughtError`, `onCaughtError` và `onRecoverableError`:

```js [[1, 7, "onCaughtError"], [2, 7, "error", 1], [3, 7, "errorInfo"], [4, 11, "componentStack", 15]]
import { hydrateRoot } from "react-dom/client";
import App from "./App.js";
import { reportCaughtError } from "./reportError";

const container = document.getElementById("root");
const root = hydrateRoot(container, <App />, {
  onCaughtError: (error, errorInfo) => {
    if (error.message !== "Known error") {
      reportCaughtError({
        error,
        componentStack: errorInfo.componentStack,
      });
    }
  },
});
```

Tùy chọn <CodeStep step={1}>onCaughtError</CodeStep> là một hàm được gọi với hai đối số:

1. <CodeStep step={2}>error</CodeStep> đã được ném ra.
2. Một object <CodeStep step={3}>errorInfo</CodeStep> chứa <CodeStep step={4}>componentStack</CodeStep> của lỗi.

Cùng với `onUncaughtError` và `onRecoverableError`, bạn có thể triển khai hệ thống báo cáo lỗi riêng:

<Sandpack>

```js src/reportError.js
function reportError({ type, error, errorInfo }) {
  // The specific implementation is up to you.
  // `console.error()` is only used for demonstration purposes.
  console.error(type, error, "Component Stack: ");
  console.error("Component Stack: ", errorInfo.componentStack);
}

export function onCaughtErrorProd(error, errorInfo) {
  if (error.message !== "Known error") {
    reportError({ type: "Caught", error, errorInfo });
  }
}

export function onUncaughtErrorProd(error, errorInfo) {
  reportError({ type: "Uncaught", error, errorInfo });
}

export function onRecoverableErrorProd(error, errorInfo) {
  reportError({ type: "Recoverable", error, errorInfo });
}
```

```js src/index.js active
import { hydrateRoot } from "react-dom/client";
import App from "./App.js";
import {
  onCaughtErrorProd,
  onRecoverableErrorProd,
  onUncaughtErrorProd,
} from "./reportError";

const container = document.getElementById("root");
hydrateRoot(container, <App />, {
  // Keep in mind to remove these options in development to leverage
  // React's default handlers or implement your own overlay for development.
  // The handlers are only specfied unconditionally here for demonstration purposes.
  onCaughtError: onCaughtErrorProd,
  onRecoverableError: onRecoverableErrorProd,
  onUncaughtError: onUncaughtErrorProd,
});
```

```js src/App.js
import { Component, useState } from "react";

function Boom() {
  foo.bar = "baz";
}

class ErrorBoundary extends Component {
  state = { hasError: false };

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}

export default function App() {
  const [triggerUncaughtError, settriggerUncaughtError] = useState(false);
  const [triggerCaughtError, setTriggerCaughtError] = useState(false);

  return (
    <>
      <button onClick={() => settriggerUncaughtError(true)}>
        Trigger uncaught error
      </button>
      {triggerUncaughtError && <Boom />}
      <button onClick={() => setTriggerCaughtError(true)}>
        Trigger caught error
      </button>
      {triggerCaughtError && (
        <ErrorBoundary>
          <Boom />
        </ErrorBoundary>
      )}
    </>
  );
}
```

```html public/index.html hidden
<!DOCTYPE html>
<html>
<head>
  <title>My app</title>
</head>
<body>
<!--
  Purposefully using HTML content that differs from the server-rendered content to trigger recoverable errors.
-->
<div id="root">Server content before hydration.</div>
</body>
</html>
```
</Sandpack>

## Xử lý sự cố {/*troubleshooting*/}


### Tôi gặp lỗi: "You passed a second argument to root.render" {/*im-getting-an-error-you-passed-a-second-argument-to-root-render*/}

Một lỗi phổ biến là truyền các tùy chọn cho `hydrateRoot` vào `root.render(...)`:

<ConsoleBlock level="error">

Warning: You passed a second argument to root.render(...) but it only accepts one argument.

</ConsoleBlock>

Để sửa, hãy truyền các tùy chọn root cho `hydrateRoot(...)`, không phải `root.render(...)`:
```js {2,5}
// 🚩 Wrong: root.render only takes one argument.
root.render(App, {onUncaughtError});

// ✅ Correct: pass options to createRoot.
const root = hydrateRoot(container, <App />, {onUncaughtError});
```
