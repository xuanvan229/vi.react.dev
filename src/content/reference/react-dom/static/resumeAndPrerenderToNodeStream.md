---
title: resumeAndPrerenderToNodeStream
---

<Intro>

`resumeAndPrerenderToNodeStream` tiếp tục một cây React đã được prerender thành một chuỗi HTML tĩnh sử dụng một [Node.js Stream.](https://nodejs.org/api/stream.html)

```js
const {prelude, postponed} = await resumeAndPrerenderToNodeStream(reactNode, postponedState, options?)
```

</Intro>

<InlineToc />

<Note>

API này dành riêng cho Node.js. Các môi trường có [Web Streams,](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API) như Deno và các edge runtime hiện đại, nên sử dụng [`prerender`](/reference/react-dom/static/prerender) thay thế.

</Note>

---

## Tham khảo {/*reference*/}

### `resumeAndPrerenderToNodeStream(reactNode, postponedState, options?)` {/*resumeandprerendertolnodestream*/}

Gọi `resumeAndPrerenderToNodeStream` để tiếp tục một cây React đã được prerender thành một chuỗi HTML tĩnh.

```js
import { resumeAndPrerenderToNodeStream } from 'react-dom/static';
import { getPostponedState } from 'storage';

async function handler(request, writable) {
  const postponedState = getPostponedState(request);
  const { prelude } = await resumeAndPrerenderToNodeStream(<App />, JSON.parse(postponedState));
  prelude.pipe(writable);
}
```

Trên client, gọi [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) để làm cho HTML được tạo từ server trở nên tương tác.

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `reactNode`: React node mà bạn đã gọi `prerender` (hoặc một `resumeAndPrerenderToNodeStream` trước đó) với. Ví dụ, một phần tử JSX như `<App />`. Nó được kỳ vọng đại diện cho toàn bộ tài liệu, vì vậy component `App` nên render thẻ `<html>`.
* `postponedState`: Object `postpone` opaque được trả về từ [prerender API](/reference/react-dom/static/index), được tải từ nơi bạn đã lưu trữ nó (ví dụ: redis, một file, hoặc S3).
* **tùy chọn** `options`: Một object với các tùy chọn streaming.
  * **tùy chọn** `signal`: Một [abort signal](https://developer.mozilla.org/en-US/docs/Web/API/AbortSignal) cho phép bạn [hủy bỏ render phía server](#aborting-server-rendering) và render phần còn lại trên client.
  * **tùy chọn** `onError`: Một callback được kích hoạt bất cứ khi nào có lỗi server, dù là [có thể phục hồi](#recovering-from-errors-outside-the-shell) hay [không.](#recovering-from-errors-inside-the-shell) Mặc định, nó chỉ gọi `console.error`. Nếu bạn ghi đè nó để [ghi log báo cáo lỗi,](#logging-crashes-on-the-server) hãy đảm bảo rằng bạn vẫn gọi `console.error`.

#### Giá trị trả về {/*returns*/}

`resumeAndPrerenderToNodeStream` trả về một Promise:
- Nếu render thành công, Promise sẽ resolve thành một object chứa:
  - `prelude`: một [Web Stream](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API) của HTML. Bạn có thể sử dụng stream này để gửi response theo từng chunk, hoặc bạn có thể đọc toàn bộ stream thành một chuỗi.
  - `postponed`: một object opaque có thể serialize JSON có thể được truyền cho [`resumeToNodeStream`](/reference/react-dom/server/resume) hoặc [`resumeAndPrerenderToNodeStream`](/reference/react-dom/static/resumeAndPrerenderToNodeStream) nếu `resumeAndPrerenderToNodeStream` bị hủy bỏ.
- Nếu render thất bại, Promise sẽ bị reject. [Sử dụng điều này để xuất một shell dự phòng.](/reference/react-dom/server/renderToReadableStream#recovering-from-errors-inside-the-shell)

#### Lưu ý {/*caveats*/}

`nonce` không phải là tùy chọn có sẵn khi prerendering. Các nonce phải là duy nhất cho mỗi request và nếu bạn sử dụng nonce để bảo mật ứng dụng với [CSP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CSP), sẽ không phù hợp và không an toàn khi đưa giá trị nonce vào trong chính prerender.

<Note>

### Khi nào nên sử dụng `resumeAndPrerenderToNodeStream`? {/*when-to-use-prerender*/}

API `resumeAndPrerenderToNodeStream` tĩnh được sử dụng cho tạo trang tĩnh phía server (SSG). Không giống như `renderToString`, `resumeAndPrerenderToNodeStream` chờ tất cả dữ liệu tải xong trước khi resolve. Điều này làm cho nó phù hợp để tạo HTML tĩnh cho một trang đầy đủ, bao gồm dữ liệu cần được fetch bằng Suspense. Để stream nội dung khi nó tải, hãy sử dụng streaming server-side render (SSR) API như [renderToReadableStream](/reference/react-dom/server/renderToReadableStream).

`resumeAndPrerenderToNodeStream` có thể bị hủy bỏ và sau đó tiếp tục với một `resumeAndPrerenderToNodeStream` khác hoặc tiếp tục với `resume` để hỗ trợ partial pre-rendering.

</Note>

---

## Cách sử dụng {/*usage*/}

### Đọc thêm {/*further-reading*/}

`resumeAndPrerenderToNodeStream` hoạt động tương tự như [`prerender`](/reference/react-dom/static/prerender) nhưng có thể được sử dụng để tiếp tục một quy trình prerendering đã bắt đầu trước đó và bị hủy bỏ.
Để biết thêm thông tin về việc tiếp tục một cây đã được prerender, hãy xem [tài liệu resume](/reference/react-dom/server/resume#resuming-a-prerender).
