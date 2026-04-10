---
title: compilationMode
---

<Intro>

Tùy chọn `compilationMode` kiểm soát cách React Compiler chọn những hàm nào để compile.

</Intro>

```js
{
  compilationMode: 'infer' // hoặc 'annotation', 'syntax', 'all'
}
```

<InlineToc />

---

## Tham khảo {/*reference*/}

### `compilationMode` {/*compilationmode*/}

Kiểm soát chiến lược xác định những hàm nào React Compiler sẽ tối ưu hóa.

#### Kiểu {/*type*/}

```
'infer' | 'syntax' | 'annotation' | 'all'
```

#### Giá trị mặc định {/*default-value*/}

`'infer'`

#### Các tùy chọn {/*options*/}

- **`'infer'`** (mặc định): Compiler sử dụng phương pháp suy luận thông minh để nhận diện các React component và hook:
  - Các hàm được chú thích rõ ràng bằng directive `"use memo"`
  - Các hàm được đặt tên giống component (PascalCase) hoặc hook (tiền tố `use`) VÀ tạo JSX và/hoặc gọi các hook khác

- **`'annotation'`**: Chỉ compile các hàm được đánh dấu rõ ràng bằng directive `"use memo"`. Lý tưởng cho việc áp dụng dần dần.

- **`'syntax'`**: Chỉ compile các component và hook sử dụng cú pháp [component](https://flow.org/en/docs/react/component-syntax/) và [hook](https://flow.org/en/docs/react/hook-syntax/) của Flow.

- **`'all'`**: Compile tất cả các hàm cấp cao nhất. Không được khuyến nghị vì có thể compile cả các hàm không phải React.

#### Lưu ý {/*caveats*/}

- Chế độ `'infer'` yêu cầu các hàm phải tuân theo quy ước đặt tên của React để được phát hiện
- Sử dụng chế độ `'all'` có thể ảnh hưởng tiêu cực đến hiệu suất bằng cách compile các hàm tiện ích
- Chế độ `'syntax'` yêu cầu Flow và sẽ không hoạt động với TypeScript
- Bất kể chế độ nào, các hàm có directive `"use no memo"` luôn được bỏ qua

---

## Cách sử dụng {/*usage*/}

### Chế độ suy luận mặc định {/*default-inference-mode*/}

Chế độ `'infer'` mặc định hoạt động tốt cho hầu hết các codebase tuân theo quy ước React:

```js
{
  compilationMode: 'infer'
}
```

Với chế độ này, các hàm sau sẽ được compile:

```js
// ✅ Được compile: Đặt tên giống component + trả về JSX
function Button(props) {
  return <button>{props.label}</button>;
}

// ✅ Được compile: Đặt tên giống hook + gọi hook
function useCounter() {
  const [count, setCount] = useState(0);
  return [count, setCount];
}

// ✅ Được compile: Directive rõ ràng
function expensiveCalculation(data) {
  "use memo";
  return data.reduce(/* ... */);
}

// ❌ Không được compile: Không theo mẫu component/hook
function calculateTotal(items) {
  return items.reduce((a, b) => a + b, 0);
}
```

### Áp dụng dần dần với chế độ annotation {/*incremental-adoption*/}

Để di chuyển từng bước, sử dụng chế độ `'annotation'` để chỉ compile các hàm được đánh dấu:

```js
{
  compilationMode: 'annotation'
}
```

Sau đó đánh dấu rõ ràng các hàm cần compile:

```js
// Chỉ hàm này sẽ được compile
function ExpensiveList(props) {
  "use memo";
  return (
    <ul>
      {props.items.map(item => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
}

// Hàm này sẽ không được compile nếu không có directive
function NormalComponent(props) {
  return <div>{props.content}</div>;
}
```

### Sử dụng chế độ cú pháp Flow {/*flow-syntax-mode*/}

Nếu codebase của bạn sử dụng Flow thay vì TypeScript:

```js
{
  compilationMode: 'syntax'
}
```

Sau đó sử dụng cú pháp component của Flow:

```js
// Được compile: Cú pháp component của Flow
component Button(label: string) {
  return <button>{label}</button>;
}

// Được compile: Cú pháp hook của Flow
hook useCounter(initial: number) {
  const [count, setCount] = useState(initial);
  return [count, setCount];
}

// Không được compile: Cú pháp hàm thông thường
function helper(data) {
  return process(data);
}
```

### Loại trừ các hàm cụ thể {/*opting-out*/}

Bất kể chế độ compile nào, sử dụng `"use no memo"` để bỏ qua việc compile:

```js
function ComponentWithSideEffects() {
  "use no memo"; // Ngăn chặn việc compile

  // Component này có side effect không nên được memo hóa
  logToAnalytics('component_rendered');

  return <div>Content</div>;
}
```

---

## Xử lý sự cố {/*troubleshooting*/}

### Component không được compile trong chế độ infer {/*component-not-compiled-infer*/}

Trong chế độ `'infer'`, đảm bảo component của bạn tuân theo quy ước React:

```js
// ❌ Sẽ không được compile: tên viết thường
function button(props) {
  return <button>{props.label}</button>;
}

// ✅ Sẽ được compile: tên PascalCase
function Button(props) {
  return <button>{props.label}</button>;
}

// ❌ Sẽ không được compile: không tạo JSX hoặc gọi hook
function useData() {
  return window.localStorage.getItem('data');
}

// ✅ Sẽ được compile: gọi một hook
function useData() {
  const [data] = useState(() => window.localStorage.getItem('data'));
  return data;
}
```
