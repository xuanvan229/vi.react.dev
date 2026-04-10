---
title: 'Tham chiếu Giá trị với Refs'
---

<Intro>

Khi bạn muốn một component "ghi nhớ" một thông tin nào đó, nhưng bạn không muốn thông tin đó [kích hoạt các lần render mới](/learn/render-and-commit), bạn có thể dùng một *ref*.

</Intro>

<YouWillLearn>

- Cách thêm ref vào component của bạn
- Cách cập nhật giá trị của ref
- Refs khác với state như thế nào
- Cách sử dụng refs một cách an toàn

</YouWillLearn>

## Thêm ref vào component của bạn {/*adding-a-ref-to-your-component*/}

Bạn có thể thêm ref vào component của bạn bằng cách import Hook `useRef` từ React:

```js
import { useRef } from 'react';
```

Bên trong component, gọi Hook `useRef` và truyền giá trị ban đầu mà bạn muốn tham chiếu làm đối số duy nhất. Ví dụ, đây là một ref đến giá trị `0`:

```js
const ref = useRef(0);
```

`useRef` trả về một đối tượng như sau:

```js
{
  current: 0 // Giá trị bạn đã truyền cho useRef
}
```

<Illustration src="/images/docs/illustrations/i_ref.png" alt="An arrow with 'current' written on it stuffed into a pocket with 'ref' written on it." />

Bạn có thể truy cập giá trị hiện tại của ref đó thông qua thuộc tính `ref.current`. Giá trị này có thể thay đổi được, nghĩa là bạn có thể cả đọc và ghi vào nó. Nó giống như một túi bí mật của component mà React không theo dõi. (Đây là điều làm cho nó trở thành "lối thoát" khỏi luồng dữ liệu một chiều của React--thêm chi tiết bên dưới!)

Ở đây, một nút sẽ tăng `ref.current` mỗi lần nhấp:

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

Ref trỏ đến một số, nhưng giống như [state](/learn/state-a-components-memory), bạn có thể trỏ đến bất cứ thứ gì: một chuỗi, một đối tượng, thậm chí một hàm. Không giống như state, ref là một đối tượng JavaScript thuần túy với thuộc tính `current` mà bạn có thể đọc và sửa đổi.

Lưu ý rằng **component không re-render với mỗi lần tăng.** Giống như state, refs được React giữ lại giữa các lần re-render. Tuy nhiên, việc thiết lập state re-renders một component. Thay đổi ref thì không!

## Ví dụ: xây dựng đồng hồ bấm giờ {/*example-building-a-stopwatch*/}

Bạn có thể kết hợp refs và state trong một component. Ví dụ, hãy tạo một đồng hồ bấm giờ mà người dùng có thể bắt đầu hoặc dừng bằng cách nhấn nút. Để hiển thị thời gian đã trôi qua kể từ khi người dùng nhấn "Start", bạn cần theo dõi khi nào nút Start được nhấn và thời gian hiện tại là bao nhiêu. **Thông tin này được sử dụng để render, vì vậy bạn sẽ lưu nó trong state:**

```js
const [startTime, setStartTime] = useState(null);
const [now, setNow] = useState(null);
```

