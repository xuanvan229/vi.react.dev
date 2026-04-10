---
title: useEffectEvent
---

<Intro>

`useEffectEvent` là một React Hook cho phép bạn tách các sự kiện ra khỏi các Effect.

```js
const onEvent = useEffectEvent(callback)
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `useEffectEvent(callback)` {/*useeffectevent*/}

Gọi `useEffectEvent` ở cấp cao nhất của component để tạo một Effect Event.

```js {4,6}
import { useEffectEvent, useEffect } from 'react';

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(() => {
    showNotification('Connected!', theme);
  });
}
```

Effect Event là một phần logic của Effect, nhưng chúng hoạt động giống như một event handler hơn. Chúng luôn "nhìn thấy" các giá trị mới nhất từ lần render (như props và state) mà không cần đồng bộ hóa lại Effect của bạn, vì vậy chúng được loại trừ khỏi các dependency của Effect. Xem [Tách sự kiện khỏi các Effect](/learn/separating-events-from-effects#extracting-non-reactive-logic-out-of-effects) để tìm hiểu thêm.

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `callback`: Một hàm chứa logic cho Effect Event của bạn. Hàm có thể nhận bất kỳ số lượng đối số nào và trả về bất kỳ giá trị nào. Khi bạn gọi hàm Effect Event được trả về, `callback` luôn truy cập các giá trị đã được commit mới nhất từ lần render tại thời điểm gọi.

#### Giá trị trả về {/*returns*/}

`useEffectEvent` trả về một hàm Effect Event có cùng chữ ký kiểu với `callback` của bạn.

Bạn có thể gọi hàm này bên trong `useEffect`, `useLayoutEffect`, `useInsertionEffect`, hoặc từ bên trong các Effect Event khác trong cùng component.

#### Lưu ý {/*caveats*/}

* `useEffectEvent` là một Hook, vì vậy bạn chỉ có thể gọi nó **ở cấp cao nhất của component** hoặc các Hook của riêng bạn. Bạn không thể gọi nó bên trong các vòng lặp hoặc điều kiện. Nếu bạn cần làm điều đó, hãy trích xuất một component mới và chuyển Effect Event vào đó.
* Effect Event chỉ có thể được gọi từ bên trong các Effect hoặc các Effect Event khác. Đừng gọi chúng trong quá trình render hoặc truyền chúng cho các component hoặc Hook khác. Bộ kiểm tra lint [`eslint-plugin-react-hooks`](/reference/eslint-plugin-react-hooks) thực thi hạn chế này.
* Đừng sử dụng `useEffectEvent` để tránh chỉ định các dependency trong mảng dependency của Effect. Điều này che giấu các lỗi và làm cho code của bạn khó hiểu hơn. Chỉ sử dụng nó cho logic thực sự là một sự kiện được kích hoạt từ Effect.
* Các hàm Effect Event không có danh tính ổn định. Danh tính của chúng cố ý thay đổi trên mỗi lần render.

<DeepDive>

#### Tại sao Effect Event không ổn định? {/*why-are-effect-events-not-stable*/}

Không giống như các hàm `set` từ `useState` hoặc refs, các hàm Effect Event không có danh tính ổn định. Danh tính của chúng cố ý thay đổi trên mỗi lần render:

```js
// 🔴 Sai: đưa Effect Event vào dependency
useEffect(() => {
  onSomething();
}, [onSomething]); // ESLint sẽ cảnh báo về điều này
```

Đây là lựa chọn thiết kế có chủ ý. Effect Event chỉ được gọi từ bên trong các Effect trong cùng component. Vì bạn chỉ có thể gọi chúng cục bộ và không thể truyền chúng cho các component khác hoặc đưa vào mảng dependency, một danh tính ổn định sẽ không có mục đích gì, và thực ra sẽ che giấu các lỗi.

Danh tính không ổn định hoạt động như một xác nhận runtime: nếu code của bạn phụ thuộc không chính xác vào danh tính hàm, bạn sẽ thấy Effect chạy lại trên mỗi lần render, làm lỗi trở nên rõ ràng.

Thiết kế này củng cố rằng Effect Event về mặt khái niệm thuộc về một effect cụ thể, và không phải là API đa mục đích để thoát khỏi tính reactive.

</DeepDive>

---

## Cách sử dụng {/*usage*/}


### Sử dụng một sự kiện trong Effect {/*using-an-event-in-an-effect*/}

Gọi `useEffectEvent` ở cấp cao nhất của component để tạo một *Effect Event*:


```js [[1, 1, "onConnected"]]
const onConnected = useEffectEvent(() => {
  if (!muted) {
    showNotification('Connected!');
  }
});
```

`useEffectEvent` nhận một `event callback` và trả về một <CodeStep step={1}>Effect Event</CodeStep>. Effect Event là một hàm có thể được gọi bên trong các Effect mà không kết nối lại Effect:

```js [[1, 3, "onConnected"]]
useEffect(() => {
  const connection = createConnection(roomId);
  connection.on('connected', onConnected);
  connection.connect();
  return () => {
    connection.disconnect();
  }
}, [roomId]);
```

Vì `onConnected` là một <CodeStep step={1}>Effect Event</CodeStep>, `muted` và `onConnect` không nằm trong các dependency của Effect.

<Pitfall>

##### Đừng sử dụng Effect Event để bỏ qua dependency {/*pitfall-skip-dependencies*/}

Có thể bạn sẽ bị cám dỗ sử dụng `useEffectEvent` để tránh liệt kê các dependency mà bạn cho là "không cần thiết." Tuy nhiên, điều này che giấu các lỗi và làm cho code của bạn khó hiểu hơn:

```js
// 🔴 Sai: Sử dụng Effect Event để che giấu dependency
const logVisit = useEffectEvent(() => {
  log(pageUrl);
});

