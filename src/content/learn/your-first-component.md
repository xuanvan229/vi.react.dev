---
title: Component Đầu Tiên Của Bạn
---

<Intro>

*Component* là một trong những khái niệm cốt lõi của React. Chúng là nền tảng mà bạn xây dựng giao diện người dùng (UI), điều này làm cho chúng trở thành điểm khởi đầu hoàn hảo cho hành trình React của bạn!

</Intro>

<YouWillLearn>

* Component là gì
* Vai trò của các component trong một ứng dụng React
* Cách viết React component đầu tiên của bạn

</YouWillLearn>

## Component: Các khối xây dựng UI {/*components-ui-building-blocks*/}

Trên Web, HTML cho phép chúng ta tạo ra các tài liệu có cấu trúc phong phú với bộ thẻ tích hợp như `<h1>` và `<li>`:

```html
<article>
  <h1>My First Component</h1>
  <ol>
    <li>Components: UI Building Blocks</li>
    <li>Defining a Component</li>
    <li>Using a Component</li>
  </ol>
</article>
```

Markup này đại diện cho bài viết `<article>`, tiêu đề `<h1>`, và mục lục (được rút gọn) dưới dạng danh sách có thứ tự `<ol>`. Markup như thế này, kết hợp với CSS để tạo style, và JavaScript để tương tác, nằm sau mỗi sidebar, avatar, modal, dropdown -- mọi phần UI bạn thấy trên Web.

React cho phép bạn kết hợp markup, CSS, và JavaScript thành các "component" tùy chỉnh, **các phần tử UI có thể tái sử dụng cho ứng dụng của bạn.** Code mục lục bạn thấy ở trên có thể được chuyển thành component `<TableOfContents />` bạn có thể render trên mỗi trang. Bên dưới, nó vẫn sử dụng các thẻ HTML giống nhau như `<article>`, `<h1>`, v.v.

Giống như với các thẻ HTML, bạn có thể soạn thảo, sắp xếp và lồng các component để thiết kế toàn bộ trang. Ví dụ, trang tài liệu bạn đang đọc được tạo từ các React component:

```js
<PageLayout>
  <NavigationHeader>
    <SearchBar />
    <Link to="/docs">Docs</Link>
  </NavigationHeader>
  <Sidebar />
  <PageContent>
    <TableOfContents />
    <DocumentationText />
  </PageContent>
</PageLayout>
```