Khi người dùng nhấn "Start", bạn sẽ dùng [`setInterval`](https://developer.mozilla.org/docs/Web/API/setInterval) để cập nhật thời gian mỗi 10 mili giây:

<Sandpack>

```js
import { useState } from 'react';

export default function Stopwatch() {
  const [startTime, setStartTime] = useState(null);
  const [now, setNow] = useState(null);

  function handleStart() {
    // Start counting.
    setStartTime(Date.now());
    setNow(Date.now());

    setInterval(() => {
      // Update the current time every 10ms.
      setNow(Date.now());
    }, 10);
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
    </>
  );
}
```

</Sandpack>

Khi nút "Stop" được nhấn, bạn cần hủy interval hiện tại để nó ngừng cập nhật biến state `now`. Bạn có thể làm điều này bằng cách gọi [`clearInterval`](https://developer.mozilla.org/en-US/docs/Web/API/clearInterval), nhưng bạn cần cho nó interval ID đã được trả về trước đó bởi lời gọi `setInterval` khi người dùng nhấn Start. Bạn cần lưu interval ID ở đâu đó. **Vì interval ID không được dùng để render, bạn có thể lưu nó trong một ref:**

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

Khi một thông tin được dùng để render, hãy giữ nó trong state. Khi một thông tin chỉ cần bởi event handlers và việc thay đổi nó không yêu cầu re-render, việc dùng ref có thể hiệu quả hơn.

## Sự khác biệt giữa refs và state {/*differences-between-refs-and-state*/}

Có lẽ bạn đang nghĩ refs có vẻ ít "nghiêm ngặt" hơn state—bạn có thể thay đổi chúng thay vì luôn phải dùng hàm thiết lập state. Nhưng trong hầu hết các trường hợp, bạn sẽ muốn dùng state. Refs là một "lối thoát" mà bạn sẽ không cần thường xuyên. Đây là cách so sánh state và refs:

| refs                                                                                  | state                                                                                                                     |
| ------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| `useRef(initialValue)` trả về `{ current: initialValue }`                            | `useState(initialValue)` trả về giá trị hiện tại của biến state và hàm setter state ( `[value, setValue]`) |
| Không kích hoạt re-render khi bạn thay đổi nó.                                         | Kích hoạt re-render khi bạn thay đổi nó.                                                                                    |
| Có thể thay đổi—bạn có thể sửa đổi và cập nhật giá trị `current` bên ngoài quá trình render. | "Bất biến"—bạn phải dùng hàm thiết lập state để sửa đổi biến state để đặt vào hàng đợi re-render.                       |
| Bạn không nên đọc (hoặc ghi) giá trị `current` trong khi render. | Bạn có thể đọc state bất cứ lúc nào. Tuy nhiên, mỗi lần render có [snapshot](/learn/state-as-a-snapshot) riêng của state, snapshot này không thay đổi.

Đây là nút đếm được triển khai với state:

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      You clicked {count} times
    </button>
  );
}
```

</Sandpack>

Vì giá trị `count` được hiển thị, nên việc dùng giá trị state cho nó là hợp lý. Khi giá trị của counter được thiết lập với `setCount()`, React re-renders component và màn hình cập nhật để phản ánh số đếm mới.

Nếu bạn thử triển khai điều này với ref, React sẽ không bao giờ re-render component, vì vậy bạn sẽ không bao giờ thấy số đếm thay đổi! Xem cách nhấp vào nút này **không cập nhật text của nó**:

<Sandpack>

```js {expectedErrors: {'react-compiler': [13]}}
import { useRef } from 'react';

