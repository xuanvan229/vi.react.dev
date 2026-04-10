---
title: useRef
---

<Intro>

`useRef` là một React Hook cho phép bạn tham chiếu đến một giá trị không cần thiết cho việc render.

```js
const ref = useRef(initialValue)
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `useRef(initialValue)` {/*useref*/}

Gọi `useRef` ở cấp cao nhất của component để khai báo một [ref.](/learn/referencing-values-with-refs)

```js
import { useRef } from 'react';

function MyComponent() {
  const intervalRef = useRef(0);
  const inputRef = useRef(null);
  // ...
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `initialValue`: Giá trị bạn muốn thuộc tính `current` của đối tượng ref có ban đầu. Nó có thể là giá trị của bất kỳ kiểu nào. Tham số này sẽ bị bỏ qua sau lần render đầu tiên.

#### Giá trị trả về {/*returns*/}

`useRef` trả về một đối tượng với một thuộc tính duy nhất:

* `current`: Ban đầu, nó được đặt thành `initialValue` mà bạn đã truyền vào. Bạn có thể đặt nó thành giá trị khác sau đó. Nếu bạn truyền đối tượng ref cho React như một thuộc tính `ref` của một JSX node, React sẽ đặt thuộc tính `current` của nó.

Trong các lần render tiếp theo, `useRef` sẽ trả về cùng một đối tượng.

#### Lưu ý {/*caveats*/}

* Bạn có thể thay đổi thuộc tính `ref.current`. Không giống như state, nó có thể thay đổi trực tiếp (mutable). Tuy nhiên, nếu nó chứa một đối tượng được sử dụng để render (ví dụ, một phần của state), thì bạn không nên thay đổi trực tiếp đối tượng đó.
* Khi bạn thay đổi thuộc tính `ref.current`, React không render lại component của bạn. React không biết khi nào bạn thay đổi nó vì ref chỉ là một đối tượng JavaScript thông thường.
* Không ghi _hoặc đọc_ `ref.current` trong quá trình render, ngoại trừ [khởi tạo.](#avoiding-recreating-the-ref-contents) Điều này làm cho hành vi của component trở nên không thể dự đoán.
* Trong Strict Mode, React sẽ **gọi hàm component của bạn hai lần** để [giúp bạn tìm ra các tạp chất (impurities) vô tình.](/reference/react/useState#my-initializer-or-updater-function-runs-twice) Đây là hành vi chỉ xảy ra trong môi trường development và không ảnh hưởng đến production. Mỗi đối tượng ref sẽ được tạo hai lần, nhưng một trong các phiên bản sẽ bị loại bỏ. Nếu hàm component của bạn là pure (như nó nên là), điều này sẽ không ảnh hưởng đến hành vi.

---

## Cách sử dụng {/*usage*/}

### Tham chiếu một giá trị với ref {/*referencing-a-value-with-a-ref*/}

Gọi `useRef` ở cấp cao nhất của component để khai báo một hoặc nhiều [refs.](/learn/referencing-values-with-refs)

```js [[1, 4, "intervalRef"], [3, 4, "0"]]
import { useRef } from 'react';

function Stopwatch() {
  const intervalRef = useRef(0);
  // ...
```

`useRef` trả về một <CodeStep step={1}>đối tượng ref</CodeStep> với một thuộc tính <CodeStep step={2}>`current`</CodeStep> duy nhất được đặt ban đầu thành <CodeStep step={3}>giá trị ban đầu</CodeStep> mà bạn đã cung cấp.

Trong các lần render tiếp theo, `useRef` sẽ trả về cùng một đối tượng. Bạn có thể thay đổi thuộc tính `current` của nó để lưu trữ thông tin và đọc lại sau. Điều này có thể làm bạn nhớ đến [state](/reference/react/useState), nhưng có một sự khác biệt quan trọng.

**Thay đổi ref không kích hoạt việc render lại.** Điều này có nghĩa là refs rất phù hợp để lưu trữ thông tin không ảnh hưởng đến đầu ra hình ảnh của component. Ví dụ, nếu bạn cần lưu trữ một [interval ID](https://developer.mozilla.org/en-US/docs/Web/API/setInterval) và truy xuất nó sau, bạn có thể đặt nó vào ref. Để cập nhật giá trị bên trong ref, bạn cần thay đổi thủ công thuộc tính <CodeStep step={2}>`current`</CodeStep> của nó:

```js [[2, 5, "intervalRef.current"]]
function handleStartClick() {
  const intervalId = setInterval(() => {
    // ...
  }, 1000);
  intervalRef.current = intervalId;
}
```

Sau đó, bạn có thể đọc interval ID từ ref để có thể [xóa interval đó](https://developer.mozilla.org/en-US/docs/Web/API/clearInterval):

```js [[2, 2, "intervalRef.current"]]
function handleStopClick() {
  const intervalId = intervalRef.current;
  clearInterval(intervalId);
}
```

Bằng cách sử dụng ref, bạn đảm bảo rằng:

- Bạn có thể **lưu trữ thông tin** giữa các lần render lại (không giống như biến thông thường, sẽ bị reset mỗi lần render).
- Thay đổi nó **không kích hoạt render lại** (không giống như biến state, sẽ kích hoạt render lại).
- **Thông tin là cục bộ** cho mỗi bản sao của component (không giống như biến bên ngoài, được chia sẻ).

Thay đổi ref không kích hoạt render lại, nên refs không phù hợp để lưu trữ thông tin bạn muốn hiển thị trên màn hình. Sử dụng state cho điều đó. Đọc thêm về [chọn giữa `useRef` và `useState`.](/learn/referencing-values-with-refs#differences-between-refs-and-state)

<Recipes titleText="Ví dụ về tham chiếu giá trị với useRef" titleId="examples-value">

#### Bộ đếm click {/*click-counter*/}

Component này sử dụng ref để theo dõi số lần nút được click. Lưu ý rằng việc sử dụng ref thay vì state ở đây là hợp lý vì số lần click chỉ được đọc và ghi trong event handler.

<Sandpack>

```js
import { useRef } from 'react';

export default function Counter() {
  let ref = useRef(0);

  function handleClick() {
    ref.current = ref.current + 1;
    alert('You clicked ' + ref.current + ' times!');
  }

  return (
    <button onClick={handleClick}>
      Click me!
    </button>
  );
}
```

</Sandpack>

Nếu bạn hiển thị `{ref.current}` trong JSX, số sẽ không cập nhật khi click. Điều này là vì việc đặt `ref.current` không kích hoạt render lại. Thông tin được sử dụng để render nên là state.

<Solution />

#### Đồng hồ bấm giờ {/*a-stopwatch*/}

Ví dụ này sử dụng kết hợp state và refs. Cả `startTime` và `now` đều là biến state vì chúng được sử dụng để render. Nhưng chúng ta cũng cần giữ một [interval ID](https://developer.mozilla.org/en-US/docs/Web/API/setInterval) để có thể dừng interval khi nhấn nút. Vì interval ID không được sử dụng để render, nên việc giữ nó trong ref là phù hợp, và cập nhật thủ công.

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function Stopwatch() {
  const [startTime, setStartTime] = useState(null);
  const [now, setNow] = useState(null);
  const intervalRef = useRef(null);

  function handleStart() {
    setStartTime(Date.now());
    setNow(Date.now());

    clearInterval(intervalRef.current);
    intervalRef.current = setInterval(() => {
      setNow(Date.now());
    }, 10);
  }

  function handleStop() {
    clearInterval(intervalRef.current);
  }

  let secondsPassed = 0;
  if (startTime != null && now != null) {
    secondsPassed = (now - startTime) / 1000;
  }

  return (
    <>
      <h1>Time passed: {secondsPassed.toFixed(3)}</h1>
      <button onClick={handleStart}>
        Start
      </button>
      <button onClick={handleStop}>
        Stop
      </button>
    </>
  );
}
```

</Sandpack>

<Solution />

</Recipes>

<Pitfall>

**Không ghi _hoặc đọc_ `ref.current` trong quá trình render.**

React mong đợi rằng thân của component của bạn [hoạt động như một hàm pure](/learn/keeping-components-pure):

- Nếu các đầu vào ([props](/learn/passing-props-to-a-component), [state](/learn/state-a-components-memory), và [context](/learn/passing-data-deeply-with-context)) là giống nhau, nó nên trả về chính xác cùng JSX.
- Gọi nó theo thứ tự khác hoặc với các tham số khác sẽ không ảnh hưởng đến kết quả của các lời gọi khác.

Đọc hoặc ghi ref **trong quá trình render** vi phạm những kỳ vọng này.

```js {expectedErrors: {'react-compiler': [4]}} {3-4,6-7}
function MyComponent() {
  // ...
  // 🚩 Không ghi ref trong quá trình render
  myRef.current = 123;
  // ...
  // 🚩 Không đọc ref trong quá trình render
  return <h1>{myOtherRef.current}</h1>;
}
```

Bạn có thể đọc hoặc ghi refs **từ event handlers hoặc effects**.

```js {4-5,9-10}
function MyComponent() {
  // ...
  useEffect(() => {
    // ✅ Bạn có thể đọc hoặc ghi refs trong effects
    myRef.current = 123;
  });
  // ...
  function handleClick() {
    // ✅ Bạn có thể đọc hoặc ghi refs trong event handlers
    doSomething(myOtherRef.current);
  }
  // ...
}
```

Nếu bạn *phải* đọc [hoặc ghi](/reference/react/useState#storing-information-from-previous-renders) gì đó trong quá trình render, [sử dụng state](/reference/react/useState) thay thế.

Khi bạn vi phạm các quy tắc này, component của bạn có thể vẫn hoạt động, nhưng hầu hết các tính năng mới mà chúng tôi đang thêm vào React sẽ dựa vào những kỳ vọng này. Đọc thêm về [giữ cho components của bạn pure.](/learn/keeping-components-pure#where-you-_can_-cause-side-effects)

</Pitfall>

---

### Thao tác DOM với ref {/*manipulating-the-dom-with-a-ref*/}

Đặc biệt phổ biến khi sử dụng ref để thao tác [DOM.](https://developer.mozilla.org/en-US/docs/Web/API/HTML_DOM_API) React có hỗ trợ tích hợp cho điều này.

Đầu tiên, khai báo một <CodeStep step={1}>đối tượng ref</CodeStep> với <CodeStep step={3}>giá trị ban đầu</CodeStep> là `null`:

```js [[1, 4, "inputRef"], [3, 4, "null"]]
import { useRef } from 'react';

function MyComponent() {
  const inputRef = useRef(null);
  // ...
```

Sau đó truyền đối tượng ref của bạn như thuộc tính `ref` cho JSX của DOM node bạn muốn thao tác:

```js [[1, 2, "inputRef"]]
  // ...
  return <input ref={inputRef} />;
```

Sau khi React tạo DOM node và đặt nó lên màn hình, React sẽ đặt <CodeStep step={2}>thuộc tính `current`</CodeStep> của đối tượng ref của bạn thành DOM node đó. Bây giờ bạn có thể truy cập DOM node của `<input>` và gọi các phương thức như [`focus()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus):

```js [[2, 2, "inputRef.current"]]
  function handleClick() {
    inputRef.current.focus();
  }
```

React sẽ đặt thuộc tính `current` trở lại `null` khi node bị xóa khỏi màn hình.

Đọc thêm về [thao tác DOM với refs.](/learn/manipulating-the-dom-with-refs)

<Recipes titleText="Ví dụ về thao tác DOM với useRef" titleId="examples-dom">

#### Focus vào ô nhập văn bản {/*focusing-a-text-input*/}

Trong ví dụ này, click vào nút sẽ focus vào ô nhập:

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

<Solution />

#### Cuộn hình ảnh vào vùng nhìn {/*scrolling-an-image-into-view*/}

Trong ví dụ này, click vào nút sẽ cuộn một hình ảnh vào vùng nhìn. Nó sử dụng ref đến DOM node của danh sách, sau đó gọi API DOM [`querySelectorAll`](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelectorAll) để tìm hình ảnh chúng ta muốn cuộn đến.

<Sandpack>

```js
import { useRef } from 'react';

export default function CatFriends() {
  const listRef = useRef(null);

  function scrollToIndex(index) {
    const listNode = listRef.current;
    // Dòng này giả định một cấu trúc DOM cụ thể:
    const imgNode = listNode.querySelectorAll('li > img')[index];
    imgNode.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  return (
    <>
      <nav>
        <button onClick={() => scrollToIndex(0)}>
          Neo
        </button>
        <button onClick={() => scrollToIndex(1)}>
          Millie
        </button>
        <button onClick={() => scrollToIndex(2)}>
          Bella
        </button>
      </nav>
      <div>
        <ul ref={listRef}>
          <li>
            <img
              src="https://placecats.com/neo/300/200"
              alt="Neo"
            />
          </li>
          <li>
            <img
              src="https://placecats.com/millie/200/200"
              alt="Millie"
            />
          </li>
          <li>
            <img
              src="https://placecats.com/bella/199/200"
              alt="Bella"
            />
          </li>
        </ul>
      </div>
    </>
  );
}
```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}
```

</Sandpack>

<Solution />

#### Phát và tạm dừng video {/*playing-and-pausing-a-video*/}

Ví dụ này sử dụng ref để gọi [`play()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/play) và [`pause()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/pause) trên một DOM node `<video>`.

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function VideoPlayer() {
  const [isPlaying, setIsPlaying] = useState(false);
  const ref = useRef(null);

  function handleClick() {
    const nextIsPlaying = !isPlaying;
    setIsPlaying(nextIsPlaying);

    if (nextIsPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  }

  return (
    <>
      <button onClick={handleClick}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <video
        width="250"
        ref={ref}
        onPlay={() => setIsPlaying(true)}
        onPause={() => setIsPlaying(false)}
      >
        <source
          src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
          type="video/mp4"
        />
      </video>
    </>
  );
}
```

```css
button { display: block; margin-bottom: 20px; }
```

</Sandpack>

<Solution />

#### Expose ref cho component của bạn {/*exposing-a-ref-to-your-own-component*/}

Đôi khi, bạn có thể muốn cho component cha thao tác DOM bên trong component của bạn. Ví dụ, có thể bạn đang viết component `MyInput`, nhưng bạn muốn component cha có thể focus vào input (mà component cha không có quyền truy cập). Bạn có thể tạo một `ref` trong component cha và truyền `ref` như prop cho component con. Đọc [hướng dẫn chi tiết](/learn/manipulating-the-dom-with-refs#accessing-another-components-dom-nodes) tại đây.

<Sandpack>

```js
import { useRef } from 'react';

function MyInput({ ref }) {
  return <input ref={ref} />;
};

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

</Sandpack>

<Solution />

</Recipes>

---

### Tránh tạo lại nội dung ref {/*avoiding-recreating-the-ref-contents*/}

React lưu giá trị ref ban đầu một lần và bỏ qua nó trong các lần render tiếp theo.

```js
function Video() {
  const playerRef = useRef(new VideoPlayer());
  // ...
```

Mặc dù kết quả của `new VideoPlayer()` chỉ được sử dụng cho lần render đầu tiên, bạn vẫn gọi hàm này trong mọi lần render. Điều này có thể lãng phí nếu nó tạo các đối tượng tốn kém.

Để giải quyết, bạn có thể khởi tạo ref như thế này:

```js
function Video() {
  const playerRef = useRef(null);
  if (playerRef.current === null) {
    playerRef.current = new VideoPlayer();
  }
  // ...
```

Thông thường, việc ghi hoặc đọc `ref.current` trong quá trình render là không được phép. Tuy nhiên, trong trường hợp này thì không sao vì kết quả luôn giống nhau, và điều kiện chỉ thực thi trong quá trình khởi tạo nên nó hoàn toàn có thể dự đoán được.

<DeepDive>

#### Cách tránh kiểm tra null khi khởi tạo useRef sau {/*how-to-avoid-null-checks-when-initializing-use-ref-later*/}

Nếu bạn sử dụng trình kiểm tra kiểu và không muốn luôn kiểm tra `null`, bạn có thể thử mẫu sau:

```js
function Video() {
  const playerRef = useRef(null);

  function getPlayer() {
    if (playerRef.current !== null) {
      return playerRef.current;
    }
    const player = new VideoPlayer();
    playerRef.current = player;
    return player;
  }

  // ...
```

Ở đây, `playerRef` có thể là nullable. Tuy nhiên, bạn có thể thuyết phục trình kiểm tra kiểu của bạn rằng không có trường hợp nào `getPlayer()` trả về `null`. Sau đó sử dụng `getPlayer()` trong các event handlers của bạn.

</DeepDive>

---

## Xử lý sự cố {/*troubleshooting*/}

### Tôi không thể lấy ref cho component tự định nghĩa {/*i-cant-get-a-ref-to-a-custom-component*/}

Nếu bạn có truyền `ref` cho component của mình như thế này:

```js
const inputRef = useRef(null);

return <MyInput ref={inputRef} />;
```

Bạn có thể gặp lỗi trong console:

<ConsoleBlock level="error">

TypeError: Cannot read properties of null

</ConsoleBlock>

Mặc định, các component của bạn không expose refs đến các DOM nodes bên trong chúng.

Để sửa lỗi này, tìm component bạn muốn lấy ref:

```js
export default function MyInput({ value, onChange }) {
  return (
    <input
      value={value}
      onChange={onChange}
    />
  );
}
```

Và sau đó thêm `ref` vào danh sách props mà component của bạn chấp nhận và truyền `ref` như prop cho [component tích hợp](/reference/react-dom/components/common) liên quan như thế này:

```js {1,6}
function MyInput({ value, onChange, ref }) {
  return (
    <input
      value={value}
      onChange={onChange}
      ref={ref}
    />
  );
};

export default MyInput;
```

Sau đó component cha có thể lấy ref cho nó.
