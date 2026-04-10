---
title: renderToStaticMarkup
---

<Intro>

`renderToStaticMarkup` render một cây React không tương tác thành một chuỗi HTML.

```js
const html = renderToStaticMarkup(reactNode, options?)
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `renderToStaticMarkup(reactNode, options?)` {/*rendertostaticmarkup*/}

Trên server, gọi `renderToStaticMarkup` để render ứng dụng của bạn thành HTML.

```js
import { renderToStaticMarkup } from 'react-dom/server';

const html = renderToStaticMarkup(<Page />);
```

Nó sẽ tạo ra đầu ra HTML không tương tác của các component React của bạn.

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `reactNode`: Một React node mà bạn muốn render thành HTML. Ví dụ, một JSX node như `<Page />`.
* **tùy chọn** `options`: Một object cho việc render phía server.
  * **tùy chọn** `identifierPrefix`: Một chuỗi tiền tố mà React sử dụng cho các ID được tạo bởi [`useId`.](/reference/react/useId) Hữu ích để tránh xung đột khi sử dụng nhiều root trên cùng một trang.

#### Giá trị trả về {/*returns*/}

Một chuỗi HTML.

#### Lưu ý {/*caveats*/}

* Đầu ra của `renderToStaticMarkup` không thể được hydrate.

* `renderToStaticMarkup` có hỗ trợ Suspense hạn chế. Nếu một component bị suspend, `renderToStaticMarkup` sẽ ngay lập tức gửi fallback của nó dưới dạng HTML.

* `renderToStaticMarkup` hoạt động trong trình duyệt, nhưng việc sử dụng nó trong code phía client không được khuyến nghị. Nếu bạn cần render một component thành HTML trong trình duyệt, [hãy lấy HTML bằng cách render nó vào một DOM node.](/reference/react-dom/server/renderToString#removing-rendertostring-from-the-client-code)

---

## Cách sử dụng {/*usage*/}

### Render một cây React không tương tác thành chuỗi HTML {/*rendering-a-non-interactive-react-tree-as-html-to-a-string*/}

Gọi `renderToStaticMarkup` để render ứng dụng của bạn thành một chuỗi HTML mà bạn có thể gửi cùng với response từ server:

```js {5-6}
import { renderToStaticMarkup } from 'react-dom/server';

// The route handler syntax depends on your backend framework
app.use('/', (request, response) => {
  const html = renderToStaticMarkup(<Page />);
  response.send(html);
});
```

Điều này sẽ tạo ra đầu ra HTML không tương tác ban đầu của các component React của bạn.

<Pitfall>

Phương thức này render **HTML không tương tác và không thể được hydrate.** Điều này hữu ích nếu bạn muốn sử dụng React như một trình tạo trang tĩnh đơn giản, hoặc nếu bạn đang render nội dung hoàn toàn tĩnh như email.

Các ứng dụng tương tác nên sử dụng [`renderToString`](/reference/react-dom/server/renderToString) trên server và [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) trên client.

</Pitfall>
