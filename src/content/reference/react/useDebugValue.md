---
title: useDebugValue
---

<Intro>

`useDebugValue` là một React Hook cho phép bạn thêm nhãn vào một custom Hook trong [React DevTools.](/learn/react-developer-tools)

```js
useDebugValue(value, format?)
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `useDebugValue(value, format?)` {/*usedebugvalue*/}

Gọi `useDebugValue` ở cấp cao nhất của [custom Hook](/learn/reusing-logic-with-custom-hooks) để hiển thị giá trị debug có thể đọc được:

```js
import { useDebugValue } from 'react';

function useOnlineStatus() {
  // ...
  useDebugValue(isOnline ? 'Online' : 'Offline');
  // ...
}
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `value`: Giá trị bạn muốn hiển thị trong React DevTools. Nó có thể có bất kỳ kiểu nào.
* **tùy chọn** `format`: Hàm định dạng. Khi component được kiểm tra, React DevTools sẽ gọi hàm định dạng với `value` làm đối số, rồi hiển thị giá trị đã được định dạng (có thể có bất kỳ kiểu nào). Nếu bạn không chỉ định hàm định dạng, `value` gốc sẽ được hiển thị.

#### Giá trị trả về {/*returns*/}

`useDebugValue` không trả về gì cả.

## Cách sử dụng {/*usage*/}

### Thêm nhãn vào custom Hook {/*adding-a-label-to-a-custom-hook*/}

Gọi `useDebugValue` ở cấp cao nhất của [custom Hook](/learn/reusing-logic-with-custom-hooks) để hiển thị <CodeStep step={1}>giá trị debug</CodeStep> có thể đọc được trong [React DevTools.](/learn/react-developer-tools)

```js [[1, 5, "isOnline ? 'Online' : 'Offline'"]]
import { useDebugValue } from 'react';

function useOnlineStatus() {
  // ...
  useDebugValue(isOnline ? 'Online' : 'Offline');
  // ...
}
```

Điều này giúp các component gọi `useOnlineStatus` có nhãn như `OnlineStatus: "Online"` khi bạn kiểm tra chúng:

![Ảnh chụp màn hình React DevTools hiển thị giá trị debug](/images/docs/react-devtools-usedebugvalue.png)

Nếu không có lời gọi `useDebugValue`, chỉ dữ liệu cơ bản (trong ví dụ này là `true`) mới được hiển thị.

<Sandpack>

```js
import { useOnlineStatus } from './useOnlineStatus.js';

function StatusBar() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}

export default function App() {
  return <StatusBar />;
}
```

```js src/useOnlineStatus.js active
import { useSyncExternalStore, useDebugValue } from 'react';

export function useOnlineStatus() {
  const isOnline = useSyncExternalStore(subscribe, () => navigator.onLine, () => true);
  useDebugValue(isOnline ? 'Online' : 'Offline');
  return isOnline;
}

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}
```

</Sandpack>

<Note>

Đừng thêm giá trị debug vào mọi custom Hook. Nó có giá trị nhất cho các custom Hook là một phần của thư viện chia sẻ và có cấu trúc dữ liệu nội bộ phức tạp khó kiểm tra.

</Note>

---

### Trì hoãn định dạng giá trị debug {/*deferring-formatting-of-a-debug-value*/}

Bạn cũng có thể truyền hàm định dạng làm đối số thứ hai cho `useDebugValue`:

```js [[1, 1, "date", 18], [2, 1, "date.toDateString()"]]
useDebugValue(date, date => date.toDateString());
```

Hàm định dạng của bạn sẽ nhận <CodeStep step={1}>giá trị debug</CodeStep> làm tham số và nên trả về <CodeStep step={2}>giá trị hiển thị đã được định dạng</CodeStep>. Khi component của bạn được kiểm tra, React DevTools sẽ gọi hàm này và hiển thị kết quả.

Điều này cho phép bạn tránh chạy logic định dạng tốn kém trừ khi component thực sự được kiểm tra. Ví dụ, nếu `date` là giá trị Date, việc này tránh gọi `toDateString()` trên nó cho mỗi lần render.