useEffect(() => {
  logVisit()
}, []); // Thiếu pageUrl nghĩa là bạn bỏ lỡ các log
```

Nếu một giá trị nên làm cho Effect của bạn chạy lại, hãy giữ nó như một dependency. Chỉ sử dụng Effect Event cho logic thực sự không nên kích hoạt lại Effect của bạn.

Xem [Tách sự kiện khỏi các Effect](/learn/separating-events-from-effects) để tìm hiểu thêm.

</Pitfall>

---

### Sử dụng timer với các giá trị mới nhất {/*using-a-timer-with-latest-values*/}

Khi bạn sử dụng `setInterval` hoặc `setTimeout` trong một Effect, bạn thường muốn đọc các giá trị mới nhất từ lần render mà không khởi động lại timer mỗi khi các giá trị đó thay đổi.

Counter này tăng `count` theo giá trị `increment` hiện tại mỗi giây. Effect Event `onTick` đọc `count` và `increment` mới nhất mà không làm cho interval khởi động lại:

<Sandpack>

```js
import { useState, useEffect, useEffectEvent } from 'react';

export default function Timer() {
  const [count, setCount] = useState(0);
  const [increment, setIncrement] = useState(1);

  const onTick = useEffectEvent(() => {
    setCount(count + increment);
  });

  useEffect(() => {
    const id = setInterval(() => {
      onTick();
    }, 1000);
    return () => {
      clearInterval(id);
    };
  }, []);

  return (
    <>
      <h1>
        Counter: {count}
        <button onClick={() => setCount(0)}>Reset</button>
      </h1>
      <hr />
      <p>
        Every second, increment by:
        <button disabled={increment === 0} onClick={() => {
          setIncrement(i => i - 1);
        }}>–</button>
        <b>{increment}</b>
        <button onClick={() => {
          setIncrement(i => i + 1);
        }}>+</button>
      </p>
    </>
  );
}
```

```css
button { margin: 10px; }
```

</Sandpack>

Hãy thử thay đổi giá trị increment trong khi timer đang chạy. Counter ngay lập tức sử dụng giá trị increment mới, nhưng timer vẫn chạy trơn tru mà không khởi động lại.

---

### Sử dụng event listener với các giá trị mới nhất {/*using-an-event-listener-with-latest-values*/}

Khi bạn thiết lập một event listener trong Effect, bạn thường cần đọc các giá trị mới nhất từ lần render trong callback. Không có `useEffectEvent`, bạn sẽ cần đưa các giá trị vào dependency, khiến listener bị xóa và thêm lại mỗi khi thay đổi.

Ví dụ này hiển thị một dấu chấm theo con trỏ, nhưng chỉ khi "Can move" được chọn. Effect Event `onMove` luôn đọc giá trị `canMove` mới nhất mà không chạy lại Effect:

<Sandpack>

```js
import { useState, useEffect, useEffectEvent } from 'react';

