---
title: createRoot
---

<Intro>

`createRoot` cho phép bạn tạo một root để hiển thị các component React bên trong một DOM node của trình duyệt.

```js
const root = createRoot(domNode, options?)
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `createRoot(domNode, options?)` {/*createroot*/}

Gọi `createRoot` để tạo một React root cho việc hiển thị nội dung bên trong một phần tử DOM của trình duyệt.

```js
import { createRoot } from 'react-dom/client';

const domNode = document.getElementById('root');
const root = createRoot(domNode);
```

React sẽ tạo một root cho `domNode`, và tiếp quản việc quản lý DOM bên trong nó. Sau khi bạn đã tạo root, bạn cần gọi [`root.render`](#root-render) để hiển thị một component React bên trong nó:

```js
root.render(<App />);
```

Một ứng dụng được xây dựng hoàn toàn bằng React thường chỉ có một lệnh gọi `createRoot` cho component root của nó. Một trang sử dụng React "rải rác" cho một số phần có thể có nhiều root riêng biệt tùy cần.

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `domNode`: Một [phần tử DOM.](https://developer.mozilla.org/en-US/docs/Web/API/Element) React sẽ tạo một root cho phần tử DOM này và cho phép bạn gọi các hàm trên root, chẳng hạn như `render` để hiển thị nội dung React đã được render.

* **tùy chọn** `options`: Một object với các tùy chọn cho React root này.

  * **tùy chọn** `onCaughtError`: Callback được gọi khi React bắt được lỗi trong Error Boundary. Được gọi với `error` được bắt bởi Error Boundary, và một object `errorInfo` chứa `componentStack`.
  * **tùy chọn** `onUncaughtError`: Callback được gọi khi một lỗi được ném ra và không được bắt bởi Error Boundary. Được gọi với `error` đã được ném, và một object `errorInfo` chứa `componentStack`.
  * **tùy chọn** `onRecoverableError`: Callback được gọi khi React tự động phục hồi từ lỗi. Được gọi với `error` mà React ném ra, và một object `errorInfo` chứa `componentStack`. Một số lỗi có thể phục hồi có thể bao gồm nguyên nhân lỗi gốc dưới dạng `error.cause`.
  * **tùy chọn** `identifierPrefix`: Một tiền tố chuỗi mà React sử dụng cho các ID được tạo bởi [`useId`.](/reference/react/useId) Hữu ích để tránh xung đột khi sử dụng nhiều root trên cùng một trang.

#### Giá trị trả về {/*returns*/}

`createRoot` trả về một object với hai phương thức: [`render`](#root-render) và [`unmount`.](#root-unmount)

#### Lưu ý {/*caveats*/}
* Nếu ứng dụng của bạn được render từ server, việc sử dụng `createRoot()` không được hỗ trợ. Hãy sử dụng [`hydrateRoot()`](/reference/react-dom/client/hydrateRoot) thay thế.
* Bạn có thể chỉ có một lệnh gọi `createRoot` trong ứng dụng. Nếu bạn sử dụng framework, nó có thể thực hiện lệnh gọi này cho bạn.
* Khi bạn muốn render một đoạn JSX trong một phần khác của cây DOM mà không phải là con của component (ví dụ, một modal hoặc tooltip), hãy sử dụng [`createPortal`](/reference/react-dom/createPortal) thay vì `createRoot`.

---

### `root.render(reactNode)` {/*root-render*/}

Gọi `root.render` để hiển thị một đoạn [JSX](/learn/writing-markup-with-jsx) ("React node") vào DOM node của React root trong trình duyệt.

```js
root.render(<App />);
```

React sẽ hiển thị `<App />` trong `root`, và tiếp quản việc quản lý DOM bên trong nó.

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*root-render-parameters*/}

* `reactNode`: Một *React node* mà bạn muốn hiển thị. Thường sẽ là một đoạn JSX như `<App />`, nhưng bạn cũng có thể truyền một phần tử React được tạo với [`createElement()`](/reference/react/createElement), một chuỗi, một số, `null`, hoặc `undefined`.


#### Giá trị trả về {/*root-render-returns*/}

`root.render` trả về `undefined`.

#### Lưu ý {/*root-render-caveats*/}

* Lần đầu tiên bạn gọi `root.render`, React sẽ xóa tất cả nội dung HTML hiện có bên trong React root trước khi render component React vào đó.

* Nếu DOM node của root chứa HTML được tạo bởi React trên server hoặc trong quá trình build, hãy sử dụng [`hydrateRoot()`](/reference/react-dom/client/hydrateRoot) thay thế, nó sẽ gắn các event handler vào HTML hiện có.

* Nếu bạn gọi `render` trên cùng một root nhiều lần, React sẽ cập nhật DOM khi cần thiết để phản ánh JSX mới nhất mà bạn đã truyền. React sẽ quyết định phần nào của DOM có thể được tái sử dụng và phần nào cần được tạo lại bằng cách ["so khớp"](/learn/preserving-and-resetting-state) với cây đã render trước đó. Gọi `render` trên cùng root lần nữa tương tự như gọi [hàm `set`](/reference/react/useState#setstate) trên component root: React tránh các cập nhật DOM không cần thiết.

* Mặc dù việc render là đồng bộ khi nó bắt đầu, `root.render(...)` thì không. Điều này có nghĩa là code sau `root.render()` có thể chạy trước khi bất kỳ effect (`useLayoutEffect`, `useEffect`) nào của render cụ thể đó được kích hoạt. Điều này thường không sao và hiếm khi cần điều chỉnh. Trong những trường hợp hiếm hoi khi thời gian của effect quan trọng, bạn có thể bọc `root.render(...)` trong [`flushSync`](https://react.dev/reference/react-dom/flushSync) để đảm bảo render ban đầu chạy hoàn toàn đồng bộ.

  ```js
  const root = createRoot(document.getElementById('root'));
  root.render(<App />);
  // 🚩 The HTML will not include the rendered <App /> yet:
  console.log(document.body.innerHTML);
  ```

---

### `root.unmount()` {/*root-unmount*/}

Gọi `root.unmount` để hủy cây đã render bên trong một React root.

```js
root.unmount();
```

Một ứng dụng được xây dựng hoàn toàn bằng React thường không có bất kỳ lệnh gọi `root.unmount` nào.

Điều này chủ yếu hữu ích nếu DOM node của React root (hoặc bất kỳ tổ tiên nào của nó) có thể bị xóa khỏi DOM bởi code khác. Ví dụ, hãy tưởng tượng một panel tab jQuery xóa các tab không hoạt động khỏi DOM. Nếu một tab bị xóa, mọi thứ bên trong nó (bao gồm cả các React root bên trong) cũng sẽ bị xóa khỏi DOM. Trong trường hợp đó, bạn cần thông báo cho React "dừng" quản lý nội dung của root đã bị xóa bằng cách gọi `root.unmount`. Nếu không, các component bên trong root đã bị xóa sẽ không biết cách dọn dẹp và giải phóng các tài nguyên toàn cục như subscription.

Gọi `root.unmount` sẽ unmount tất cả các component trong root và "tách" React khỏi DOM node root, bao gồm việc xóa bất kỳ event handler hoặc state nào trong cây.


#### Tham số {/*root-unmount-parameters*/}

`root.unmount` không nhận bất kỳ tham số nào.


#### Giá trị trả về {/*root-unmount-returns*/}

`root.unmount` trả về `undefined`.

#### Lưu ý {/*root-unmount-caveats*/}

* Gọi `root.unmount` sẽ unmount tất cả các component trong cây và "tách" React khỏi DOM node root.

* Sau khi bạn gọi `root.unmount`, bạn không thể gọi `root.render` lại trên cùng root. Cố gắng gọi `root.render` trên một root đã unmount sẽ ném lỗi "Cannot update an unmounted root". Tuy nhiên, bạn có thể tạo một root mới cho cùng DOM node sau khi root trước đó cho node đó đã bị unmount.

---

## Cách sử dụng {/*usage*/}

### Render một ứng dụng được xây dựng hoàn toàn bằng React {/*rendering-an-app-fully-built-with-react*/}

Nếu ứng dụng của bạn được xây dựng hoàn toàn bằng React, hãy tạo một root duy nhất cho toàn bộ ứng dụng.

```js [[1, 3, "document.getElementById('root')"], [2, 4, "<App />"]]
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

