---
title: refs
---

<Intro>

Kiểm tra việc sử dụng ref đúng cách, không đọc/ghi trong quá trình render. Xem phần "lưu ý" trong [cách sử dụng `useRef()`](/reference/react/useRef#usage).

</Intro>

## Chi tiết quy tắc {/*rule-details*/}

Ref giữ các giá trị không được sử dụng cho render. Không giống state, thay đổi ref không kích hoạt re-render. Đọc hoặc ghi `ref.current` trong quá trình render phá vỡ kỳ vọng của React. Ref có thể chưa được khởi tạo khi bạn cố đọc chúng, và giá trị của chúng có thể cũ hoặc không nhất quán.

## Cách phát hiện ref {/*how-it-detects-refs*/}

Lint chỉ áp dụng các quy tắc này cho các giá trị mà nó biết là ref. Một giá trị được suy luận là ref khi compiler thấy bất kỳ pattern nào sau đây:

- Được trả về từ `useRef()` hoặc `React.createRef()`.

  ```js
  const scrollRef = useRef(null);
  ```

- Một định danh có tên `ref` hoặc kết thúc bằng `Ref` mà đọc hoặc ghi vào `.current`.

  ```js
  buttonRef.current = node;
  ```

- Được truyền qua prop JSX `ref` (ví dụ `<div ref={someRef} />`).

  ```jsx
  <input ref={inputRef} />
  ```

Khi một thứ được đánh dấu là ref, suy luận đó theo giá trị qua các phép gán, destructuring, hoặc các lệnh gọi hàm trợ giúp. Điều này cho phép lint phát hiện vi phạm ngay cả khi `ref.current` được truy cập bên trong một hàm khác nhận ref làm tham số.

## Các vi phạm phổ biến {/*common-violations*/}

- Đọc `ref.current` trong quá trình render
- Cập nhật `refs` trong quá trình render
- Sử dụng `refs` cho các giá trị nên là state

### Không hợp lệ {/*invalid*/}

Ví dụ về code không đúng cho quy tắc này:

```js
// ❌ Đọc ref trong quá trình render
function Component() {
  const ref = useRef(0);
  const value = ref.current; // Không đọc trong quá trình render
  return <div>{value}</div>;
}

// ❌ Thay đổi ref trong quá trình render
function Component({value}) {
  const ref = useRef(null);
  ref.current = value; // Không thay đổi trong quá trình render
  return <div />;
}
```

### Hợp lệ {/*valid*/}

Ví dụ về code đúng cho quy tắc này:

```js
// ✅ Đọc ref trong effect/handler
function Component() {
  const ref = useRef(null);

  useEffect(() => {
    if (ref.current) {
      console.log(ref.current.offsetWidth); // OK trong effect
    }
  });

  return <div ref={ref} />;
}

// ✅ Dùng state cho giá trị UI
function Component() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}

// ✅ Khởi tạo lazy giá trị ref
function Component() {
  const ref = useRef(null);

  // Chỉ khởi tạo một lần khi sử dụng lần đầu
  if (ref.current === null) {
    ref.current = expensiveComputation(); // OK - khởi tạo lazy
  }

  const handleClick = () => {
    console.log(ref.current); // Sử dụng giá trị đã khởi tạo
  };

  return <button onClick={handleClick}>Nhấn</button>;
}
```

## Xử lý sự cố {/*troubleshooting*/}

### Lint đánh dấu object thường của tôi có `.current` {/*plain-object-current*/}

Heuristic về tên cố ý coi `ref.current` và `fooRef.current` là ref thật. Nếu bạn đang mô hình hóa một object container tùy chỉnh, hãy chọn tên khác (ví dụ, `box`) hoặc chuyển giá trị có thể thay đổi vào state. Đổi tên tránh được lint vì compiler ngừng suy luận nó là ref.
