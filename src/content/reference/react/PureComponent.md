---
title: PureComponent
---

<Pitfall>

Chúng tôi khuyến nghị định nghĩa các component như các hàm thay vì class. [Xem cách chuyển đổi.](#alternatives)

</Pitfall>

<Intro>

`PureComponent` tương tự như [`Component`](/reference/react/Component) nhưng bỏ qua re-render khi props và state giống nhau. Class components vẫn được React hỗ trợ, nhưng chúng tôi không khuyến nghị dùng chúng trong code mới.

```js
class Greeting extends PureComponent {
  render() {
    return <h1>Hello, {this.props.name}!</h1>;
  }
}
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `PureComponent` {/*purecomponent*/}

Để bỏ qua việc re-render một class component khi props và state giống nhau, hãy extend `PureComponent` thay vì [`Component`:](/reference/react/Component)

```js
import { PureComponent } from 'react';

class Greeting extends PureComponent {
  render() {
    return <h1>Hello, {this.props.name}!</h1>;
  }
}
```

`PureComponent` là một subclass của `Component` và hỗ trợ [tất cả các API của `Component`.](/reference/react/Component#reference) Extending `PureComponent` tương đương với việc định nghĩa một phương thức [`shouldComponentUpdate`](/reference/react/Component#shouldcomponentupdate) tùy chỉnh so sánh nông (shallow) props và state.


[Xem thêm ví dụ bên dưới.](#usage)

---

## Cách sử dụng {/*usage*/}

### Bỏ qua các lần re-render không cần thiết cho class components {/*skipping-unnecessary-re-renders-for-class-components*/}

React thông thường re-render một component bất cứ khi nào component cha của nó re-render. Như một tối ưu hóa, bạn có thể tạo ra một component mà React sẽ không re-render khi component cha re-render miễn là props và state mới của nó giống với props và state cũ. [Class components](/reference/react/Component) có thể tham gia vào hành vi này bằng cách extend `PureComponent`:

```js {1}
class Greeting extends PureComponent {
  render() {
    return <h1>Hello, {this.props.name}!</h1>;
  }
}
```

Một React component phải luôn có [logic render thuần túy.](/learn/keeping-components-pure) Điều này có nghĩa là nó phải trả về cùng đầu ra nếu props, state và context của nó không thay đổi. Bằng cách sử dụng `PureComponent`, bạn đang nói với React rằng component của bạn tuân thủ yêu cầu này, vì vậy React không cần re-render miễn là props và state của nó không thay đổi. Tuy nhiên, component của bạn vẫn sẽ re-render nếu một context mà nó đang sử dụng thay đổi.

Trong ví dụ này, lưu ý rằng component `Greeting` re-render bất cứ khi nào `name` thay đổi (vì đó là một trong các props của nó), nhưng không khi `address` thay đổi (vì nó không được truyền cho `Greeting` như một prop):

<Sandpack>

```js
import { PureComponent, useState } from 'react';

class Greeting extends PureComponent {
  render() {
    console.log("Greeting was rendered at", new Date().toLocaleTimeString());
    return <h3>Hello{this.props.name && ', '}{this.props.name}!</h3>;
  }
}

export default function MyApp() {
  const [name, setName] = useState('');
  const [address, setAddress] = useState('');
  return (
    <>
      <label>
        Name{': '}
        <input value={name} onChange={e => setName(e.target.value)} />
      </label>
      <label>
        Address{': '}
        <input value={address} onChange={e => setAddress(e.target.value)} />
      </label>
      <Greeting name={name} />
    </>
  );
}
```

```css
label {
  display: block;
  margin-bottom: 16px;
}
```

</Sandpack>

<Pitfall>

Chúng tôi khuyến nghị định nghĩa các component như các hàm thay vì class. [Xem cách chuyển đổi.](#alternatives)

</Pitfall>

---

## Các lựa chọn thay thế {/*alternatives*/}

### Chuyển đổi từ class component `PureComponent` sang function {/*migrating-from-a-purecomponent-class-component-to-a-function*/}

Chúng tôi khuyến nghị sử dụng function components thay vì [class components](/reference/react/Component) trong code mới. Nếu bạn có một số class component hiện tại sử dụng `PureComponent`, đây là cách bạn có thể chuyển đổi chúng. Đây là code gốc:

<Sandpack>

```js
import { PureComponent, useState } from 'react';

class Greeting extends PureComponent {
  render() {
    console.log("Greeting was rendered at", new Date().toLocaleTimeString());
    return <h3>Hello{this.props.name && ', '}{this.props.name}!</h3>;
  }
}

export default function MyApp() {
  const [name, setName] = useState('');
  const [address, setAddress] = useState('');
  return (
    <>
      <label>
        Name{': '}
        <input value={name} onChange={e => setName(e.target.value)} />
      </label>
      <label>
        Address{': '}
        <input value={address} onChange={e => setAddress(e.target.value)} />
      </label>
      <Greeting name={name} />
    </>
  );
}
```

```css
label {
  display: block;
  margin-bottom: 16px;
}
```

</Sandpack>

Khi bạn [chuyển đổi component này từ class sang function,](/reference/react/Component#alternatives) hãy bọc nó trong [`memo`:](/reference/react/memo)

<Sandpack>

```js
import { memo, useState } from 'react';

const Greeting = memo(function Greeting({ name }) {
  console.log("Greeting was rendered at", new Date().toLocaleTimeString());
  return <h3>Hello{name && ', '}{name}!</h3>;
});

export default function MyApp() {
  const [name, setName] = useState('');
  const [address, setAddress] = useState('');
  return (
    <>
      <label>
        Name{': '}
        <input value={name} onChange={e => setName(e.target.value)} />
      </label>
      <label>
        Address{': '}
        <input value={address} onChange={e => setAddress(e.target.value)} />
      </label>
      <Greeting name={name} />
    </>
  );
}
```

```css
label {
  display: block;
  margin-bottom: 16px;
}
```

</Sandpack>

<Note>

Không giống như `PureComponent`, [`memo`](/reference/react/memo) không so sánh state mới và cũ. Trong function components, việc gọi [hàm `set`](/reference/react/useState#setstate) với cùng state [đã ngăn re-render theo mặc định,](/reference/react/memo#updating-a-memoized-component-using-state) ngay cả khi không có `memo`.

</Note>
