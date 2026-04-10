---
title: Viết Markup với JSX
---

<Intro>

*JSX* là một phần mở rộng cú pháp cho JavaScript cho phép bạn viết markup dạng HTML bên trong file JavaScript. Mặc dù có các cách khác để viết component, hầu hết các nhà phát triển React ưa thích sự ngắn gọn của JSX, và hầu hết các codebase sử dụng nó.

</Intro>

<YouWillLearn>

* Tại sao React kết hợp markup với logic render
* Sự khác biệt giữa JSX và HTML
* Cách hiển thị thông tin với JSX

</YouWillLearn>

## JSX: Đưa markup vào JavaScript {/*jsx-putting-markup-into-javascript*/}

Web được xây dựng trên HTML, CSS, và JavaScript. Trong nhiều năm, các nhà phát triển web giữ nội dung trong HTML, thiết kế trong CSS, và logic trong JavaScript -- thường trong các file riêng biệt! Nội dung được đánh dấu bên trong HTML trong khi logic của trang nằm riêng biệt trong JavaScript:

<DiagramGroup>

<Diagram name="writing_jsx_html" height={237} width={325} alt="HTML markup với nền màu tím và một div với hai thẻ con: p và form.">

HTML

</Diagram>

<Diagram name="writing_jsx_js" height={237} width={325} alt="Ba JavaScript handler với nền màu vàng: onSubmit, onLogin, và onClick.">

JavaScript

</Diagram>

</DiagramGroup>

Nhưng khi Web trở nên tương tác hơn, logic ngày càng quyết định nội dung. JavaScript đảm nhiệm HTML! Đây là lý do tại sao **trong React, logic render và markup nằm cùng một nơi -- component.**

<DiagramGroup>

<Diagram name="writing_jsx_sidebar" height={330} width={325} alt="React component với HTML và JavaScript từ các ví dụ trước được kết hợp. Tên hàm là Sidebar gọi hàm isLoggedIn, được tô sáng màu vàng. Được lồng bên trong hàm được tô sáng màu tím là thẻ p từ trước, và thẻ Form tham chiếu component được hiển thị trong sơ đồ tiếp theo.">

`Sidebar.js` React component

</Diagram>

<Diagram name="writing_jsx_form" height={330} width={325} alt="React component với HTML và JavaScript từ các ví dụ trước được kết hợp. Tên hàm là Form chứa hai handler onClick và onSubmit được tô sáng màu vàng. Theo sau các handler là HTML được tô sáng màu tím. HTML chứa phần tử form với phần tử input được lồng vào, mỗi phần tử có prop onClick.">

`Form.js` React component

</Diagram>

</DiagramGroup>

Giữ logic render của nút bấm và markup cùng nhau đảm bảo chúng luôn đồng bộ với nhau trên mọi lần chỉnh sửa. Ngược lại, các chi tiết không liên quan, như markup của nút bấm và markup của sidebar, được tách biệt nhau, giúp thay đổi bất kỳ một trong số chúng một cách an toàn hơn.

Mỗi React component là một hàm JavaScript có thể chứa một số markup mà React render vào trình duyệt. Các React component sử dụng một phần mở rộng cú pháp gọi là JSX để biểu diễn markup đó. JSX trông rất giống HTML, nhưng nó nghiêm ngặt hơn một chút và có thể hiển thị thông tin động. Cách tốt nhất để hiểu điều này là chuyển đổi một số HTML markup sang JSX markup.

<Note>

