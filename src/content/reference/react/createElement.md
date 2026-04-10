---
title: createElement
---

<Intro>

`createElement` cho phép bạn tạo một React element. Nó phục vụ như một lựa chọn thay thế cho việc viết [JSX.](/learn/writing-markup-with-jsx)

```js
const element = createElement(type, props, ...children)
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `createElement(type, props, ...children)` {/*createelement*/}

Gọi `createElement` để tạo một React element với `type`, `props` và `children` đã cho.

```js
import { createElement } from 'react';

function Greeting({ name }) {
  return createElement(
    'h1',
    { className: 'greeting' },
    'Hello'
  );
}
```

[Xem thêm ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `type`: Tham số `type` phải là một loại component React hợp lệ. Ví dụ, nó có thể là một chuỗi tên thẻ (như `'div'` hay `'span'`), hoặc một React component (một hàm, một class, hoặc một component đặc biệt như [`Fragment`](/reference/react/Fragment)).

* `props`: Tham số `props` phải là một object hoặc `null`. Nếu bạn truyền `null`, nó sẽ được coi như một object rỗng. React sẽ tạo ra một element với props khớp với `props` bạn đã truyền. Lưu ý rằng `ref` và `key` từ object `props` của bạn là đặc biệt và sẽ *không* có sẵn như `element.props.ref` và `element.props.key` trên `element` được trả về. Chúng sẽ có sẵn như `element.ref` và `element.key`.

* **tùy chọn** `...children`: Không hoặc nhiều node con. Chúng có thể là bất kỳ React node nào, bao gồm React elements, strings, numbers, [portals](/reference/react-dom/createPortal), các node rỗng (`null`, `undefined`, `true` và `false`), và các mảng React node.

#### Giá trị trả về {/*returns*/}

`createElement` trả về một đối tượng React element với một số thuộc tính:

* `type`: `type` bạn đã truyền.
* `props`: `props` bạn đã truyền ngoại trừ `ref` và `key`.
* `ref`: `ref` bạn đã truyền. Nếu thiếu, `null`.
* `key`: `key` bạn đã truyền, được chuyển thành chuỗi. Nếu thiếu, `null`.

Thông thường, bạn sẽ trả về element từ component của mình hoặc biến nó thành con của element khác. Mặc dù bạn có thể đọc các thuộc tính của element, tốt nhất là coi mỗi element như mờ đục sau khi tạo, và chỉ render nó.

#### Lưu ý {/*caveats*/}

* Bạn phải **coi React elements và props của chúng là [bất biến](https://en.wikipedia.org/wiki/Immutable_object)** và không bao giờ thay đổi nội dung của chúng sau khi tạo. Trong quá trình phát triển, React sẽ [đóng băng](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze) element được trả về và thuộc tính `props` của nó một cách nông để thực thi điều này.

* Khi bạn sử dụng JSX, **bạn phải bắt đầu thẻ bằng chữ in hoa để render component tùy chỉnh của mình.** Nói cách khác, `<Something />` tương đương với `createElement(Something)`, nhưng `<something />` (chữ thường) tương đương với `createElement('something')` (lưu ý đây là chuỗi, do đó sẽ được coi như thẻ HTML tích hợp).

* Bạn chỉ nên **truyền children như nhiều tham số cho `createElement` nếu tất cả chúng đều được biết tĩnh,** như `createElement('h1', {}, child1, child2, child3)`. Nếu children của bạn là động, hãy truyền toàn bộ mảng như tham số thứ ba: `createElement('ul', {}, listItems)`. Điều này đảm bảo rằng React sẽ [cảnh báo bạn về các `key` bị thiếu](/learn/rendering-lists#keeping-list-items-in-order-with-key) cho bất kỳ danh sách động nào. Đối với danh sách tĩnh, điều này không cần thiết vì chúng không bao giờ sắp xếp lại.

---

## Cách sử dụng {/*usage*/}

### Tạo một element mà không cần JSX {/*creating-an-element-without-jsx*/}

Nếu bạn không thích [JSX](/learn/writing-markup-with-jsx) hoặc không thể sử dụng nó trong dự án của mình, bạn có thể sử dụng `createElement` như một lựa chọn thay thế.

Để tạo một element mà không cần JSX, hãy gọi `createElement` với một số <CodeStep step={1}>type</CodeStep>, <CodeStep step={2}>props</CodeStep> và <CodeStep step={3}>children</CodeStep>:

```js [[1, 5, "'h1'"], [2, 6, "{ className: 'greeting' }"], [3, 7, "'Hello ',"], [3, 8, "createElement('i', null, name),"], [3, 9, "'. Welcome!'"]]
import { createElement } from 'react';