export default function Counter() {
  let countRef = useRef(0);

  function handleClick() {
    // This doesn't re-render the component!
    countRef.current = countRef.current + 1;
  }

  return (
    <button onClick={handleClick}>
      You clicked {countRef.current} times
    </button>
  );
}
```

</Sandpack>

Đây là lý do tại sao việc đọc `ref.current` trong quá trình render dẫn đến code không đáng tin cậy. Nếu bạn cần điều đó, hãy dùng state thay thế.

<DeepDive>

#### useRef hoạt động bên trong như thế nào? {/*how-does-use-ref-work-inside*/}

Mặc dù cả `useState` và `useRef` đều được cung cấp bởi React, về nguyên tắc `useRef` có thể được triển khai _trên cơ sở của_ `useState`. Bạn có thể tưởng tượng rằng bên trong React, `useRef` được triển khai như sau:

```js
// Bên trong React
function useRef(initialValue) {
  const [ref, unused] = useState({ current: initialValue });
  return ref;
}
```

Trong lần render đầu tiên, `useRef` trả về `{ current: initialValue }`. Đối tượng này được lưu trữ bởi React, vì vậy trong lần render tiếp theo cùng một đối tượng sẽ được trả về. Lưu ý cách state setter không được dùng trong ví dụ này. Nó không cần thiết vì `useRef` luôn cần trả về cùng một đối tượng!

React cung cấp phiên bản tích hợp sẵn của `useRef` vì nó đủ phổ biến trong thực tế. Nhưng bạn có thể nghĩ nó như là một biến state thông thường không có setter. Nếu bạn quen với lập trình hướng đối tượng, refs có thể nhắc bạn về các trường instance--nhưng thay vì `this.something` bạn viết `somethingRef.current`.

</DeepDive>

## Khi nào nên dùng refs {/*when-to-use-refs*/}

Thông thường, bạn sẽ dùng ref khi component của bạn cần "bước ra ngoài" React và giao tiếp với các API bên ngoài—thường là browser API không ảnh hưởng đến giao diện của component. Dưới đây là một số tình huống hiếm gặp:

- Lưu trữ [timeout IDs](https://developer.mozilla.org/docs/Web/API/setTimeout)
- Lưu trữ và thao tác [DOM elements](https://developer.mozilla.org/docs/Web/API/Element), được đề cập ở [trang tiếp theo](/learn/manipulating-the-dom-with-refs)
- Lưu trữ các đối tượng khác không cần thiết để tính toán JSX.

Nếu component của bạn cần lưu trữ một giá trị nào đó, nhưng nó không ảnh hưởng đến logic render, hãy chọn refs.

## Các thực hành tốt nhất với refs {/*best-practices-for-refs*/}

Tuân theo các nguyên tắc này sẽ làm cho các component của bạn có thể dự đoán hơn:

- **Coi refs như lối thoát.** Refs hữu ích khi bạn làm việc với các hệ thống bên ngoài hoặc browser APIs. Nếu nhiều logic ứng dụng và luồng dữ liệu của bạn phụ thuộc vào refs, bạn có thể muốn xem xét lại cách tiếp cận của mình.
- **Đừng đọc hoặc ghi `ref.current` trong quá trình render.** Nếu một số thông tin cần thiết trong quá trình render, hãy dùng [state](/learn/state-a-components-memory) thay thế. Vì React không biết khi nào `ref.current` thay đổi, ngay cả việc đọc nó trong khi render khiến hành vi của component khó dự đoán. (Ngoại lệ duy nhất là code như `if (!ref.current) ref.current = new Thing()` chỉ thiết lập ref một lần trong lần render đầu tiên.)

Các hạn chế của React state không áp dụng cho refs. Ví dụ, state hoạt động như [snapshot cho mỗi lần render](/learn/state-as-a-snapshot) và [không cập nhật đồng bộ.](/learn/queueing-a-series-of-state-updates) Nhưng khi bạn thay đổi giá trị hiện tại của ref, nó thay đổi ngay lập tức:

```js
ref.current = 5;
console.log(ref.current); // 5
```

Điều này là vì **bản thân ref là một đối tượng JavaScript thông thường,** và vì vậy nó hoạt động như vậy.

Bạn cũng không cần lo lắng về [việc tránh mutation](/learn/updating-objects-in-state) khi làm việc với ref. Miễn là đối tượng bạn đang thay đổi không được dùng để render, React không quan tâm bạn làm gì với ref hoặc nội dung của nó.

## Refs và DOM {/*refs-and-the-dom*/}

Bạn có thể trỏ ref đến bất kỳ giá trị nào. Tuy nhiên, trường hợp sử dụng phổ biến nhất của ref là truy cập phần tử DOM. Ví dụ, điều này tiện lợi nếu bạn muốn focus vào input theo lập trình. Khi bạn truyền ref vào thuộc tính `ref` trong JSX, như `<div ref={myRef}>`, React sẽ đưa phần tử DOM tương ứng vào `myRef.current`. Khi phần tử bị xóa khỏi DOM, React sẽ cập nhật `myRef.current` thành `null`. Bạn có thể đọc thêm về điều này trong [Thao tác DOM với Refs.](/learn/manipulating-the-dom-with-refs)

<Recap>

- Refs là lối thoát để giữ các giá trị không được dùng để render. Bạn sẽ không cần chúng thường xuyên.
- Ref là một đối tượng JavaScript thuần túy với một thuộc tính duy nhất gọi là `current`, mà bạn có thể đọc hoặc thiết lập.
- Bạn có thể yêu cầu React cung cấp cho bạn một ref bằng cách gọi Hook `useRef`.
- Giống như state, refs cho phép bạn giữ lại thông tin giữa các lần re-render của component.
- Không giống như state, việc thiết lập giá trị `current` của ref không kích hoạt re-render.
- Đừng đọc hoặc ghi `ref.current` trong quá trình render. Điều này làm cho component của bạn khó dự đoán.

</Recap>



<Challenges>

#### Sửa input chat bị hỏng {/*fix-a-broken-chat-input*/}

Gõ một tin nhắn và nhấp "Send". Bạn sẽ nhận thấy có một độ trễ ba giây trước khi bạn thấy alert "Sent!". Trong khoảng thời gian chờ này, bạn có thể thấy nút "Undo". Nhấp vào nó. Nút "Undo" này được cho là ngăn tin nhắn "Sent!" xuất hiện. Nó thực hiện điều này bằng cách gọi [`clearTimeout`](https://developer.mozilla.org/en-US/docs/Web/API/clearTimeout) với timeout ID được lưu trong `handleSend`. Tuy nhiên, ngay cả sau khi "Undo" được nhấp, tin nhắn "Sent!" vẫn xuất hiện. Tìm hiểu tại sao nó không hoạt động, và sửa nó.

<Hint>

Các biến thông thường như `let timeoutID` không "tồn tại" giữa các lần re-render vì mỗi lần render chạy component của bạn (và khởi tạo các biến của nó) từ đầu. Bạn có nên lưu timeout ID ở nơi khác không?

</Hint>

<Sandpack>

```js {expectedErrors: {'react-compiler': [10]}}
import { useState } from 'react';

