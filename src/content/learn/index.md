---
title: Bắt đầu nhanh
---

<Intro>

Chào mừng bạn đến với tài liệu React! Trang này sẽ giới thiệu cho bạn 80% các khái niệm React mà bạn sẽ sử dụng hàng ngày.

</Intro>

<YouWillLearn>

- Cách tạo và lồng các component
- Cách thêm markup và style
- Cách hiển thị dữ liệu
- Cách render theo điều kiện và danh sách
- Cách phản hồi sự kiện và cập nhật màn hình
- Cách chia sẻ dữ liệu giữa các component

</YouWillLearn>

## Tạo và lồng các component {/*components*/}

Các ứng dụng React được xây dựng từ các *component*. Một component là một phần của giao diện người dùng (UI) có logic và giao diện riêng. Một component có thể nhỏ như một nút bấm, hoặc lớn như cả một trang.

Các React component là các hàm JavaScript trả về markup:

```js
function MyButton() {
  return (
    <button>I'm a button</button>
  );
}
```

Bây giờ bạn đã khai báo `MyButton`, bạn có thể lồng nó vào một component khác:

```js {5}
export default function MyApp() {
  return (
    <div>
      <h1>Welcome to my app</h1>
      <MyButton />
    </div>
  );
}
```

Lưu ý rằng `<MyButton />` bắt đầu bằng chữ cái viết hoa. Đó là cách bạn nhận biết đó là một React component. Tên React component phải luôn bắt đầu bằng chữ cái viết hoa, trong khi các thẻ HTML phải viết thường.

Hãy xem kết quả:

<Sandpack>

```js
function MyButton() {
  return (
    <button>
      I'm a button
    </button>
  );
}

export default function MyApp() {
  return (
    <div>
      <h1>Welcome to my app</h1>
      <MyButton />
    </div>
  );
}
```

</Sandpack>

