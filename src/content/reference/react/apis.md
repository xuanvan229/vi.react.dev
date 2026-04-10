---
title: "Các API React Tích hợp sẵn"
---

<Intro>

Ngoài [Hooks](/reference/react/hooks) và [Components](/reference/react/components), package `react` còn export một số API khác hữu ích cho việc định nghĩa component. Trang này liệt kê tất cả các API React hiện đại còn lại.

</Intro>

---

* [`createContext`](/reference/react/createContext) cho phép bạn định nghĩa và cung cấp context cho các component con. Được sử dụng với [`useContext`.](/reference/react/useContext)
* [`lazy`](/reference/react/lazy) cho phép bạn trì hoãn việc tải mã của một component cho đến khi nó được render lần đầu tiên.
* [`memo`](/reference/react/memo) cho phép component của bạn bỏ qua việc re-render khi props giống nhau. Được sử dụng với [`useMemo`](/reference/react/useMemo) và [`useCallback`.](/reference/react/useCallback)
* [`startTransition`](/reference/react/startTransition) cho phép bạn đánh dấu một cập nhật state là không khẩn cấp. Tương tự như [`useTransition`.](/reference/react/useTransition)
* [`act`](/reference/react/act) cho phép bạn bọc các render và tương tác trong test để đảm bảo các cập nhật đã được xử lý trước khi đưa ra các assertion.

---

## API Resource {/*resource-apis*/}

*Resources* có thể được truy cập bởi một component mà không cần chúng là một phần của state. Ví dụ, một component có thể đọc một tin nhắn từ một Promise hoặc đọc thông tin style từ một context.

Để đọc một giá trị từ resource, sử dụng API này:

* [`use`](/reference/react/use) cho phép bạn đọc giá trị của một resource như [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) hoặc [context](/learn/passing-data-deeply-with-context).
```js
function MessageComponent({ messagePromise }) {
  const message = use(messagePromise);
  const theme = use(ThemeContext);
  // ...
}
```
