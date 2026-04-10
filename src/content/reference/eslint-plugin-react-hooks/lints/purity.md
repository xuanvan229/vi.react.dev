---
title: purity
---

<Intro>

Kiểm tra rằng [component/hook là thuần túy](/reference/rules/components-and-hooks-must-be-pure) bằng cách kiểm tra rằng chúng không gọi các hàm không thuần túy đã biết.

</Intro>

## Chi tiết quy tắc {/*rule-details*/}

Component React phải là các hàm thuần túy - với cùng props, chúng nên luôn trả về cùng JSX. Khi component sử dụng các hàm như `Math.random()` hoặc `Date.now()` trong quá trình render, chúng tạo ra đầu ra khác nhau mỗi lần, phá vỡ các giả định của React và gây ra lỗi như hydration mismatch, memoization không chính xác, và hành vi không thể dự đoán.

## Các vi phạm phổ biến {/*common-violations*/}

Nói chung, bất kỳ API nào trả về giá trị khác nhau cho cùng đầu vào đều vi phạm quy tắc này. Các ví dụ thông thường bao gồm:

- `Math.random()`
- `Date.now()` / `new Date()`
- `crypto.randomUUID()`
- `performance.now()`

### Không hợp lệ {/*invalid*/}

Ví dụ về code không đúng cho quy tắc này:

```js
// ❌ Math.random() trong render
function Component() {
  const id = Math.random(); // Khác nhau mỗi lần render
  return <div key={id}>Nội dung</div>;
}

// ❌ Date.now() cho giá trị
function Component() {
  const timestamp = Date.now(); // Thay đổi mỗi lần render
  return <div>Tạo lúc: {timestamp}</div>;
}
```

### Hợp lệ {/*valid*/}

Ví dụ về code đúng cho quy tắc này:

```js
// ✅ ID ổn định từ state ban đầu
function Component() {
  const [id] = useState(() => crypto.randomUUID());
  return <div key={id}>Nội dung</div>;
}
```

## Xử lý sự cố {/*troubleshooting*/}

### Tôi cần hiển thị thời gian hiện tại {/*current-time*/}

Gọi `Date.now()` trong quá trình render làm component của bạn không thuần túy:

```js {expectedErrors: {'react-compiler': [3]}}
// ❌ Sai: Thời gian thay đổi mỗi lần render
function Clock() {
  return <div>Thời gian hiện tại: {Date.now()}</div>;
}
```

Thay vào đó, [chuyển hàm không thuần túy ra ngoài render](/reference/rules/components-and-hooks-must-be-pure#components-and-hooks-must-be-idempotent):

```js
function Clock() {
  const [time, setTime] = useState(() => Date.now());

  useEffect(() => {
    const interval = setInterval(() => {
      setTime(Date.now());
    }, 1000);

    return () => clearInterval(interval);
  }, []);

  return <div>Thời gian hiện tại: {time}</div>;
}
```
