---
title: flushSync
---

<Pitfall>

Sử dụng `flushSync` là không phổ biến và có thể ảnh hưởng đến hiệu suất của ứng dụng.

</Pitfall>

<Intro>

`flushSync` cho phép bạn buộc React flush bất kỳ cập nhật nào bên trong callback được cung cấp một cách đồng bộ. Điều này đảm bảo rằng DOM được cập nhật ngay lập tức.

```js
flushSync(callback)
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `flushSync(callback)` {/*flushsync*/}

Gọi `flushSync` để buộc React flush bất kỳ công việc đang chờ nào và cập nhật DOM một cách đồng bộ.

```js
import { flushSync } from 'react-dom';

flushSync(() => {
  setSomething(123);
});
```

Hầu hết thời gian, `flushSync` có thể được tránh. Sử dụng `flushSync` như giải pháp cuối cùng.

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}


* `callback`: Một hàm. React sẽ gọi callback này ngay lập tức và flush bất kỳ cập nhật nào nó chứa một cách đồng bộ. Nó cũng có thể flush bất kỳ cập nhật đang chờ nào, hoặc Effect, hoặc các cập nhật bên trong Effect. Nếu một cập nhật bị suspend do lệnh gọi `flushSync` này, các fallback có thể được hiển thị lại.

#### Giá trị trả về {/*returns*/}

`flushSync` trả về `undefined`.

#### Lưu ý {/*caveats*/}

* `flushSync` có thể ảnh hưởng đáng kể đến hiệu suất. Sử dụng một cách tiết kiệm.
* `flushSync` có thể buộc các Suspense boundary đang chờ hiển thị state `fallback` của chúng.
* `flushSync` có thể chạy các Effect đang chờ và đồng bộ áp dụng bất kỳ cập nhật nào chúng chứa trước khi trả về.
* `flushSync` có thể flush các cập nhật bên ngoài callback khi cần thiết để flush các cập nhật bên trong callback. Ví dụ, nếu có các cập nhật đang chờ từ một lần click, React có thể flush chúng trước khi flush các cập nhật bên trong callback.

---

## Cách sử dụng {/*usage*/}

### Flush các cập nhật cho tích hợp bên thứ ba {/*flushing-updates-for-third-party-integrations*/}

Khi tích hợp với code bên thứ ba như API trình duyệt hoặc thư viện UI, có thể cần buộc React flush các cập nhật. Sử dụng `flushSync` để buộc React flush bất kỳ <CodeStep step={1}>cập nhật state</CodeStep> nào bên trong callback một cách đồng bộ:

```js [[1, 2, "setSomething(123)"]]
flushSync(() => {
  setSomething(123);
});
// By this line, the DOM is updated.
```

Điều này đảm bảo rằng, khi dòng code tiếp theo chạy, React đã cập nhật DOM.

**Sử dụng `flushSync` là không phổ biến, và sử dụng nó thường xuyên có thể ảnh hưởng đáng kể đến hiệu suất ứng dụng.** Nếu ứng dụng của bạn chỉ sử dụng các API React và không tích hợp với thư viện bên thứ ba, `flushSync` không cần thiết.

Tuy nhiên, nó có thể hữu ích cho việc tích hợp với code bên thứ ba như API trình duyệt.

Một số API trình duyệt mong đợi kết quả bên trong callback được ghi vào DOM một cách đồng bộ, trước khi callback kết thúc, để trình duyệt có thể làm điều gì đó với DOM đã render. Trong hầu hết các trường hợp, React xử lý điều này cho bạn tự động. Nhưng trong một số trường hợp, có thể cần buộc cập nhật đồng bộ.

Ví dụ, API `onbeforeprint` của trình duyệt cho phép bạn thay đổi trang ngay trước khi hộp thoại in mở ra. Điều này hữu ích để áp dụng các style in tùy chỉnh cho phép tài liệu hiển thị tốt hơn khi in. Trong ví dụ dưới đây, bạn sử dụng `flushSync` bên trong callback `onbeforeprint` để ngay lập tức "flush" state React vào DOM. Sau đó, khi hộp thoại in mở ra, `isPrinting` hiển thị "yes":

<Sandpack>

```js src/App.js active
import { useState, useEffect } from 'react';
import { flushSync } from 'react-dom';