export default function App() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  const [canMove, setCanMove] = useState(true);

  const onMove = useEffectEvent(e => {
    if (canMove) {
      setPosition({ x: e.clientX, y: e.clientY });
    }
  });

  useEffect(() => {
    window.addEventListener('pointermove', onMove);
    return () => window.removeEventListener('pointermove', onMove);
  }, []);

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={canMove}
          onChange={e => setCanMove(e.target.checked)}
        />
        The dot is allowed to move
      </label>
      <hr />
      <div style={{
        position: 'absolute',
        backgroundColor: 'pink',
        borderRadius: '50%',
        opacity: 0.6,
        transform: `translate(${position.x}px, ${position.y}px)`,
        pointerEvents: 'none',
        left: -20,
        top: -20,
        width: 40,
        height: 40,
      }} />
    </>
  );
}
```

```css
body {
  height: 200px;
}
```

</Sandpack>

Bật/tắt checkbox và di chuyển con trỏ. Dấu chấm phản hồi ngay lập tức với trạng thái checkbox, nhưng event listener chỉ được thiết lập một lần khi component được mount.

---

### Tránh kết nối lại các hệ thống bên ngoài {/*showing-a-notification-without-reconnecting*/}

Một trường hợp sử dụng phổ biến cho `useEffectEvent` là khi bạn muốn làm gì đó để phản hồi một Effect, nhưng "điều gì đó" đó phụ thuộc vào một giá trị bạn không muốn reactive.

Trong ví dụ này, một component chat kết nối đến một phòng và hiển thị thông báo khi được kết nối. Người dùng có thể tắt thông báo bằng checkbox. Tuy nhiên, bạn không muốn kết nối lại phòng chat mỗi khi người dùng thay đổi cài đặt:

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "react": "latest",
    "react-dom": "latest",
    "react-scripts": "latest",
    "toastify-js": "1.12.0"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js
import { useState, useEffect, useEffectEvent } from 'react';
import { createConnection } from './chat.js';
import { showNotification } from './notifications.js';

function ChatRoom({ roomId, muted }) {
  const onConnected = useEffectEvent((roomId) => {
    console.log('✅ Connected to ' + roomId + ' (muted: ' + muted + ')');
    if (!muted) {
      showNotification('Connected to ' + roomId);
    }
  });

  useEffect(() => {
    const connection = createConnection(roomId);
    console.log('⏳ Connecting to ' + roomId + '...');
    connection.on('connected', () => {
      onConnected(roomId);
    });
    connection.connect();
    return () => {
      console.log('❌ Disconnected from ' + roomId);
      connection.disconnect();
    }
  }, [roomId]);

  return <h1>Welcome to the {roomId} room!</h1>;
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [muted, setMuted] = useState(false);
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <label>
        <input
          type="checkbox"
          checked={muted}
          onChange={e => setMuted(e.target.checked)}
        />
        Mute notifications
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
        muted={muted}
      />
    </>
  );
}
```

```js src/chat.js
const serverUrl = 'https://localhost:1234';

export function createConnection(roomId) {
  // A real implementation would actually connect to the server
  let connectedCallback;
  let timeout;
  return {
    connect() {
      timeout = setTimeout(() => {
        if (connectedCallback) {
          connectedCallback();
        }
      }, 100);
    },
    on(event, callback) {
      if (connectedCallback) {
        throw Error('Cannot add the handler twice.');
      }
      if (event !== 'connected') {
        throw Error('Only "connected" event is supported.');
      }
      connectedCallback = callback;
    },
    disconnect() {
      clearTimeout(timeout);
    }
  };
}
```

```js src/notifications.js
import Toastify from 'toastify-js';
import 'toastify-js/src/toastify.css';

export function showNotification(message, theme) {
  Toastify({
    text: message,
    duration: 2000,
    gravity: 'top',
    position: 'right',
    style: {
      background: theme === 'dark' ? 'black' : 'white',
      color: theme === 'dark' ? 'white' : 'black',
    },
  }).showToast();
}
```

```css
label { display: block; margin-top: 10px; }
```

</Sandpack>

Hãy thử chuyển đổi phòng. Chat kết nối lại và hiển thị thông báo. Bây giờ hãy tắt thông báo. Vì `muted` được đọc bên trong Effect Event thay vì Effect, chat vẫn được kết nối.

---

### Sử dụng Effect Event trong custom Hook {/*using-effect-events-in-custom-hooks*/}

Bạn có thể sử dụng `useEffectEvent` bên trong các custom Hook của riêng bạn. Điều này cho phép bạn tạo các Hook có thể tái sử dụng đóng gói các Effect trong khi giữ một số giá trị không reactive:

