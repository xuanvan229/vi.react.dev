---
title: forwardRef
---

<Deprecated>

Trong React 19, `forwardRef` không còn cần thiết nữa. Hãy truyền `ref` như một prop thay thế.

`forwardRef` sẽ bị deprecated trong một phiên bản tương lai. Tìm hiểu thêm [tại đây](/blog/2024/04/25/react-19#ref-as-a-prop).

</Deprecated>

<Intro>

`forwardRef` cho phép component của bạn hiển thị một DOM node cho component cha bằng một [ref.](/learn/manipulating-the-dom-with-refs)

```js
const SomeComponent = forwardRef(render)
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `forwardRef(render)` {/*forwardref*/}

Gọi `forwardRef()` để cho phép component của bạn nhận một ref và chuyển tiếp nó cho một component con:

```js
import { forwardRef } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  // ...
});
```

[Xem thêm ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `render`: Hàm render cho component của bạn. React gọi hàm này với props và `ref` mà component của bạn nhận được từ cha của nó. JSX bạn trả về sẽ là đầu ra của component của bạn.

#### Giá trị trả về {/*returns*/}

`forwardRef` trả về một React component mà bạn có thể render trong JSX. Không giống như các React component được định nghĩa như các hàm thông thường, một component được trả về bởi `forwardRef` cũng có thể nhận một prop `ref`.

#### Lưu ý {/*caveats*/}

* Trong Strict Mode, React sẽ **gọi hàm render của bạn hai lần** để [giúp bạn tìm các tác dụng phụ ngẫu nhiên.](/reference/react/useState#my-initializer-or-updater-function-runs-twice) Đây là hành vi chỉ dành cho môi trường phát triển và không ảnh hưởng đến production. Nếu hàm render của bạn là thuần túy (như nó nên như vậy), điều này không nên ảnh hưởng đến logic của component của bạn. Kết quả từ một trong các lần gọi sẽ bị bỏ qua.


---

### Hàm `render` {/*render-function*/}

`forwardRef` nhận một hàm render như một tham số. React gọi hàm này với `props` và `ref`:

```js
const MyInput = forwardRef(function MyInput(props, ref) {
  return (
    <label>
      {props.label}
      <input ref={ref} />
    </label>
  );
});
```

#### Tham số {/*render-parameters*/}

* `props`: Props được truyền bởi component cha.

* `ref`: Thuộc tính `ref` được truyền bởi component cha. `ref` có thể là một object hoặc một hàm. Nếu component cha không truyền ref, nó sẽ là `null`. Bạn nên truyền `ref` bạn nhận được cho một component khác, hoặc truyền nó cho [`useImperativeHandle`.](/reference/react/useImperativeHandle)

#### Giá trị trả về {/*render-returns*/}

`forwardRef` trả về một React component mà bạn có thể render trong JSX. Không giống như các React component được định nghĩa như các hàm thông thường, component được trả về bởi `forwardRef` có thể nhận một prop `ref`.

---

## Cách sử dụng {/*usage*/}

### Hiển thị một DOM node cho component cha {/*exposing-a-dom-node-to-the-parent-component*/}

Theo mặc định, mỗi DOM node của component là riêng tư. Tuy nhiên, đôi khi hữu ích khi hiển thị một DOM node cho cha--ví dụ, để cho phép focus vào nó. Để tham gia, hãy bọc định nghĩa component của bạn vào `forwardRef()`:

```js {3,11}
import { forwardRef } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  const { label, ...otherProps } = props;
  return (
    <label>
      {label}
      <input {...otherProps} />
    </label>
  );
});
```

Bạn sẽ nhận được một <CodeStep step={1}>ref</CodeStep> như tham số thứ hai sau props. Truyền nó vào DOM node mà bạn muốn hiển thị:

```js {8} [[1, 3, "ref"], [1, 8, "ref", 30]]
import { forwardRef } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  const { label, ...otherProps } = props;
  return (
    <label>
      {label}
      <input {...otherProps} ref={ref} />
    </label>
  );
});
```

Điều này cho phép component cha `Form` truy cập <CodeStep step={2}>DOM node `<input>`</CodeStep> được hiển thị bởi `MyInput`:

```js [[1, 2, "ref"], [1, 10, "ref", 41], [2, 5, "ref.current"]]
function Form() {
  const ref = useRef(null);

  function handleClick() {
    ref.current.focus();
  }

  return (
    <form>
      <MyInput label="Enter your name:" ref={ref} />
      <button type="button" onClick={handleClick}>
        Edit
      </button>
    </form>
  );
}
```

Component `Form` này [truyền một ref](/reference/react/useRef#manipulating-the-dom-with-a-ref) cho `MyInput`. Component `MyInput` *chuyển tiếp* ref đó cho thẻ trình duyệt `<input>`. Kết quả là, component `Form` có thể truy cập DOM node `<input>` đó và gọi [`focus()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus) trên nó.

