---
style: "<style>"
---

<Intro>

[Component `<style>` tích hợp sẵn của trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/style) cho phép bạn thêm các stylesheet CSS inline vào tài liệu của bạn.

```js
<style>{` p { color: red; } `}</style>
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `<style>` {/*style*/}

Để thêm style inline vào tài liệu của bạn, hãy render [component `<style>` tích hợp sẵn của trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/style). Bạn có thể render `<style>` từ bất kỳ component nào và React sẽ [trong một số trường hợp nhất định](#special-rendering-behavior) đặt phần tử DOM tương ứng trong phần head của tài liệu và loại bỏ các style giống hệt nhau.

```js
<style>{` p { color: red; } `}</style>
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Props {/*props*/}

`<style>` hỗ trợ tất cả [các props phần tử chung.](/reference/react-dom/components/common#common-props)

* `children`: một chuỗi, bắt buộc. Nội dung của stylesheet.
* `precedence`: một chuỗi. Cho React biết nơi xếp hạng DOM node `<style>` so với các node khác trong `<head>` của tài liệu, điều này xác định stylesheet nào có thể ghi đè stylesheet khác. React sẽ suy ra rằng các giá trị precedence nó phát hiện trước là "thấp hơn" và các giá trị precedence nó phát hiện sau là "cao hơn". Nhiều hệ thống style có thể hoạt động tốt bằng cách sử dụng một giá trị precedence duy nhất vì các quy tắc style mang tính nguyên tử. Các stylesheet có cùng precedence đi cùng nhau dù chúng là thẻ `<link>` hay `<style>` inline hay được tải bằng các hàm [`preinit`](/reference/react-dom/preinit).
* `href`: một chuỗi. Cho phép React [loại bỏ trùng lặp style](#special-rendering-behavior) có cùng `href`.
* `media`: một chuỗi. Giới hạn stylesheet cho một [media query](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_media_queries/Using_media_queries) nhất định.
* `nonce`: một chuỗi. Một [nonce mã hóa để cho phép tài nguyên](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/nonce) khi sử dụng Content Security Policy nghiêm ngặt.
* `title`: một chuỗi. Chỉ định tên của một [stylesheet thay thế](https://developer.mozilla.org/en-US/docs/Web/CSS/Alternative_style_sheets).

Các props **không được khuyến nghị** sử dụng với React:

* `blocking`: một chuỗi. Nếu được đặt thành `"render"`, hướng dẫn trình duyệt không render trang cho đến khi stylesheet được tải. React cung cấp khả năng kiểm soát chi tiết hơn bằng cách sử dụng Suspense.

#### Hành vi render đặc biệt {/*special-rendering-behavior*/}

React có thể di chuyển các component `<style>` đến `<head>` của tài liệu, loại bỏ trùng lặp các stylesheet giống hệt nhau, và [suspend](/reference/react/Suspense) trong khi stylesheet đang tải.

Để tham gia vào hành vi này, hãy cung cấp các props `href` và `precedence`. React sẽ loại bỏ trùng lặp các style nếu chúng có cùng `href`. Prop precedence cho React biết nơi xếp hạng DOM node `<style>` so với các node khác trong `<head>` của tài liệu, điều này xác định stylesheet nào có thể ghi đè stylesheet khác.

Xử lý đặc biệt này đi kèm với ba lưu ý:

* React sẽ bỏ qua các thay đổi đối với props sau khi style đã được render. (React sẽ đưa ra cảnh báo trong môi trường development nếu điều này xảy ra.)
* React sẽ bỏ tất cả các props không liên quan khi sử dụng prop `precedence` (ngoài `href` và `precedence`).
* React có thể để lại style trong DOM ngay cả sau khi component đã render nó bị unmount.

---

## Cách sử dụng {/*usage*/}

### Render một stylesheet CSS inline {/*rendering-an-inline-css-stylesheet*/}

Nếu một component phụ thuộc vào một số style CSS nhất định để được hiển thị đúng cách, bạn có thể render một stylesheet inline trong component.

Prop `href` phải xác định duy nhất stylesheet, vì React sẽ loại bỏ trùng lặp các stylesheet có cùng `href`.
Nếu bạn cung cấp prop `precedence`, React sẽ sắp xếp lại các stylesheet inline dựa trên thứ tự các giá trị này xuất hiện trong cây component.

Các stylesheet inline sẽ không kích hoạt ranh giới Suspense trong khi chúng đang tải.
Kể cả khi chúng tải các tài nguyên async như font hoặc hình ảnh.

<SandpackWithHTMLOutput>

```js src/App.js active
import ShowRenderedHTML from './ShowRenderedHTML.js';
import { useId } from 'react';

function PieChart({data, colors}) {
  const id = useId();
  const stylesheet = colors.map((color, index) =>
    `#${id} .color-${index}: \{ color: "${color}"; \}`
  ).join();
  return (
    <>
      <style href={"PieChart-" + JSON.stringify(colors)} precedence="medium">
        {stylesheet}
      </style>
      <svg id={id}>
        …
      </svg>
    </>
  );
}

export default function App() {
  return (
    <ShowRenderedHTML>
      <PieChart data="..." colors={['red', 'green', 'blue']} />
    </ShowRenderedHTML>
  );
}
```

</SandpackWithHTMLOutput>
