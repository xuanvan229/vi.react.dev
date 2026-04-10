---
title: prerenderToNodeStream
---

<Intro>

`prerenderToNodeStream` render một cây React thành một chuỗi HTML tĩnh sử dụng một [Node.js Stream.](https://nodejs.org/api/stream.html)

```js
const {prelude, postponed} = await prerenderToNodeStream(reactNode, options?)
```

</Intro>

<InlineToc />

<Note>

API này dành riêng cho Node.js. Các môi trường có [Web Streams,](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API) như Deno và các edge runtime hiện đại, nên sử dụng [`prerender`](/reference/react-dom/static/prerender) thay thế.

</Note>

---

## Tham khảo {/*reference*/}

### `prerenderToNodeStream(reactNode, options?)` {/*prerender*/}

Gọi `prerenderToNodeStream` để render ứng dụng của bạn thành HTML tĩnh.

```js
import { prerenderToNodeStream } from 'react-dom/static';

// The route handler syntax depends on your backend framework
app.use('/', async (request, response) => {
  const { prelude } = await prerenderToNodeStream(<App />, {
    bootstrapScripts: ['/main.js'],
  });

  response.setHeader('Content-Type', 'text/plain');
  prelude.pipe(response);
});
```

Trên client, gọi [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) để làm cho HTML được tạo từ server trở nên tương tác.

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `reactNode`: Một React node mà bạn muốn render thành HTML. Ví dụ, một JSX node như `<App />`. Nó được kỳ vọng đại diện cho toàn bộ tài liệu, vì vậy component App nên render thẻ `<html>`.

* **tùy chọn** `options`: Một object với các tùy chọn tạo trang tĩnh.
  * **tùy chọn** `bootstrapScriptContent`: Nếu được chỉ định, chuỗi này sẽ được đặt trong một thẻ `<script>` inline.
  * **tùy chọn** `bootstrapScripts`: Một mảng các URL chuỗi cho các thẻ `<script>` để phát ra trên trang. Sử dụng điều này để bao gồm `<script>` gọi [`hydrateRoot`.](/reference/react-dom/client/hydrateRoot) Bỏ qua nếu bạn không muốn chạy React trên client.
  * **tùy chọn** `bootstrapModules`: Giống như `bootstrapScripts`, nhưng phát ra [`<script type="module">`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) thay thế.
  * **tùy chọn** `identifierPrefix`: Một chuỗi tiền tố mà React sử dụng cho các ID được tạo bởi [`useId`.](/reference/react/useId) Hữu ích để tránh xung đột khi sử dụng nhiều root trên cùng một trang. Phải là cùng tiền tố được truyền cho [`hydrateRoot`.](/reference/react-dom/client/hydrateRoot#parameters)
  * **tùy chọn** `namespaceURI`: Một chuỗi với [namespace URI](https://developer.mozilla.org/en-US/docs/Web/API/Document/createElementNS#important_namespace_uris) gốc cho stream. Mặc định là HTML thông thường. Truyền `'http://www.w3.org/2000/svg'` cho SVG hoặc `'http://www.w3.org/1998/Math/MathML'` cho MathML.
  * **tùy chọn** `onError`: Một callback được kích hoạt bất cứ khi nào có lỗi server, dù là [có thể phục hồi](/reference/react-dom/server/renderToPipeableStream#recovering-from-errors-outside-the-shell) hay [không.](/reference/react-dom/server/renderToPipeableStream#recovering-from-errors-inside-the-shell) Mặc định, nó chỉ gọi `console.error`. Nếu bạn ghi đè nó để [ghi log báo cáo lỗi,](/reference/react-dom/server/renderToPipeableStream#logging-crashes-on-the-server) hãy đảm bảo rằng bạn vẫn gọi `console.error`. Bạn cũng có thể sử dụng nó để [điều chỉnh mã trạng thái](/reference/react-dom/server/renderToPipeableStream#setting-the-status-code) trước khi shell được phát ra.
  * **tùy chọn** `progressiveChunkSize`: Số byte trong một chunk. [Đọc thêm về heuristic mặc định.](https://github.com/facebook/react/blob/14c2be8dac2d5482fda8a0906a31d239df8551fc/packages/react-server/src/ReactFizzServer.js#L210-L225)
  * **tùy chọn** `signal`: Một [abort signal](https://developer.mozilla.org/en-US/docs/Web/API/AbortSignal) cho phép bạn [hủy bỏ prerendering](#aborting-prerendering) và render phần còn lại trên client.

#### Giá trị trả về {/*returns*/}

`prerenderToNodeStream` trả về một Promise:
- Nếu render thành công, Promise sẽ resolve thành một object chứa:
  - `prelude`: một [Node.js Stream.](https://nodejs.org/api/stream.html) của HTML. Bạn có thể sử dụng stream này để gửi response theo từng chunk, hoặc bạn có thể đọc toàn bộ stream thành một chuỗi.
  - `postponed`: một object opaque có thể serialize JSON có thể được truyền cho [`resumeToPipeableStream`](/reference/react-dom/server/resumeToPipeableStream) nếu `prerenderToNodeStream` chưa hoàn thành. Ngược lại `null` cho biết `prelude` chứa tất cả nội dung và không cần resume.
- Nếu render thất bại, Promise sẽ bị reject. [Sử dụng điều này để xuất một shell dự phòng.](/reference/react-dom/server/renderToPipeableStream#recovering-from-errors-inside-the-shell)

#### Lưu ý {/*caveats*/}

`nonce` không phải là tùy chọn có sẵn khi prerendering. Các nonce phải là duy nhất cho mỗi request và nếu bạn sử dụng nonce để bảo mật ứng dụng với [CSP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CSP), sẽ không phù hợp và không an toàn khi đưa giá trị nonce vào trong chính prerender.

<Note>

### Khi nào nên sử dụng `prerenderToNodeStream`? {/*when-to-use-prerender*/}

API `prerenderToNodeStream` tĩnh được sử dụng cho tạo trang tĩnh phía server (SSG). Không giống như `renderToString`, `prerenderToNodeStream` chờ tất cả dữ liệu tải xong trước khi resolve. Điều này làm cho nó phù hợp để tạo HTML tĩnh cho một trang đầy đủ, bao gồm dữ liệu cần được fetch bằng Suspense. Để stream nội dung khi nó tải, hãy sử dụng streaming server-side render (SSR) API như [renderToReadableStream](/reference/react-dom/server/renderToReadableStream).

`prerenderToNodeStream` có thể bị hủy bỏ và tiếp tục sau đó với `resumeToPipeableStream` để hỗ trợ partial pre-rendering.

</Note>

---

## Cách sử dụng {/*usage*/}

### Render một cây React thành stream HTML tĩnh {/*rendering-a-react-tree-to-a-stream-of-static-html*/}

Gọi `prerenderToNodeStream` để render cây React của bạn thành HTML tĩnh vào một [Node.js Stream](https://nodejs.org/api/stream.html):

```js [[1, 5, "<App />"], [2, 6, "['/main.js']"]]
import { prerenderToNodeStream } from 'react-dom/static';

// The route handler syntax depends on your backend framework
app.use('/', async (request, response) => {
  const { prelude } = await prerenderToNodeStream(<App />, {
    bootstrapScripts: ['/main.js'],
  });

  response.setHeader('Content-Type', 'text/plain');
  prelude.pipe(response);
});
```

Cùng với <CodeStep step={1}>component gốc</CodeStep>, bạn cần cung cấp một danh sách các <CodeStep step={2}>đường dẫn `<script>` bootstrap</CodeStep>. Component gốc của bạn nên trả về **toàn bộ tài liệu bao gồm thẻ `<html>` gốc.**

Ví dụ, nó có thể trông như thế này:

```js [[1, 1, "App"]]
export default function App() {
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

React sẽ chèn [doctype](https://developer.mozilla.org/en-US/docs/Glossary/Doctype) và các <CodeStep step={2}>thẻ `<script>` bootstrap</CodeStep> của bạn vào stream HTML kết quả:

```html [[2, 5, "/main.js"]]
<!DOCTYPE html>
<html>
  <!-- ... HTML from your components ... -->
</html>
<script src="/main.js" async=""></script>
```

Trên client, script bootstrap của bạn nên [hydrate toàn bộ `document` bằng lệnh gọi `hydrateRoot`:](/reference/react-dom/client/hydrateRoot#hydrating-an-entire-document)

```js [[1, 4, "<App />"]]
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(document, <App />);
```

Điều này sẽ gắn các event listener vào HTML tĩnh được tạo từ server và làm cho nó tương tác.

<DeepDive>

#### Đọc đường dẫn asset CSS và JS từ đầu ra build {/*reading-css-and-js-asset-paths-from-the-build-output*/}

Các URL asset cuối cùng (như các file JavaScript và CSS) thường được hash sau khi build. Ví dụ, thay vì `styles.css` bạn có thể nhận được `styles.123456.css`. Việc hash tên file asset tĩnh đảm bảo rằng mỗi build khác nhau của cùng một asset sẽ có tên file khác nhau. Điều này hữu ích vì nó cho phép bạn bật bộ nhớ đệm dài hạn một cách an toàn cho các asset tĩnh: một file với tên nhất định sẽ không bao giờ thay đổi nội dung.

Tuy nhiên, nếu bạn không biết URL asset cho đến sau khi build, không có cách nào để bạn đặt chúng trong mã nguồn. Ví dụ, hardcode `"/styles.css"` vào JSX như trước đó sẽ không hoạt động. Để giữ chúng ngoài mã nguồn, component gốc của bạn có thể đọc tên file thực từ một map được truyền dưới dạng prop:

```js {1,6}
export default function App({ assetMap }) {
  return (
    <html>
      <head>
        <title>My app</title>
        <link rel="stylesheet" href={assetMap['styles.css']}></link>
      </head>
      ...
    </html>
  );
}
```

Trên server, render `<App assetMap={assetMap} />` và truyền `assetMap` của bạn với các URL asset:

```js {1-5,8,9}
// You'd need to get this JSON from your build tooling, e.g. read it from the build output.
const assetMap = {
  'styles.css': '/styles.123456.css',
  'main.js': '/main.123456.js'
};

app.use('/', async (request, response) => {
  const { prelude } = await prerenderToNodeStream(<App />, {
    bootstrapScripts: [assetMap['/main.js']]
  });

  response.setHeader('Content-Type', 'text/html');
  prelude.pipe(response);
});
```

Vì server của bạn hiện đang render `<App assetMap={assetMap} />`, bạn cũng cần render nó với `assetMap` trên client để tránh lỗi hydration. Bạn có thể serialize và truyền `assetMap` cho client như thế này:

```js {9-10}
// You'd need to get this JSON from your build tooling.
const assetMap = {
  'styles.css': '/styles.123456.css',
  'main.js': '/main.123456.js'
};

app.use('/', async (request, response) => {
  const { prelude } = await prerenderToNodeStream(<App />, {
    // Careful: It's safe to stringify() this because this data isn't user-generated.
    bootstrapScriptContent: `window.assetMap = ${JSON.stringify(assetMap)};`,
    bootstrapScripts: [assetMap['/main.js']],
  });

  response.setHeader('Content-Type', 'text/html');
  prelude.pipe(response);
});
```

Trong ví dụ trên, tùy chọn `bootstrapScriptContent` thêm một thẻ `<script>` inline bổ sung đặt biến global `window.assetMap` trên client. Điều này cho phép code phía client đọc cùng `assetMap`:

```js {4}
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(document, <App assetMap={window.assetMap} />);
```

Cả client và server đều render `App` với cùng prop `assetMap`, vì vậy sẽ không có lỗi hydration.

</DeepDive>

---

### Render một cây React thành chuỗi HTML tĩnh {/*rendering-a-react-tree-to-a-string-of-static-html*/}

Gọi `prerenderToNodeStream` để render ứng dụng của bạn thành một chuỗi HTML tĩnh:

```js
import { prerenderToNodeStream } from 'react-dom/static';

async function renderToString() {
  const {prelude} = await prerenderToNodeStream(<App />, {
    bootstrapScripts: ['/main.js']
  });

  return new Promise((resolve, reject) => {
    let data = '';
    prelude.on('data', chunk => {
      data += chunk;
    });
    prelude.on('end', () => resolve(data));
    prelude.on('error', reject);
  });
}
```

Điều này sẽ tạo ra đầu ra HTML không tương tác ban đầu của các component React của bạn. Trên client, bạn sẽ cần gọi [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) để *hydrate* HTML được tạo từ server đó và làm cho nó tương tác.

---

### Chờ tất cả dữ liệu tải xong {/*waiting-for-all-data-to-load*/}

`prerenderToNodeStream` chờ tất cả dữ liệu tải xong trước khi hoàn thành việc tạo HTML tĩnh và resolve. Ví dụ, hãy xem xét một trang profile hiển thị ảnh bìa, một thanh sidebar với bạn bè và ảnh, và danh sách bài đăng:

```js
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Sidebar>
        <Friends />
        <Photos />
      </Sidebar>
      <Suspense fallback={<PostsGlimmer />}>
        <Posts />
      </Suspense>
    </ProfileLayout>
  );
}
```

Hãy tưởng tượng rằng `<Posts />` cần tải một số dữ liệu, điều này mất một thời gian. Lý tưởng nhất, bạn muốn chờ các bài đăng hoàn thành để chúng được bao gồm trong HTML. Để làm điều này, bạn có thể sử dụng Suspense để suspend trên dữ liệu, và `prerenderToNodeStream` sẽ chờ nội dung bị suspend hoàn thành trước khi resolve thành HTML tĩnh.

<Note>

**Chỉ các nguồn dữ liệu hỗ trợ Suspense mới kích hoạt component Suspense.** Chúng bao gồm:

- Fetch dữ liệu với các framework hỗ trợ Suspense như [Relay](https://relay.dev/docs/guided-tour/rendering/loading-states/) và [Next.js](https://nextjs.org/docs/getting-started/react-essentials)
- Lazy-loading code component với [`lazy`](/reference/react/lazy)
- Đọc giá trị của một Promise với [`use`](/reference/react/use)

Suspense **không** phát hiện khi dữ liệu được fetch bên trong Effect hoặc event handler.

Cách chính xác để bạn tải dữ liệu trong component `Posts` ở trên phụ thuộc vào framework của bạn. Nếu bạn sử dụng framework hỗ trợ Suspense, bạn sẽ tìm thấy chi tiết trong tài liệu fetch dữ liệu của nó.

Việc fetch dữ liệu hỗ trợ Suspense mà không sử dụng framework có quan điểm riêng chưa được hỗ trợ. Các yêu cầu để triển khai một nguồn dữ liệu hỗ trợ Suspense vẫn chưa ổn định và chưa được ghi lại. Một API chính thức để tích hợp nguồn dữ liệu với Suspense sẽ được phát hành trong một phiên bản tương lai của React.

</Note>

---

### Hủy bỏ prerendering {/*aborting-prerendering*/}

Bạn có thể buộc prerender "từ bỏ" sau một khoảng thời gian chờ:

```js {2-5,11}
async function renderToString() {
  const controller = new AbortController();
  setTimeout(() => {
    controller.abort()
  }, 10000);

  try {
    // the prelude will contain all the HTML that was prerendered
    // before the controller aborted.
    const {prelude} = await prerenderToNodeStream(<App />, {
      signal: controller.signal,
    });
    //...
```

Bất kỳ ranh giới Suspense nào có children chưa hoàn thành sẽ được bao gồm trong prelude ở trạng thái fallback.

Điều này có thể được sử dụng cho partial prerendering cùng với [`resumeToPipeableStream`](/reference/react-dom/server/resumeToPipeableStream) hoặc [`resumeAndPrerenderToNodeStream`](/reference/react-dom/static/resumeAndPrerenderToNodeStream).

## Khắc phục sự cố {/*troubleshooting*/}

### Stream của tôi không bắt đầu cho đến khi toàn bộ ứng dụng được render {/*my-stream-doesnt-start-until-the-entire-app-is-rendered*/}

Response của `prerenderToNodeStream` chờ toàn bộ ứng dụng hoàn thành render, bao gồm chờ tất cả các ranh giới Suspense resolve, trước khi resolve. Nó được thiết kế cho việc tạo trang tĩnh (SSG) trước và không hỗ trợ streaming thêm nội dung khi nó tải.

Để stream nội dung khi nó tải, hãy sử dụng streaming server render API như [renderToPipeableStream](/reference/react-dom/server/renderToPipeableStream).
