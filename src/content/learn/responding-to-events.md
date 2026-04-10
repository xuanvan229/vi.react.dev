---
title: Phản Hồi Sự Kiện
---

<Intro>

React cho phép bạn thêm *event handler* vào JSX. Event handler là các hàm của bạn sẽ được kích hoạt để phản hồi lại các tương tác như nhấp chuột, di chuột, focus vào các ô nhập liệu của form, và nhiều hơn nữa.

</Intro>

<YouWillLearn>

* Các cách khác nhau để viết một event handler
* Cách truyền logic xử lý sự kiện từ component cha
* Cách sự kiện lan truyền và cách dừng chúng

</YouWillLearn>

## Thêm event handler {/*adding-event-handlers*/}

Để thêm một event handler, trước tiên bạn cần định nghĩa một hàm và sau đó [truyền nó như một prop](/learn/passing-props-to-a-component) vào thẻ JSX phù hợp. Ví dụ, đây là một nút chưa làm gì cả:

<Sandpack>

```js
export default function Button() {
  return (
    <button>
      I don't do anything
    </button>
  );
}
```

</Sandpack>

Bạn có thể làm nó hiển thị một thông báo khi người dùng nhấp bằng cách làm theo ba bước sau:

1. Khai báo một hàm có tên `handleClick` *bên trong* component `Button` của bạn.
2. Triển khai logic bên trong hàm đó (dùng `alert` để hiển thị thông báo).
3. Thêm `onClick={handleClick}` vào thẻ `<button>` trong JSX.

<Sandpack>

```js
export default function Button() {
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

```css
button { margin-right: 10px; }
```

</Sandpack>

Bạn đã định nghĩa hàm `handleClick` và sau đó [truyền nó như một prop](/learn/passing-props-to-a-component) vào `<button>`. `handleClick` là một **event handler.** Các hàm event handler:

* Thường được định nghĩa *bên trong* component của bạn.
* Có tên bắt đầu bằng `handle`, theo sau là tên của sự kiện.

Theo quy ước, người ta thường đặt tên event handler là `handle` theo sau là tên sự kiện. Bạn sẽ thường thấy `onClick={handleClick}`, `onMouseEnter={handleMouseEnter}`, và tương tự.

Ngoài ra, bạn có thể định nghĩa một event handler trực tiếp trong JSX:

```jsx
<button onClick={function handleClick() {
  alert('You clicked me!');
}}>
```

Hoặc ngắn gọn hơn, sử dụng arrow function:

```jsx
<button onClick={() => {
  alert('You clicked me!');
}}>
```

Tất cả các cách này đều tương đương nhau. Event handler được viết trực tiếp thì tiện lợi cho các hàm ngắn.

<Pitfall>

Các hàm truyền vào event handler phải được truyền, không phải gọi. Ví dụ:

| truyền một hàm (đúng)            | gọi một hàm (sai)                  |
| -------------------------------- | ---------------------------------- |
| `<button onClick={handleClick}>` | `<button onClick={handleClick()}>` |

Sự khác biệt là tinh tế. Trong ví dụ đầu tiên, hàm `handleClick` được truyền như một event handler `onClick`. Điều này cho React biết cần ghi nhớ và chỉ gọi hàm của bạn khi người dùng nhấp vào nút.

Trong ví dụ thứ hai, dấu `()` ở cuối `handleClick()` kích hoạt hàm *ngay lập tức* trong quá trình [rendering](/learn/render-and-commit), mà không cần bất kỳ cú nhấp nào. Điều này là vì JavaScript bên trong [JSX `{` và `}`](/learn/javascript-in-jsx-with-curly-braces) thực thi ngay lập tức.

Khi bạn viết code trực tiếp, cùng một vấn đề xuất hiện theo một cách khác:

| truyền một hàm (đúng)                   | gọi một hàm (sai)                 |
| --------------------------------------- | --------------------------------- |
| `<button onClick={() => alert('...')}>` | `<button onClick={alert('...')}>` |


Truyền code trực tiếp như thế này sẽ không kích hoạt khi nhấp—nó kích hoạt mỗi lần component render:

```jsx
// Alert này kích hoạt khi component render, không phải khi nhấp!
<button onClick={alert('You clicked me!')}>
```

Nếu bạn muốn định nghĩa event handler trực tiếp, hãy bọc nó trong một hàm ẩn danh như sau:

```jsx
<button onClick={() => alert('You clicked me!')}>
```

Thay vì thực thi code bên trong mỗi lần render, cách này tạo ra một hàm để gọi sau.

Trong cả hai trường hợp, những gì bạn muốn truyền là một hàm:

* `<button onClick={handleClick}>` truyền hàm `handleClick`.
* `<button onClick={() => alert('...')}>` truyền hàm `() => alert('...')`.

[Đọc thêm về arrow function.](https://javascript.info/arrow-functions-basics)

</Pitfall>

### Đọc props trong event handler {/*reading-props-in-event-handlers*/}

Vì event handler được khai báo bên trong component, chúng có quyền truy cập vào props của component. Đây là một nút khi được nhấp, hiển thị alert với prop `message` của nó:

<Sandpack>

```js
function AlertButton({ message, children }) {
  return (
    <button onClick={() => alert(message)}>
      {children}
    </button>
  );
}