Các từ khóa `export default` chỉ định component chính trong file. Nếu bạn không quen với một số cú pháp JavaScript, [MDN](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/export) và [javascript.info](https://javascript.info/import-export) có các tài liệu tham khảo tuyệt vời.

## Viết markup với JSX {/*writing-markup-with-jsx*/}

Cú pháp markup bạn thấy ở trên được gọi là *JSX*. Nó là tùy chọn, nhưng hầu hết các dự án React sử dụng JSX vì sự tiện lợi của nó. Tất cả các [công cụ chúng tôi khuyến nghị cho phát triển cục bộ](/learn/installation) đều hỗ trợ JSX ngay từ đầu.

JSX nghiêm ngặt hơn HTML. Bạn phải đóng các thẻ như `<br />`. Component của bạn cũng không thể trả về nhiều thẻ JSX. Bạn phải bọc chúng trong một phần tử cha chung, như `<div>...</div>` hoặc một wrapper rỗng `<>...</>`:

```js {3,6}
function AboutPage() {
  return (
    <>
      <h1>About</h1>
      <p>Hello there.<br />How do you do?</p>
    </>
  );
}
```

Nếu bạn có nhiều HTML cần chuyển sang JSX, bạn có thể sử dụng [công cụ chuyển đổi trực tuyến.](https://transform.tools/html-to-jsx)

## Thêm style {/*adding-styles*/}

Trong React, bạn chỉ định một CSS class với `className`. Nó hoạt động giống như thuộc tính HTML [`class`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/class):

```js
<img className="avatar" />
```

Sau đó bạn viết các quy tắc CSS cho nó trong một file CSS riêng:

```css
/* In your CSS */
.avatar {
  border-radius: 50%;
}
```

React không quy định cách bạn thêm file CSS. Trong trường hợp đơn giản nhất, bạn sẽ thêm một thẻ [`<link>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link) vào HTML của bạn. Nếu bạn sử dụng một công cụ build hoặc framework, hãy tham khảo tài liệu của nó để tìm hiểu cách thêm file CSS vào dự án.

## Hiển thị dữ liệu {/*displaying-data*/}

JSX cho phép bạn đặt markup vào trong JavaScript. Dấu ngoặc nhọn cho phép bạn "thoát ra" JavaScript để bạn có thể nhúng một biến từ code và hiển thị nó cho người dùng. Ví dụ, đoạn sau sẽ hiển thị `user.name`:

```js {3}
return (
  <h1>
    {user.name}
  </h1>
);
```

Bạn cũng có thể "thoát sang JavaScript" từ các thuộc tính JSX, nhưng bạn phải sử dụng dấu ngoặc nhọn *thay vì* dấu ngoặc kép. Ví dụ, `className="avatar"` truyền chuỗi `"avatar"` làm CSS class, nhưng `src={user.imageUrl}` đọc giá trị biến JavaScript `user.imageUrl`, và sau đó truyền giá trị đó làm thuộc tính `src`:

```js {3,4}
return (
  <img
    className="avatar"
    src={user.imageUrl}
  />
);
```

Bạn cũng có thể đặt các biểu thức phức tạp hơn bên trong dấu ngoặc nhọn JSX, ví dụ, [nối chuỗi](https://javascript.info/operators#string-concatenation-with-binary):

<Sandpack>

```js
const user = {
  name: 'Hedy Lamarr',
  imageUrl: 'https://react.dev/images/docs/scientists/yXOvdOSs.jpg',
  imageSize: 90,
};

export default function Profile() {
  return (
    <>
      <h1>{user.name}</h1>
      <img
        className="avatar"
        src={user.imageUrl}
        alt={'Photo of ' + user.name}
        style={{
          width: user.imageSize,
          height: user.imageSize
        }}
      />
    </>
  );
}
```

```css
.avatar {
  border-radius: 50%;
}

.large {
  border: 4px solid gold;
}
```

</Sandpack>

Trong ví dụ trên, `style={{}}` không phải là cú pháp đặc biệt, mà là một object `{}` thông thường bên trong dấu ngoặc nhọn JSX `style={ }`. Bạn có thể sử dụng thuộc tính `style` khi các style của bạn phụ thuộc vào biến JavaScript.

## Render theo điều kiện {/*conditional-rendering*/}

Trong React, không có cú pháp đặc biệt để viết điều kiện. Thay vào đó, bạn sẽ sử dụng các kỹ thuật tương tự như khi viết code JavaScript thông thường. Ví dụ, bạn có thể sử dụng câu lệnh [`if`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else) để bao gồm JSX có điều kiện:

```js
let content;
if (isLoggedIn) {
  content = <AdminPanel />;
} else {
  content = <LoginForm />;
}
return (
  <div>
    {content}
  </div>
);
```

Nếu bạn thích code gọn hơn, bạn có thể sử dụng [toán tử điều kiện `?`.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator) Không giống `if`, nó hoạt động bên trong JSX:

```js
<div>
  {isLoggedIn ? (
    <AdminPanel />
  ) : (
    <LoginForm />
  )}
</div>
```

Khi bạn không cần nhánh `else`, bạn cũng có thể sử dụng [cú pháp logic `&&`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND#short-circuit_evaluation) ngắn hơn:

```js
<div>
  {isLoggedIn && <AdminPanel />}
</div>
```

Tất cả các cách tiếp cận này cũng hoạt động cho việc chỉ định thuộc tính có điều kiện. Nếu bạn không quen với một số cú pháp JavaScript này, bạn có thể bắt đầu bằng cách luôn sử dụng `if...else`.

## Render danh sách {/*rendering-lists*/}

Bạn sẽ dựa vào các tính năng JavaScript như [vòng lặp `for`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for) và [hàm `map()` của mảng](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) để render danh sách các component.

Ví dụ, giả sử bạn có một mảng các sản phẩm:

```js
const products = [
  { title: 'Cabbage', id: 1 },
  { title: 'Garlic', id: 2 },
  { title: 'Apple', id: 3 },
];
```

Bên trong component của bạn, sử dụng hàm `map()` để chuyển đổi một mảng sản phẩm thành một mảng các phần tử `<li>`:

```js
const listItems = products.map(product =>
  <li key={product.id}>
    {product.title}
  </li>
);

return (
  <ul>{listItems}</ul>
);
```

Lưu ý rằng `<li>` có thuộc tính `key`. Đối với mỗi phần tử trong danh sách, bạn nên truyền một chuỗi hoặc số để xác định duy nhất phần tử đó trong số các phần tử anh em. Thông thường, một key nên đến từ dữ liệu của bạn, chẳng hạn như ID cơ sở dữ liệu. React sử dụng các key của bạn để biết điều gì đã xảy ra nếu bạn sau đó chèn, xóa hoặc sắp xếp lại các phần tử.

<Sandpack>

```js
const products = [
  { title: 'Cabbage', isFruit: false, id: 1 },
  { title: 'Garlic', isFruit: false, id: 2 },
  { title: 'Apple', isFruit: true, id: 3 },
];

export default function ShoppingList() {
  const listItems = products.map(product =>
    <li
      key={product.id}
      style={{
        color: product.isFruit ? 'magenta' : 'darkgreen'
      }}
    >
      {product.title}
    </li>
  );

  return (
    <ul>{listItems}</ul>
  );
}
```

</Sandpack>

## Phản hồi sự kiện {/*responding-to-events*/}

Bạn có thể phản hồi sự kiện bằng cách khai báo các hàm *event handler* bên trong component của bạn:

```js {2-4,7}
function MyButton() {
  function handleClick() {
    alert('You clicked me!');
  }

  return (
    <button onClick={handleClick}>
      Click me
    </button>
  );
}
```

Lưu ý rằng `onClick={handleClick}` không có dấu ngoặc đơn ở cuối! Đừng _gọi_ hàm event handler: bạn chỉ cần *truyền nó xuống*. React sẽ gọi event handler của bạn khi người dùng nhấp vào nút.

## Cập nhật màn hình {/*updating-the-screen*/}

Thường xuyên, bạn sẽ muốn component của mình "nhớ" một số thông tin và hiển thị nó. Ví dụ, có thể bạn muốn đếm số lần một nút được nhấp. Để làm điều này, hãy thêm *state* vào component của bạn.

Đầu tiên, import [`useState`](/reference/react/useState) từ React:

```js
import { useState } from 'react';
```

Bây giờ bạn có thể khai báo một *biến state* bên trong component của bạn:

```js
function MyButton() {
  const [count, setCount] = useState(0);
  // ...
```

Bạn sẽ nhận được hai thứ từ `useState`: state hiện tại (`count`), và hàm cho phép bạn cập nhật nó (`setCount`). Bạn có thể đặt tên bất kỳ cho chúng, nhưng quy ước là viết `[something, setSomething]`.

Lần đầu tiên nút được hiển thị, `count` sẽ là `0` vì bạn đã truyền `0` cho `useState()`. Khi bạn muốn thay đổi state, hãy gọi `setCount()` và truyền giá trị mới cho nó. Nhấp vào nút này sẽ tăng bộ đếm:

```js {5}
function MyButton() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      Clicked {count} times
    </button>
  );
}
```

React sẽ gọi lại hàm component của bạn. Lần này, `count` sẽ là `1`. Sau đó sẽ là `2`. Và cứ thế tiếp tục.

Nếu bạn render cùng một component nhiều lần, mỗi component sẽ có state riêng. Hãy nhấp vào từng nút riêng biệt:

<Sandpack>

```js
import { useState } from 'react';

export default function MyApp() {
  return (
    <div>
      <h1>Counters that update separately</h1>
      <MyButton />
      <MyButton />
    </div>
  );
}

function MyButton() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      Clicked {count} times
    </button>
  );
}
```

```css
button {
  display: block;
  margin-bottom: 5px;
}
```

</Sandpack>

Lưu ý cách mỗi nút "nhớ" state `count` riêng của nó và không ảnh hưởng đến các nút khác.

## Sử dụng Hooks {/*using-hooks*/}

Các hàm bắt đầu bằng `use` được gọi là *Hooks*. `useState` là một Hook tích hợp sẵn do React cung cấp. Bạn có thể tìm các Hook tích hợp sẵn khác trong [tài liệu tham khảo API.](/reference/react) Bạn cũng có thể viết các Hook riêng bằng cách kết hợp các Hook hiện có.

Hooks có nhiều hạn chế hơn các hàm khác. Bạn chỉ có thể gọi Hooks *ở đầu* các component (hoặc các Hook khác). Nếu bạn muốn sử dụng `useState` trong một điều kiện hoặc vòng lặp, hãy trích xuất một component mới và đặt nó ở đó.

## Chia sẻ dữ liệu giữa các component {/*sharing-data-between-components*/}

Trong ví dụ trước, mỗi `MyButton` có `count` độc lập riêng, và khi mỗi nút được nhấp, chỉ `count` của nút được nhấp mới thay đổi:

<DiagramGroup>

<Diagram name="sharing_data_child" height={367} width={407} alt="Sơ đồ hiển thị một cây gồm ba component, một cha có tên MyApp và hai con có tên MyButton. Cả hai component MyButton đều chứa count với giá trị bằng không.">

Ban đầu, state `count` của mỗi `MyButton` là `0`

</Diagram>

<Diagram name="sharing_data_child_clicked" height={367} width={407} alt="Sơ đồ giống như trước, với count của component con MyButton đầu tiên được tô sáng cho thấy một lần nhấp với giá trị count tăng lên một. Component MyButton thứ hai vẫn chứa giá trị không." >

`MyButton` đầu tiên cập nhật `count` của nó thành `1`

</Diagram>

</DiagramGroup>

Tuy nhiên, thường bạn sẽ cần các component *chia sẻ dữ liệu và luôn cập nhật cùng nhau*.

Để cả hai component `MyButton` hiển thị cùng `count` và cập nhật cùng nhau, bạn cần di chuyển state từ các nút riêng lẻ "lên trên" đến component gần nhất chứa tất cả chúng.

Trong ví dụ này, đó là `MyApp`:

<DiagramGroup>

<Diagram name="sharing_data_parent" height={385} width={410} alt="Sơ đồ hiển thị một cây gồm ba component, một cha có tên MyApp và hai con có tên MyButton. MyApp chứa giá trị count bằng không được truyền xuống cả hai component MyButton, cũng hiển thị giá trị không." >

Ban đầu, state `count` của `MyApp` là `0` và được truyền xuống cả hai con

</Diagram>

<Diagram name="sharing_data_parent_clicked" height={385} width={410} alt="Sơ đồ giống như trước, với count của component cha MyApp được tô sáng cho thấy một lần nhấp với giá trị tăng lên một. Luồng đến cả hai component con MyButton cũng được tô sáng, và giá trị count trong mỗi con được đặt thành một cho thấy giá trị đã được truyền xuống." >

Khi nhấp, `MyApp` cập nhật state `count` thành `1` và truyền nó xuống cả hai con

</Diagram>

</DiagramGroup>

Bây giờ khi bạn nhấp vào bất kỳ nút nào, `count` trong `MyApp` sẽ thay đổi, điều này sẽ thay đổi cả hai count trong `MyButton`. Đây là cách bạn có thể biểu đạt điều này trong code.

Đầu tiên, *di chuyển state lên* từ `MyButton` vào `MyApp`:

```js {2-6,18}
export default function MyApp() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <div>
      <h1>Counters that update separately</h1>
      <MyButton />
      <MyButton />
    </div>
  );
}