export default function Chat() {
  const [text, setText] = useState('');
  const [isSending, setIsSending] = useState(false);
  let timeoutID = null;

  function handleSend() {
    setIsSending(true);
    timeoutID = setTimeout(() => {
      alert('Sent!');
      setIsSending(false);
    }, 3000);
  }

  function handleUndo() {
    setIsSending(false);
    clearTimeout(timeoutID);
  }

  return (
    <>
      <input
        disabled={isSending}
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button
        disabled={isSending}
        onClick={handleSend}>
        {isSending ? 'Sending...' : 'Send'}
      </button>
      {isSending &&
        <button onClick={handleUndo}>
          Undo
        </button>
      }
    </>
  );
}
```

</Sandpack>

<Solution>

Bất cứ khi nào component của bạn re-renders (chẳng hạn như khi bạn thiết lập state), tất cả các biến cục bộ được khởi tạo từ đầu. Đây là lý do tại sao bạn không thể lưu timeout ID trong một biến cục bộ như `timeoutID` và sau đó kỳ vọng event handler khác sẽ "thấy" nó trong tương lai. Thay vào đó, hãy lưu nó trong một ref mà React sẽ giữ lại giữa các lần render.

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function Chat() {
  const [text, setText] = useState('');
  const [isSending, setIsSending] = useState(false);
  const timeoutRef = useRef(null);

  function handleSend() {
    setIsSending(true);
    timeoutRef.current = setTimeout(() => {
      alert('Sent!');
      setIsSending(false);
    }, 3000);
  }

  function handleUndo() {
    setIsSending(false);
    clearTimeout(timeoutRef.current);
  }

  return (
    <>
      <input
        disabled={isSending}
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button
        disabled={isSending}
        onClick={handleSend}>
        {isSending ? 'Sending...' : 'Send'}
      </button>
      {isSending &&
        <button onClick={handleUndo}>
          Undo
        </button>
      }
    </>
  );
}
```

