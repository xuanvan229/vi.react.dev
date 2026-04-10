---
title: resumeToPipeableStream
---

<Intro>

`resumeToPipeableStream` stream một cây React đã được pre-render thành một [Node.js Stream](https://nodejs.org/api/stream.html) có thể pipe.

```js
const {pipe, abort} = await resumeToPipeableStream(reactNode, postponedState, options?)
```

</Intro>

<InlineToc />

<Note>

API này dành riêng cho Node.js. Các môi trường có [Web Streams,](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API) như Deno và các edge runtime hiện đại, nên sử dụng [`resume`](/reference/react-dom/server/renderToReadableStream) thay thế.

</Note>

---

## Tham khảo {/*reference*/}

### `resumeToPipeableStream(node, postponed, options?)` {/*resume-to-pipeable-stream*/}

Gọi `resume` để tiếp tục render một cây React đã được pre-render thành HTML vào một [Node.js Stream.](https://nodejs.org/api/stream.html#writable-streams)

```js
import { resume } from 'react-dom/server';
import {getPostponedState} from './storage';

async function handler(request, response) {
  const postponed = await getPostponedState(request);
  const {pipe} = resumeToPipeableStream(<App />, postponed, {
    onShellReady: () => {
      pipe(response);
    }
  });
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
  * **tùy chọn** `onShellReady`: Một callback được kích hoạt ngay sau khi [shell](#specifying-what-goes-into-the-shell) đã hoàn thành. Bạn có thể gọi `pipe` ở đây để bắt đầu streaming. React sẽ [stream nội dung bổ sung](#streaming-more-content-as-it-loads) sau shell cùng với các thẻ `<script>` inline thay thế các fallback loading HTML bằng nội dung.
  * **tùy chọn** `onShellError`: Một callback được kích hoạt nếu có lỗi khi render shell. Nó nhận lỗi làm đối số. Chưa có byte nào được phát ra từ stream, và cả `onShellReady` lẫn `onAllReady` đều sẽ không được gọi, vì vậy bạn có thể [xuất một shell HTML dự phòng](#recovering-from-errors-inside-the-shell) hoặc sử dụng prelude.


#### Giá trị trả về {/*returns*/}

`resume` trả về một object với hai phương thức:

* `pipe` xuất HTML vào [Writable Node.js Stream](https://nodejs.org/api/stream.html#writable-streams) được cung cấp. Gọi `pipe` trong `onShellReady` nếu bạn muốn bật streaming, hoặc trong `onAllReady` cho các crawler và tạo trang tĩnh.
* `abort` cho phép bạn [hủy bỏ việc render phía server](#aborting-server-rendering) và render phần còn lại trên client.

#### Lưu ý {/*caveats*/}

- `resumeToPipeableStream` không chấp nhận các tùy chọn cho `bootstrapScripts`, `bootstrapScriptContent`, hoặc `bootstrapModules`. Thay vào đó, bạn cần truyền những tùy chọn này cho lệnh gọi `prerender` tạo ra `postponedState`. Bạn cũng có thể chèn nội dung bootstrap vào writable stream theo cách thủ công.
- `resumeToPipeableStream` không chấp nhận `identifierPrefix` vì prefix cần phải giống nhau trong cả `prerender` và `resumeToPipeableStream`.
- Vì `nonce` không thể được cung cấp cho prerender, bạn chỉ nên cung cấp `nonce` cho `resumeToPipeableStream` nếu bạn không cung cấp script cho prerender.
- `resumeToPipeableStream` re-render từ root cho đến khi tìm thấy một component chưa được pre-render đầy đủ. Chỉ các Component được pre-render đầy đủ (Component và các children của nó đã hoàn thành prerendering) mới bị bỏ qua hoàn toàn.

## Cách sử dụng {/*usage*/}

### Đọc thêm {/*further-reading*/}

Việc tiếp tục (resuming) hoạt động giống như `renderToReadableStream`. Để biết thêm ví dụ, hãy xem [phần usage của `renderToReadableStream`](/reference/react-dom/server/renderToReadableStream#usage).
[Phần usage của `prerender`](/reference/react-dom/static/prerender#usage) bao gồm các ví dụ về cách sử dụng `prerenderToNodeStream` cụ thể.