Khi dự án của bạn phát triển, bạn sẽ nhận thấy rằng nhiều thiết kế có thể được soạn thảo bằng cách tái sử dụng các component bạn đã viết, giúp tăng tốc độ phát triển. Mục lục của chúng ta ở trên có thể được thêm vào bất kỳ màn hình nào với `<TableOfContents />`! Bạn thậm chí có thể khởi động dự án của mình với hàng nghìn component được chia sẻ bởi cộng đồng React open source như [Chakra UI](https://chakra-ui.com/) và [Material UI.](https://material-ui.com/)

## Định nghĩa một component {/*defining-a-component*/}

Theo truyền thống khi tạo các trang web, các nhà phát triển web đánh dấu nội dung của họ và sau đó thêm tương tác bằng cách rắc thêm một số JavaScript. Điều này hoạt động tốt khi tương tác là một điều tốt để có trên web. Bây giờ nó được mong đợi cho nhiều trang web và tất cả các ứng dụng. React đặt tương tác lên hàng đầu trong khi vẫn sử dụng cùng công nghệ: **một React component là một hàm JavaScript mà bạn có thể _thêm markup vào_.** Đây là cách nó trông như thế nào (bạn có thể chỉnh sửa ví dụ bên dưới):

<Sandpack>

```js
export default function Profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/MK3eW3Am.jpg"
      alt="Katherine Johnson"
    />
  )
}
```

```css
img { height: 200px; }
```

</Sandpack>

Và đây là cách để xây dựng một component:

### Bước 1: Export component {/*step-1-export-the-component*/}

Tiền tố `export default` là [cú pháp JavaScript tiêu chuẩn](https://developer.mozilla.org/docs/web/javascript/reference/statements/export) (không riêng cho React). Nó cho phép bạn đánh dấu hàm chính trong một file để bạn có thể import nó từ các file khác sau này. (Xem thêm về import trong [Import và Export các Component](/learn/importing-and-exporting-components)!)

### Bước 2: Định nghĩa hàm {/*step-2-define-the-function*/}

Với `function Profile() { }` bạn định nghĩa một hàm JavaScript có tên `Profile`.

<Pitfall>

Các React component là các hàm JavaScript thông thường, nhưng **tên của chúng phải bắt đầu bằng chữ hoa** hoặc chúng sẽ không hoạt động!

</Pitfall>

### Bước 3: Thêm markup {/*step-3-add-markup*/}

Component trả về thẻ `<img />` với các thuộc tính `src` và `alt`. `<img />` được viết như HTML, nhưng thực ra là JavaScript bên dưới! Cú pháp này được gọi là [JSX](/learn/writing-markup-with-jsx), và nó cho phép bạn nhúng markup bên trong JavaScript.

Câu lệnh return có thể được viết trên một dòng, như trong component này:

```js
return <img src="https://react.dev/images/docs/scientists/MK3eW3As.jpg" alt="Katherine Johnson" />;
```

Nhưng nếu markup của bạn không nằm trên cùng một dòng với từ khóa `return`, bạn phải bọc nó trong một cặp dấu ngoặc đơn:

```js
return (
  <div>
    <img src="https://react.dev/images/docs/scientists/MK3eW3As.jpg" alt="Katherine Johnson" />
  </div>
);
```

<Pitfall>

Không có dấu ngoặc đơn, bất kỳ code nào trên các dòng sau `return` [sẽ bị bỏ qua](https://stackoverflow.com/questions/2846283/what-are-the-rules-for-javascripts-automatic-semicolon-insertion-asi)!

</Pitfall>

## Sử dụng một component {/*using-a-component*/}

Bây giờ bạn đã định nghĩa component `Profile`, bạn có thể lồng nó vào các component khác. Ví dụ, bạn có thể export component `Gallery` sử dụng nhiều component `Profile`:

<Sandpack>

```js
function Profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/MK3eW3As.jpg"
      alt="Katherine Johnson"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

### Trình duyệt nhìn thấy gì {/*what-the-browser-sees*/}

Lưu ý sự khác biệt về chữ hoa/thường:

* `<section>` viết thường, vì vậy React biết chúng ta đang tham chiếu đến thẻ HTML.
* `<Profile />` bắt đầu bằng `P` viết hoa, vì vậy React biết rằng chúng ta muốn sử dụng component có tên `Profile`.

Và `Profile` chứa thêm HTML: `<img />`. Cuối cùng, đây là những gì trình duyệt nhìn thấy:

```html
<section>
  <h1>Amazing scientists</h1>
  <img src="https://react.dev/images/docs/scientists/MK3eW3As.jpg" alt="Katherine Johnson" />
  <img src="https://react.dev/images/docs/scientists/MK3eW3As.jpg" alt="Katherine Johnson" />
  <img src="https://react.dev/images/docs/scientists/MK3eW3As.jpg" alt="Katherine Johnson" />
</section>
```

### Lồng và tổ chức các component {/*nesting-and-organizing-components*/}

Các component là các hàm JavaScript thông thường, vì vậy bạn có thể giữ nhiều component trong cùng một file. Điều này tiện lợi khi các component tương đối nhỏ hoặc liên quan chặt chẽ với nhau. Nếu file này trở nên đông đúc, bạn luôn có thể di chuyển `Profile` sang một file riêng. Bạn sẽ học cách làm điều này ngay trên [trang về import.](/learn/importing-and-exporting-components)

Vì các component `Profile` được render bên trong `Gallery`--thậm chí nhiều lần!--chúng ta có thể nói rằng `Gallery` là **component cha,** render mỗi `Profile` như một "con". Đây là một phần phép màu của React: bạn có thể định nghĩa một component một lần, và sau đó sử dụng nó ở bao nhiêu nơi và bao nhiêu lần tùy ý.

<Pitfall>

Các component có thể render các component khác, nhưng **bạn không bao giờ được lồng định nghĩa của chúng:**

```js {2-5}
export default function Gallery() {
  // 🔴 Không bao giờ định nghĩa một component bên trong component khác!
  function Profile() {
    // ...
  }
  // ...
}
```

Đoạn code trên [rất chậm và gây ra lỗi.](/learn/preserving-and-resetting-state#different-components-at-the-same-position-reset-state) Thay vào đó, hãy định nghĩa mọi component ở cấp cao nhất:

```js {5-8}
export default function Gallery() {
  // ...
}

// ✅ Khai báo các component ở cấp cao nhất
function Profile() {
  // ...
}
```

Khi một component con cần một số dữ liệu từ cha, [hãy truyền nó qua props](/learn/passing-props-to-a-component) thay vì lồng các định nghĩa.

</Pitfall>

<DeepDive>

#### Component từ trên xuống dưới {/*components-all-the-way-down*/}

Ứng dụng React của bạn bắt đầu tại một component "root". Thường thì nó được tạo tự động khi bạn bắt đầu một dự án mới. Ví dụ, nếu bạn sử dụng [CodeSandbox](https://codesandbox.io/) hoặc nếu bạn sử dụng framework [Next.js](https://nextjs.org/), component root được định nghĩa trong `pages/index.js`. Trong những ví dụ này, bạn đã export các component root.

Hầu hết các ứng dụng React sử dụng component từ trên xuống dưới. Điều này có nghĩa là bạn không chỉ sử dụng component cho các phần có thể tái sử dụng như nút bấm, mà còn cho các phần lớn hơn như sidebar, danh sách, và cuối cùng, các trang hoàn chỉnh! Các component là cách tiện lợi để tổ chức code UI và markup, ngay cả khi một số trong chúng chỉ được sử dụng một lần.

[Các framework dựa trên React](/learn/creating-a-react-app) tiến thêm một bước. Thay vì sử dụng một file HTML trống và để React "tiếp quản" quản lý trang với JavaScript, chúng *cũng* tạo HTML tự động từ các React component của bạn. Điều này cho phép ứng dụng của bạn hiển thị một số nội dung trước khi code JavaScript tải.

Tuy nhiên, nhiều website chỉ sử dụng React để [thêm tương tác vào các trang HTML hiện có.](/learn/add-react-to-an-existing-project#using-react-for-a-part-of-your-existing-page) Chúng có nhiều component root thay vì một component cho toàn bộ trang. Bạn có thể sử dụng nhiều hay ít React tùy theo nhu cầu.

</DeepDive>

<Recap>

Bạn vừa có được trải nghiệm đầu tiên với React! Hãy tóm tắt một số điểm chính.

* React cho phép bạn tạo các component, **các phần tử UI có thể tái sử dụng cho ứng dụng của bạn.**
* Trong một ứng dụng React, mọi phần UI đều là một component.
* Các React component là các hàm JavaScript thông thường ngoại trừ:

  1. Tên của chúng luôn bắt đầu bằng chữ hoa.
  2. Chúng trả về markup JSX.

</Recap>



<Challenges>

#### Export component {/*export-the-component*/}

Sandbox này không hoạt động vì component root không được export:

<Sandpack>

```js
function Profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/lICfvbD.jpg"
      alt="Aklilu Lemma"
    />
  );
}
```

```css
img { height: 181px; }
```

</Sandpack>

Hãy thử tự sửa trước khi xem giải pháp!

<Solution>

Thêm `export default` trước định nghĩa hàm như sau:

<Sandpack>

```js
export default function Profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/lICfvbD.jpg"
      alt="Aklilu Lemma"
    />
  );
}
```

```css
img { height: 181px; }
```

</Sandpack>

Bạn có thể tự hỏi tại sao viết `export` một mình lại không đủ để sửa ví dụ này. Bạn có thể tìm hiểu sự khác biệt giữa `export` và `export default` trong [Import và Export các Component.](/learn/importing-and-exporting-components)

</Solution>

#### Sửa câu lệnh return {/*fix-the-return-statement*/}

Có điều gì đó không ổn với câu lệnh `return` này. Bạn có thể sửa nó không?

<Hint>

Bạn có thể gặp lỗi "Unexpected token" khi cố gắng sửa lỗi này. Trong trường hợp đó, hãy kiểm tra xem dấu chấm phẩy xuất hiện *sau* dấu ngoặc đóng. Để dấu chấm phẩy bên trong `return ( )` sẽ gây ra lỗi.

</Hint>


<Sandpack>

```js
export default function Profile() {
  return
    <img src="https://react.dev/images/docs/scientists/jA8hHMpm.jpg" alt="Katsuko Saruhashi" />;
}
```

```css
img { height: 180px; }
```

</Sandpack>

<Solution>

Bạn có thể sửa component này bằng cách di chuyển câu lệnh return lên một dòng như sau:

<Sandpack>

```js
export default function Profile() {
  return <img src="https://react.dev/images/docs/scientists/jA8hHMpm.jpg" alt="Katsuko Saruhashi" />;
}
```

```css
img { height: 180px; }
```

</Sandpack>

Hoặc bằng cách bọc markup JSX được trả về trong dấu ngoặc đơn mở ngay sau `return`:

<Sandpack>

```js
export default function Profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/jA8hHMpm.jpg"
      alt="Katsuko Saruhashi"
    />
  );
}
```

```css
img { height: 180px; }
```

</Sandpack>

</Solution>

#### Tìm lỗi {/*spot-the-mistake*/}

Có điều gì đó sai với cách component `Profile` được khai báo và sử dụng. Bạn có thể tìm ra lỗi không? (Hãy nhớ cách React phân biệt component với các thẻ HTML thông thường!)

<Sandpack>

```js
function profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <profile />
      <profile />
      <profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

<Solution>

Tên React component phải bắt đầu bằng chữ hoa.

Đổi `function profile()` thành `function Profile()`, và sau đó đổi mỗi `<profile />` thành `<Profile />`:

<Sandpack>

```js
function Profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; }
```

</Sandpack>

</Solution>

#### Component của riêng bạn {/*your-own-component*/}

Viết một component từ đầu. Bạn có thể đặt cho nó bất kỳ tên hợp lệ nào và trả về bất kỳ markup nào. Nếu bạn hết ý tưởng, bạn có thể viết component `Congratulations` hiển thị `<h1>Good job!</h1>`. Đừng quên export nó!

<Sandpack>

```js
// Viết component của bạn bên dưới!

```

</Sandpack>

<Solution>

<Sandpack>

```js
export default function Congratulations() {
  return (
    <h1>Good job!</h1>
  );
}
```

</Sandpack>

</Solution>

</Challenges>
