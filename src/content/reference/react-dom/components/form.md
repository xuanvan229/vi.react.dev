---
title: "<form>"
---

<Intro>

Component [`<form>` có sẵn trong trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form) cho phép bạn tạo các điều khiển tương tác để gửi thông tin.

```js
<form action={search}>
    <input name="query" />
    <button type="submit">Search</button>
</form>
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `<form>` {/*form*/}

Để tạo các điều khiển tương tác cho việc gửi thông tin, render component [`<form>` có sẵn trong trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form).

```js
<form action={search}>
    <input name="query" />
    <button type="submit">Search</button>
</form>
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Props {/*props*/}

`<form>` hỗ trợ tất cả [common element props.](/reference/react-dom/components/common#common-props)

[`action`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form#action): một URL hoặc hàm. Khi một URL được truyền cho `action` form sẽ hoạt động như component form HTML. Khi một hàm được truyền cho `action` hàm đó sẽ xử lý việc gửi form trong một Transition theo [pattern prop Action](/reference/react/useTransition#exposing-action-props-from-components). Hàm được truyền cho `action` có thể là async và sẽ được gọi với một đối số duy nhất chứa [dữ liệu form](https://developer.mozilla.org/en-US/docs/Web/API/FormData) của form đã gửi. Prop `action` có thể bị ghi đè bởi thuộc tính `formAction` trên component `<button>`, `<input type="submit">`, hoặc `<input type="image">`.

#### Lưu ý {/*caveats*/}

* Khi một hàm được truyền cho `action` hoặc `formAction`, phương thức HTTP sẽ là POST bất kể giá trị của prop `method`.

---

## Cách sử dụng {/*usage*/}

### Xử lý gửi form trên client {/*handle-form-submission-on-the-client*/}

Truyền một hàm vào prop `action` của form để chạy hàm khi form được gửi. [`formData`](https://developer.mozilla.org/en-US/docs/Web/API/FormData) sẽ được truyền vào hàm như một đối số để bạn có thể truy cập dữ liệu được gửi bởi form. Điều này khác với [HTML action](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form#action) thông thường, chỉ chấp nhận URL. Sau khi hàm `action` thành công, tất cả các phần tử field không được kiểm soát trong form sẽ được reset.

<Sandpack>

```js src/App.js
export default function Search() {
  function search(formData) {
    const query = formData.get("query");
    alert(`You searched for '${query}'`);
  }
  return (
    <form action={search}>
      <input name="query" />
      <button type="submit">Search</button>
    </form>
  );
}
```

</Sandpack>

### Xử lý gửi form với Server Function {/*handle-form-submission-with-a-server-function*/}

Render một `<form>` với một input và nút gửi. Truyền một Server Function (một hàm được đánh dấu với [`'use server'`](/reference/rsc/use-server)) vào prop `action` của form để chạy hàm khi form được gửi.

Việc truyền Server Function vào `<form action>` cho phép người dùng gửi form mà không cần JavaScript được bật hoặc trước khi code được tải. Điều này có lợi cho những người dùng có kết nối chậm, thiết bị chậm, hoặc đã tắt JavaScript và tương tự với cách form hoạt động khi một URL được truyền cho prop `action`.

Bạn có thể sử dụng các trường form ẩn để cung cấp dữ liệu cho action của `<form>`. Server Function sẽ được gọi với dữ liệu trường form ẩn như một instance của [`FormData`](https://developer.mozilla.org/en-US/docs/Web/API/FormData).

```jsx
import { updateCart } from './lib.js';

function AddToCart({productId}) {
  async function addToCart(formData) {
    'use server'
    const productId = formData.get('productId')
    await updateCart(productId)
  }
  return (
    <form action={addToCart}>
        <input type="hidden" name="productId" value={productId} />
        <button type="submit">Add to Cart</button>
    </form>

  );
}
```

Thay vì sử dụng các trường form ẩn để cung cấp dữ liệu cho action của `<form>`, bạn có thể gọi phương thức <CodeStep step={1}>`bind`</CodeStep> để cung cấp thêm đối số. Điều này sẽ liên kết một đối số mới (<CodeStep step={2}>`productId`</CodeStep>) với hàm ngoài <CodeStep step={3}>`formData`</CodeStep> được truyền vào hàm.

```jsx [[1, 8, "bind"], [2,8, "productId"], [2,4, "productId"], [3,4, "formData"]]
import { updateCart } from './lib.js';

function AddToCart({productId}) {
  async function addToCart(productId, formData) {
    "use server";
    await updateCart(productId)
  }
  const addProductToCart = addToCart.bind(null, productId);
  return (
    <form action={addProductToCart}>
      <button type="submit">Add to Cart</button>
    </form>
  );
}
```

Khi `<form>` được render bởi một [Server Component](/reference/rsc/use-client), và một [Server Function](/reference/rsc/server-functions) được truyền cho prop `action` của `<form>`, form được [tăng cường dần](https://developer.mozilla.org/en-US/docs/Glossary/Progressive_Enhancement).

### Hiển thị trạng thái đang chờ khi gửi form {/*display-a-pending-state-during-form-submission*/}
Để hiển thị trạng thái đang chờ khi form đang được gửi, bạn có thể gọi Hook `useFormStatus` trong một component được render trong `<form>` và đọc thuộc tính `pending` được trả về.

Ở đây, chúng ta sử dụng thuộc tính `pending` để chỉ ra rằng form đang gửi.

<Sandpack>

```js src/App.js
import { useFormStatus } from "react-dom";
import { submitForm } from "./actions.js";

function Submit() {
  const { pending } = useFormStatus();
  return (
    <button type="submit" disabled={pending}>
      {pending ? "Submitting..." : "Submit"}
    </button>
  );
}

function Form({ action }) {
  return (
    <form action={action}>
      <Submit />
    </form>
  );
}

export default function App() {
  return <Form action={submitForm} />;
}
```

```js src/actions.js hidden
export async function submitForm(query) {
    await new Promise((res) => setTimeout(res, 1000));
}
```

</Sandpack>

Để tìm hiểu thêm về Hook `useFormStatus`, hãy xem [tài liệu tham chiếu](/reference/react-dom/hooks/useFormStatus).

### Cập nhật dữ liệu form một cách lạc quan {/*optimistically-updating-form-data*/}
Hook `useOptimistic` cung cấp một cách để cập nhật giao diện người dùng một cách lạc quan trước khi một thao tác nền, như một network request, hoàn thành. Trong bối cảnh form, kỹ thuật này giúp ứng dụng cảm thấy phản hồi nhanh hơn. Khi người dùng gửi form, thay vì chờ phản hồi từ server để phản ánh các thay đổi, giao diện được cập nhật ngay lập tức với kết quả dự kiến.

Ví dụ, khi người dùng gõ tin nhắn vào form và nhấn nút "Gửi", Hook `useOptimistic` cho phép tin nhắn xuất hiện ngay lập tức trong danh sách với nhãn "Đang gửi...", ngay cả trước khi tin nhắn thực sự được gửi đến server. Cách tiếp cận "lạc quan" này tạo ấn tượng về tốc độ và khả năng phản hồi. Form sau đó cố gắng thực sự gửi tin nhắn ở phía sau. Một khi server xác nhận tin nhắn đã được nhận, nhãn "Đang gửi..." được xóa.

<Sandpack>


```js src/App.js
import { useOptimistic, useState, useRef } from "react";
import { deliverMessage } from "./actions.js";

function Thread({ messages, sendMessage }) {
  const formRef = useRef();
  async function formAction(formData) {
    addOptimisticMessage(formData.get("message"));
    formRef.current.reset();
    await sendMessage(formData);
  }
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (state, newMessage) => [
      ...state,
      {
        text: newMessage,
        sending: true
      }
    ]
  );

  return (
    <>
      {optimisticMessages.map((message, index) => (
        <div key={index}>
          {message.text}
          {!!message.sending && <small> (Sending...)</small>}
        </div>
      ))}
      <form action={formAction} ref={formRef}>
        <input type="text" name="message" placeholder="Hello!" />
        <button type="submit">Send</button>
      </form>
    </>
  );
}

export default function App() {
  const [messages, setMessages] = useState([
    { text: "Hello there!", sending: false, key: 1 }
  ]);
  async function sendMessage(formData) {
    const sentMessage = await deliverMessage(formData.get("message"));
    setMessages((messages) => [...messages, { text: sentMessage }]);
  }
  return <Thread messages={messages} sendMessage={sendMessage} />;
}
```

```js src/actions.js
export async function deliverMessage(message) {
  await new Promise((res) => setTimeout(res, 1000));
  return message;
}
```

</Sandpack>

[//]: # 'Uncomment the next line, and delete this line after the `useOptimistic` reference documentation page is published'
[//]: # 'To learn more about the `useOptimistic` Hook see the [reference documentation](/reference/react/useOptimistic).'

### Xử lý lỗi khi gửi form {/*handling-form-submission-errors*/}

Trong một số trường hợp, hàm được gọi bởi prop `action` của `<form>` ném ra lỗi. Bạn có thể xử lý những lỗi này bằng cách bọc `<form>` trong một Error Boundary. Nếu hàm được gọi bởi prop `action` của `<form>` ném ra lỗi, fallback cho error boundary sẽ được hiển thị.

<Sandpack>

```js src/App.js
import { ErrorBoundary } from "react-error-boundary";

export default function Search() {
  function search() {
    throw new Error("search error");
  }
  return (
    <ErrorBoundary
      fallback={<p>There was an error while submitting the form</p>}
    >
      <form action={search}>
        <input name="query" />
        <button type="submit">Search</button>
      </form>
    </ErrorBoundary>
  );
}

```

```json package.json hidden
{
  "dependencies": {
    "react": "19.0.0-rc-3edc000d-20240926",
    "react-dom": "19.0.0-rc-3edc000d-20240926",
    "react-scripts": "^5.0.0",
    "react-error-boundary": "4.0.3"
  },
  "main": "/index.js",
  "devDependencies": {}
}
```

</Sandpack>

### Hiển thị lỗi gửi form mà không cần JavaScript {/*display-a-form-submission-error-without-javascript*/}

Việc hiển thị thông báo lỗi gửi form trước khi JavaScript bundle tải để tăng cường dần cần:

1. `<form>` được render bởi một [Client Component](/reference/rsc/use-client)
1. hàm được truyền cho prop `action` của `<form>` là một [Server Function](/reference/rsc/server-functions)
1. Hook `useActionState` được sử dụng để hiển thị thông báo lỗi

`useActionState` nhận hai tham số: một [Server Function](/reference/rsc/server-functions) và một state khởi đầu. `useActionState` trả về hai giá trị, một biến state và một action. Action được trả về bởi `useActionState` nên được truyền cho prop `action` của form. Biến state được trả về bởi `useActionState` có thể được sử dụng để hiển thị thông báo lỗi. Giá trị được trả về bởi Server Function được truyền cho `useActionState` sẽ được sử dụng để cập nhật biến state.

<Sandpack>

```js src/App.js
import { useActionState } from "react";
import { signUpNewUser } from "./api";

export default function Page() {
  async function signup(prevState, formData) {
    "use server";
    const email = formData.get("email");
    try {
      await signUpNewUser(email);
      alert(`Added "${email}"`);
    } catch (err) {
      return err.toString();
    }
  }
  const [message, signupAction] = useActionState(signup, null);
  return (
    <>
      <h1>Signup for my newsletter</h1>
      <p>Signup with the same email twice to see an error</p>
      <form action={signupAction} id="signup-form">
        <label htmlFor="email">Email: </label>
        <input name="email" id="email" placeholder="react@example.com" />
        <button>Sign up</button>
        {!!message && <p>{message}</p>}
      </form>
    </>
  );
}
```

```js src/api.js hidden
let emails = [];

export async function signUpNewUser(newEmail) {
  if (emails.includes(newEmail)) {
    throw new Error("This email address has already been added");
  }
  emails.push(newEmail);
}
```

</Sandpack>

Tìm hiểu thêm về việc cập nhật state từ một form action với tài liệu [`useActionState`](/reference/react/useActionState)

### Xử lý nhiều loại gửi {/*handling-multiple-submission-types*/}

Các form có thể được thiết kế để xử lý nhiều hành động gửi dựa trên nút mà người dùng nhấn. Mỗi nút bên trong form có thể được liên kết với một action hoặc hành vi riêng biệt bằng cách đặt prop `formAction`.

Khi người dùng nhấn một nút cụ thể, form được gửi và một action tương ứng, được xác định bởi các thuộc tính và action của nút đó, được thực hiện. Ví dụ, một form có thể gửi bài viết để xem xét theo mặc định nhưng có một nút riêng biệt với `formAction` được đặt để lưu bài viết như bản nháp.

<Sandpack>

```js src/App.js
export default function Search() {
  function publish(formData) {
    const content = formData.get("content");
    const button = formData.get("button");
    alert(`'${content}' was published with the '${button}' button`);
  }

  function save(formData) {
    const content = formData.get("content");
    alert(`Your draft of '${content}' has been saved!`);
  }

  return (
    <form action={publish}>
      <textarea name="content" rows={4} cols={40} />
      <br />
      <button type="submit" name="button" value="submit">Publish</button>
      <button formAction={save}>Save draft</button>
    </form>
  );
}
```

</Sandpack>
