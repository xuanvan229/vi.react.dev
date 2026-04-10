---
title: createRef
---

<Pitfall>

`createRef` chủ yếu được sử dụng cho [class components.](/reference/react/Component) Function components thường dùng [`useRef`](/reference/react/useRef) thay thế.

</Pitfall>

<Intro>

`createRef` tạo ra một đối tượng [ref](/learn/referencing-values-with-refs) có thể chứa giá trị tùy ý.

```js
class MyInput extends Component {
  inputRef = createRef();
  // ...
}
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `createRef()` {/*createref*/}

Gọi `createRef` để khai báo một [ref](/learn/referencing-values-with-refs) bên trong một [class component.](/reference/react/Component)

```js
import { createRef, Component } from 'react';

class MyComponent extends Component {
  intervalRef = createRef();
  inputRef = createRef();
  // ...
```

[Xem thêm ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

`createRef` không nhận tham số nào.

#### Giá trị trả về {/*returns*/}

`createRef` trả về một object với một thuộc tính duy nhất:

* `current`: Ban đầu được set thành `null`. Bạn có thể đặt nó thành giá trị khác sau đó. Nếu bạn truyền object ref cho React như một thuộc tính `ref` cho một JSX node, React sẽ set thuộc tính `current` của nó.

#### Lưu ý {/*caveats*/}

* `createRef` luôn trả về một object *khác nhau*. Nó tương đương với việc viết `{ current: null }` tự mình.
* Trong một function component, bạn có thể muốn dùng [`useRef`](/reference/react/useRef) thay thế, vì nó luôn trả về cùng một object.
* `const ref = useRef()` tương đương với `const [ref, _] = useState(() => createRef(null))`.

---

## Cách sử dụng {/*usage*/}

### Khai báo một ref trong class component {/*declaring-a-ref-in-a-class-component*/}

Để khai báo một ref bên trong một [class component,](/reference/react/Component) hãy gọi `createRef` và gán kết quả cho một class field:

```js {4}
import { Component, createRef } from 'react';

class Form extends Component {
  inputRef = createRef();

  // ...
}
```

Nếu bây giờ bạn truyền `ref={this.inputRef}` cho một `<input>` trong JSX của bạn, React sẽ điền `this.inputRef.current` với DOM node input. Ví dụ, đây là cách bạn tạo một button để focus vào input:

<Sandpack>

```js
import { Component, createRef } from 'react';

export default class Form extends Component {
  inputRef = createRef();

  handleClick = () => {
    this.inputRef.current.focus();
  }

  render() {
    return (
      <>
        <input ref={this.inputRef} />
        <button onClick={this.handleClick}>
          Focus the input
        </button>
      </>
    );
  }
}
```

</Sandpack>

<Pitfall>

`createRef` chủ yếu được sử dụng cho [class components.](/reference/react/Component) Function components thường dùng [`useRef`](/reference/react/useRef) thay thế.

</Pitfall>

---

## Các lựa chọn thay thế {/*alternatives*/}

### Chuyển từ class dùng `createRef` sang function dùng `useRef` {/*migrating-from-a-class-with-createref-to-a-function-with-useref*/}

Chúng tôi khuyến nghị sử dụng function components thay vì [class components](/reference/react/Component) trong code mới. Nếu bạn có một số class component hiện tại đang sử dụng `createRef`, đây là cách bạn có thể chuyển đổi chúng. Đây là code gốc:

<Sandpack>

```js
import { Component, createRef } from 'react';

export default class Form extends Component {
  inputRef = createRef();

  handleClick = () => {
    this.inputRef.current.focus();
  }

  render() {
    return (
      <>
        <input ref={this.inputRef} />
        <button onClick={this.handleClick}>
          Focus the input
        </button>
      </>
    );
  }
}
```

</Sandpack>

Khi bạn [chuyển đổi component này từ class sang function,](/reference/react/Component#alternatives) hãy thay các lần gọi `createRef` bằng các lần gọi [`useRef`:](/reference/react/useRef)

<Sandpack>

```js
import { useRef } from 'react';

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

</Sandpack>