</Sandpack>

</Solution>


#### Sửa component không re-render được {/*fix-a-component-failing-to-re-render*/}

Nút này được cho là chuyển đổi giữa hiển thị "On" và "Off". Tuy nhiên, nó luôn hiển thị "Off". Điều gì sai với code này? Sửa nó.

<Sandpack>

```js {expectedErrors: {'react-compiler': [10]}}
import { useRef } from 'react';

export default function Toggle() {
  const isOnRef = useRef(false);

  return (
    <button onClick={() => {
      isOnRef.current = !isOnRef.current;
    }}>
      {isOnRef.current ? 'On' : 'Off'}
    </button>
  );
}
```

</Sandpack>

<Solution>

Trong ví dụ này, giá trị hiện tại của ref được dùng để tính toán output render: `{isOnRef.current ? 'On' : 'Off'}`. Đây là dấu hiệu cho thấy thông tin này không nên ở trong ref, và thay vào đó nên được đưa vào state. Để sửa nó, hãy xóa ref và dùng state thay thế:

<Sandpack>

```js
import { useState } from 'react';

export default function Toggle() {
  const [isOn, setIsOn] = useState(false);

  return (
    <button onClick={() => {
      setIsOn(!isOn);
    }}>
      {isOn ? 'On' : 'Off'}
    </button>
  );
}
```

</Sandpack>

</Solution>

#### Sửa debouncing {/*fix-debouncing*/}

