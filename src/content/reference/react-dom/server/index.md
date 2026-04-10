---
title: Các API React DOM phía Server
---

<Intro>

Các API `react-dom/server` cho phép bạn render các component React thành HTML ở phía server. Các API này chỉ được sử dụng trên server ở tầng cao nhất của ứng dụng để tạo HTML ban đầu. Một [framework](/learn/creating-a-react-app#full-stack-frameworks) có thể gọi chúng cho bạn. Hầu hết các component của bạn không cần import hoặc sử dụng chúng.

</Intro>

---

## Các API Server cho Web Streams {/*server-apis-for-web-streams*/}

Các phương thức này chỉ khả dụng trong các môi trường có [Web Streams](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API), bao gồm trình duyệt, Deno, và một số edge runtime hiện đại:

* [`renderToReadableStream`](/reference/react-dom/server/renderToReadableStream) render một cây React thành một [Readable Web Stream.](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream)
* [`resume`](/reference/react-dom/server/renderToPipeableStream) tiếp tục [`prerender`](/reference/react-dom/static/prerender) thành một [Readable Web Stream](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream).


<Note>

Node.js cũng bao gồm các phương thức này để tương thích, nhưng chúng không được khuyến nghị do hiệu suất kém hơn. Hãy sử dụng [các API dành riêng cho Node.js](#server-apis-for-nodejs-streams) thay thế.

</Note>
---

## Các API Server cho Node.js Streams {/*server-apis-for-nodejs-streams*/}

Các phương thức này chỉ khả dụng trong các môi trường có [Node.js Streams:](https://nodejs.org/api/stream.html)

* [`renderToPipeableStream`](/reference/react-dom/server/renderToPipeableStream) render một cây React thành một [Node.js Stream](https://nodejs.org/api/stream.html) có thể pipe.
* [`resumeToPipeableStream`](/reference/react-dom/server/renderToPipeableStream) tiếp tục [`prerenderToNodeStream`](/reference/react-dom/static/prerenderToNodeStream) thành một [Node.js Stream](https://nodejs.org/api/stream.html) có thể pipe.

---

## Các API Server cũ cho môi trường không hỗ trợ streaming {/*legacy-server-apis-for-non-streaming-environments*/}

Các phương thức này có thể được sử dụng trong các môi trường không hỗ trợ streams:

* [`renderToString`](/reference/react-dom/server/renderToString) render một cây React thành một chuỗi.
* [`renderToStaticMarkup`](/reference/react-dom/server/renderToStaticMarkup) render một cây React không tương tác thành một chuỗi.

Chúng có chức năng hạn chế so với các API streaming.
