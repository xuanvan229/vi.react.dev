---
title: Các API React DOM tĩnh
---

<Intro>

Các API `react-dom/static` cho phép bạn tạo HTML tĩnh cho các component React. Chúng có chức năng hạn chế so với các API streaming. Một [framework](/learn/creating-a-react-app#full-stack-frameworks) có thể gọi chúng cho bạn. Hầu hết các component của bạn không cần import hoặc sử dụng chúng.

</Intro>

---

## Các API tĩnh cho Web Streams {/*static-apis-for-web-streams*/}

Các phương thức này chỉ khả dụng trong các môi trường có [Web Streams](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API), bao gồm trình duyệt, Deno, và một số edge runtime hiện đại:

* [`prerender`](/reference/react-dom/static/prerender) render một cây React thành HTML tĩnh với một [Readable Web Stream.](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream)
* <ExperimentalBadge /> [`resumeAndPrerender`](/reference/react-dom/static/resumeAndPrerender) tiếp tục một cây React đã được prerender thành HTML tĩnh với một [Readable Web Stream](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream).

Node.js cũng bao gồm các phương thức này để tương thích, nhưng chúng không được khuyến nghị do hiệu suất kém hơn. Hãy sử dụng [các API dành riêng cho Node.js](#static-apis-for-nodejs-streams) thay thế.

---

## Các API tĩnh cho Node.js Streams {/*static-apis-for-nodejs-streams*/}

Các phương thức này chỉ khả dụng trong các môi trường có [Node.js Streams](https://nodejs.org/api/stream.html):

* [`prerenderToNodeStream`](/reference/react-dom/static/prerenderToNodeStream) render một cây React thành HTML tĩnh với một [Node.js Stream.](https://nodejs.org/api/stream.html)
* <ExperimentalBadge /> [`resumeAndPrerenderToNodeStream`](/reference/react-dom/static/resumeAndPrerenderToNodeStream) tiếp tục một cây React đã được prerender thành HTML tĩnh với một [Node.js Stream.](https://nodejs.org/api/stream.html)