JSX và React là hai thứ riêng biệt. Chúng thường được sử dụng cùng nhau, nhưng bạn *có thể* [sử dụng chúng độc lập](https://reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html#whats-a-jsx-transform) với nhau. JSX là phần mở rộng cú pháp, trong khi React là thư viện JavaScript.

</Note>

## Chuyển đổi HTML sang JSX {/*converting-html-to-jsx*/}

Giả sử bạn có một số HTML (hoàn toàn hợp lệ):

```html
<h1>Hedy Lamarr's Todos</h1>
<img
  src="https://react.dev/images/docs/scientists/yXOvdOSs.jpg"
  alt="Hedy Lamarr"
  class="photo"
>
<ul>
    <li>Invent new traffic lights
    <li>Rehearse a movie scene
    <li>Improve the spectrum technology
</ul>
```

Và bạn muốn đặt nó vào component của mình:

```js
export default function TodoList() {
  return (
    // ???
  )
}
```

Nếu bạn sao chép và dán nguyên xi, nó sẽ không hoạt động:


<Sandpack>

```js
export default function TodoList() {
  return (
    // Điều này không hoạt động đúng!
    <h1>Hedy Lamarr's Todos</h1>
    <img
      src="https://react.dev/images/docs/scientists/yXOvdOSs.jpg"
      alt="Hedy Lamarr"
      class="photo"
    >
    <ul>
      <li>Invent new traffic lights
      <li>Rehearse a movie scene
      <li>Improve the spectrum technology
    </ul>
  );
}
```

```css
img { height: 90px }
```

</Sandpack>

Điều này là do JSX nghiêm ngặt hơn và có một số quy tắc nhiều hơn HTML! Nếu bạn đọc các thông báo lỗi ở trên, chúng sẽ hướng dẫn bạn sửa markup, hoặc bạn có thể làm theo hướng dẫn bên dưới.

<Note>

Hầu hết thời gian, các thông báo lỗi trên màn hình của React sẽ giúp bạn tìm ra vấn đề ở đâu. Hãy đọc chúng nếu bạn bị kẹt!

</Note>

## Các quy tắc của JSX {/*the-rules-of-jsx*/}

### 1. Trả về một phần tử root duy nhất {/*1-return-a-single-root-element*/}

Để trả về nhiều phần tử từ một component, **hãy bọc chúng với một thẻ cha duy nhất.**

Ví dụ, bạn có thể sử dụng `<div>`:

```js {1,11}
<div>
  <h1>Hedy Lamarr's Todos</h1>
  <img
    src="https://react.dev/images/docs/scientists/yXOvdOSs.jpg"
    alt="Hedy Lamarr"
    class="photo"
  >
  <ul>
    ...
  </ul>
</div>
```


Nếu bạn không muốn thêm `<div>` thêm vào markup, bạn có thể viết `<>` và `</>` thay thế:

```js {1,11}
<>
  <h1>Hedy Lamarr's Todos</h1>
  <img
    src="https://react.dev/images/docs/scientists/yXOvdOSs.jpg"
    alt="Hedy Lamarr"
    class="photo"
  >
  <ul>
    ...
  </ul>
</>
```

Thẻ rỗng này được gọi là *[Fragment.](/reference/react/Fragment)* Fragment cho phép bạn nhóm các thứ lại mà không để lại bất kỳ dấu vết nào trong cây HTML trình duyệt.

<DeepDive>

#### Tại sao nhiều thẻ JSX cần được bọc? {/*why-do-multiple-jsx-tags-need-to-be-wrapped*/}

JSX trông giống HTML, nhưng bên dưới nó được chuyển đổi thành các đối tượng JavaScript thuần túy. Bạn không thể trả về hai đối tượng từ một hàm mà không bọc chúng vào một mảng. Điều này giải thích tại sao bạn cũng không thể trả về hai thẻ JSX mà không bọc chúng vào thẻ khác hoặc Fragment.

</DeepDive>

### 2. Đóng tất cả các thẻ {/*2-close-all-the-tags*/}

JSX yêu cầu các thẻ phải được đóng rõ ràng: các thẻ tự đóng như `<img>` phải trở thành `<img />`, và các thẻ bao bọc như `<li>oranges` phải được viết là `<li>oranges</li>`.

Đây là cách hình ảnh và các mục danh sách của Hedy Lamarr trông khi đóng:

```js {2-6,8-10}
<>
  <img
    src="https://react.dev/images/docs/scientists/yXOvdOSs.jpg"
    alt="Hedy Lamarr"
    class="photo"
   />
  <ul>
    <li>Invent new traffic lights</li>
    <li>Rehearse a movie scene</li>
    <li>Improve the spectrum technology</li>
  </ul>
</>
```

### 3. camelCase <s>tất cả</s> hầu hết mọi thứ! {/*3-camelcase-salls-most-of-the-things*/}

JSX chuyển đổi thành JavaScript và các thuộc tính được viết trong JSX trở thành các key của các đối tượng JavaScript. Trong các component của riêng bạn, bạn thường sẽ muốn đọc các thuộc tính đó vào các biến. Nhưng JavaScript có giới hạn về tên biến. Ví dụ, tên của chúng không thể chứa dấu gạch ngang hoặc là các từ khóa dành riêng như `class`.

Đây là lý do tại sao, trong React, nhiều thuộc tính HTML và SVG được viết theo camelCase. Ví dụ, thay vì `stroke-width` bạn sử dụng `strokeWidth`. Vì `class` là từ khóa dành riêng, trong React bạn viết `className` thay thế, được đặt tên theo [thuộc tính DOM tương ứng](https://developer.mozilla.org/en-US/docs/Web/API/Element/className):

```js {4}
<img
  src="https://react.dev/images/docs/scientists/yXOvdOSs.jpg"
  alt="Hedy Lamarr"
  className="photo"
/>
```

Bạn có thể [tìm tất cả các thuộc tính này trong danh sách DOM component props.](/reference/react-dom/components/common) Nếu bạn viết sai một cái, đừng lo -- React sẽ in một thông báo với gợi ý sửa lỗi vào [browser console.](https://developer.mozilla.org/docs/Tools/Browser_Console)

<Pitfall>

Vì lý do lịch sử, các thuộc tính [`aria-*`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA) và [`data-*`](https://developer.mozilla.org/docs/Learn/HTML/Howto/Use_data_attributes) được viết như trong HTML với dấu gạch ngang.

</Pitfall>

### Mẹo: Sử dụng JSX Converter {/*pro-tip-use-a-jsx-converter*/}

Chuyển đổi tất cả các thuộc tính này trong markup hiện có có thể tẻ nhạt! Chúng tôi khuyến nghị sử dụng [công cụ chuyển đổi](https://transform.tools/html-to-jsx) để dịch HTML và SVG hiện có của bạn sang JSX. Các công cụ chuyển đổi rất hữu ích trong thực tế, nhưng vẫn đáng để hiểu những gì đang xảy ra để bạn có thể tự thoải mái viết JSX.

Đây là kết quả cuối cùng của bạn:

<Sandpack>

```js
export default function TodoList() {
  return (
    <>
      <h1>Hedy Lamarr's Todos</h1>
      <img
        src="https://react.dev/images/docs/scientists/yXOvdOSs.jpg"
        alt="Hedy Lamarr"
        className="photo"
      />
      <ul>
        <li>Invent new traffic lights</li>
        <li>Rehearse a movie scene</li>
        <li>Improve the spectrum technology</li>
      </ul>
    </>
  );
}
```

```css
img { height: 90px }
```

</Sandpack>

<Recap>

Bây giờ bạn biết tại sao JSX tồn tại và cách sử dụng nó trong các component:

* Các React component nhóm logic render cùng với markup vì chúng có liên quan.
* JSX tương tự HTML, với một vài điểm khác biệt. Bạn có thể sử dụng [công cụ chuyển đổi](https://transform.tools/html-to-jsx) nếu cần.
* Thông báo lỗi thường sẽ chỉ bạn đi đúng hướng để sửa markup.

</Recap>



<Challenges>

#### Chuyển đổi HTML sang JSX {/*convert-some-html-to-jsx*/}

HTML này được dán vào một component, nhưng nó không phải JSX hợp lệ. Sửa nó:

<Sandpack>

```js
export default function Bio() {
  return (
    <div class="intro">
      <h1>Welcome to my website!</h1>
    </div>
    <p class="summary">
      You can find my thoughts here.
      <br><br>
      <b>And <i>pictures</b></i> of scientists!
    </p>
  );
}
```

```css
.intro {
  background-image: linear-gradient(to left, violet, indigo, blue, green, yellow, orange, red);
  background-clip: text;
  color: transparent;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.summary {
  padding: 20px;
  border: 10px solid gold;
}
```

</Sandpack>

Bạn có thể làm thủ công hoặc sử dụng công cụ chuyển đổi, tùy bạn!

<Solution>

<Sandpack>

```js
export default function Bio() {
  return (
    <div>
      <div className="intro">
        <h1>Welcome to my website!</h1>
      </div>
      <p className="summary">
        You can find my thoughts here.
        <br /><br />
        <b>And <i>pictures</i></b> of scientists!
      </p>
    </div>
  );
}
```

```css
.intro {
  background-image: linear-gradient(to left, violet, indigo, blue, green, yellow, orange, red);
  background-clip: text;
  color: transparent;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.summary {
  padding: 20px;
  border: 10px solid gold;
}
```

</Sandpack>

</Solution>

</Challenges>