export default function PrintApp() {
  const [isPrinting, setIsPrinting] = useState(false);

  useEffect(() => {
    function handleBeforePrint() {
      flushSync(() => {
        setIsPrinting(true);
      })
    }

    function handleAfterPrint() {
      setIsPrinting(false);
    }

    window.addEventListener('beforeprint', handleBeforePrint);
    window.addEventListener('afterprint', handleAfterPrint);
    return () => {
      window.removeEventListener('beforeprint', handleBeforePrint);
      window.removeEventListener('afterprint', handleAfterPrint);
    }
  }, []);

  return (
    <>
      <h1>isPrinting: {isPrinting ? 'yes' : 'no'}</h1>
      <button onClick={() => window.print()}>
        Print
      </button>
    </>
  );
}
```

</Sandpack>

Nếu không có `flushSync`, hộp thoại in sẽ hiển thị `isPrinting` là "no". Điều này là do React gom các cập nhật một cách bất đồng bộ và hộp thoại in được hiển thị trước khi state được cập nhật.

<Pitfall>

`flushSync` có thể ảnh hưởng đáng kể đến hiệu suất, và có thể bất ngờ buộc các Suspense boundary đang chờ hiển thị state fallback của chúng.

Hầu hết thời gian, `flushSync` có thể được tránh, vì vậy hãy sử dụng `flushSync` như giải pháp cuối cùng.

</Pitfall>

---

## Xử lý sự cố {/*troubleshooting*/}

### Tôi gặp lỗi: "flushSync was called from inside a lifecycle method" {/*im-getting-an-error-flushsync-was-called-from-inside-a-lifecycle-method*/}


React không thể `flushSync` trong khi đang render. Nếu bạn làm vậy, nó sẽ không có tác dụng và cảnh báo:

<ConsoleBlock level="error">

Warning: flushSync was called from inside a lifecycle method. React cannot flush when React is already rendering. Consider moving this call to a scheduler task or micro task.

</ConsoleBlock>

Điều này bao gồm việc gọi `flushSync` bên trong:

- render một component.
- Hook `useLayoutEffect` hoặc `useEffect`.
- Các phương thức lifecycle của class component.

Ví dụ, gọi `flushSync` trong một Effect sẽ không có tác dụng và cảnh báo:

```js
import { useEffect } from 'react';
import { flushSync } from 'react-dom';

function MyComponent() {
  useEffect(() => {
    // 🚩 Wrong: calling flushSync inside an effect
    flushSync(() => {
      setSomething(newValue);
    });
  }, []);

  return <div>{/* ... */}</div>;
}
```

Để sửa lỗi này, bạn thường muốn di chuyển lệnh gọi `flushSync` đến một event:

```js
function handleClick() {
  // ✅ Correct: flushSync in event handlers is safe
  flushSync(() => {
    setSomething(newValue);
  });
}
```


Nếu khó di chuyển sang event, bạn có thể trì hoãn `flushSync` trong một microtask:

```js {3,7}
useEffect(() => {
  // ✅ Correct: defer flushSync to a microtask
  queueMicrotask(() => {
    flushSync(() => {
      setSomething(newValue);
    });
  });
}, []);
```

Điều này sẽ cho phép render hiện tại hoàn thành và lên lịch một render đồng bộ khác để flush các cập nhật.

<Pitfall>

`flushSync` có thể ảnh hưởng đáng kể đến hiệu suất, nhưng pattern cụ thể này còn tệ hơn cho hiệu suất. Hãy sử dụng hết tất cả các tùy chọn khác trước khi gọi `flushSync` trong một microtask như một giải pháp thoát hiểm.

</Pitfall>