<Sandpack>

```js
import { useState, useEffect, useEffectEvent } from 'react';

function useInterval(callback, delay) {
  const onTick = useEffectEvent(callback);

  useEffect(() => {
    if (delay === null) {
      return;
    }
    const id = setInterval(() => {
      onTick();
    }, delay);
    return () => clearInterval(id);
  }, [delay]);
}

function Counter({ incrementBy }) {
  const [count, setCount] = useState(0);

  useInterval(() => {
    setCount(c => c + incrementBy);
  }, 1000);

  return (
    <div>
      <h2>Count: {count}</h2>
      <p>Incrementing by {incrementBy} every second</p>
    </div>
  );
}

export default function App() {
  const [incrementBy, setIncrementBy] = useState(1);

  return (
    <>
      <label>
        Increment by:{' '}
        <select
          value={incrementBy}
          onChange={(e) => setIncrementBy(Number(e.target.value))}
        >
          <option value={1}>1</option>
          <option value={5}>5</option>
          <option value={10}>10</option>
        </select>
      </label>
      <hr />
      <Counter incrementBy={incrementBy} />
    </>
  );
}
```

```css
label { display: block; margin-bottom: 8px; }
```

</Sandpack>

Trong ví dụ này, `useInterval` là một custom Hook thiết lập một interval. `callback` được truyền cho nó được bọc trong một Effect Event, vì vậy interval không reset ngay cả khi một `callback` mới được truyền vào mỗi lần render.

---

## Xử lý sự cố {/*troubleshooting*/}

### Tôi nhận được lỗi: "A function wrapped in useEffectEvent can't be called during rendering" {/*cant-call-during-rendering*/}

Lỗi này có nghĩa là bạn đang gọi một hàm Effect Event trong quá trình render phase của component. Effect Event chỉ có thể được gọi từ bên trong các Effect hoặc các Effect Event khác.

```js
function MyComponent({ data }) {
  const onLog = useEffectEvent(() => {
    console.log(data);
  });

  // 🔴 Sai: gọi trong quá trình render
  onLog();

  // ✅ Đúng: gọi từ một Effect
  useEffect(() => {
    onLog();
  }, []);

  return <div>{data}</div>;
}
```

Nếu bạn cần chạy logic trong quá trình render, đừng bọc nó trong `useEffectEvent`. Gọi logic trực tiếp hoặc chuyển nó vào trong một Effect.

---

### Tôi nhận được lỗi lint: "Functions returned from useEffectEvent must not be included in the dependency array" {/*effect-event-in-deps*/}

Nếu bạn thấy cảnh báo như "Functions returned from `useEffectEvent` must not be included in the dependency array", hãy xóa Effect Event khỏi dependency của bạn:

```js
const onSomething = useEffectEvent(() => {
  // ...
});

// 🔴 Sai: Effect Event trong dependency
useEffect(() => {
  onSomething();
}, [onSomething]);

// ✅ Đúng: không có Effect Event trong dependency
useEffect(() => {
  onSomething();
}, []);
```

Effect Event được thiết kế để được gọi từ các Effect mà không cần liệt kê như dependency. Bộ kiểm tra lint thực thi điều này vì danh tính hàm [cố ý không ổn định](#why-are-effect-events-not-stable). Đưa nó vào sẽ làm cho Effect của bạn chạy lại trên mỗi lần render.

---

### Tôi nhận được lỗi lint: "... is a function created with useEffectEvent, and can only be called from Effects" {/*effect-event-called-outside-effect*/}

Nếu bạn thấy cảnh báo như "... is a function created with React Hook `useEffectEvent`, and can only be called from Effects and Effect Events", bạn đang gọi hàm từ sai vị trí:

```js
const onSomething = useEffectEvent(() => {
  console.log(value);
});

// 🔴 Sai: gọi từ event handler
function handleClick() {
  onSomething();
}

// 🔴 Sai: truyền cho component con
return <Child onSomething={onSomething} />;

// ✅ Đúng: gọi từ Effect
useEffect(() => {
  onSomething();
}, []);
```

Effect Event được thiết kế đặc biệt để được sử dụng trong các Effect cục bộ với component mà chúng được định nghĩa. Nếu bạn cần một callback cho event handler hoặc để truyền cho con, hãy sử dụng hàm thông thường hoặc `useCallback` thay thế.
