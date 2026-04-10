---
meta: "<meta>"
---

<Intro>

[Component `<meta>` tích hợp sẵn của trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta) cho phép bạn thêm metadata vào tài liệu.

```js
<meta name="keywords" content="React, JavaScript, semantic markup, html" />
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `<meta>` {/*meta*/}

Để thêm metadata tài liệu, hãy render [component `<meta>` tích hợp sẵn của trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta). Bạn có thể render `<meta>` từ bất kỳ component nào và React sẽ luôn đặt phần tử DOM tương ứng trong phần head của tài liệu.

```js
<meta name="keywords" content="React, JavaScript, semantic markup, html" />
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Props {/*props*/}

`<meta>` hỗ trợ tất cả [các props phần tử chung.](/reference/react-dom/components/common#common-props)

Nó phải có *chính xác một* trong số các props sau: `name`, `httpEquiv`, `charset`, `itemProp`. Component `<meta>` làm điều gì đó khác nhau tùy thuộc vào prop nào được chỉ định.

* `name`: một chuỗi. Chỉ định [loại metadata](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta/name) cần được đính kèm vào tài liệu.
* `charset`: một chuỗi. Chỉ định bộ ký tự được sử dụng bởi tài liệu. Giá trị hợp lệ duy nhất là `"utf-8"`.
* `httpEquiv`: một chuỗi. Chỉ định một chỉ thị để xử lý tài liệu.
* `itemProp`: một chuỗi. Chỉ định metadata về một mục cụ thể trong tài liệu thay vì toàn bộ tài liệu.
* `content`: một chuỗi. Chỉ định metadata cần được đính kèm khi sử dụng với các props `name` hoặc `itemProp` hoặc hành vi của chỉ thị khi sử dụng với prop `httpEquiv`.

#### Hành vi render đặc biệt {/*special-rendering-behavior*/}

React sẽ luôn đặt phần tử DOM tương ứng với component `<meta>` trong `<head>` của tài liệu, bất kể nó được render ở đâu trong cây React. `<head>` là vị trí duy nhất hợp lệ cho `<meta>` trong DOM, tuy nhiên sẽ tiện lợi và giữ mọi thứ có thể kết hợp được nếu một component đại diện cho một trang cụ thể có thể tự render các component `<meta>`.

Có một ngoại lệ cho điều này: nếu `<meta>` có prop [`itemProp`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/itemprop), sẽ không có hành vi đặc biệt, vì trong trường hợp này nó không đại diện cho metadata về tài liệu mà là metadata về một phần cụ thể của trang.

---

## Cách sử dụng {/*usage*/}

### Chú thích tài liệu với metadata {/*annotating-the-document-with-metadata*/}

Bạn có thể chú thích tài liệu với metadata như từ khóa, tóm tắt, hoặc tên tác giả. React sẽ đặt metadata này trong `<head>` của tài liệu bất kể nó được render ở đâu trong cây React.

```html
<meta name="author" content="John Smith" />
<meta name="keywords" content="React, JavaScript, semantic markup, html" />
<meta name="description" content="API reference for the <meta> component in React DOM" />
```

Bạn có thể render component `<meta>` từ bất kỳ component nào. React sẽ đặt một DOM node `<meta>` trong `<head>` của tài liệu.

<SandpackWithHTMLOutput>

```js src/App.js active
import ShowRenderedHTML from './ShowRenderedHTML.js';

export default function SiteMapPage() {
  return (
    <ShowRenderedHTML>
      <meta name="keywords" content="React" />
      <meta name="description" content="A site map for the React website" />
      <h1>Site Map</h1>
      <p>...</p>
    </ShowRenderedHTML>
  );
}
```

</SandpackWithHTMLOutput>

### Chú thích các mục cụ thể trong tài liệu với metadata {/*annotating-specific-items-within-the-document-with-metadata*/}

Bạn có thể sử dụng component `<meta>` với prop `itemProp` để chú thích các mục cụ thể trong tài liệu với metadata. Trong trường hợp này, React sẽ *không* đặt những chú thích này trong `<head>` của tài liệu mà sẽ đặt chúng như bất kỳ component React nào khác.

```js
<section itemScope>
  <h3>Annotating specific items</h3>
  <meta itemProp="description" content="API reference for using <meta> with itemProp" />
  <p>...</p>
</section>
```
