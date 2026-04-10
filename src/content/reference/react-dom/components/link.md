---
link: "<link>"
---

<Intro>

[Component `<link>` tích hợp sẵn của trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link) cho phép bạn sử dụng các tài nguyên bên ngoài như stylesheet hoặc chú thích tài liệu với metadata dạng link.

```js
<link rel="icon" href="favicon.ico" />
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `<link>` {/*link*/}

Để liên kết đến các tài nguyên bên ngoài như stylesheet, font, và icon, hoặc chú thích tài liệu với metadata dạng link, hãy render [component `<link>` tích hợp sẵn của trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link). Bạn có thể render `<link>` từ bất kỳ component nào và React sẽ [trong hầu hết các trường hợp](#special-rendering-behavior) đặt phần tử DOM tương ứng trong phần `<head>` của tài liệu.

```js
<link rel="icon" href="favicon.ico" />
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Props {/*props*/}

`<link>` hỗ trợ tất cả [các props phần tử chung.](/reference/react-dom/components/common#common-props)

* `rel`: một chuỗi, bắt buộc. Chỉ định [mối quan hệ với tài nguyên](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/rel). React [xử lý các link với `rel="stylesheet"` theo cách khác](#special-rendering-behavior) so với các link khác.

Các props này áp dụng khi `rel="stylesheet"`:

* `precedence`: một chuỗi. Cho React biết nơi xếp hạng DOM node `<link>` so với các node khác trong `<head>` của tài liệu, điều này xác định stylesheet nào có thể ghi đè stylesheet khác. React sẽ suy ra rằng các giá trị precedence nó phát hiện trước là "thấp hơn" và các giá trị precedence nó phát hiện sau là "cao hơn". Nhiều hệ thống style có thể hoạt động tốt bằng cách sử dụng một giá trị precedence duy nhất vì các quy tắc style mang tính nguyên tử. Các stylesheet có cùng precedence đi cùng nhau dù chúng là thẻ `<link>` hay `<style>` inline hay được tải bằng các hàm [`preinit`](/reference/react-dom/preinit).
* `media`: một chuỗi. Giới hạn stylesheet cho một [media query](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_media_queries/Using_media_queries) nhất định.
* `title`: một chuỗi. Chỉ định tên của một [stylesheet thay thế](https://developer.mozilla.org/en-US/docs/Web/CSS/Alternative_style_sheets).

Các props này áp dụng khi `rel="stylesheet"` nhưng vô hiệu hóa [xử lý đặc biệt của React đối với stylesheet](#special-rendering-behavior):

* `disabled`: một boolean. Vô hiệu hóa stylesheet.
* `onError`: một hàm. Được gọi khi stylesheet không tải được.
* `onLoad`: một hàm. Được gọi khi stylesheet tải xong.

Các props này áp dụng khi `rel="preload"` hoặc `rel="modulepreload"`:

* `as`: một chuỗi. Loại tài nguyên. Các giá trị có thể là `audio`, `document`, `embed`, `fetch`, `font`, `image`, `object`, `script`, `style`, `track`, `video`, `worker`.
* `imageSrcSet`: một chuỗi. Chỉ áp dụng khi `as="image"`. Chỉ định [tập nguồn của hình ảnh](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images).
* `imageSizes`: một chuỗi. Chỉ áp dụng khi `as="image"`. Chỉ định [kích thước của hình ảnh](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images).

Các props này áp dụng khi `rel="icon"` hoặc `rel="apple-touch-icon"`:

* `sizes`: một chuỗi. [Kích thước của icon](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images).

Các props này áp dụng trong mọi trường hợp:

* `href`: một chuỗi. URL của tài nguyên được liên kết.
*  `crossOrigin`: một chuỗi. [Chính sách CORS](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) cần sử dụng. Các giá trị có thể là `anonymous` và `use-credentials`. Bắt buộc khi `as` được đặt thành `"fetch"`.
*  `referrerPolicy`: một chuỗi. [Header Referrer](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link#referrerpolicy) cần gửi khi fetch. Các giá trị có thể là `no-referrer-when-downgrade` (mặc định), `no-referrer`, `origin`, `origin-when-cross-origin`, và `unsafe-url`.
* `fetchPriority`: một chuỗi. Đề xuất mức độ ưu tiên tương đối khi fetch tài nguyên. Các giá trị có thể là `auto` (mặc định), `high`, và `low`.
* `hrefLang`: một chuỗi. Ngôn ngữ của tài nguyên được liên kết.
* `integrity`: một chuỗi. Một hash mã hóa của tài nguyên, để [xác minh tính xác thực của nó](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity).
* `type`: một chuỗi. MIME type của tài nguyên được liên kết.

Các props **không được khuyến nghị** sử dụng với React:

* `blocking`: một chuỗi. Nếu được đặt thành `"render"`, hướng dẫn trình duyệt không render trang cho đến khi stylesheet được tải. React cung cấp khả năng kiểm soát chi tiết hơn bằng cách sử dụng Suspense.

#### Hành vi render đặc biệt {/*special-rendering-behavior*/}

React sẽ luôn đặt phần tử DOM tương ứng với component `<link>` trong `<head>` của tài liệu, bất kể nó được render ở đâu trong cây React. `<head>` là vị trí duy nhất hợp lệ cho `<link>` trong DOM, tuy nhiên sẽ tiện lợi và giữ mọi thứ có thể kết hợp được nếu một component đại diện cho một trang cụ thể có thể tự render các component `<link>`.

Có một vài ngoại lệ cho điều này:

* Nếu `<link>` có prop `rel="stylesheet"`, thì nó cũng phải có prop `precedence` để có được hành vi đặc biệt này. Điều này vì thứ tự của các stylesheet trong tài liệu là quan trọng, vì vậy React cần biết cách sắp xếp stylesheet này so với các stylesheet khác, điều mà bạn chỉ định bằng prop `precedence`. Nếu prop `precedence` bị bỏ qua, sẽ không có hành vi đặc biệt.
* Nếu `<link>` có prop [`itemProp`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/itemprop), sẽ không có hành vi đặc biệt, vì trong trường hợp này nó không áp dụng cho tài liệu mà thay vào đó đại diện cho metadata về một phần cụ thể của trang.
* Nếu `<link>` có prop `onLoad` hoặc `onError`, vì trong trường hợp đó bạn đang quản lý việc tải tài nguyên được liên kết theo cách thủ công trong component React của bạn.

#### Hành vi đặc biệt đối với stylesheet {/*special-behavior-for-stylesheets*/}

Ngoài ra, nếu `<link>` là đến một stylesheet (cụ thể là nó có `rel="stylesheet"` trong props của nó), React xử lý nó một cách đặc biệt theo những cách sau:

* Component render `<link>` sẽ [suspend](/reference/react/Suspense) trong khi stylesheet đang tải.
* Nếu nhiều component render các link đến cùng một stylesheet, React sẽ loại bỏ trùng lặp và chỉ đặt một link duy nhất vào DOM. Hai link được coi là giống nhau nếu chúng có cùng prop `href`.

Có hai ngoại lệ cho hành vi đặc biệt này:

* Nếu link không có prop `precedence`, sẽ không có hành vi đặc biệt, vì thứ tự của các stylesheet trong tài liệu là quan trọng, vì vậy React cần biết cách sắp xếp stylesheet này so với các stylesheet khác, điều mà bạn chỉ định bằng prop `precedence`.
* Nếu bạn cung cấp bất kỳ props nào trong số `onLoad`, `onError`, hoặc `disabled`, sẽ không có hành vi đặc biệt, vì những props này cho thấy bạn đang quản lý việc tải stylesheet theo cách thủ công trong component của bạn.

Xử lý đặc biệt này đi kèm với hai lưu ý:

* React sẽ bỏ qua các thay đổi đối với props sau khi link đã được render. (React sẽ đưa ra cảnh báo trong môi trường development nếu điều này xảy ra.)
* React có thể để lại link trong DOM ngay cả sau khi component đã render nó bị unmount.

---

## Cách sử dụng {/*usage*/}

### Liên kết đến các tài nguyên liên quan {/*linking-to-related-resources*/}

Bạn có thể chú thích tài liệu với các link đến các tài nguyên liên quan như icon, URL canonical, hoặc pingback. React sẽ đặt metadata này trong `<head>` của tài liệu bất kể nó được render ở đâu trong cây React.

<SandpackWithHTMLOutput>

```js src/App.js active
import ShowRenderedHTML from './ShowRenderedHTML.js';

export default function BlogPage() {
  return (
    <ShowRenderedHTML>
      <link rel="icon" href="favicon.ico" />
      <link rel="pingback" href="http://www.example.com/xmlrpc.php" />
      <h1>My Blog</h1>
      <p>...</p>
    </ShowRenderedHTML>
  );
}
```

</SandpackWithHTMLOutput>

### Liên kết đến một stylesheet {/*linking-to-a-stylesheet*/}

Nếu một component phụ thuộc vào một stylesheet nhất định để được hiển thị đúng cách, bạn có thể render một link đến stylesheet đó trong component. Component của bạn sẽ [suspend](/reference/react/Suspense) trong khi stylesheet đang tải. Bạn phải cung cấp prop `precedence`, cho React biết nơi đặt stylesheet này so với các stylesheet khác — các stylesheet có precedence cao hơn có thể ghi đè những stylesheet có precedence thấp hơn.

<Note>
Khi bạn muốn sử dụng một stylesheet, việc gọi hàm [preinit](/reference/react-dom/preinit) có thể có lợi. Gọi hàm này có thể cho phép trình duyệt bắt đầu fetch stylesheet sớm hơn so với chỉ render một component `<link>`, ví dụ bằng cách gửi [phản hồi HTTP Early Hints](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/103).
</Note>

<SandpackWithHTMLOutput>

```js src/App.js active
import ShowRenderedHTML from './ShowRenderedHTML.js';

export default function SiteMapPage() {
  return (
    <ShowRenderedHTML>
      <link rel="stylesheet" href="sitemap.css" precedence="medium" />
      <p>...</p>
    </ShowRenderedHTML>
  );
}
```

</SandpackWithHTMLOutput>

### Kiểm soát độ ưu tiên stylesheet {/*controlling-stylesheet-precedence*/}

Các stylesheet có thể xung đột với nhau, và khi điều đó xảy ra, trình duyệt sẽ ưu tiên cái xuất hiện sau trong tài liệu. React cho phép bạn kiểm soát thứ tự của các stylesheet bằng prop `precedence`. Trong ví dụ này, ba component render các stylesheet, và những cái có cùng precedence được nhóm lại với nhau trong `<head>`.

<SandpackWithHTMLOutput>

```js src/App.js active
import ShowRenderedHTML from './ShowRenderedHTML.js';

export default function HomePage() {
  return (
    <ShowRenderedHTML>
      <FirstComponent />
      <SecondComponent />
      <ThirdComponent/>
      ...
    </ShowRenderedHTML>
  );
}

function FirstComponent() {
  return <link rel="stylesheet" href="first.css" precedence="first" />;
}

function SecondComponent() {
  return <link rel="stylesheet" href="second.css" precedence="second" />;
}

function ThirdComponent() {
  return <link rel="stylesheet" href="third.css" precedence="first" />;
}

```

</SandpackWithHTMLOutput>

Lưu ý rằng bản thân các giá trị `precedence` là tùy ý và cách đặt tên là tùy bạn. React sẽ suy ra rằng các giá trị precedence nó phát hiện trước là "thấp hơn" và các giá trị precedence nó phát hiện sau là "cao hơn".

### Render stylesheet không trùng lặp {/*deduplicated-stylesheet-rendering*/}

Nếu bạn render cùng một stylesheet từ nhiều component, React sẽ chỉ đặt một `<link>` duy nhất trong phần head của tài liệu.

<SandpackWithHTMLOutput>

```js src/App.js active
import ShowRenderedHTML from './ShowRenderedHTML.js';

export default function HomePage() {
  return (
    <ShowRenderedHTML>
      <Component />
      <Component />
      ...
    </ShowRenderedHTML>
  );
}

function Component() {
  return <link rel="stylesheet" href="styles.css" precedence="medium" />;
}
```

</SandpackWithHTMLOutput>

### Chú thích các mục cụ thể trong tài liệu bằng link {/*annotating-specific-items-within-the-document-with-links*/}

Bạn có thể sử dụng component `<link>` với prop `itemProp` để chú thích các mục cụ thể trong tài liệu bằng các link đến các tài nguyên liên quan. Trong trường hợp này, React sẽ *không* đặt những chú thích này trong `<head>` của tài liệu mà sẽ đặt chúng như bất kỳ component React nào khác.

```js
<section itemScope>
  <h3>Annotating specific items</h3>
  <link itemProp="author" href="http://example.com/" />
  <p>...</p>
</section>
```