function Greeting({ name }) {
  return createElement(
    'h1',
    { className: 'greeting' },
    'Hello ',
    createElement('i', null, name),
    '. Welcome!'
  );
}
```

<CodeStep step={3}>children</CodeStep> là tùy chọn, và bạn có thể truyền bao nhiêu tùy ý (ví dụ trên có ba children). Code này sẽ hiển thị tiêu đề `<h1>` với một lời chào. Để so sánh, đây là cùng một ví dụ được viết lại với JSX:

```js [[1, 3, "h1"], [2, 3, "className=\\"greeting\\""], [3, 4, "Hello <i>{name}</i>. Welcome!"], [1, 5, "h1"]]
function Greeting({ name }) {
  return (
    <h1 className="greeting">
      Hello <i>{name}</i>. Welcome!
    </h1>
  );
}
```

Để render React component của riêng bạn, hãy truyền một hàm như `Greeting` như <CodeStep step={1}>type</CodeStep> thay vì một chuỗi như `'h1'`:

```js [[1, 2, "Greeting"], [2, 2, "{ name: 'Taylor' }"]]
export default function App() {
  return createElement(Greeting, { name: 'Taylor' });
}
```

Với JSX, nó sẽ trông như thế này:

```js [[1, 2, "Greeting"], [2, 2, "name=\\"Taylor\\""]]
export default function App() {
  return <Greeting name="Taylor" />;
}
```

Đây là một ví dụ hoàn chỉnh được viết với `createElement`:

<Sandpack>

```js
import { createElement } from 'react';

function Greeting({ name }) {
  return createElement(
    'h1',
    { className: 'greeting' },
    'Hello ',
    createElement('i', null, name),
    '. Welcome!'
  );
}

export default function App() {
  return createElement(
    Greeting,
    { name: 'Taylor' }
  );
}
```

```css
.greeting {
  color: darkgreen;
  font-family: Georgia;
}
```

</Sandpack>

Và đây là cùng một ví dụ được viết bằng JSX:

<Sandpack>

```js
function Greeting({ name }) {
  return (
    <h1 className="greeting">
      Hello <i>{name}</i>. Welcome!
    </h1>
  );
}

export default function App() {
  return <Greeting name="Taylor" />;
}
```

```css
.greeting {
  color: darkgreen;
  font-family: Georgia;
}
```

</Sandpack>

Cả hai phong cách viết code đều được, vì vậy bạn có thể sử dụng cái nào bạn thích cho dự án của mình. Lợi ích chính của việc sử dụng JSX so với `createElement` là dễ dàng hơn để thấy thẻ đóng nào tương ứng với thẻ mở nào.

<DeepDive>

#### Chính xác thì React element là gì? {/*what-is-a-react-element-exactly*/}

Một element là một mô tả nhẹ về một phần của giao diện người dùng. Ví dụ, cả `<Greeting name="Taylor" />` và `createElement(Greeting, { name: 'Taylor' })` đều tạo ra một object như thế này:

```js
// Được đơn giản hóa một chút
{
  type: Greeting,
  props: {
    name: 'Taylor'
  },
  key: null,
  ref: null,
}
```

**Lưu ý rằng việc tạo object này không render component `Greeting` hay tạo bất kỳ DOM element nào.**

Một React element giống như một mô tả hơn--một hướng dẫn cho React để render component `Greeting` sau đó. Bằng cách trả về object này từ component `App` của bạn, bạn cho React biết phải làm gì tiếp theo.

Việc tạo các element rất rẻ nên bạn không cần cố gắng tối ưu hóa hay tránh nó.

</DeepDive>