Thường thì bạn chỉ cần chạy code này một lần khi khởi động. Nó sẽ:

1. Tìm <CodeStep step={1}>DOM node của trình duyệt</CodeStep> được định nghĩa trong HTML.
2. Hiển thị <CodeStep step={2}>component React</CodeStep> cho ứng dụng của bạn bên trong.

<Sandpack>

```html public/index.html
<!DOCTYPE html>
<html>
  <head><title>My app</title></head>
  <body>
    <!-- This is the DOM node -->
    <div id="root"></div>
  </body>
</html>
```

```js src/index.js active
import { createRoot } from 'react-dom/client';
import App from './App.js';
import './styles.css';

const root = createRoot(document.getElementById('root'));
root.render(<App />);
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

**Nếu ứng dụng của bạn được xây dựng hoàn toàn bằng React, bạn không cần tạo thêm root nào, hoặc gọi [`root.render`](#root-render) lại.**

Từ thời điểm này, React sẽ quản lý DOM của toàn bộ ứng dụng. Để thêm nhiều component hơn, [lồng chúng bên trong component `App`.](/learn/importing-and-exporting-components) Khi bạn cần cập nhật UI, mỗi component có thể làm điều này bằng cách [sử dụng state.](/reference/react/useState) Khi bạn cần hiển thị nội dung bổ sung như modal hoặc tooltip bên ngoài DOM node, [render nó với portal.](/reference/react-dom/createPortal)

<Note>

Khi HTML của bạn trống, người dùng sẽ thấy một trang trắng cho đến khi code JavaScript của ứng dụng tải và chạy:

```html
<div id="root"></div>
```

Điều này có thể cảm thấy rất chậm! Để giải quyết điều này, bạn có thể tạo HTML ban đầu từ các component [trên server hoặc trong quá trình build.](/reference/react-dom/server) Sau đó khách truy cập có thể đọc văn bản, xem hình ảnh và click vào các liên kết trước khi bất kỳ code JavaScript nào tải. Chúng tôi khuyến nghị [sử dụng một framework](/learn/creating-a-react-app#full-stack-frameworks) thực hiện tối ưu hóa này ngay từ đầu. Tùy thuộc vào thời điểm nó chạy, điều này được gọi là *server-side rendering (SSR)* hoặc *static site generation (SSG).*

</Note>

<Pitfall>

**Các ứng dụng sử dụng server rendering hoặc static generation phải gọi [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) thay vì `createRoot`.** React sau đó sẽ *hydrate* (tái sử dụng) các DOM node từ HTML thay vì hủy và tạo lại chúng.

</Pitfall>

---

### Render một trang được xây dựng một phần bằng React {/*rendering-a-page-partially-built-with-react*/}

Nếu trang của bạn [không được xây dựng hoàn toàn bằng React](/learn/add-react-to-an-existing-project#using-react-for-a-part-of-your-existing-page), bạn có thể gọi `createRoot` nhiều lần để tạo root cho mỗi phần UI cấp cao nhất được quản lý bởi React. Bạn có thể hiển thị nội dung khác nhau trong mỗi root bằng cách gọi [`root.render`.](#root-render)

Ở đây, hai component React khác nhau được render vào hai DOM node được định nghĩa trong file `index.html`:

<Sandpack>

```html public/index.html
<!DOCTYPE html>
<html>
  <head><title>My app</title></head>
  <body>
    <nav id="navigation"></nav>
    <main>
      <p>This paragraph is not rendered by React (open index.html to verify).</p>
      <section id="comments"></section>
    </main>
  </body>
