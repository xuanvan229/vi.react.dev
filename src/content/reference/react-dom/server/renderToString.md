---
title: renderToString
---

<Pitfall>

`renderToString` không hỗ trợ streaming hoặc chờ dữ liệu. [Xem các giải pháp thay thế.](#alternatives)

</Pitfall>

<Intro>

`renderToString` render một cây React thành một chuỗi HTML.

```js
const html = renderToString(reactNode, options?)
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `renderToString(reactNode, options?)` {/*rendertostring*/}

Trên server, gọi `renderToString` để render ứng dụng của bạn thành HTML.

```js
import { renderToString } from 'react-dom/server';

const html = renderToString(<App />);
```

Trên client, gọi [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) để làm cho HTML được tạo từ server trở nên tương tác.

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `reactNode`: Một React node mà bạn muốn render thành HTML. Ví dụ, một JSX node như `<App />`.

* **tùy chọn** `options`: Một object cho việc render phía server.
  * **tùy chọn** `identifierPrefix`: Một chuỗi tiền tố mà React sử dụng cho các ID được tạo bởi [`useId`.](/reference/react/useId) Hữu ích để tránh xung đột khi sử dụng nhiều root trên cùng một trang. Phải là cùng tiền tố được truyền cho [`hydrateRoot`.](/reference/react-dom/client/hydrateRoot#parameters)

#### Giá trị trả về {/*returns*/}

Một chuỗi HTML.

#### Lưu ý {/*caveats*/}

* `renderToString` có hỗ trợ Suspense hạn chế. Nếu một component bị suspend, `renderToString` sẽ ngay lập tức gửi fallback của nó dưới dạng HTML.

* `renderToString` hoạt động trong trình duyệt, nhưng việc sử dụng nó trong code phía client [không được khuyến nghị.](#removing-rendertostring-from-the-client-code)

---

## Cách sử dụng {/*usage*/}

### Render một cây React thành chuỗi HTML {/*rendering-a-react-tree-as-html-to-a-string*/}

Gọi `renderToString` để render ứng dụng của bạn thành một chuỗi HTML mà bạn có thể gửi cùng với response từ server:

```js {5-6}
import { renderToString } from 'react-dom/server';

// The route handler syntax depends on your backend framework
app.use('/', (request, response) => {
  const html = renderToString(<App />);
  response.send(html);
});
```

Điều này sẽ tạo ra đầu ra HTML không tương tác ban đầu của các component React của bạn. Trên client, bạn sẽ cần gọi [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) để *hydrate* HTML được tạo từ server đó và làm cho nó tương tác.


<Pitfall>

`renderToString` không hỗ trợ streaming hoặc chờ dữ liệu. [Xem các giải pháp thay thế.](#alternatives)

</Pitfall>

---

## Giải pháp thay thế {/*alternatives*/}

### Chuyển từ `renderToString` sang streaming render trên server {/*migrating-from-rendertostring-to-a-streaming-method-on-the-server*/}

`renderToString` trả về một chuỗi ngay lập tức, vì vậy nó không hỗ trợ streaming nội dung khi nội dung được tải.

Khi có thể, chúng tôi khuyến nghị sử dụng các giải pháp đầy đủ tính năng sau:

* Nếu bạn sử dụng Node.js, hãy dùng [`renderToPipeableStream`.](/reference/react-dom/server/renderToPipeableStream)
* Nếu bạn sử dụng Deno hoặc một edge runtime hiện đại với [Web Streams](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API), hãy dùng [`renderToReadableStream`.](/reference/react-dom/server/renderToReadableStream)

Bạn có thể tiếp tục sử dụng `renderToString` nếu môi trường server của bạn không hỗ trợ streams.

---

### Chuyển từ `renderToString` sang static prerender trên server {/*migrating-from-rendertostring-to-a-static-prerender-on-the-server*/}

`renderToString` trả về một chuỗi ngay lập tức, vì vậy nó không hỗ trợ chờ dữ liệu tải để tạo HTML tĩnh.

Chúng tôi khuyến nghị sử dụng các giải pháp đầy đủ tính năng sau:

* Nếu bạn sử dụng Node.js, hãy dùng [`prerenderToNodeStream`.](/reference/react-dom/static/prerenderToNodeStream)
* Nếu bạn sử dụng Deno hoặc một edge runtime hiện đại với [Web Streams](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API), hãy dùng [`prerender`.](/reference/react-dom/static/prerender)

Bạn có thể tiếp tục sử dụng `renderToString` nếu môi trường tạo trang tĩnh của bạn không hỗ trợ streams.

---

### Loại bỏ `renderToString` khỏi code phía client {/*removing-rendertostring-from-the-client-code*/}

Đôi khi, `renderToString` được sử dụng trên client để chuyển đổi một component thành HTML.

```js {1-2}
// 🚩 Unnecessary: using renderToString on the client
import { renderToString } from 'react-dom/server';

const html = renderToString(<MyIcon />);
console.log(html); // For example, "<svg>...</svg>"
```

Import `react-dom/server` **trên client** làm tăng kích thước bundle một cách không cần thiết và nên được tránh. Nếu bạn cần render một component thành HTML trong trình duyệt, hãy sử dụng [`createRoot`](/reference/react-dom/client/createRoot) và đọc HTML từ DOM:

```js
import { createRoot } from 'react-dom/client';
import { flushSync } from 'react-dom';

const div = document.createElement('div');
const root = createRoot(div);
flushSync(() => {
  root.render(<MyIcon />);
});
console.log(div.innerHTML); // For example, "<svg>...</svg>"
```

Lệnh gọi [`flushSync`](/reference/react-dom/flushSync) là cần thiết để DOM được cập nhật trước khi đọc thuộc tính [`innerHTML`](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML) của nó.

---

## Khắc phục sự cố {/*troubleshooting*/}

### Khi một component bị suspend, HTML luôn chứa fallback {/*when-a-component-suspends-the-html-always-contains-a-fallback*/}

`renderToString` không hỗ trợ đầy đủ Suspense.

Nếu một component bị suspend (ví dụ, vì nó được định nghĩa với [`lazy`](/reference/react/lazy) hoặc fetch dữ liệu), `renderToString` sẽ không chờ nội dung của nó được resolve. Thay vào đó, `renderToString` sẽ tìm ranh giới [`<Suspense>`](/reference/react/Suspense) gần nhất phía trên nó và render prop `fallback` của nó trong HTML. Nội dung sẽ không xuất hiện cho đến khi code phía client được tải.

Để giải quyết điều này, hãy sử dụng một trong các [giải pháp streaming được khuyến nghị.](#alternatives) Đối với server-side rendering, chúng có thể stream nội dung theo từng phần khi nó được resolve trên server để người dùng thấy trang được điền dần trước khi code phía client được tải. Đối với tạo trang tĩnh, chúng có thể chờ tất cả nội dung được resolve trước khi tạo HTML tĩnh.