function MyButton() {
  // ... chúng ta đang di chuyển code từ đây ...
}

```

Sau đó, *truyền state xuống* từ `MyApp` đến mỗi `MyButton`, cùng với hàm xử lý click chung. Bạn có thể truyền thông tin đến `MyButton` sử dụng dấu ngoặc nhọn JSX, giống như bạn đã làm trước đó với các thẻ tích hợp như `<img>`:

```js {11-12}
export default function MyApp() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <div>
      <h1>Counters that update together</h1>
      <MyButton count={count} onClick={handleClick} />
      <MyButton count={count} onClick={handleClick} />
    </div>
  );
}
```

Thông tin bạn truyền xuống như thế này được gọi là _props_. Bây giờ component `MyApp` chứa state `count` và event handler `handleClick`, và *truyền cả hai xuống dưới dạng props* cho mỗi nút.

Cuối cùng, thay đổi `MyButton` để *đọc* các props bạn đã truyền từ component cha:

```js {1,3}
function MyButton({ count, onClick }) {
  return (
    <button onClick={onClick}>
      Clicked {count} times
    </button>
  );
}
```

Khi bạn nhấp vào nút, handler `onClick` được kích hoạt. Prop `onClick` của mỗi nút được đặt thành hàm `handleClick` bên trong `MyApp`, vì vậy code bên trong nó chạy. Code đó gọi `setCount(count + 1)`, tăng biến state `count`. Giá trị `count` mới được truyền dưới dạng prop cho mỗi nút, vì vậy tất cả đều hiển thị giá trị mới. Điều này được gọi là "nâng state lên". Bằng cách di chuyển state lên, bạn đã chia sẻ nó giữa các component.

<Sandpack>

```js
import { useState } from 'react';

export default function MyApp() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <div>
      <h1>Counters that update together</h1>
      <MyButton count={count} onClick={handleClick} />
      <MyButton count={count} onClick={handleClick} />
    </div>
  );
}

function MyButton({ count, onClick }) {
  return (
    <button onClick={onClick}>
      Clicked {count} times
    </button>
  );
}
```

```css
button {
  display: block;
  margin-bottom: 5px;
}
```

</Sandpack>

## Bước tiếp theo {/*next-steps*/}

Đến đây, bạn đã biết những kiến thức cơ bản về cách viết code React!

Hãy xem [Hướng dẫn](/learn/tutorial-tic-tac-toe) để thực hành và xây dựng ứng dụng mini đầu tiên với React.
