---
title: React gọi Components và Hooks
---

<Intro>
React chịu trách nhiệm render các component và Hooks khi cần thiết để tối ưu hóa trải nghiệm người dùng. Nó mang tính khai báo: bạn nói cho React biết cần render gì trong logic của component, và React sẽ tìm ra cách tốt nhất để hiển thị nó cho người dùng.
</Intro>

<InlineToc />

---

## Không bao giờ gọi trực tiếp hàm component {/*never-call-component-functions-directly*/}
Các component chỉ nên được sử dụng trong JSX. Đừng gọi chúng như các hàm thông thường. React nên gọi chúng.

React phải quyết định khi nào hàm component của bạn được gọi [trong quá trình rendering](/reference/rules/components-and-hooks-must-be-pure#how-does-react-run-your-code). Trong React, bạn thực hiện điều này bằng JSX.

```js {2}
function BlogPost() {
  return <Layout><Article /></Layout>; // ✅ Good: Only use components in JSX
}
```

```js {expectedErrors: {'react-compiler': [2]}} {2}
function BlogPost() {
  return <Layout>{Article()}</Layout>; // 🔴 Bad: Never call them directly
}
```

Nếu một component chứa Hooks, việc gọi trực tiếp các component trong vòng lặp hoặc có điều kiện rất dễ vi phạm [Các quy tắc của Hooks](/reference/rules/rules-of-hooks).

Để React điều phối rendering cũng mang lại nhiều lợi ích:

* **Các component trở thành nhiều hơn là hàm.** React có thể tăng cường chúng với các tính năng như _local state_ thông qua Hooks gắn liền với danh tính của component trong cây.
* **Các kiểu component tham gia vào reconciliation.** Bằng cách để React gọi các component của bạn, bạn cũng cho nó biết thêm về cấu trúc khái niệm của cây của bạn. Ví dụ, khi bạn chuyển từ render `<Feed>` sang trang `<Profile>`, React sẽ không cố gắng tái sử dụng chúng.
* **React có thể nâng cao trải nghiệm người dùng.** Ví dụ, nó có thể cho phép trình duyệt thực hiện một số công việc giữa các lần gọi component để việc re-render một cây component lớn không chặn luồng chính.
* **Một câu chuyện debug tốt hơn.** Nếu các component là công dân hạng nhất mà thư viện nhận biết, chúng ta có thể xây dựng các công cụ phát triển phong phú để kiểm tra trong quá trình phát triển.
* **Reconciliation hiệu quả hơn.** React có thể quyết định chính xác component nào trong cây cần re-render và bỏ qua những cái không cần. Điều đó làm cho ứng dụng của bạn nhanh hơn và mượt mà hơn.

---

## Không bao giờ truyền Hooks như giá trị thông thường {/*never-pass-around-hooks-as-regular-values*/}

Hooks chỉ nên được gọi bên trong các component hoặc Hooks. Không bao giờ truyền nó như một giá trị thông thường.

Hooks cho phép bạn tăng cường component với các tính năng React. Chúng phải luôn được gọi như một hàm, và không bao giờ được truyền đi như một giá trị thông thường. Điều này cho phép _local reasoning_, hay khả năng cho các developer hiểu tất cả những gì một component có thể làm bằng cách nhìn vào component đó một cách độc lập.

Vi phạm quy tắc này sẽ khiến React không tự động tối ưu hóa component của bạn.

### Không dynamically mutate một Hook {/*dont-dynamically-mutate-a-hook*/}

Hooks nên "tĩnh" nhất có thể. Điều này có nghĩa là bạn không nên dynamically mutate chúng. Ví dụ, điều này có nghĩa là bạn không nên viết higher order Hooks:

```js {expectedErrors: {'react-compiler': [2, 3]}} {2}
function ChatInput() {
  const useDataWithLogging = withLogging(useData); // 🔴 Bad: don't write higher order Hooks
  const data = useDataWithLogging();
}
```

Hooks nên bất biến và không được mutate. Thay vì dynamically mutate một Hook, hãy tạo một phiên bản tĩnh của Hook với chức năng mong muốn.

```js {2,6}
function ChatInput() {
  const data = useDataWithLogging(); // ✅ Good: Create a new version of the Hook
}

function useDataWithLogging() {
  // ... Create a new version of the Hook and inline the logic here
}
```

### Không dynamically sử dụng Hooks {/*dont-dynamically-use-hooks*/}

Hooks cũng không nên được sử dụng một cách dynamic: ví dụ, thay vì thực hiện dependency injection trong component bằng cách truyền một Hook như một giá trị:

```js {expectedErrors: {'react-compiler': [2]}} {2}
function ChatInput() {
  return <Button useData={useDataWithLogging} /> // 🔴 Bad: don't pass Hooks as props
}
```

Bạn phải luôn inline lời gọi Hook vào component đó và xử lý bất kỳ logic nào ở đó.

```js {6}
function ChatInput() {
  return <Button />
}

function Button() {
  const data = useDataWithLogging(); // ✅ Good: Use the Hook directly
}

function useDataWithLogging() {
  // If there's any conditional logic to change the Hook's behavior, it should be inlined into
  // the Hook
}
```

Theo cách này, `<Button />` dễ hiểu và debug hơn nhiều. Khi Hooks được sử dụng theo cách dynamic, nó tăng đáng kể độ phức tạp của ứng dụng và ức chế local reasoning, làm cho team của bạn kém năng suất hơn về lâu dài. Nó cũng làm cho việc vô tình vi phạm [Các quy tắc của Hooks](/reference/rules/rules-of-hooks) rằng Hooks không nên được gọi có điều kiện dễ xảy ra hơn. Nếu bạn thấy mình cần mock các component cho các bài test, tốt hơn là mock server để phản hồi với dữ liệu cố định. Nếu có thể, thông thường cũng hiệu quả hơn khi test ứng dụng của bạn với các end-to-end tests.