export default function Toolbar() {
  return (
    <div>
      <AlertButton message="Playing!">
        Play Movie
      </AlertButton>
      <AlertButton message="Uploading!">
        Upload Image
      </AlertButton>
    </div>
  );
}
```

```css
button { margin-right: 10px; }
```

</Sandpack>

Điều này cho phép hai nút này hiển thị các thông báo khác nhau. Hãy thử thay đổi các thông báo được truyền vào chúng.

### Truyền event handler như props {/*passing-event-handlers-as-props*/}

Thường bạn sẽ muốn component cha chỉ định event handler của component con. Hãy xem xét các nút: tùy thuộc vào nơi bạn đang dùng component `Button`, bạn có thể muốn thực thi một hàm khác nhau—có thể một cái phát phim và một cái tải lên hình ảnh.

Để làm điều này, hãy truyền một prop mà component nhận được từ cha của nó như event handler như sau:

<Sandpack>

```js
function Button({ onClick, children }) {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}

function PlayButton({ movieName }) {
  function handlePlayClick() {
    alert(`Playing ${movieName}!`);
  }

  return (
    <Button onClick={handlePlayClick}>
      Play "{movieName}"
    </Button>
  );
}

function UploadButton() {
  return (
    <Button onClick={() => alert('Uploading!')}>
      Upload Image
    </Button>
  );
}

export default function Toolbar() {
  return (
    <div>
      <PlayButton movieName="Kiki's Delivery Service" />
      <UploadButton />
    </div>
  );
}
```

```css
button { margin-right: 10px; }
```

</Sandpack>

Ở đây, component `Toolbar` render một `PlayButton` và một `UploadButton`:

- `PlayButton` truyền `handlePlayClick` như prop `onClick` vào `Button` bên trong.
- `UploadButton` truyền `() => alert('Uploading!')` như prop `onClick` vào `Button` bên trong.

Cuối cùng, component `Button` của bạn chấp nhận một prop gọi là `onClick`. Nó truyền prop đó trực tiếp vào `<button>` tích hợp của trình duyệt với `onClick={onClick}`. Điều này cho React biết cần gọi hàm đã truyền khi nhấp.

Nếu bạn sử dụng một [design system](https://uxdesign.cc/everything-you-need-to-know-about-design-systems-54b109851969), thường các component như button sẽ chứa styling nhưng không chỉ định hành vi. Thay vào đó, các component như `PlayButton` và `UploadButton` sẽ truyền event handler xuống.

### Đặt tên props event handler {/*naming-event-handler-props*/}

Các component tích hợp như `<button>` và `<div>` chỉ hỗ trợ [tên sự kiện của trình duyệt](/reference/react-dom/components/common#common-props) như `onClick`. Tuy nhiên, khi bạn xây dựng các component của riêng mình, bạn có thể đặt tên props event handler của chúng theo bất kỳ cách nào bạn muốn.

Theo quy ước, props event handler nên bắt đầu bằng `on`, theo sau là một chữ cái viết hoa.

Ví dụ, prop `onClick` của component `Button` có thể được gọi là `onSmash`:

<Sandpack>

```js
function Button({ onSmash, children }) {
  return (
    <button onClick={onSmash}>
      {children}
    </button>
  );
}

