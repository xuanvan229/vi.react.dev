---
title: preloadModule
---

<Note>

[Các framework dựa trên React](/learn/creating-a-react-app) thường xử lý việc tải tài nguyên cho bạn, vì vậy bạn có thể không cần tự gọi API này. Hãy tham khảo tài liệu của framework của bạn để biết chi tiết.

</Note>

<Intro>

`preloadModule` cho phép bạn tải trước một ESM module mà bạn dự kiến sẽ sử dụng.

```js
preloadModule("https://example.com/module.js", {as: "script"});
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `preloadModule(href, options)` {/*preloadmodule*/}

Để tải trước một ESM module, gọi hàm `preloadModule` từ `react-dom`.

```js
import { preloadModule } from 'react-dom';

function AppRoot() {
  preloadModule("https://example.com/module.js", {as: "script"});
  // ...
}

```

[Xem thêm các ví dụ bên dưới.](#usage)

Hàm `preloadModule` cung cấp cho trình duyệt một gợi ý rằng nó nên bắt đầu tải module đã cho, điều này có thể tiết kiệm thời gian.

#### Tham số {/*parameters*/}

* `href`: một chuỗi. URL của module bạn muốn tải.
* `options`: một object. Nó chứa các thuộc tính sau:
  *  `as`: một chuỗi bắt buộc. Phải là `'script'`.
  *  `crossOrigin`: một chuỗi. [Chính sách CORS](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) để sử dụng. Các giá trị có thể là `anonymous` và `use-credentials`.
  *  `integrity`: một chuỗi. Một hash mật mã của module, để [xác minh tính xác thực](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity).
  *  `nonce`: một chuỗi. Một [nonce mật mã để cho phép module](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/nonce) khi sử dụng Content Security Policy nghiêm ngặt.


#### Giá trị trả về {/*returns*/}

`preloadModule` không trả về gì.

#### Lưu ý {/*caveats*/}

* Nhiều lần gọi `preloadModule` với cùng `href` có tác dụng giống như một lần gọi duy nhất.
* Trong trình duyệt, bạn có thể gọi `preloadModule` trong bất kỳ tình huống nào: khi render một component, trong Effect, trong event handler, v.v.
* Trong server-side rendering hoặc khi render Server Components, `preloadModule` chỉ có tác dụng nếu bạn gọi nó khi render một component hoặc trong một ngữ cảnh bất đồng bộ bắt nguồn từ việc render một component. Bất kỳ lệnh gọi nào khác sẽ bị bỏ qua.

---

## Cách sử dụng {/*usage*/}

### Tải trước khi render {/*preloading-when-rendering*/}

Gọi `preloadModule` khi render một component nếu bạn biết rằng nó hoặc các phần tử con của nó sẽ sử dụng một module cụ thể.

```js
import { preloadModule } from 'react-dom';

function AppRoot() {
  preloadModule("https://example.com/module.js", {as: "script"});
  return ...;
}
```

Nếu bạn muốn trình duyệt bắt đầu thực thi module ngay lập tức (thay vì chỉ tải xuống), hãy sử dụng [`preinitModule`](/reference/react-dom/preinitModule) thay thế. Nếu bạn muốn tải một script không phải ESM module, hãy sử dụng [`preload`](/reference/react-dom/preload).

### Tải trước trong event handler {/*preloading-in-an-event-handler*/}

Gọi `preloadModule` trong event handler trước khi chuyển đổi đến trang hoặc state nơi module sẽ được cần. Điều này bắt đầu quá trình sớm hơn so với việc gọi nó trong quá trình render trang hoặc state mới.

```js
import { preloadModule } from 'react-dom';

function CallToAction() {
  const onClick = () => {
    preloadModule("https://example.com/module.js", {as: "script"});
    startWizard();
  }
  return (
    <button onClick={onClick}>Start Wizard</button>
  );
}
```
