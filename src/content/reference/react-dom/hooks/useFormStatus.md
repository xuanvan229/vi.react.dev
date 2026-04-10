---
title: useFormStatus
---

<Intro>

`useFormStatus` là một Hook cung cấp cho bạn thông tin trạng thái của lần gửi form gần nhất.

```js
const { pending, data, method, action } = useFormStatus();
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `useFormStatus()` {/*use-form-status*/}

Hook `useFormStatus` cung cấp thông tin trạng thái của lần gửi form gần nhất.

```js {5},[[1, 6, "status.pending"]]
import { useFormStatus } from "react-dom";
import action from './actions';

function Submit() {
  const status = useFormStatus();
  return <button disabled={status.pending}>Submit</button>
}

export default function App() {
  return (
    <form action={action}>
      <Submit />
    </form>
  );
}
```

Để lấy thông tin trạng thái, component `Submit` phải được render bên trong một `<form>`. Hook trả về thông tin như thuộc tính <CodeStep step={1}>`pending`</CodeStep> cho bạn biết form có đang gửi hay không.

Trong ví dụ trên, `Submit` sử dụng thông tin này để vô hiệu hóa nút `<button>` khi form đang gửi.

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

`useFormStatus` không nhận bất kỳ tham số nào.

#### Giá trị trả về {/*returns*/}

Một object `status` với các thuộc tính sau:

* `pending`: Một boolean. Nếu `true`, điều này có nghĩa là `<form>` cha đang chờ gửi. Nếu không, `false`.

* `data`: Một object triển khai [`FormData interface`](https://developer.mozilla.org/en-US/docs/Web/API/FormData) chứa dữ liệu mà `<form>` cha đang gửi. Nếu không có lần gửi nào đang hoạt động hoặc không có `<form>` cha, nó sẽ là `null`.

* `method`: Một giá trị chuỗi `'get'` hoặc `'post'`. Đại diện cho việc `<form>` cha đang gửi bằng [phương thức HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) `GET` hay `POST`. Theo mặc định, `<form>` sẽ sử dụng phương thức `GET` và có thể được chỉ định bởi thuộc tính [`method`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form#method).

[//]: # (Link to `<form>` documentation. "Read more on the `action` prop on `<form>`.")
* `action`: Một tham chiếu đến hàm được truyền cho prop `action` trên `<form>` cha. Nếu không có `<form>` cha, thuộc tính này là `null`. Nếu có giá trị URI được cung cấp cho prop `action`, hoặc không có prop `action` được chỉ định, `status.action` sẽ là `null`.

#### Lưu ý {/*caveats*/}

* Hook `useFormStatus` phải được gọi từ một component được render bên trong một `<form>`.
* `useFormStatus` sẽ chỉ trả về thông tin trạng thái cho `<form>` cha. Nó sẽ không trả về thông tin trạng thái cho bất kỳ `<form>` nào được render trong cùng component hoặc các component con.

---

## Cách sử dụng {/*usage*/}

### Hiển thị trạng thái đang chờ khi gửi form {/*display-a-pending-state-during-form-submission*/}
Để hiển thị trạng thái đang chờ khi form đang gửi, bạn có thể gọi Hook `useFormStatus` trong một component được render bên trong `<form>` và đọc thuộc tính `pending` được trả về.

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

<Pitfall>

##### `useFormStatus` sẽ không trả về thông tin trạng thái cho `<form>` được render trong cùng component. {/*useformstatus-will-not-return-status-information-for-a-form-rendered-in-the-same-component*/}

Hook `useFormStatus` chỉ trả về thông tin trạng thái cho `<form>` cha và không trả về cho bất kỳ `<form>` nào được render trong cùng component gọi Hook, hoặc các component con.

```js
function Form() {
  // 🚩 `pending` will never be true
  // useFormStatus does not track the form rendered in this component
  const { pending } = useFormStatus();
  return <form action={submit}></form>;
}
```

Thay vào đó, hãy gọi `useFormStatus` từ bên trong một component nằm bên trong `<form>`.

```js
function Submit() {
  // ✅ `pending` will be derived from the form that wraps the Submit component
  const { pending } = useFormStatus();
  return <button disabled={pending}>...</button>;
}

function Form() {
  // This is the <form> `useFormStatus` tracks
  return (
    <form action={submit}>
      <Submit />
    </form>
  );
}
```

</Pitfall>

### Đọc dữ liệu form đang được gửi {/*read-form-data-being-submitted*/}

Bạn có thể sử dụng thuộc tính `data` của thông tin trạng thái được trả về từ `useFormStatus` để hiển thị dữ liệu nào đang được gửi bởi người dùng.

Ở đây, chúng ta có một form nơi người dùng có thể yêu cầu tên người dùng. Chúng ta có thể sử dụng `useFormStatus` để hiển thị một thông báo trạng thái tạm thời xác nhận tên người dùng họ đã yêu cầu.

<Sandpack>

```js src/UsernameForm.js active
import {useState, useMemo, useRef} from 'react';
import {useFormStatus} from 'react-dom';

export default function UsernameForm() {
  const {pending, data} = useFormStatus();

  return (
    <div>
      <h3>Request a Username: </h3>
      <input type="text" name="username" disabled={pending}/>
      <button type="submit" disabled={pending}>
        Submit
      </button>
      <br />
      <p>{data ? `Requesting ${data?.get("username")}...`: ''}</p>
    </div>
  );
}
```

```js src/App.js
import UsernameForm from './UsernameForm';
import { submitForm } from "./actions.js";
import {useRef} from 'react';

export default function App() {
  const ref = useRef(null);
  return (
    <form ref={ref} action={async (formData) => {
      await submitForm(formData);
      ref.current.reset();
    }}>
      <UsernameForm />
    </form>
  );
}
```

```js src/actions.js hidden
export async function submitForm(query) {
    await new Promise((res) => setTimeout(res, 2000));
}
```

```css
p {
    height: 14px;
    padding: 0;
    margin: 2px 0 0 0 ;
    font-size: 14px
}

button {
    margin-left: 2px;
}

```

</Sandpack>

---

## Xử lý sự cố {/*troubleshooting*/}

### `status.pending` không bao giờ là `true` {/*pending-is-never-true*/}

`useFormStatus` sẽ chỉ trả về thông tin trạng thái cho `<form>` cha.

Nếu component gọi `useFormStatus` không được lồng trong một `<form>`, `status.pending` sẽ luôn trả về `false`. Hãy xác minh rằng `useFormStatus` được gọi trong một component là con của phần tử `<form>`.

`useFormStatus` sẽ không theo dõi trạng thái của `<form>` được render trong cùng component. Xem [Lưu ý](#useformstatus-will-not-return-status-information-for-a-form-rendered-in-the-same-component) để biết thêm chi tiết.
