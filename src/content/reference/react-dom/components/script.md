---
script: "<script>"
---

<Intro>

[Component `<script>` tích hợp sẵn của trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script) cho phép bạn thêm một script vào tài liệu của bạn.

```js
<script> alert("hi!") </script>
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `<script>` {/*script*/}

Để thêm script inline hoặc bên ngoài vào tài liệu của bạn, hãy render [component `<script>` tích hợp sẵn của trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script). Bạn có thể render `<script>` từ bất kỳ component nào và React sẽ [trong một số trường hợp nhất định](#special-rendering-behavior) đặt phần tử DOM tương ứng trong phần head của tài liệu và loại bỏ các script giống hệt nhau.

```js
<script> alert("hi!") </script>
<script src="script.js" />
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Props {/*props*/}

`<script>` hỗ trợ tất cả [các props phần tử chung.](/reference/react-dom/components/common#common-props)

Nó phải có *hoặc* `children` hoặc prop `src`.

* `children`: một chuỗi. Mã nguồn của một script inline.
* `src`: một chuỗi. URL của một script bên ngoài.

Các props được hỗ trợ khác:

* `async`: một boolean. Cho phép trình duyệt trì hoãn việc thực thi script cho đến khi phần còn lại của tài liệu được xử lý — hành vi ưa thích để đảm bảo hiệu suất.
*  `crossOrigin`: một chuỗi. [Chính sách CORS](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) cần sử dụng. Các giá trị có thể là `anonymous` và `use-credentials`.
* `fetchPriority`: một chuỗi. Cho phép trình duyệt xếp hạng các script theo mức độ ưu tiên khi fetch nhiều script cùng lúc. Có thể là `"high"`, `"low"`, hoặc `"auto"` (mặc định).
* `integrity`: một chuỗi. Một hash mã hóa của script, để [xác minh tính xác thực của nó](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity).
* `noModule`: một boolean. Vô hiệu hóa script trong các trình duyệt hỗ trợ ES module — cho phép script dự phòng cho các trình duyệt không hỗ trợ.
* `nonce`: một chuỗi. Một [nonce mã hóa để cho phép tài nguyên](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/nonce) khi sử dụng Content Security Policy nghiêm ngặt.
* `referrer`: một chuỗi. Chỉ định [header Referer cần gửi](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script#referrerpolicy) khi fetch script và bất kỳ tài nguyên nào mà script fetch.
* `type`: một chuỗi. Chỉ định script là [classic script, ES module, hay import map](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script/type).

Các props vô hiệu hóa [xử lý đặc biệt của React đối với scripts](#special-rendering-behavior):

* `onError`: một hàm. Được gọi khi script không tải được.
* `onLoad`: một hàm. Được gọi khi script tải xong.

Các props **không được khuyến nghị** sử dụng với React:

* `blocking`: một chuỗi. Nếu được đặt thành `"render"`, hướng dẫn trình duyệt không render trang cho đến khi scriptsheet được tải. React cung cấp khả năng kiểm soát chi tiết hơn bằng cách sử dụng Suspense.
* `defer`: một chuỗi. Ngăn trình duyệt thực thi script cho đến khi tài liệu tải xong. Không tương thích với các component được render phía server bằng streaming. Hãy sử dụng prop `async` thay thế.

#### Hành vi render đặc biệt {/*special-rendering-behavior*/}

React có thể di chuyển các component `<script>` đến `<head>` của tài liệu và loại bỏ các script giống hệt nhau.

Để tham gia vào hành vi này, hãy cung cấp các props `src` và `async={true}`. React sẽ loại bỏ trùng lặp các script nếu chúng có cùng `src`. Prop `async` phải là true để cho phép script được di chuyển một cách an toàn.

Xử lý đặc biệt này đi kèm với hai lưu ý:

* React sẽ bỏ qua các thay đổi đối với props sau khi script đã được render. (React sẽ đưa ra cảnh báo trong môi trường development nếu điều này xảy ra.)
* React có thể để lại script trong DOM ngay cả sau khi component đã render nó bị unmount. (Điều này không có tác dụng vì script chỉ thực thi một lần khi được chèn vào DOM.)

---

## Cách sử dụng {/*usage*/}

### Render một script bên ngoài {/*rendering-an-external-script*/}

Nếu một component phụ thuộc vào một số script nhất định để được hiển thị đúng cách, bạn có thể render một `<script>` trong component.
Tuy nhiên, component có thể được commit trước khi script tải xong.
Bạn có thể bắt đầu phụ thuộc vào nội dung script sau khi sự kiện `load` được kích hoạt, ví dụ bằng cách sử dụng prop `onLoad`.

React sẽ loại bỏ trùng lặp các script có cùng `src`, chỉ chèn một trong số chúng vào DOM ngay cả khi nhiều component render nó.

<SandpackWithHTMLOutput>

```js src/App.js active
import ShowRenderedHTML from './ShowRenderedHTML.js';

function Map({lat, long}) {
  return (
    <>
      <script async src="map-api.js" onLoad={() => console.log('script loaded')} />
      <div id="map" data-lat={lat} data-long={long} />
    </>
  );
}

export default function Page() {
  return (
    <ShowRenderedHTML>
      <Map />
    </ShowRenderedHTML>
  );
}
```

</SandpackWithHTMLOutput>

<Note>
Khi bạn muốn sử dụng một script, việc gọi hàm [preinit](/reference/react-dom/preinit) có thể có lợi. Gọi hàm này có thể cho phép trình duyệt bắt đầu fetch script sớm hơn so với chỉ render một component `<script>`, ví dụ bằng cách gửi [phản hồi HTTP Early Hints](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/103).
</Note>

### Render một script inline {/*rendering-an-inline-script*/}

Để bao gồm một script inline, hãy render component `<script>` với mã nguồn script là children của nó. Các script inline không bị loại bỏ trùng lặp hay di chuyển đến `<head>` của tài liệu.

<SandpackWithHTMLOutput>

```js src/App.js active
import ShowRenderedHTML from './ShowRenderedHTML.js';

function Tracking() {
  return (
    <script>
      ga('send', 'pageview');
    </script>
  );
}

export default function Page() {
  return (
    <ShowRenderedHTML>
      <h1>My Website</h1>
      <Tracking />
      <p>Welcome</p>
    </ShowRenderedHTML>
  );
}
```

</SandpackWithHTMLOutput>