Hãy nhớ rằng việc hiển thị một ref cho DOM node bên trong component của bạn làm cho việc thay đổi nội bộ của component trở nên khó khăn hơn sau này. Bạn thường sẽ hiển thị DOM nodes từ các component cấp thấp có thể tái sử dụng như buttons hoặc text inputs, nhưng bạn sẽ không làm vậy cho các component cấp ứng dụng như avatar hoặc comment.

<Recipes titleText="Các ví dụ về chuyển tiếp ref">

#### Focus một text input {/*focusing-a-text-input*/}

Nhấn button sẽ focus vào input. Component `Form` định nghĩa một ref và truyền nó cho component `MyInput`. Component `MyInput` chuyển tiếp ref đó cho `<input>` trình duyệt. Điều này cho phép component `Form` focus vào `<input>`.

<Sandpack>

```js
import { useRef } from 'react';
import MyInput from './MyInput.js';

export default function Form() {
  const ref = useRef(null);

  function handleClick() {
    ref.current.focus();
  }

  return (
    <form>
      <MyInput label="Enter your name:" ref={ref} />
      <button type="button" onClick={handleClick}>
        Edit
      </button>
    </form>
  );
}
```

```js src/MyInput.js
import { forwardRef } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  const { label, ...otherProps } = props;
  return (
    <label>
      {label}
      <input {...otherProps} ref={ref} />
    </label>
  );
});

export default MyInput;
```

```css
input {
  margin: 5px;
}
```

</Sandpack>

<Solution />

#### Phát và tạm dừng một video {/*playing-and-pausing-a-video*/}

Nhấn button sẽ gọi [`play()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/play) và [`pause()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/pause) trên một DOM node `<video>`. Component `App` định nghĩa một ref và truyền nó cho component `MyVideoPlayer`. Component `MyVideoPlayer` chuyển tiếp ref đó cho node `<video>` trình duyệt. Điều này cho phép component `App` phát và tạm dừng `<video>`.

<Sandpack>

```js
import { useRef } from 'react';
import MyVideoPlayer from './MyVideoPlayer.js';

export default function App() {
  const ref = useRef(null);
  return (
    <>
      <button onClick={() => ref.current.play()}>
        Play
      </button>
      <button onClick={() => ref.current.pause()}>
        Pause
      </button>
      <br />
      <MyVideoPlayer
        ref={ref}
        src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
        type="video/mp4"
        width="250"
      />
    </>
  );
}
```

```js src/MyVideoPlayer.js
import { forwardRef } from 'react';

const VideoPlayer = forwardRef(function VideoPlayer({ src, type, width }, ref) {
  return (
    <video width={width} ref={ref}>
      <source
        src={src}
        type={type}
      />
    </video>
  );
});

export default VideoPlayer;
```

```css
button { margin-bottom: 10px; margin-right: 10px; }
```

</Sandpack>

<Solution />

</Recipes>

---

### Chuyển tiếp một ref qua nhiều component {/*forwarding-a-ref-through-multiple-components*/}

Thay vì chuyển tiếp một `ref` tới một DOM node, bạn có thể chuyển tiếp nó cho component của riêng bạn như `MyInput`:

```js {1,5}
const FormField = forwardRef(function FormField(props, ref) {
  // ...
  return (
    <>
      <MyInput ref={ref} />
      ...
    </>
  );
});
```

Nếu component `MyInput` đó chuyển tiếp một ref tới `<input>` của nó, một ref tới `FormField` sẽ cho bạn `<input>` đó:

```js {2,5,10}
function Form() {
  const ref = useRef(null);

  function handleClick() {
    ref.current.focus();
  }

  return (
    <form>
      <FormField label="Enter your name:" ref={ref} isRequired={true} />
      <button type="button" onClick={handleClick}>
        Edit
      </button>
    </form>
  );
}
```

Component `Form` định nghĩa một ref và truyền nó cho `FormField`. Component `FormField` chuyển tiếp ref đó cho `MyInput`, component này chuyển tiếp nó cho một DOM node trình duyệt `<input>`. Đây là cách `Form` truy cập DOM node đó.


<Sandpack>

```js
import { useRef } from 'react';
import FormField from './FormField.js';

export default function Form() {
  const ref = useRef(null);

  function handleClick() {
    ref.current.focus();
  }

  return (
    <form>
      <FormField label="Enter your name:" ref={ref} isRequired={true} />
      <button type="button" onClick={handleClick}>
        Edit
      </button>
    </form>
  );
}
```

```js src/FormField.js
import { forwardRef, useState } from 'react';
import MyInput from './MyInput.js';

const FormField = forwardRef(function FormField({ label, isRequired }, ref) {
  const [value, setValue] = useState('');
  return (
    <>
      <MyInput
        ref={ref}
        label={label}
        value={value}
        onChange={e => setValue(e.target.value)}
      />
      {(isRequired && value === '') &&
        <i>Required</i>
      }
    </>
  );
});

export default FormField;
```


```js src/MyInput.js
import { forwardRef } from 'react';

const MyInput = forwardRef((props, ref) => {
  const { label, ...otherProps } = props;
  return (
    <label>
      {label}
      <input {...otherProps} ref={ref} />
    </label>
  );
});

export default MyInput;
```

