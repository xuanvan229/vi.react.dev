---
title: preinit
---

<Note>

[Các framework dựa trên React](/learn/creating-a-react-app) thường xử lý việc tải tài nguyên cho bạn, vì vậy bạn có thể không cần tự gọi API này. Hãy tham khảo tài liệu của framework của bạn để biết chi tiết.

</Note>

<Intro>

`preinit` cho phép bạn tải trước và thực thi một stylesheet hoặc script bên ngoài.

```js
preinit("https://example.com/script.js", {as: "script"});
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `preinit(href, options)` {/*preinit*/}

Để khởi tạo trước một script hoặc stylesheet, gọi hàm `preinit` từ `react-dom`.

```js
import { preinit } from 'react-dom';

function AppRoot() {
  preinit("https://example.com/script.js", {as: "script"});
  // ...
}

```

[Xem thêm các ví dụ bên dưới.](#usage)

Hàm `preinit` cung cấp cho trình duyệt một gợi ý rằng nó nên bắt đầu tải xuống và thực thi tài nguyên đã cho, điều này có thể tiết kiệm thời gian. Các script mà bạn `preinit` được thực thi khi chúng tải xong. Các stylesheet mà bạn preinit được chèn vào tài liệu, điều này khiến chúng có hiệu lực ngay lập tức.

#### Tham số {/*parameters*/}

* `href`: một chuỗi. URL của tài nguyên bạn muốn tải xuống và thực thi.
* `options`: một object. Nó chứa các thuộc tính sau:
  *  `as`: một chuỗi bắt buộc. Loại tài nguyên. Các giá trị có thể là `script` và `style`.
  * `precedence`: một chuỗi. Bắt buộc với stylesheet. Cho biết vị trí chèn stylesheet so với các stylesheet khác. Các stylesheet có precedence cao hơn có thể ghi đè những stylesheet có precedence thấp hơn. Các giá trị có thể là `reset`, `low`, `medium`, `high`.
  *  `crossOrigin`: một chuỗi. [Chính sách CORS](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) để sử dụng. Các giá trị có thể là `anonymous` và `use-credentials`.
  *  `integrity`: một chuỗi. Một hash mật mã của tài nguyên, để [xác minh tính xác thực](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity).
  *  `nonce`: một chuỗi. Một [nonce mật mã để cho phép tài nguyên](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/nonce) khi sử dụng Content Security Policy nghiêm ngặt.
  *  `fetchPriority`: một chuỗi. Gợi ý mức ưu tiên tương đối để tải tài nguyên. Các giá trị có thể là `auto` (mặc định), `high`, và `low`.

#### Giá trị trả về {/*returns*/}

`preinit` không trả về gì.

#### Lưu ý {/*caveats*/}

* Nhiều lần gọi `preinit` với cùng `href` có tác dụng giống như một lần gọi duy nhất.
* Trong trình duyệt, bạn có thể gọi `preinit` trong bất kỳ tình huống nào: khi render một component, trong Effect, trong event handler, v.v.
* Trong server-side rendering hoặc khi render Server Components, `preinit` chỉ có tác dụng nếu bạn gọi nó khi render một component hoặc trong một ngữ cảnh bất đồng bộ bắt nguồn từ việc render một component. Bất kỳ lệnh gọi nào khác sẽ bị bỏ qua.

---

## Cách sử dụng {/*usage*/}

### Khởi tạo trước khi render {/*preiniting-when-rendering*/}

Gọi `preinit` khi render một component nếu bạn biết rằng nó hoặc các phần tử con của nó sẽ sử dụng một tài nguyên cụ thể, và bạn đồng ý với việc tài nguyên được thực thi và có hiệu lực ngay khi tải xong.

<Recipes titleText="Các ví dụ về khởi tạo trước">

#### Khởi tạo trước một script bên ngoài {/*preiniting-an-external-script*/}

```js
import { preinit } from 'react-dom';

function AppRoot() {
  preinit("https://example.com/script.js", {as: "script"});
  return ...;
}
```

Nếu bạn muốn trình duyệt tải script nhưng không thực thi ngay, hãy sử dụng [`preload`](/reference/react-dom/preload) thay thế. Nếu bạn muốn tải một ESM module, hãy sử dụng [`preinitModule`](/reference/react-dom/preinitModule).

<Solution />

#### Khởi tạo trước một stylesheet {/*preiniting-a-stylesheet*/}

```js
import { preinit } from 'react-dom';

function AppRoot() {
  preinit("https://example.com/style.css", {as: "style", precedence: "medium"});
  return ...;
}
```

Tùy chọn `precedence`, bắt buộc phải có, cho phép bạn kiểm soát thứ tự của các stylesheet trong tài liệu. Các stylesheet có precedence cao hơn có thể ghi đè những stylesheet có precedence thấp hơn.

Nếu bạn muốn tải stylesheet nhưng không chèn vào tài liệu ngay, hãy sử dụng [`preload`](/reference/react-dom/preload) thay thế.

<Solution />

</Recipes>

### Khởi tạo trước trong event handler {/*preiniting-in-an-event-handler*/}

Gọi `preinit` trong event handler trước khi chuyển đổi đến trang hoặc state nơi tài nguyên bên ngoài sẽ được cần. Điều này bắt đầu quá trình sớm hơn so với việc gọi nó trong quá trình render trang hoặc state mới.

```js
import { preinit } from 'react-dom';

function CallToAction() {
  const onClick = () => {
    preinit("https://example.com/wizardStyles.css", {as: "style"});
    startWizard();
  }
  return (
    <button onClick={onClick}>Start Wizard</button>
  );
}
```