</html>
```

```js src/index.js active
import './styles.css';
import { createRoot } from 'react-dom/client';
import { Comments, Navigation } from './Components.js';

const navDomNode = document.getElementById('navigation');
const navRoot = createRoot(navDomNode);
navRoot.render(<Navigation />);

const commentDomNode = document.getElementById('comments');
const commentRoot = createRoot(commentDomNode);
commentRoot.render(<Comments />);
```

```js src/Components.js
export function Navigation() {
  return (
    <ul>
      <NavLink href="/">Home</NavLink>
      <NavLink href="/about">About</NavLink>
    </ul>
  );
}

function NavLink({ href, children }) {
  return (
    <li>
      <a href={href}>{children}</a>
    </li>
  );
}

export function Comments() {
  return (
    <>
      <h2>Comments</h2>
      <Comment text="Hello!" author="Sophie" />
      <Comment text="How are you?" author="Sunil" />
    </>
  );
}

function Comment({ text, author }) {
  return (
    <p>{text} — <i>{author}</i></p>
  );
}
```

```css
nav ul { padding: 0; margin: 0; }
nav ul li { display: inline-block; margin-right: 20px; }
```

</Sandpack>

Bạn cũng có thể tạo một DOM node mới với [`document.createElement()`](https://developer.mozilla.org/en-US/docs/Web/API/Document/createElement) và thêm nó vào tài liệu thủ công.

```js
const domNode = document.createElement('div');
const root = createRoot(domNode);
root.render(<Comment />);
document.body.appendChild(domNode); // You can add it anywhere in the document
```

Để xóa cây React khỏi DOM node và dọn dẹp tất cả tài nguyên được sử dụng bởi nó, gọi [`root.unmount`.](#root-unmount)

```js
root.unmount();
```

Điều này chủ yếu hữu ích nếu các component React của bạn nằm bên trong một ứng dụng được viết bằng framework khác.

---

### Cập nhật component root {/*updating-a-root-component*/}

Bạn có thể gọi `render` nhiều lần trên cùng một root. Miễn là cấu trúc cây component khớp với những gì đã được render trước đó, React sẽ [bảo toàn state.](/learn/preserving-and-resetting-state) Lưu ý rằng bạn có thể gõ vào input, nghĩa là các cập nhật từ các lệnh gọi `render` lặp lại mỗi giây trong ví dụ này không phá hủy dữ liệu:

<Sandpack>

```js src/index.js active
import { createRoot } from 'react-dom/client';
import './styles.css';
import App from './App.js';

