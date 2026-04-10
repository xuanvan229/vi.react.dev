---
title: resume
---

<Intro>

`resume` stream một cây React đã được pre-render thành một [Readable Web Stream.](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream)

```js
const stream = await resume(reactNode, postponedState, options?)
```

</Intro>

<InlineToc />

<Note>

API này phụ thuộc vào [Web Streams.](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API) Đối với Node.js, hãy sử dụng [`resumeToNodeStream`](/reference/react-dom/server/renderToPipeableStream) thay thế.

</Note>

---

## Tham khảo {/*reference*/}

### `resume(node, postponedState, options?)` {/*resume*/}

Gọi `resume` để tiếp tục render một cây React đã được pre-render thành HTML vào một [Readable Web Stream.](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream)

```js
import { resume } from 'react-dom/server';
import {getPostponedState} from './storage';

async function handler(request, writable) {
  const postponed = await getPostponedState(request);
  const resumeStream = await resume(<App />, postponed);
  return resumeStream.pipeTo(writable)
}
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `reactNode`: React node mà bạn đã gọi `prerender` với. Ví dụ, một phần tử JSX như `<App />`. Nó được kỳ vọng đại diện cho toàn bộ tài liệu, vì vậy component `App` nên render thẻ `<html>`.
* `postponedState`: Object `postpone` opaque được trả về từ [prerender API](/reference/react-dom/static/index), được tải từ nơi bạn đã lưu trữ nó (ví dụ: redis, một file, hoặc S3).
* **tùy chọn** `options`: Một object với các tùy chọn streaming.
  * **tùy chọn** `nonce`: Một chuỗi [`nonce`](http://developer.mozilla.org/en-US/docs/Web/HTML/Element/script#nonce) để cho phép script cho [`script-src` Content-Security-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/script-src).
  * **tùy chọn** `signal`: Một [abort signal](https://developer.mozilla.org/en-US/docs/Web/API/AbortSignal) cho phép bạn [hủy bỏ render phía server](#aborting-server-rendering) và render phần còn lại trên client.
  * **tùy chọn** `onError`: Một callback được kích hoạt bất cứ khi nào có lỗi server, dù là [có thể phục hồi](/reference/react-dom/server/renderToReadableStream#recovering-from-errors-outside-the-shell) hay [không.](/reference/react-dom/server/renderToReadableStream#recovering-from-errors-inside-the-shell) Mặc định, nó chỉ gọi `console.error`. Nếu bạn ghi đè nó để [ghi log báo cáo lỗi,](/reference/react-dom/server/renderToReadableStream#logging-crashes-on-the-server) hãy đảm bảo rằng bạn vẫn gọi `console.error`.


#### Giá trị trả về {/*returns*/}

`resume` trả về một Promise:

- Nếu `resume` tạo ra thành công một [shell](/reference/react-dom/server/renderToReadableStream#specifying-what-goes-into-the-shell), Promise đó sẽ resolve thành một [Readable Web Stream.](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream) có thể được pipe đến một [Writable Web Stream.](https://developer.mozilla.org/en-US/docs/Web/API/WritableStream).
- Nếu có lỗi trong shell, Promise sẽ bị reject với lỗi đó.

Stream trả về có một thuộc tính bổ sung:

* `allReady`: Một Promise resolve khi tất cả việc render hoàn tất. Bạn có thể `await stream.allReady` trước khi trả về response [cho các crawler và tạo trang tĩnh.](/reference/react-dom/server/renderToReadableStream#waiting-for-all-content-to-load-for-crawlers-and-static-generation) Nếu bạn làm vậy, bạn sẽ không có progressive loading. Stream sẽ chứa HTML cuối cùng.

#### Lưu ý {/*caveats*/}

- `resume` không chấp nhận các tùy chọn cho `bootstrapScripts`, `bootstrapScriptContent`, hoặc `bootstrapModules`. Thay vào đó, bạn cần truyền những tùy chọn này cho lệnh gọi `prerender` tạo ra `postponedState`. Bạn cũng có thể chèn nội dung bootstrap vào writable stream theo cách thủ công.
- `resume` không chấp nhận `identifierPrefix` vì prefix cần phải giống nhau trong cả `prerender` và `resume`.
- Vì `nonce` không thể được cung cấp cho prerender, bạn chỉ nên cung cấp `nonce` cho `resume` nếu bạn không cung cấp script cho prerender.
- `resume` re-render từ root cho đến khi tìm thấy một component chưa được pre-render đầy đủ. Chỉ các Component được pre-render đầy đủ (Component và các children của nó đã hoàn thành prerendering) mới bị bỏ qua hoàn toàn.

## Cách sử dụng {/*usage*/}

### Tiếp tục một prerender {/*resuming-a-prerender*/}

<Sandpack>

```js src/App.js hidden
```

```html public/index.html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <iframe id="container"></iframe>
</body>
</html>
```

```js src/index.js
import {
  flushReadableStreamToFrame,
  getUser,
  Postponed,
  sleep,
} from "./demo-helpers";
import { StrictMode, Suspense, use, useEffect } from "react";
import { prerender } from "react-dom/static";
import { resume } from "react-dom/server";
import { hydrateRoot } from "react-dom/client";

