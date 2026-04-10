---
title: "<title>"
---

<Intro>

[Component `<title>` tích hợp sẵn của trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/title) cho phép bạn chỉ định tiêu đề của tài liệu.

```js
<title>My Blog</title>
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `<title>` {/*title*/}

Để chỉ định tiêu đề của tài liệu, hãy render [component `<title>` tích hợp sẵn của trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/title). Bạn có thể render `<title>` từ bất kỳ component nào và React sẽ luôn đặt phần tử DOM tương ứng trong phần head của tài liệu.

```js
<title>My Blog</title>
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Props {/*props*/}

`<title>` hỗ trợ tất cả [các props phần tử chung.](/reference/react-dom/components/common#common-props)

* `children`: `<title>` chỉ chấp nhận văn bản làm children. Văn bản này sẽ trở thành tiêu đề của tài liệu. Bạn cũng có thể truyền các component của riêng bạn miễn là chúng chỉ render văn bản.

#### Hành vi render đặc biệt {/*special-rendering-behavior*/}

React sẽ luôn đặt phần tử DOM tương ứng với component `<title>` trong `<head>` của tài liệu, bất kể nó được render ở đâu trong cây React. `<head>` là vị trí duy nhất hợp lệ cho `<title>` trong DOM, tuy nhiên sẽ tiện lợi và giữ mọi thứ có thể kết hợp được nếu một component đại diện cho một trang cụ thể có thể tự render `<title>` của nó.

Có hai ngoại lệ cho điều này:
* Nếu `<title>` nằm trong một component `<svg>`, thì sẽ không có hành vi đặc biệt, vì trong ngữ cảnh này nó không đại diện cho tiêu đề tài liệu mà là một [chú thích trợ năng cho đồ họa SVG đó](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/title).
* Nếu `<title>` có prop [`itemProp`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/itemprop), sẽ không có hành vi đặc biệt, vì trong trường hợp này nó không đại diện cho tiêu đề tài liệu mà là metadata về một phần cụ thể của trang.

<Pitfall>

Chỉ render một `<title>` tại một thời điểm. Nếu nhiều hơn một component render thẻ `<title>` cùng một lúc, React sẽ đặt tất cả những tiêu đề đó vào phần head của tài liệu. Khi điều này xảy ra, hành vi của trình duyệt và công cụ tìm kiếm là không xác định.

</Pitfall>

---

## Cách sử dụng {/*usage*/}

### Đặt tiêu đề tài liệu {/*set-the-document-title*/}

Render component `<title>` từ bất kỳ component nào với văn bản là children của nó. React sẽ đặt một DOM node `<title>` trong `<head>` của tài liệu.

<SandpackWithHTMLOutput>

```js src/App.js active
import ShowRenderedHTML from './ShowRenderedHTML.js';

export default function ContactUsPage() {
  return (
    <ShowRenderedHTML>
      <title>My Site: Contact Us</title>
      <h1>Contact Us</h1>
      <p>Email us at support@example.com</p>
    </ShowRenderedHTML>
  );
}
```

</SandpackWithHTMLOutput>

### Sử dụng biến trong tiêu đề {/*use-variables-in-the-title*/}

Children của component `<title>` phải là một chuỗi văn bản đơn. (Hoặc một số đơn hoặc một object đơn có phương thức `toString`.) Điều này có thể không rõ ràng, nhưng sử dụng dấu ngoặc nhọn JSX như thế này:

```js
<title>Results page {pageNumber}</title> // 🔴 Vấn đề: Đây không phải là một chuỗi đơn
```

... thực ra khiến component `<title>` nhận được một mảng hai phần tử làm children (chuỗi `"Results page"` và giá trị của `pageNumber`). Điều này sẽ gây ra lỗi. Thay vào đó, hãy sử dụng string interpolation để truyền cho `<title>` một chuỗi đơn:

```js
<title>{`Results page ${pageNumber}`}</title>
```
