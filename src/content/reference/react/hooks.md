---
title: "Các Hook React Tích hợp sẵn"
---

<Intro>

*Hooks* cho phép bạn sử dụng các tính năng khác nhau của React từ component của bạn. Bạn có thể sử dụng các Hook tích hợp sẵn hoặc kết hợp chúng để xây dựng Hook của riêng mình. Trang này liệt kê tất cả các Hook tích hợp sẵn trong React.

</Intro>

---

## State Hooks {/*state-hooks*/}

*State* cho phép một component ["ghi nhớ" thông tin như dữ liệu nhập của người dùng.](/learn/state-a-components-memory) Ví dụ, một component form có thể sử dụng state để lưu trữ giá trị nhập, trong khi một component gallery hình ảnh có thể sử dụng state để lưu trữ chỉ mục hình ảnh được chọn.

Để thêm state vào một component, sử dụng một trong các Hook sau:

* [`useState`](/reference/react/useState) khai báo một biến state mà bạn có thể cập nhật trực tiếp.
* [`useReducer`](/reference/react/useReducer) khai báo một biến state với logic cập nhật bên trong một [hàm reducer.](/learn/extracting-state-logic-into-a-reducer)

```js
function ImageGallery() {
  const [index, setIndex] = useState(0);
  // ...
```

---

## Context Hooks {/*context-hooks*/}

*Context* cho phép một component [nhận thông tin từ các component cha xa mà không cần truyền qua props.](/learn/passing-props-to-a-component) Ví dụ, component cấp cao nhất của ứng dụng có thể truyền theme UI hiện tại cho tất cả các component bên dưới, bất kể sâu đến đâu.

* [`useContext`](/reference/react/useContext) đọc và đăng ký theo dõi một context.

```js
function Button() {
  const theme = useContext(ThemeContext);
  // ...
```

---

## Ref Hooks {/*ref-hooks*/}

*Refs* cho phép một component [giữ một số thông tin không được sử dụng để render,](/learn/referencing-values-with-refs) như một node DOM hoặc một timeout ID. Không giống state, việc cập nhật ref không làm re-render component của bạn. Refs là một "lối thoát" khỏi mô hình React. Chúng hữu ích khi bạn cần làm việc với các hệ thống ngoài React, chẳng hạn như các API tích hợp sẵn của trình duyệt.

* [`useRef`](/reference/react/useRef) khai báo một ref. Bạn có thể giữ bất kỳ giá trị nào trong đó, nhưng thường được sử dụng nhiều nhất để giữ một node DOM.
* [`useImperativeHandle`](/reference/react/useImperativeHandle) cho phép bạn tùy chỉnh ref được expose bởi component của bạn. Điều này hiếm khi được sử dụng.

```js
function Form() {
  const inputRef = useRef(null);
  // ...
```

---

## Effect Hooks {/*effect-hooks*/}

*Effects* cho phép một component [kết nối và đồng bộ với các hệ thống bên ngoài.](/learn/synchronizing-with-effects) Điều này bao gồm việc xử lý mạng, DOM trình duyệt, animation, widget được viết bằng thư viện UI khác, và các mã ngoài React khác.

* [`useEffect`](/reference/react/useEffect) kết nối một component với một hệ thống bên ngoài.

```js
function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);
  // ...
```

Effects là một "lối thoát" khỏi mô hình React. Không sử dụng Effects để điều phối luồng dữ liệu của ứng dụng. Nếu bạn không tương tác với hệ thống bên ngoài, [bạn có thể không cần Effect.](/learn/you-might-not-need-an-effect)

Có hai biến thể hiếm khi sử dụng của `useEffect` với sự khác biệt về thời điểm:

* [`useLayoutEffect`](/reference/react/useLayoutEffect) được kích hoạt trước khi trình duyệt vẽ lại màn hình. Bạn có thể đo layout ở đây.
* [`useInsertionEffect`](/reference/react/useInsertionEffect) được kích hoạt trước khi React thay đổi DOM. Các thư viện có thể chèn CSS động ở đây.

Bạn cũng có thể tách events khỏi Effects:

- [`useEffectEvent`](/reference/react/useEffectEvent) tạo một event không reactive để kích hoạt từ bất kỳ Effect hook nào.
---

## Performance Hooks {/*performance-hooks*/}

Một cách phổ biến để tối ưu hiệu suất re-render là bỏ qua các công việc không cần thiết. Ví dụ, bạn có thể yêu cầu React tái sử dụng một phép tính đã được cache hoặc bỏ qua re-render nếu dữ liệu không thay đổi kể từ lần render trước.

Để bỏ qua các phép tính và re-render không cần thiết, sử dụng một trong các Hook sau:

- [`useMemo`](/reference/react/useMemo) cho phép bạn cache kết quả của một phép tính tốn kém.
- [`useCallback`](/reference/react/useCallback) cho phép bạn cache một định nghĩa hàm trước khi truyền nó xuống một component đã được tối ưu.

```js
function TodoList({ todos, tab, theme }) {
  const visibleTodos = useMemo(() => filterTodos(todos, tab), [todos, tab]);
  // ...
}
```

Đôi khi, bạn không thể bỏ qua re-render vì màn hình thực sự cần cập nhật. Trong trường hợp đó, bạn có thể cải thiện hiệu suất bằng cách tách các cập nhật blocking phải đồng bộ (như gõ vào input) khỏi các cập nhật non-blocking không cần chặn giao diện người dùng (như cập nhật biểu đồ).

Để ưu tiên render, sử dụng một trong các Hook sau:

- [`useTransition`](/reference/react/useTransition) cho phép bạn đánh dấu một transition state là non-blocking và cho phép các cập nhật khác gián đoạn nó.
- [`useDeferredValue`](/reference/react/useDeferredValue) cho phép bạn trì hoãn cập nhật một phần không quan trọng của UI và để các phần khác cập nhật trước.

---

## Các Hook khác {/*other-hooks*/}

Các Hook này chủ yếu hữu ích cho tác giả thư viện và không thường được sử dụng trong mã ứng dụng.

- [`useDebugValue`](/reference/react/useDebugValue) cho phép bạn tùy chỉnh nhãn mà React DevTools hiển thị cho Hook tùy chỉnh của bạn.
- [`useId`](/reference/react/useId) cho phép một component liên kết một ID duy nhất với chính nó. Thường được sử dụng với các API accessibility.
- [`useSyncExternalStore`](/reference/react/useSyncExternalStore) cho phép một component đăng ký theo dõi một store bên ngoài.
* [`useActionState`](/reference/react/useActionState) cho phép bạn quản lý state của các action.

---

## Hook của riêng bạn {/*your-own-hooks*/}

Bạn cũng có thể [định nghĩa Hook tùy chỉnh của riêng mình](/learn/reusing-logic-with-custom-hooks#extracting-your-own-custom-hook-from-a-component) dưới dạng các hàm JavaScript.