function Header() {
  return <header>Me and my descendants can be prerendered</header>;
}

const { promise: cookies, resolve: resolveCookies } = Promise.withResolvers();

function Main() {
  const { sessionID } = use(cookies);
  const user = getUser(sessionID);

  useEffect(() => {
    console.log("reached interactivity!");
  }, []);

  return (
    <main>
      Hello, {user.name}!
      <button onClick={() => console.log("hydrated!")}>
        Clicking me requires hydration.
      </button>
    </main>
  );
}

function Shell({ children }) {
  // In a real app, this is where you would put your html and body.
  // We're just using tags here we can include in an existing body for demonstration purposes
  return (
    <html>
      <body>{children}</body>
    </html>
  );
}

function App() {
  return (
    <Shell>
      <Suspense fallback="loading header">
        <Header />
      </Suspense>
      <Suspense fallback="loading main">
        <Main />
      </Suspense>
    </Shell>
  );
}

async function main(frame) {
  // Layer 1
  const controller = new AbortController();
  const prerenderedApp = prerender(<App />, {
    signal: controller.signal,
    onError(error) {
      if (error instanceof Postponed) {
      } else {
        console.error(error);
      }
    },
  });
  // We're immediately aborting in a macrotask.
  // Any data fetching that's not available synchronously, or in a microtask, will not have finished.
  setTimeout(() => {
    controller.abort(new Postponed());
  });

  const { prelude, postponed } = await prerenderedApp;
  await flushReadableStreamToFrame(prelude, frame);

  // Layer 2
  // Just waiting here for demonstration purposes.
  // In a real app, the prelude and postponed state would've been serialized in Layer 1 and Layer would deserialize them.
  // The prelude content could be flushed immediated as plain HTML while
  // React is continuing to render from where the prerender left off.
  await sleep(2000);

  // You would get the cookies from the incoming HTTP request
  resolveCookies({ sessionID: "abc" });

  const stream = await resume(<App />, postponed);

  await flushReadableStreamToFrame(stream, frame);

  // Layer 3
  // Just waiting here for demonstration purposes.
  await sleep(2000);

  hydrateRoot(frame.contentWindow.document, <App />);
}

main(document.getElementById("container"));

```

```js src/demo-helpers.js
export async function flushReadableStreamToFrame(readable, frame) {
  const document = frame.contentWindow.document;
  const decoder = new TextDecoder();
  for await (const chunk of readable) {
    const partialHTML = decoder.decode(chunk);
    document.write(partialHTML);
  }
}

// This doesn't need to be an error.
// You can use any other means to check if an error during prerender was
// from an intentional abort or a real error.
export class Postponed extends Error {}

// We're just hardcoding a session here.
export function getUser(sessionID) {
  return {
    name: "Alice",
  };
}

export function sleep(timeoutMS) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve();
    }, timeoutMS);
  });
}
```

</Sandpack>

### Đọc thêm {/*further-reading*/}

Việc tiếp tục (resuming) hoạt động giống như `renderToReadableStream`. Để biết thêm ví dụ, hãy xem [phần usage của `renderToReadableStream`](/reference/react-dom/server/renderToReadableStream#usage).
[Phần usage của `prerender`](/reference/react-dom/static/prerender#usage) bao gồm các ví dụ về cách sử dụng `prerender` cụ thể.