```css
input, button {
  margin: 5px;
}
```

</Sandpack>

---

### Hiển thị một imperative handle thay vì một DOM node {/*exposing-an-imperative-handle-instead-of-a-dom-node*/}

Thay vì hiển thị toàn bộ DOM node, bạn có thể hiển thị một object tùy chỉnh, được gọi là *imperative handle,* với một tập hợp phương thức bị hạn chế hơn. Để làm điều này, bạn cần định nghĩa một ref riêng để giữ DOM node:

```js {2,6}
const MyInput = forwardRef(function MyInput(props, ref) {
  const inputRef = useRef(null);

  // ...

  return <input {...props} ref={inputRef} />;
});
```

Truyền `ref` bạn nhận được cho [`useImperativeHandle`](/reference/react/useImperativeHandle) và chỉ định giá trị bạn muốn hiển thị cho `ref`:

```js {6-15}
import { forwardRef, useRef, useImperativeHandle } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => {
    return {
      focus() {
        inputRef.current.focus();
      },
      scrollIntoView() {
        inputRef.current.scrollIntoView();
      },
    };
  }, []);

  return <input {...props} ref={inputRef} />;
});
```

Nếu một component nào đó nhận được ref tới `MyInput`, nó sẽ chỉ nhận được object `{ focus, scrollIntoView }` của bạn thay vì DOM node. Điều này cho phép bạn giới hạn thông tin bạn hiển thị về DOM node của mình ở mức tối thiểu.

<Sandpack>

```js
import { useRef } from 'react';
import MyInput from './MyInput.js';

export default function Form() {
  const ref = useRef(null);

  function handleClick() {
    ref.current.focus();
    // This won't work because the DOM node isn't exposed:
    // ref.current.style.opacity = 0.5;
  }

  return (
    <form>
      <MyInput placeholder="Enter your name" ref={ref} />
      <button type="button" onClick={handleClick}>
        Edit
      </button>
    </form>
  );
}
```

```js src/MyInput.js
import { forwardRef, useRef, useImperativeHandle } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => {
    return {
      focus() {
        inputRef.current.focus();
      },
      scrollIntoView() {
        inputRef.current.scrollIntoView();
      },
    };
  }, []);

  return <input {...props} ref={inputRef} />;
});

export default MyInput;
```

```css
input {
  margin: 5px;
}
```

</Sandpack>

[Đọc thêm về cách sử dụng imperative handles.](/reference/react/useImperativeHandle)

<Pitfall>

**Đừng lạm dụng refs.** Bạn chỉ nên sử dụng refs cho các hành vi *mệnh lệnh* mà bạn không thể biểu đạt như props: ví dụ, cuộn đến một node, focus một node, kích hoạt một animation, chọn văn bản, v.v.

**Nếu bạn có thể biểu đạt một thứ gì đó như một prop, bạn không nên sử dụng ref.** Ví dụ, thay vì hiển thị một imperative handle như `{ open, close }` từ component `Modal`, tốt hơn là nhận `isOpen` như một prop như `<Modal isOpen={isOpen} />`. [Effects](/learn/synchronizing-with-effects) có thể giúp bạn hiển thị các hành vi mệnh lệnh thông qua props.

</Pitfall>

---

## Xử lý sự cố {/*troubleshooting*/}

### Component của tôi được bọc trong `forwardRef`, nhưng `ref` tới nó luôn là `null` {/*my-component-is-wrapped-in-forwardref-but-the-ref-to-it-is-always-null*/}

Điều này thường có nghĩa là bạn quên thực sự sử dụng `ref` mà bạn nhận được.

Ví dụ, component này không làm gì với `ref` của nó:

```js {1}
const MyInput = forwardRef(function MyInput({ label }, ref) {
  return (
    <label>
      {label}
      <input />
    </label>
  );
});
```

Để sửa, hãy truyền `ref` xuống cho một DOM node hoặc một component khác có thể nhận ref:

```js {1,5}
const MyInput = forwardRef(function MyInput({ label }, ref) {
  return (
    <label>
      {label}
      <input ref={ref} />
    </label>
  );
});
```

`ref` tới `MyInput` cũng có thể là `null` nếu một số logic có điều kiện:

```js {1,5}
const MyInput = forwardRef(function MyInput({ label, showInput }, ref) {
  return (
    <label>
      {label}
      {showInput && <input ref={ref} />}
    </label>
  );
});
```

Nếu `showInput` là `false`, thì ref sẽ không được chuyển tiếp đến node nào, và ref tới `MyInput` sẽ vẫn rỗng. Điều này đặc biệt dễ bỏ qua nếu điều kiện ẩn bên trong một component khác, như `Panel` trong ví dụ này:

```js {5,7}
const MyInput = forwardRef(function MyInput({ label, showInput }, ref) {
  return (
    <label>
      {label}
      <Panel isExpanded={showInput}>
        <input ref={ref} />
      </Panel>
    </label>
  );
});
```