Trong ví dụ này, tất cả các click handler của nút đều được ["debounced".](https://kettanaito.com/blog/debounce-vs-throttle) Để thấy điều đó có nghĩa là gì, hãy nhấn một trong các nút. Lưu ý cách tin nhắn xuất hiện sau một giây. Nếu bạn nhấn nút trong khi chờ đợi tin nhắn, bộ đếm sẽ reset. Vì vậy nếu bạn tiếp tục nhấp vào cùng một nút nhanh nhiều lần, tin nhắn sẽ không xuất hiện cho đến một giây *sau* khi bạn ngừng nhấp. Debouncing cho phép bạn trì hoãn một hành động nào đó cho đến khi người dùng "ngừng làm mọi thứ".

Ví dụ này hoạt động, nhưng không hoàn toàn như dự định. Các nút không độc lập. Để thấy vấn đề, nhấp vào một trong các nút, và sau đó ngay lập tức nhấp vào nút khác. Bạn sẽ kỳ vọng rằng sau một độ trễ, bạn sẽ thấy tin nhắn của cả hai nút. Nhưng chỉ tin nhắn của nút cuối cùng xuất hiện. Tin nhắn của nút đầu tiên bị mất.

Tại sao các nút lại can thiệp vào nhau? Tìm và sửa vấn đề.

<Hint>

Biến timeout ID cuối cùng được chia sẻ giữa tất cả các component `DebouncedButton`. Đây là lý do tại sao nhấp vào một nút đặt lại timeout của nút khác. Bạn có thể lưu một timeout ID riêng biệt cho mỗi nút không?

</Hint>

<Sandpack>

```js
let timeoutID;

function DebouncedButton({ onClick, children }) {
  return (
    <button onClick={() => {
      clearTimeout(timeoutID);
      timeoutID = setTimeout(() => {
        onClick();
      }, 1000);
    }}>
      {children}
    </button>
  );
}

export default function Dashboard() {
  return (
    <>
      <DebouncedButton
        onClick={() => alert('Spaceship launched!')}
      >
        Launch the spaceship
      </DebouncedButton>
      <DebouncedButton
        onClick={() => alert('Soup boiled!')}
      >
        Boil the soup
      </DebouncedButton>
      <DebouncedButton
        onClick={() => alert('Lullaby sung!')}
      >
        Sing a lullaby
      </DebouncedButton>
    </>
  )
}
```

```css
button { display: block; margin: 10px; }
```

</Sandpack>

<Solution>

Một biến như `timeoutID` được chia sẻ giữa tất cả các component. Đây là lý do tại sao nhấp vào nút thứ hai đặt lại timeout đang chờ xử lý của nút đầu tiên. Để sửa điều này, bạn có thể giữ timeout trong một ref. Mỗi nút sẽ có ref riêng của nó, vì vậy chúng sẽ không xung đột với nhau. Lưu ý cách nhấp vào hai nút nhanh chóng sẽ hiển thị cả hai tin nhắn.

<Sandpack>

```js
import { useRef } from 'react';

function DebouncedButton({ onClick, children }) {
  const timeoutRef = useRef(null);
  return (
    <button onClick={() => {
      clearTimeout(timeoutRef.current);
      timeoutRef.current = setTimeout(() => {
        onClick();
      }, 1000);
    }}>
      {children}
    </button>
  );
}

export default function Dashboard() {
  return (
    <>
      <DebouncedButton
        onClick={() => alert('Spaceship launched!')}
      >
        Launch the spaceship
      </DebouncedButton>
      <DebouncedButton
        onClick={() => alert('Soup boiled!')}
      >
        Boil the soup
      </DebouncedButton>
      <DebouncedButton
        onClick={() => alert('Lullaby sung!')}
      >
        Sing a lullaby
      </DebouncedButton>
    </>
  )
}
```

```css
button { display: block; margin: 10px; }
```

</Sandpack>

</Solution>

#### Đọc state mới nhất {/*read-the-latest-state*/}

Trong ví dụ này, sau khi bạn nhấn "Send", có một độ trễ nhỏ trước khi tin nhắn được hiển thị. Gõ "hello", nhấn Send, và sau đó nhanh chóng chỉnh sửa input lại. Dù bạn có chỉnh sửa, alert vẫn sẽ hiển thị "hello" (đây là giá trị của state [tại thời điểm](/learn/state-as-a-snapshot#state-over-time) nút được nhấp).

Thông thường, hành vi này là điều bạn muốn trong ứng dụng. Tuy nhiên, có thể có những trường hợp hiếm hoi khi bạn muốn một số code bất đồng bộ đọc phiên bản *mới nhất* của một số state. Bạn có thể nghĩ ra cách để alert hiển thị *text input hiện tại* thay vì những gì nó ở thời điểm nhấp không?

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function Chat() {
  const [text, setText] = useState('');

  function handleSend() {
    setTimeout(() => {
      alert('Sending: ' + text);
    }, 3000);
  }

  return (
    <>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button
        onClick={handleSend}>
        Send
      </button>
    </>
  );
}
```

</Sandpack>

<Solution>

State hoạt động [như snapshot](/learn/state-as-a-snapshot), vì vậy bạn không thể đọc state mới nhất từ một thao tác bất đồng bộ như timeout. Tuy nhiên, bạn có thể giữ text input mới nhất trong một ref. Ref có thể thay đổi được, vì vậy bạn có thể đọc thuộc tính `current` bất cứ lúc nào. Vì text hiện tại cũng được dùng để render, trong ví dụ này, bạn sẽ cần *cả* biến state (để render), *và* ref (để đọc nó trong timeout). Bạn sẽ cần cập nhật giá trị ref hiện tại theo cách thủ công.

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function Chat() {
  const [text, setText] = useState('');
  const textRef = useRef(text);

  function handleChange(e) {
    setText(e.target.value);
    textRef.current = e.target.value;
  }

  function handleSend() {
    setTimeout(() => {
      alert('Sending: ' + textRef.current);
    }, 3000);
  }

  return (
    <>
      <input
        value={text}
        onChange={handleChange}
      />
      <button
        onClick={handleSend}>
        Send
      </button>
    </>
  );
}
```

</Sandpack>

</Solution>

</Challenges>