const root = createRoot(document.getElementById('root'));

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

Việc gọi `render` nhiều lần là không phổ biến. Thường thì các component của bạn sẽ [cập nhật state](/reference/react/useState) thay thế.

### Ghi log lỗi trong production {/*error-logging-in-production*/}

Theo mặc định, React sẽ ghi log tất cả lỗi ra console. Để triển khai báo cáo lỗi riêng, bạn có thể cung cấp các tùy chọn xử lý lỗi tùy chọn cho root `onUncaughtError`, `onCaughtError` và `onRecoverableError`:

```js [[1, 6, "onCaughtError"], [2, 6, "error", 1], [3, 6, "errorInfo"], [4, 10, "componentStack", 15]]
import { createRoot } from "react-dom/client";
import { reportCaughtError } from "./reportError";

const container = document.getElementById("root");
const root = createRoot(container, {
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
import { createRoot } from "react-dom/client";
import App from "./App.js";
import {
  onCaughtErrorProd,
  onRecoverableErrorProd,
  onUncaughtErrorProd,
} from "./reportError";

const container = document.getElementById("root");
const root = createRoot(container, {
  // Keep in mind to remove these options in development to leverage
  // React's default handlers or implement your own overlay for development.
  // The handlers are only specfied unconditionally here for demonstration purposes.
  onCaughtError: onCaughtErrorProd,
  onRecoverableError: onRecoverableErrorProd,
  onUncaughtError: onUncaughtErrorProd,
});
root.render(<App />);
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

</Sandpack>

## Xử lý sự cố {/*troubleshooting*/}

### Tôi đã tạo root, nhưng không có gì hiển thị {/*ive-created-a-root-but-nothing-is-displayed*/}

Hãy chắc chắn rằng bạn không quên thực sự *render* ứng dụng vào root:

```js {5}
import { createRoot } from 'react-dom/client';
import App from './App.js';

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

