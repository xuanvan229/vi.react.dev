---
title: preload
---

<Note>

[Các framework dựa trên React](/learn/creating-a-react-app) thường xử lý việc tải tài nguyên cho bạn, vì vậy bạn có thể không cần tự gọi API này. Hãy tham khảo tài liệu của framework của bạn để biết chi tiết.

</Note>

<Intro>

`preload` cho phép bạn tải trước một tài nguyên như stylesheet, font hoặc script bên ngoài mà bạn dự kiến sẽ sử dụng.

```js
preload("https://example.com/font.woff2", {as: "font"});
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `preload(href, options)` {/*preload*/}

Để tải trước một tài nguyên, gọi hàm `preload` từ `react-dom`.

```js
import { preload } from 'react-dom';

function AppRoot() {
  preload("https://example.com/font.woff2", {as: "font"});
  // ...
}

```

[Xem thêm các ví dụ bên dưới.](#usage)

Hàm `preload` cung cấp cho trình duyệt một gợi ý rằng nó nên bắt đầu tải tài nguyên đã cho, điều này có thể tiết kiệm thời gian.

#### Tham số {/*parameters*/}

* `href`: một chuỗi. URL của tài nguyên bạn muốn tải.
* `options`: một object. Nó chứa các thuộc tính sau:
  *  `as`: một chuỗi bắt buộc. Loại tài nguyên. Các [giá trị có thể](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link#as) là `audio`, `document`, `embed`, `fetch`, `font`, `image`, `object`, `script`, `style`, `track`, `video`, `worker`.
  *  `crossOrigin`: một chuỗi. [Chính sách CORS](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) để sử dụng. Các giá trị có thể là `anonymous` và `use-credentials`. Bắt buộc khi `as` được đặt là `"fetch"`.
  *  `referrerPolicy`: một chuỗi. [Header Referrer](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link#referrerpolicy) để gửi khi tải. Các giá trị có thể là `no-referrer-when-downgrade` (mặc định), `no-referrer`, `origin`, `origin-when-cross-origin`, và `unsafe-url`.
  *  `integrity`: một chuỗi. Một hash mật mã của tài nguyên, để [xác minh tính xác thực](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity).
  *  `type`: một chuỗi. Loại MIME của tài nguyên.
  *  `nonce`: một chuỗi. Một [nonce mật mã để cho phép tài nguyên](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/nonce) khi sử dụng Content Security Policy nghiêm ngặt.
  *  `fetchPriority`: một chuỗi. Gợi ý mức ưu tiên tương đối để tải tài nguyên. Các giá trị có thể là `auto` (mặc định), `high`, và `low`.
  *  `imageSrcSet`: một chuỗi. Chỉ sử dụng với `as: "image"`. Chỉ định [tập nguồn của hình ảnh](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images).
  *  `imageSizes`: một chuỗi. Chỉ sử dụng với `as: "image"`. Chỉ định [kích thước của hình ảnh](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images).

#### Giá trị trả về {/*returns*/}

`preload` không trả về gì.

#### Lưu ý {/*caveats*/}

* Nhiều lần gọi `preload` tương đương có tác dụng giống như một lần gọi duy nhất. Các lần gọi `preload` được coi là tương đương theo các quy tắc sau:
  * Hai lần gọi được coi là tương đương nếu chúng có cùng `href`, ngoại trừ:
  * Nếu `as` được đặt là `image`, hai lần gọi được coi là tương đương nếu chúng có cùng `href`, `imageSrcSet`, và `imageSizes`.
* Trong trình duyệt, bạn có thể gọi `preload` trong bất kỳ tình huống nào: khi render một component, trong Effect, trong event handler, v.v.
* Trong server-side rendering hoặc khi render Server Components, `preload` chỉ có tác dụng nếu bạn gọi nó khi render một component hoặc trong một ngữ cảnh bất đồng bộ bắt nguồn từ việc render một component. Bất kỳ lệnh gọi nào khác sẽ bị bỏ qua.

---

## Cách sử dụng {/*usage*/}

### Tải trước khi render {/*preloading-when-rendering*/}

Gọi `preload` khi render một component nếu bạn biết rằng nó hoặc các phần tử con của nó sẽ sử dụng một tài nguyên cụ thể.

<Recipes titleText="Các ví dụ về tải trước">

#### Tải trước một script bên ngoài {/*preloading-an-external-script*/}

```js
import { preload } from 'react-dom';

function AppRoot() {
  preload("https://example.com/script.js", {as: "script"});
  return ...;
}
```

Nếu bạn muốn trình duyệt bắt đầu thực thi script ngay lập tức (thay vì chỉ tải xuống), hãy sử dụng [`preinit`](/reference/react-dom/preinit) thay thế. Nếu bạn muốn tải một ESM module, hãy sử dụng [`preloadModule`](/reference/react-dom/preloadModule).

<Solution />

#### Tải trước một stylesheet {/*preloading-a-stylesheet*/}

```js
import { preload } from 'react-dom';

function AppRoot() {
  preload("https://example.com/style.css", {as: "style"});
  return ...;
}
```

Nếu bạn muốn stylesheet được chèn vào tài liệu ngay lập tức (nghĩa là trình duyệt sẽ bắt đầu phân tích nó ngay thay vì chỉ tải xuống), hãy sử dụng [`preinit`](/reference/react-dom/preinit) thay thế.

<Solution />

#### Tải trước một font {/*preloading-a-font*/}

```js
import { preload } from 'react-dom';

function AppRoot() {
  preload("https://example.com/style.css", {as: "style"});
  preload("https://example.com/font.woff2", {as: "font"});
  return ...;
}
```

Nếu bạn tải trước một stylesheet, việc tải trước luôn cả các font mà stylesheet tham chiếu cũng là điều khôn ngoan. Bằng cách đó, trình duyệt có thể bắt đầu tải font trước khi nó tải xong và phân tích stylesheet.

<Solution />

#### Tải trước một hình ảnh {/*preloading-an-image*/}

```js
import { preload } from 'react-dom';

function AppRoot() {
  preload("/banner.png", {
    as: "image",
    imageSrcSet: "/banner512.png 512w, /banner1024.png 1024w",
    imageSizes: "(max-width: 512px) 512px, 1024px",
  });
  return ...;
}
```

Khi tải trước một hình ảnh, các tùy chọn `imageSrcSet` và `imageSizes` giúp trình duyệt [tải hình ảnh có kích thước phù hợp cho kích thước màn hình](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images).

<Solution />

</Recipes>

### Tải trước trong event handler {/*preloading-in-an-event-handler*/}

Gọi `preload` trong event handler trước khi chuyển đổi đến trang hoặc state nơi tài nguyên bên ngoài sẽ được cần. Điều này bắt đầu quá trình sớm hơn so với việc gọi nó trong quá trình render trang hoặc state mới.

```js
import { preload } from 'react-dom';

function CallToAction() {
  const onClick = () => {
    preload("https://example.com/wizardStyles.css", {as: "style"});
    startWizard();
  }
  return (
    <button onClick={onClick}>Start Wizard</button>
  );
}
```