export default function App() {
  return (
    <div>
      <Button onSmash={() => alert('Playing!')}>
        Play Movie
      </Button>
      <Button onSmash={() => alert('Uploading!')}>
        Upload Image
      </Button>
    </div>
  );
}
```

```css
button { margin-right: 10px; }
```

</Sandpack>

Trong ví dụ này, `<button onClick={onSmash}>` cho thấy rằng `<button>` (chữ thường) của trình duyệt vẫn cần một prop gọi là `onClick`, nhưng tên prop mà component `Button` tùy chỉnh của bạn nhận thì tùy ý bạn!

Khi component của bạn hỗ trợ nhiều tương tác, bạn có thể đặt tên props event handler theo các khái niệm dành riêng cho ứng dụng. Ví dụ, component `Toolbar` này nhận event handler `onPlayMovie` và `onUploadImage`:

<Sandpack>

```js
export default function App() {
  return (
    <Toolbar
      onPlayMovie={() => alert('Playing!')}
      onUploadImage={() => alert('Uploading!')}
    />
  );
}

function Toolbar({ onPlayMovie, onUploadImage }) {
  return (
    <div>
      <Button onClick={onPlayMovie}>
        Play Movie
      </Button>
      <Button onClick={onUploadImage}>
        Upload Image
      </Button>
    </div>
  );
}

