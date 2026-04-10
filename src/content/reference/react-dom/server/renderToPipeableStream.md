---
title: renderToPipeableStream
---

<Intro>

`renderToPipeableStream` render một cây React thành một [Node.js Stream](https://nodejs.org/api/stream.html) có thể pipe.

```js
const { pipe, abort } = renderToPipeableStream(reactNode, options?)
```

</Intro>

<InlineToc />

<Note>

API này dành riêng cho Node.js. Các môi trường có [Web Streams,](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API) như Deno và các edge runtime hiện đại, nên sử dụng [`renderToReadableStream`](/reference/react-dom/server/renderToReadableStream) thay thế.

</Note>

---

## Tham khảo {/*reference*/}

### `renderToPipeableStream(reactNode, options?)` {/*rendertopipeablestream*/}

Gọi `renderToPipeableStream` để render cây React của bạn thành HTML vào một [Node.js Stream.](https://nodejs.org/api/stream.html#writable-streams)

```js
import { renderToPipeableStream } from 'react-dom/server';

const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.setHeader('content-type', 'text/html');
    pipe(response);
  }
});
```

Trên client, gọi [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) để làm cho HTML được tạo từ server trở nên tương tác.

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `reactNode`: Một React node mà bạn muốn render thành HTML. Ví dụ, một phần tử JSX như `<App />`. Nó được kỳ vọng đại diện cho toàn bộ tài liệu, vì vậy component `App` nên render thẻ `<html>`.

* **tùy chọn** `options`: Một object với các tùy chọn streaming.
  * **tùy chọn** `bootstrapScriptContent`: Nếu được chỉ định, chuỗi này sẽ được đặt trong một thẻ `<script>` inline.
  * **tùy chọn** `bootstrapScripts`: Một mảng các URL chuỗi cho các thẻ `<script>` để phát ra trên trang. Sử dụng điều này để bao gồm `<script>` gọi [`hydrateRoot`.](/reference/react-dom/client/hydrateRoot) Bỏ qua nếu bạn không muốn chạy React trên client.
  * **tùy chọn** `bootstrapModules`: Giống như `bootstrapScripts`, nhưng phát ra [`<script type="module">`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) thay thế.
  * **tùy chọn** `identifierPrefix`: Một chuỗi tiền tố mà React sử dụng cho các ID được tạo bởi [`useId`.](/reference/react/useId) Hữu ích để tránh xung đột khi sử dụng nhiều root trên cùng một trang. Phải là cùng tiền tố được truyền cho [`hydrateRoot`.](/reference/react-dom/client/hydrateRoot#parameters)
  * **tùy chọn** `namespaceURI`: Một chuỗi với [namespace URI](https://developer.mozilla.org/en-US/docs/Web/API/Document/createElementNS#important_namespace_uris) gốc cho stream. Mặc định là HTML thông thường. Truyền `'http://www.w3.org/2000/svg'` cho SVG hoặc `'http://www.w3.org/1998/Math/MathML'` cho MathML.
  * **tùy chọn** `nonce`: Một chuỗi [`nonce`](http://developer.mozilla.org/en-US/docs/Web/HTML/Element/script#nonce) để cho phép script cho [`script-src` Content-Security-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/script-src).
  * **tùy chọn** `onAllReady`: Một callback được kích hoạt khi tất cả việc render hoàn tất, bao gồm cả [shell](#specifying-what-goes-into-the-shell) và tất cả [nội dung](#streaming-more-content-as-it-loads) bổ sung. Bạn có thể sử dụng điều này thay cho `onShellReady` [cho các crawler và tạo trang tĩnh.](#waiting-for-all-content-to-load-for-crawlers-and-static-generation) Nếu bạn bắt đầu streaming ở đây, bạn sẽ không có progressive loading. Stream sẽ chứa HTML cuối cùng.
  * **tùy chọn** `onError`: Một callback được kích hoạt bất cứ khi nào có lỗi server, dù là [có thể phục hồi](#recovering-from-errors-outside-the-shell) hay [không.](#recovering-from-errors-inside-the-shell) Mặc định, nó chỉ gọi `console.error`. Nếu bạn ghi đè nó để [ghi log báo cáo lỗi,](#logging-crashes-on-the-server) hãy đảm bảo rằng bạn vẫn gọi `console.error`. Bạn cũng có thể sử dụng nó để [điều chỉnh mã trạng thái](#setting-the-status-code) trước khi shell được phát ra.
  * **tùy chọn** `onShellReady`: Một callback được kích hoạt ngay sau khi [shell ban đầu](#specifying-what-goes-into-the-shell) đã được render. Bạn có thể [đặt mã trạng thái](#setting-the-status-code) và gọi `pipe` ở đây để bắt đầu streaming. React sẽ [stream nội dung bổ sung](#streaming-more-content-as-it-loads) sau shell cùng với các thẻ `<script>` inline thay thế các fallback loading HTML bằng nội dung.
  * **tùy chọn** `onShellError`: Một callback được kích hoạt nếu có lỗi khi render shell ban đầu. Nó nhận lỗi làm đối số. Chưa có byte nào được phát ra từ stream, và cả `onShellReady` lẫn `onAllReady` đều sẽ không được gọi, vì vậy bạn có thể [xuất một shell HTML dự phòng.](#recovering-from-errors-inside-the-shell)
  * **tùy chọn** `progressiveChunkSize`: Số byte trong một chunk. [Đọc thêm về heuristic mặc định.](https://github.com/facebook/react/blob/14c2be8dac2d5482fda8a0906a31d239df8551fc/packages/react-server/src/ReactFizzServer.js#L210-L225)


#### Giá trị trả về {/*returns*/}

`renderToPipeableStream` trả về một object với hai phương thức:

* `pipe` xuất HTML vào [Writable Node.js Stream](https://nodejs.org/api/stream.html#writable-streams) được cung cấp. Gọi `pipe` trong `onShellReady` nếu bạn muốn bật streaming, hoặc trong `onAllReady` cho các crawler và tạo trang tĩnh.
* `abort` cho phép bạn [hủy bỏ việc render phía server](#aborting-server-rendering) và render phần còn lại trên client.

---

## Cách sử dụng {/*usage*/}

### Render một cây React thành HTML vào một Node.js Stream {/*rendering-a-react-tree-as-html-to-a-nodejs-stream*/}

Gọi `renderToPipeableStream` để render cây React của bạn thành HTML vào một [Node.js Stream:](https://nodejs.org/api/stream.html#writable-streams)

```js [[1, 5, "<App />"], [2, 6, "['/main.js']"]]
import { renderToPipeableStream } from 'react-dom/server';

// The route handler syntax depends on your backend framework
app.use('/', (request, response) => {
  const { pipe } = renderToPipeableStream(<App />, {
    bootstrapScripts: ['/main.js'],
    onShellReady() {
      response.setHeader('content-type', 'text/html');
      pipe(response);
    }
  });
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

Điều này sẽ gắn các event listener vào HTML được tạo từ server và làm cho nó tương tác.

<DeepDive>

#### Đọc đường dẫn asset CSS và JS từ đầu ra build {/*reading-css-and-js-asset-paths-from-the-build-output*/}

Các URL asset cuối cùng (như các file JavaScript và CSS) thường được hash sau khi build. Ví dụ, thay vì `styles.css` bạn có thể nhận được `styles.123456.css`. Việc hash tên file asset tĩnh đảm bảo rằng mỗi build khác nhau của cùng một asset sẽ có tên file khác nhau. Điều này hữu ích vì nó cho phép bạn bật bộ nhớ đệm dài hạn một cách an toàn cho các asset tĩnh: một file với tên nhất định sẽ không bao giờ thay đổi nội dung.

Tuy nhiên, nếu bạn không biết URL asset cho đến sau khi build, không có cách nào để bạn đặt chúng trong mã nguồn. Ví dụ, hardcode `"/styles.css"` vào JSX như trước đó sẽ không hoạt động. Để giữ chúng ngoài mã nguồn, component gốc của bạn có thể đọc tên file thực từ một map được truyền dưới dạng prop:

```js {1,6}
export default function App({ assetMap }) {
  return (
    <html>
      <head>
        ...
        <link rel="stylesheet" href={assetMap['styles.css']}></link>
        ...
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

app.use('/', (request, response) => {
  const { pipe } = renderToPipeableStream(<App assetMap={assetMap} />, {
    bootstrapScripts: [assetMap['main.js']],
    onShellReady() {
      response.setHeader('content-type', 'text/html');
      pipe(response);
    }
  });
});
```

Vì server của bạn hiện đang render `<App assetMap={assetMap} />`, bạn cũng cần render nó với `assetMap` trên client để tránh lỗi hydration. Bạn có thể serialize và truyền `assetMap` cho client như thế này:

```js {9-10}
// You'd need to get this JSON from your build tooling.
const assetMap = {
  'styles.css': '/styles.123456.css',
  'main.js': '/main.123456.js'
};

app.use('/', (request, response) => {
  const { pipe } = renderToPipeableStream(<App assetMap={assetMap} />, {
    // Careful: It's safe to stringify() this because this data isn't user-generated.
    bootstrapScriptContent: `window.assetMap = ${JSON.stringify(assetMap)};`,
    bootstrapScripts: [assetMap['main.js']],
    onShellReady() {
      response.setHeader('content-type', 'text/html');
      pipe(response);
    }
  });
});
```

Trong ví dụ trên, tùy chọn `bootstrapScriptContent` thêm một thẻ `<script>` inline bổ sung đặt biến global `window.assetMap` trên client. Điều này cho phép code phía client đọc cùng `assetMap`:

```js {4}
import { hydrateRoot } from 'react-dom/client';
import App from './App.js';

hydrateRoot(document, <App assetMap={window.assetMap} />);
```

Cả client và server đều render `App` với cùng prop `assetMap`, vì vậy không có lỗi hydration.

</DeepDive>

---

### Streaming thêm nội dung khi nó được tải {/*streaming-more-content-as-it-loads*/}

Streaming cho phép người dùng bắt đầu xem nội dung ngay cả trước khi tất cả dữ liệu đã được tải trên server. Ví dụ, hãy xem xét một trang hồ sơ hiển thị ảnh bìa, thanh bên với bạn bè và ảnh, và danh sách bài viết:

```js
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Sidebar>
        <Friends />
        <Photos />
      </Sidebar>
      <Posts />
    </ProfileLayout>
  );
}
```

Hãy tưởng tượng rằng việc tải dữ liệu cho `<Posts />` mất một thời gian. Lý tưởng nhất, bạn muốn hiển thị phần còn lại của nội dung trang hồ sơ cho người dùng mà không cần chờ các bài viết. Để làm điều này, [bọc `Posts` trong một ranh giới `<Suspense>`:](/reference/react/Suspense#displaying-a-fallback-while-content-is-loading)

```js {9,11}
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

Điều này yêu cầu React bắt đầu streaming HTML trước khi `Posts` tải dữ liệu của nó. React sẽ gửi HTML cho fallback loading (`PostsGlimmer`) trước, và sau đó, khi `Posts` hoàn thành việc tải dữ liệu, React sẽ gửi HTML còn lại cùng với một thẻ `<script>` inline thay thế fallback loading bằng HTML đó. Từ góc nhìn của người dùng, trang sẽ xuất hiện trước tiên với `PostsGlimmer`, sau đó được thay thế bằng `Posts`.

Bạn có thể [lồng thêm các ranh giới `<Suspense>`](/reference/react/Suspense#revealing-nested-content-as-it-loads) để tạo một chuỗi loading chi tiết hơn:

```js {5,13}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Suspense fallback={<BigSpinner />}>
        <Sidebar>
          <Friends />
          <Photos />
        </Sidebar>
        <Suspense fallback={<PostsGlimmer />}>
          <Posts />
        </Suspense>
      </Suspense>
    </ProfileLayout>
  );
}
```

Trong ví dụ này, React có thể bắt đầu streaming trang sớm hơn. Chỉ `ProfileLayout` và `ProfileCover` phải hoàn thành render trước vì chúng không được bọc trong bất kỳ ranh giới `<Suspense>` nào. Tuy nhiên, nếu `Sidebar`, `Friends`, hoặc `Photos` cần tải dữ liệu, React sẽ gửi HTML cho fallback `BigSpinner` thay thế. Sau đó, khi có thêm dữ liệu, nội dung sẽ tiếp tục được hiển thị cho đến khi tất cả trở nên hiển thị.

Streaming không cần chờ React tải trong trình duyệt, hoặc ứng dụng của bạn trở nên tương tác. Nội dung HTML từ server sẽ được hiển thị dần dần trước khi bất kỳ thẻ `<script>` nào được tải.

[Đọc thêm về cách streaming HTML hoạt động.](https://github.com/reactwg/react-18/discussions/37)

<Note>

**Chỉ các nguồn dữ liệu hỗ trợ Suspense mới kích hoạt component Suspense.** Chúng bao gồm:

- Fetch dữ liệu với các framework hỗ trợ Suspense như [Relay](https://relay.dev/docs/guided-tour/rendering/loading-states/) và [Next.js](https://nextjs.org/docs/getting-started/react-essentials)
- Lazy-loading code component với [`lazy`](/reference/react/lazy)
- Đọc giá trị của một Promise với [`use`](/reference/react/use)

Suspense **không** phát hiện khi dữ liệu được fetch bên trong một Effect hoặc event handler.

Cách chính xác bạn tải dữ liệu trong component `Posts` ở trên phụ thuộc vào framework của bạn. Nếu bạn sử dụng một framework hỗ trợ Suspense, bạn sẽ tìm thấy chi tiết trong tài liệu fetch dữ liệu của nó.

Fetch dữ liệu hỗ trợ Suspense mà không sử dụng framework có quan điểm vẫn chưa được hỗ trợ. Các yêu cầu để triển khai một nguồn dữ liệu hỗ trợ Suspense vẫn chưa ổn định và chưa được tài liệu hóa. Một API chính thức để tích hợp các nguồn dữ liệu với Suspense sẽ được phát hành trong phiên bản tương lai của React.

</Note>

---

### Chỉ định những gì nằm trong shell {/*specifying-what-goes-into-the-shell*/}

Phần ứng dụng của bạn nằm ngoài bất kỳ ranh giới `<Suspense>` nào được gọi là *shell:*

```js {3-5,13,14}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Suspense fallback={<BigSpinner />}>
        <Sidebar>
          <Friends />
          <Photos />
        </Sidebar>
        <Suspense fallback={<PostsGlimmer />}>
          <Posts />
        </Suspense>
      </Suspense>
    </ProfileLayout>
  );
}
```

Nó xác định trạng thái loading sớm nhất mà người dùng có thể thấy:

```js {3-5,13
<ProfileLayout>
  <ProfileCover />
  <BigSpinner />
</ProfileLayout>
```

Nếu bạn bọc toàn bộ ứng dụng vào một ranh giới `<Suspense>` ở gốc, shell sẽ chỉ chứa spinner đó. Tuy nhiên, đó không phải là trải nghiệm người dùng tốt vì việc thấy một spinner lớn trên màn hình có thể cảm thấy chậm hơn và khó chịu hơn so với việc chờ thêm một chút và thấy layout thực sự. Đây là lý do tại sao thông thường bạn sẽ muốn đặt các ranh giới `<Suspense>` sao cho shell cảm thấy *tối thiểu nhưng hoàn chỉnh*--giống như bộ khung của toàn bộ layout trang.

Callback `onShellReady` được kích hoạt khi toàn bộ shell đã được render. Thông thường, bạn sẽ bắt đầu streaming lúc này:

```js {3-6}
const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.setHeader('content-type', 'text/html');
    pipe(response);
  }
});
```

Vào thời điểm `onShellReady` được kích hoạt, các component trong các ranh giới `<Suspense>` lồng nhau có thể vẫn đang tải dữ liệu.

---

### Ghi log các lỗi nghiêm trọng trên server {/*logging-crashes-on-the-server*/}

Mặc định, tất cả các lỗi trên server được ghi vào console. Bạn có thể ghi đè hành vi này để ghi log báo cáo lỗi:

```js {7-10}
const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.setHeader('content-type', 'text/html');
    pipe(response);
  },
  onError(error) {
    console.error(error);
    logServerCrashReport(error);
  }
});
```

Nếu bạn cung cấp một implementation `onError` tùy chỉnh, đừng quên cũng ghi log lỗi ra console như trên.

---

### Phục hồi từ lỗi bên trong shell {/*recovering-from-errors-inside-the-shell*/}

Trong ví dụ này, shell chứa `ProfileLayout`, `ProfileCover`, và `PostsGlimmer`:

```js {3-5,7-8}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Suspense fallback={<PostsGlimmer />}>
        <Posts />
      </Suspense>
    </ProfileLayout>
  );
}
```

Nếu lỗi xảy ra khi render các component đó, React sẽ không có HTML có ý nghĩa nào để gửi cho client. Ghi đè `onShellError` để gửi một HTML dự phòng không phụ thuộc vào server rendering như giải pháp cuối cùng:

```js {7-11}
const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.setHeader('content-type', 'text/html');
    pipe(response);
  },
  onShellError(error) {
    response.statusCode = 500;
    response.setHeader('content-type', 'text/html');
    response.send('<h1>Something went wrong</h1>');
  },
  onError(error) {
    console.error(error);
    logServerCrashReport(error);
  }
});
```

Nếu có lỗi khi tạo shell, cả `onError` và `onShellError` đều sẽ được kích hoạt. Sử dụng `onError` để báo cáo lỗi và sử dụng `onShellError` để gửi tài liệu HTML dự phòng. HTML dự phòng của bạn không nhất thiết phải là trang lỗi. Thay vào đó, bạn có thể bao gồm một shell thay thế chỉ render ứng dụng trên client.

---

### Phục hồi từ lỗi bên ngoài shell {/*recovering-from-errors-outside-the-shell*/}

Trong ví dụ này, component `<Posts />` được bọc trong `<Suspense>` nên nó *không* phải là một phần của shell:

```js {6}
function ProfilePage() {
  return (
    <ProfileLayout>
      <ProfileCover />
      <Suspense fallback={<PostsGlimmer />}>
        <Posts />
      </Suspense>
    </ProfileLayout>
  );
}
```

Nếu lỗi xảy ra trong component `Posts` hoặc bên trong nó, React sẽ [cố gắng phục hồi từ nó:](/reference/react/Suspense#providing-a-fallback-for-server-errors-and-client-only-content)

1. Nó sẽ phát ra fallback loading cho ranh giới `<Suspense>` gần nhất (`PostsGlimmer`) vào HTML.
2. Nó sẽ "từ bỏ" việc cố render nội dung `Posts` trên server.
3. Khi code JavaScript tải trên client, React sẽ *thử lại* render `Posts` trên client.

Nếu việc thử lại render `Posts` trên client *cũng* thất bại, React sẽ throw lỗi trên client. Như với tất cả các lỗi được throw trong quá trình render, [ranh giới lỗi cha gần nhất](/reference/react/Component#static-getderivedstatefromerror) xác định cách hiển thị lỗi cho người dùng. Trong thực tế, điều này có nghĩa là người dùng sẽ thấy một chỉ báo loading cho đến khi chắc chắn rằng lỗi không thể phục hồi.

Nếu việc thử lại render `Posts` trên client thành công, fallback loading từ server sẽ được thay thế bằng đầu ra render từ client. Người dùng sẽ không biết rằng đã có lỗi server. Tuy nhiên, callback `onError` của server và callback [`onRecoverableError`](/reference/react-dom/client/hydrateRoot#hydrateroot) của client sẽ được kích hoạt để bạn có thể được thông báo về lỗi.

---

### Đặt mã trạng thái {/*setting-the-status-code*/}

Streaming đưa ra một sự đánh đổi. Bạn muốn bắt đầu streaming trang sớm nhất có thể để người dùng có thể thấy nội dung sớm hơn. Tuy nhiên, một khi bạn bắt đầu streaming, bạn không thể đặt mã trạng thái response nữa.

Bằng cách [chia ứng dụng của bạn](#specifying-what-goes-into-the-shell) thành shell (phía trên tất cả các ranh giới `<Suspense>`) và phần nội dung còn lại, bạn đã giải quyết một phần của vấn đề này. Nếu shell có lỗi, bạn sẽ nhận được callback `onShellError` cho phép bạn đặt mã trạng thái lỗi. Nếu không, bạn biết rằng ứng dụng có thể phục hồi trên client, vì vậy bạn có thể gửi "OK".

```js {4}
const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.statusCode = 200;
    response.setHeader('content-type', 'text/html');
    pipe(response);
  },
  onShellError(error) {
    response.statusCode = 500;
    response.setHeader('content-type', 'text/html');
    response.send('<h1>Something went wrong</h1>');
  },
  onError(error) {
    console.error(error);
    logServerCrashReport(error);
  }
});
```

Nếu một component *ngoài* shell (tức là bên trong một ranh giới `<Suspense>`) throw lỗi, React sẽ không dừng render. Điều này có nghĩa là callback `onError` sẽ được kích hoạt, nhưng bạn vẫn sẽ nhận được `onShellReady` thay vì `onShellError`. Điều này là vì React sẽ cố phục hồi từ lỗi đó trên client, [như đã mô tả ở trên.](#recovering-from-errors-outside-the-shell)

Tuy nhiên, nếu bạn muốn, bạn có thể sử dụng thực tế là đã có lỗi xảy ra để đặt mã trạng thái:

```js {1,6,16}
let didError = false;

const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.statusCode = didError ? 500 : 200;
    response.setHeader('content-type', 'text/html');
    pipe(response);
  },
  onShellError(error) {
    response.statusCode = 500;
    response.setHeader('content-type', 'text/html');
    response.send('<h1>Something went wrong</h1>');
  },
  onError(error) {
    didError = true;
    console.error(error);
    logServerCrashReport(error);
  }
});
```

Điều này chỉ bắt các lỗi ngoài shell xảy ra trong khi tạo nội dung shell ban đầu, vì vậy nó không toàn diện. Nếu việc biết liệu lỗi có xảy ra cho một nội dung nào đó là quan trọng, bạn có thể di chuyển nó lên vào shell.

---

### Xử lý các lỗi khác nhau theo các cách khác nhau {/*handling-different-errors-in-different-ways*/}

Bạn có thể [tạo các lớp con `Error` của riêng mình](https://javascript.info/custom-errors) và sử dụng toán tử [`instanceof`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/instanceof) để kiểm tra lỗi nào được throw. Ví dụ, bạn có thể định nghĩa một `NotFoundError` tùy chỉnh và throw nó từ component. Sau đó các callback `onError`, `onShellReady`, và `onShellError` của bạn có thể làm điều gì đó khác nhau tùy thuộc vào loại lỗi:

```js {2,4-14,19,24,30}
let didError = false;
let caughtError = null;

function getStatusCode() {
  if (didError) {
    if (caughtError instanceof NotFoundError) {
      return 404;
    } else {
      return 500;
    }
  } else {
    return 200;
  }
}

const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    response.statusCode = getStatusCode();
    response.setHeader('content-type', 'text/html');
    pipe(response);
  },
  onShellError(error) {
   response.statusCode = getStatusCode();
   response.setHeader('content-type', 'text/html');
   response.send('<h1>Something went wrong</h1>');
  },
  onError(error) {
    didError = true;
    caughtError = error;
    console.error(error);
    logServerCrashReport(error);
  }
});
```

Hãy nhớ rằng một khi bạn phát ra shell và bắt đầu streaming, bạn không thể thay đổi mã trạng thái.

---

### Chờ tất cả nội dung tải cho các crawler và tạo trang tĩnh {/*waiting-for-all-content-to-load-for-crawlers-and-static-generation*/}

Streaming mang lại trải nghiệm người dùng tốt hơn vì người dùng có thể thấy nội dung khi nó trở nên khả dụng.

Tuy nhiên, khi một crawler truy cập trang của bạn, hoặc nếu bạn đang tạo các trang vào thời điểm build, bạn có thể muốn cho tất cả nội dung tải trước rồi mới tạo ra đầu ra HTML cuối cùng thay vì hiển thị dần dần.

Bạn có thể chờ tất cả nội dung tải bằng cách sử dụng callback `onAllReady`:


```js {2,7,11,18-24}
let didError = false;
let isCrawler = // ... depends on your bot detection strategy ...

const { pipe } = renderToPipeableStream(<App />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() {
    if (!isCrawler) {
      response.statusCode = didError ? 500 : 200;
      response.setHeader('content-type', 'text/html');
      pipe(response);
    }
  },
  onShellError(error) {
    response.statusCode = 500;
    response.setHeader('content-type', 'text/html');
    response.send('<h1>Something went wrong</h1>');
  },
  onAllReady() {
    if (isCrawler) {
      response.statusCode = didError ? 500 : 200;
      response.setHeader('content-type', 'text/html');
      pipe(response);
    }
  },
  onError(error) {
    didError = true;
    console.error(error);
    logServerCrashReport(error);
  }
});
```

Một người truy cập thông thường sẽ nhận được một stream nội dung được tải dần dần. Một crawler sẽ nhận được đầu ra HTML cuối cùng sau khi tất cả dữ liệu tải xong. Tuy nhiên, điều này cũng có nghĩa là crawler sẽ phải chờ *tất cả* dữ liệu, một số trong đó có thể tải chậm hoặc có lỗi. Tùy thuộc vào ứng dụng của bạn, bạn có thể chọn gửi shell cho các crawler luôn.

---

### Hủy bỏ render phía server {/*aborting-server-rendering*/}

Bạn có thể buộc việc render phía server "từ bỏ" sau một khoảng thời gian chờ:

```js {1,5-7}
const { pipe, abort } = renderToPipeableStream(<App />, {
  // ...
});

setTimeout(() => {
  abort();
}, 10000);
```

React sẽ flush các fallback loading còn lại dưới dạng HTML, và sẽ cố gắng render phần còn lại trên client.