Cho đến khi bạn làm điều đó, không có gì được hiển thị.

---

### Tôi gặp lỗi: "You passed a second argument to root.render" {/*im-getting-an-error-you-passed-a-second-argument-to-root-render*/}

Một lỗi phổ biến là truyền các tùy chọn cho `createRoot` vào `root.render(...)`:

<ConsoleBlock level="error">

Warning: You passed a second argument to root.render(...) but it only accepts one argument.

</ConsoleBlock>

Để sửa, hãy truyền các tùy chọn root cho `createRoot(...)`, không phải `root.render(...)`:
```js {2,5}
// 🚩 Wrong: root.render only takes one argument.
root.render(App, {onUncaughtError});

// ✅ Correct: pass options to createRoot.
const root = createRoot(container, {onUncaughtError});
root.render(<App />);
```

---

### Tôi gặp lỗi: "Target container is not a DOM element" {/*im-getting-an-error-target-container-is-not-a-dom-element*/}

Lỗi này có nghĩa là bất cứ thứ gì bạn truyền cho `createRoot` không phải là một DOM node.

Nếu bạn không chắc chuyện gì đang xảy ra, hãy thử log nó ra:

```js {2}
const domNode = document.getElementById('root');
console.log(domNode); // ???
const root = createRoot(domNode);
root.render(<App />);
```

Ví dụ, nếu `domNode` là `null`, điều đó có nghĩa là [`getElementById`](https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementById) trả về `null`. Điều này sẽ xảy ra nếu không có node nào trong tài liệu với ID đã cho tại thời điểm gọi. Có thể có một vài lý do cho điều này:

1. ID bạn đang tìm có thể khác với ID bạn đã sử dụng trong file HTML. Kiểm tra lỗi đánh máy!
2. Tag `<script>` của bundle không thể "nhìn thấy" bất kỳ DOM node nào xuất hiện *sau* nó trong HTML.

Một cách phổ biến khác để gặp lỗi này là viết `createRoot(<App />)` thay vì `createRoot(domNode)`.

---

### Tôi gặp lỗi: "Functions are not valid as a React child." {/*im-getting-an-error-functions-are-not-valid-as-a-react-child*/}

Lỗi này có nghĩa là bất cứ thứ gì bạn truyền cho `root.render` không phải là một component React.

Điều này có thể xảy ra nếu bạn gọi `root.render` với `Component` thay vì `<Component />`:

```js {2,5}
// 🚩 Wrong: App is a function, not a Component.
root.render(App);

// ✅ Correct: <App /> is a component.
root.render(<App />);
```

Hoặc nếu bạn truyền một hàm cho `root.render`, thay vì kết quả của việc gọi nó:

```js {2,5}
// 🚩 Wrong: createApp is a function, not a component.
root.render(createApp);

// ✅ Correct: call createApp to return a component.
root.render(createApp());
```

---

### HTML được render từ server bị tạo lại từ đầu {/*my-server-rendered-html-gets-re-created-from-scratch*/}

Nếu ứng dụng của bạn được render từ server và bao gồm HTML ban đầu được tạo bởi React, bạn có thể nhận thấy rằng tạo root và gọi `root.render` sẽ xóa tất cả HTML đó, rồi tạo lại tất cả các DOM node từ đầu. Điều này có thể chậm hơn, reset focus và vị trí cuộn, và có thể mất dữ liệu đầu vào khác của người dùng.

Các ứng dụng render từ server phải sử dụng [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) thay vì `createRoot`:

```js {1,4-7}
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(
  document.getElementById('root'),
  <App />
);
```

Lưu ý rằng API của nó khác. Cụ thể, thường sẽ không có thêm lệnh gọi `root.render` nào.