function Button({ onClick, children }) {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}
```

```css
button { margin-right: 10px; }
```

</Sandpack>

Lưu ý cách component `App` không cần biết *những gì* `Toolbar` sẽ làm với `onPlayMovie` hay `onUploadImage`. Đó là chi tiết triển khai của `Toolbar`. Ở đây, `Toolbar` truyền chúng xuống như handler `onClick` cho các `Button` của nó, nhưng sau này cũng có thể kích hoạt chúng trên một phím tắt bàn phím. Đặt tên props theo các tương tác dành riêng cho ứng dụng như `onPlayMovie` cho bạn sự linh hoạt để thay đổi cách chúng được sử dụng sau này.

<Note>

Hãy đảm bảo rằng bạn sử dụng các thẻ HTML phù hợp cho event handler của mình. Ví dụ, để xử lý các cú nhấp, hãy sử dụng [`<button onClick={handleClick}>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button) thay vì `<div onClick={handleClick}>`. Sử dụng `<button>` thực sự của trình duyệt cho phép các hành vi tích hợp của trình duyệt như điều hướng bàn phím. Nếu bạn không thích style mặc định của trình duyệt cho button và muốn làm cho nó trông giống link hoặc phần tử UI khác hơn, bạn có thể đạt được điều đó bằng CSS. [Tìm hiểu thêm về cách viết markup có khả năng truy cập.](https://developer.mozilla.org/en-US/docs/Learn/Accessibility/HTML)

</Note>

## Lan truyền sự kiện {/*event-propagation*/}

Event handler cũng sẽ bắt các sự kiện từ bất kỳ component con nào mà component của bạn có thể có. Chúng ta nói rằng sự kiện "nổi bọt" hay "lan truyền" lên cây: nó bắt đầu từ nơi sự kiện xảy ra, và sau đó đi lên cây.

`<div>` này chứa hai nút. Cả `<div>` *và* mỗi nút đều có handler `onClick` riêng. Bạn nghĩ handler nào sẽ kích hoạt khi bạn nhấp vào một nút?

<Sandpack>

```js
export default function Toolbar() {
  return (
    <div className="Toolbar" onClick={() => {
      alert('You clicked on the toolbar!');
    }}>
      <button onClick={() => alert('Playing!')}>
        Play Movie
      </button>
      <button onClick={() => alert('Uploading!')}>
        Upload Image
      </button>
    </div>
  );
}
```

```css
.Toolbar {
  background: #aaa;
  padding: 5px;
}
button { margin: 5px; }
```

</Sandpack>

Nếu bạn nhấp vào một trong hai nút, `onClick` của nó sẽ chạy trước, theo sau là `onClick` của `<div>` cha. Vì vậy hai thông báo sẽ xuất hiện. Nếu bạn nhấp vào thanh công cụ, chỉ có `onClick` của `<div>` cha mới chạy.

<Pitfall>

Tất cả sự kiện đều lan truyền trong React ngoại trừ `onScroll`, cái chỉ hoạt động trên thẻ JSX bạn gắn vào.

</Pitfall>

### Dừng lan truyền {/*stopping-propagation*/}

Event handler nhận một **đối tượng sự kiện** như là đối số duy nhất của chúng. Theo quy ước, nó thường được gọi là `e`, viết tắt của "event". Bạn có thể sử dụng đối tượng này để đọc thông tin về sự kiện.

Đối tượng sự kiện đó cũng cho phép bạn dừng lan truyền. Nếu bạn muốn ngăn một sự kiện tiếp cận các component cha, bạn cần gọi `e.stopPropagation()` như component `Button` này thực hiện:

<Sandpack>

```js
function Button({ onClick, children }) {
  return (
    <button onClick={e => {
      e.stopPropagation();
      onClick();
    }}>
      {children}
    </button>
  );
}

export default function Toolbar() {
  return (
    <div className="Toolbar" onClick={() => {
      alert('You clicked on the toolbar!');
    }}>
      <Button onClick={() => alert('Playing!')}>
        Play Movie
      </Button>
      <Button onClick={() => alert('Uploading!')}>
        Upload Image
      </Button>
    </div>
  );
}
```

```css
.Toolbar {
  background: #aaa;
  padding: 5px;
}
button { margin: 5px; }
```

</Sandpack>

Khi bạn nhấp vào một nút:

1. React gọi handler `onClick` được truyền vào `<button>`.
2. Handler đó, được định nghĩa trong `Button`, thực hiện những việc sau:
   * Gọi `e.stopPropagation()`, ngăn sự kiện lan truyền thêm.
   * Gọi hàm `onClick`, là một prop được truyền từ component `Toolbar`.
3. Hàm đó, được định nghĩa trong component `Toolbar`, hiển thị alert của chính nút đó.
4. Vì lan truyền đã bị dừng, handler `onClick` của `<div>` cha *không* chạy.

Kết quả của `e.stopPropagation()`, nhấp vào các nút bây giờ chỉ hiển thị một alert duy nhất (từ `<button>`) thay vì cả hai (từ `<button>` và thanh công cụ `<div>` cha). Nhấp vào một nút không giống như nhấp vào thanh công cụ bao quanh, vì vậy việc dừng lan truyền có nghĩa cho giao diện người dùng này.

<DeepDive>

#### Sự kiện giai đoạn capture {/*capture-phase-events*/}

Trong những trường hợp hiếm gặp, bạn có thể cần bắt tất cả sự kiện trên các phần tử con, *ngay cả khi chúng đã dừng lan truyền*. Ví dụ, có thể bạn muốn ghi lại mọi lần nhấp vào analytics, bất kể logic lan truyền. Bạn có thể làm điều này bằng cách thêm `Capture` vào cuối tên sự kiện:

```js
<div onClickCapture={() => { /* this runs first */ }}>
  <button onClick={e => e.stopPropagation()} />
  <button onClick={e => e.stopPropagation()} />
</div>
```

Mỗi sự kiện lan truyền theo ba giai đoạn:

1. Nó đi xuống, gọi tất cả handler `onClickCapture`.
2. Nó chạy handler `onClick` của phần tử được nhấp.
3. Nó đi lên, gọi tất cả handler `onClick`.

Sự kiện Capture hữu ích cho code như router hoặc analytics, nhưng bạn có thể sẽ không dùng chúng trong code ứng dụng.

</DeepDive>

### Truyền handler như một lựa chọn thay thế cho lan truyền {/*passing-handlers-as-alternative-to-propagation*/}

Lưu ý cách click handler này chạy một dòng code _và sau đó_ gọi prop `onClick` được truyền bởi cha:

```js {4,5}
function Button({ onClick, children }) {
  return (
    <button onClick={e => {
      e.stopPropagation();
      onClick();
    }}>
      {children}
    </button>
  );
}
```

Bạn cũng có thể thêm nhiều code hơn vào handler này trước khi gọi event handler `onClick` cha. Pattern này cung cấp một *lựa chọn thay thế* cho lan truyền. Nó cho phép component con xử lý sự kiện, đồng thời cũng cho component cha chỉ định một số hành vi bổ sung. Không giống như lan truyền, nó không tự động. Nhưng lợi ích của pattern này là bạn có thể theo dõi rõ ràng toàn bộ chuỗi code thực thi như là kết quả của một sự kiện nào đó.

Nếu bạn dựa vào lan truyền và gặp khó khăn khi theo dõi handler nào thực thi và tại sao, hãy thử cách tiếp cận này.

### Ngăn hành vi mặc định {/*preventing-default-behavior*/}

Một số sự kiện trình duyệt có hành vi mặc định liên quan đến chúng. Ví dụ, sự kiện submit của `<form>`, xảy ra khi một nút bên trong nó được nhấp, sẽ tải lại toàn bộ trang theo mặc định:

<Sandpack>

```js
export default function Signup() {
  return (
    <form onSubmit={() => alert('Submitting!')}>
      <input />
      <button>Send</button>
    </form>
  );
}
```

```css
button { margin-left: 5px; }
```

</Sandpack>

Bạn có thể gọi `e.preventDefault()` trên đối tượng sự kiện để ngăn điều này xảy ra:

<Sandpack>

```js
export default function Signup() {
  return (
    <form onSubmit={e => {
      e.preventDefault();
      alert('Submitting!');
    }}>
      <input />
      <button>Send</button>
    </form>
  );
}
```

```css
button { margin-left: 5px; }
```

</Sandpack>

Đừng nhầm lẫn `e.stopPropagation()` và `e.preventDefault()`. Cả hai đều hữu ích, nhưng không liên quan đến nhau:

* [`e.stopPropagation()`](https://developer.mozilla.org/docs/Web/API/Event/stopPropagation) ngăn các event handler gắn vào các thẻ phía trên kích hoạt.
* [`e.preventDefault()` ](https://developer.mozilla.org/docs/Web/API/Event/preventDefault) ngăn hành vi mặc định của trình duyệt đối với một số sự kiện có hành vi đó.

## Event handler có thể có side effect không? {/*can-event-handlers-have-side-effects*/}

Hoàn toàn có thể! Event handler là nơi tốt nhất cho side effect.

Không giống như các hàm rendering, event handler không cần phải là [pure](/learn/keeping-components-pure), vì vậy đây là một nơi tuyệt vời để *thay đổi* điều gì đó—ví dụ, thay đổi giá trị của input khi đánh máy, hoặc thay đổi một danh sách khi nhấn nút. Tuy nhiên, để thay đổi một số thông tin, trước tiên bạn cần một cách để lưu trữ nó. Trong React, điều này được thực hiện bằng cách sử dụng [state, bộ nhớ của component.](/learn/state-a-components-memory) Bạn sẽ tìm hiểu tất cả về nó ở trang tiếp theo.

<Recap>

* Bạn có thể xử lý sự kiện bằng cách truyền một hàm như prop vào một phần tử như `<button>`.
* Event handler phải được truyền, **không phải gọi!** `onClick={handleClick}`, không phải `onClick={handleClick()}`.
* Bạn có thể định nghĩa một hàm event handler riêng biệt hoặc trực tiếp.
* Event handler được định nghĩa bên trong component, vì vậy chúng có thể truy cập props.
* Bạn có thể khai báo event handler trong cha và truyền nó như prop xuống con.
* Bạn có thể định nghĩa props event handler của riêng mình với các tên dành riêng cho ứng dụng.
* Sự kiện lan truyền lên trên. Gọi `e.stopPropagation()` trên đối số đầu tiên để ngăn điều đó.
* Sự kiện có thể có hành vi mặc định không mong muốn của trình duyệt. Gọi `e.preventDefault()` để ngăn điều đó.
* Gọi rõ ràng một prop event handler từ handler con là một lựa chọn thay thế tốt cho lan truyền.

</Recap>



<Challenges>

#### Sửa event handler {/*fix-an-event-handler*/}

Nhấp vào nút này được cho là chuyển đổi nền trang giữa trắng và đen. Tuy nhiên, không có gì xảy ra khi bạn nhấp vào nó. Hãy sửa lỗi. (Đừng lo lắng về logic bên trong `handleClick`—phần đó ổn.)

<Sandpack>

```js {expectedErrors: {'react-compiler': [5, 7]}}
export default function LightSwitch() {
  function handleClick() {
    let bodyStyle = document.body.style;
    if (bodyStyle.backgroundColor === 'black') {
      bodyStyle.backgroundColor = 'white';
    } else {
      bodyStyle.backgroundColor = 'black';
    }
  }

  return (
    <button onClick={handleClick()}>
      Toggle the lights
    </button>
  );
}
```

</Sandpack>

<Solution>

Vấn đề là `<button onClick={handleClick()}>` _gọi_ hàm `handleClick` trong khi rendering thay vì _truyền_ nó. Xóa dấu `()` để thành `<button onClick={handleClick}>` sẽ khắc phục sự cố:

<Sandpack>

```js
export default function LightSwitch() {
  function handleClick() {
    let bodyStyle = document.body.style;
    if (bodyStyle.backgroundColor === 'black') {
      bodyStyle.backgroundColor = 'white';
    } else {
      bodyStyle.backgroundColor = 'black';
    }
  }

  return (
    <button onClick={handleClick}>
      Toggle the lights
    </button>
  );
}
```

</Sandpack>

Ngoài ra, bạn có thể bọc lệnh gọi trong một hàm khác, như `<button onClick={() => handleClick()}>`:

<Sandpack>

```js
export default function LightSwitch() {
  function handleClick() {
    let bodyStyle = document.body.style;
    if (bodyStyle.backgroundColor === 'black') {
      bodyStyle.backgroundColor = 'white';
    } else {
      bodyStyle.backgroundColor = 'black';
    }
  }

  return (
    <button onClick={() => handleClick()}>
      Toggle the lights
    </button>
  );
}
```

</Sandpack>

</Solution>

#### Kết nối các sự kiện {/*wire-up-the-events*/}

Component `ColorSwitch` này render một nút. Nó được cho là thay đổi màu trang. Hãy kết nối nó với prop event handler `onChangeColor` mà nó nhận từ cha để nhấp vào nút thay đổi màu.

Sau khi bạn làm điều này, hãy chú ý rằng nhấp vào nút cũng tăng bộ đếm nhấp trang. Đồng nghiệp của bạn, người đã viết component cha, khẳng định rằng `onChangeColor` không tăng bất kỳ bộ đếm nào. Điều gì khác có thể đang xảy ra? Hãy sửa để nhấp vào nút *chỉ* thay đổi màu, và _không_ tăng bộ đếm.

<Sandpack>

```js src/ColorSwitch.js active
export default function ColorSwitch({
  onChangeColor
}) {
  return (
    <button>
      Change color
    </button>
  );
}
```

```js src/App.js hidden
import { useState } from 'react';
import ColorSwitch from './ColorSwitch.js';

export default function App() {
  const [clicks, setClicks] = useState(0);

  function handleClickOutside() {
    setClicks(c => c + 1);
  }

  function getRandomLightColor() {
    let r = 150 + Math.round(100 * Math.random());
    let g = 150 + Math.round(100 * Math.random());
    let b = 150 + Math.round(100 * Math.random());
    return `rgb(${r}, ${g}, ${b})`;
  }

  function handleChangeColor() {
    let bodyStyle = document.body.style;
    bodyStyle.backgroundColor = getRandomLightColor();
  }

  return (
    <div style={{ width: '100%', height: '100%' }} onClick={handleClickOutside}>
      <ColorSwitch onChangeColor={handleChangeColor} />
      <br />
      <br />
      <h2>Clicks on the page: {clicks}</h2>
    </div>
  );
}
```

</Sandpack>

<Solution>

Đầu tiên, bạn cần thêm event handler, như `<button onClick={onChangeColor}>`.

Tuy nhiên, điều này gây ra vấn đề tăng bộ đếm. Nếu `onChangeColor` không làm điều này, như đồng nghiệp của bạn khẳng định, thì vấn đề là sự kiện này lan truyền lên, và một số handler phía trên thực hiện điều đó. Để giải quyết vấn đề này, bạn cần dừng lan truyền. Nhưng đừng quên rằng bạn vẫn nên gọi `onChangeColor`.

<Sandpack>

```js src/ColorSwitch.js active
export default function ColorSwitch({
  onChangeColor
}) {
  return (
    <button onClick={e => {
      e.stopPropagation();
      onChangeColor();
    }}>
      Change color
    </button>
  );
}
```

```js src/App.js hidden
import { useState } from 'react';
import ColorSwitch from './ColorSwitch.js';

export default function App() {
  const [clicks, setClicks] = useState(0);

  function handleClickOutside() {
    setClicks(c => c + 1);
  }

  function getRandomLightColor() {
    let r = 150 + Math.round(100 * Math.random());
    let g = 150 + Math.round(100 * Math.random());
    let b = 150 + Math.round(100 * Math.random());
    return `rgb(${r}, ${g}, ${b})`;
  }

  function handleChangeColor() {
    let bodyStyle = document.body.style;
    bodyStyle.backgroundColor = getRandomLightColor();
  }

  return (
    <div style={{ width: '100%', height: '100%' }} onClick={handleClickOutside}>
      <ColorSwitch onChangeColor={handleChangeColor} />
      <br />
      <br />
      <h2>Clicks on the page: {clicks}</h2>
    </div>
  );
}
```

</Sandpack>

</Solution>

</Challenges>
