---
title: Server Functions
---

<RSC>

Server Functions được sử dụng trong [React Server Components](/reference/rsc/server-components).

**Lưu ý:** Cho đến tháng 9 năm 2024, chúng tôi gọi tất cả Server Functions là "Server Actions". Nếu một Server Function được truyền cho một action prop hoặc được gọi từ bên trong một action thì nó là một Server Action, nhưng không phải tất cả Server Functions đều là Server Actions. Cách đặt tên trong tài liệu này đã được cập nhật để phản ánh rằng Server Functions có thể được sử dụng cho nhiều mục đích.

</RSC>

<Intro>

Server Functions cho phép Client Components gọi các hàm async được thực thi trên server.

</Intro>

<InlineToc />

<Note>

#### Làm thế nào để tôi xây dựng hỗ trợ cho Server Functions? {/*how-do-i-build-support-for-server-functions*/}

Mặc dù Server Functions trong React 19 đã ổn định và sẽ không bị thay đổi đáng kể giữa các phiên bản minor, các API nền tảng được sử dụng để triển khai Server Functions trong bundler hoặc framework React Server Components không tuân theo semver và có thể thay đổi giữa các phiên bản minor trong React 19.x.

Để hỗ trợ Server Functions với tư cách là bundler hoặc framework, chúng tôi khuyến nghị ghim vào một phiên bản React cụ thể, hoặc sử dụng bản Canary. Chúng tôi sẽ tiếp tục làm việc với các bundler và framework để ổn định các API được sử dụng để triển khai Server Functions trong tương lai.

</Note>

Khi một Server Function được định nghĩa với directive [`"use server"`](/reference/rsc/use-server), framework của bạn sẽ tự động tạo một tham chiếu đến Server Function, và truyền tham chiếu đó cho Client Component. Khi hàm đó được gọi trên client, React sẽ gửi một request đến server để thực thi hàm, và trả về kết quả.

Server Functions có thể được tạo trong Server Components và truyền dưới dạng props cho Client Components, hoặc chúng có thể được import và sử dụng trong Client Components.

## Cách sử dụng {/*usage*/}

### Tạo Server Function từ Server Component {/*creating-a-server-function-from-a-server-component*/}

Server Components có thể định nghĩa Server Functions với directive `"use server"`:

```js [[2, 7, "'use server'"], [1, 5, "createNoteAction"], [1, 12, "createNoteAction"]]
// Server Component
import Button from './Button';

function EmptyNote () {
  async function createNoteAction() {
    // Server Function
    'use server';

    await db.notes.create();
  }

  return <Button onClick={createNoteAction}/>;
}
```

Khi React render Server Component `EmptyNote`, nó sẽ tạo một tham chiếu đến hàm `createNoteAction`, và truyền tham chiếu đó cho Client Component `Button`. Khi nút được nhấn, React sẽ gửi một request đến server để thực thi hàm `createNoteAction` với tham chiếu được cung cấp:

```js {5}
"use client";

export default function Button({onClick}) {
  console.log(onClick);
  // {$$typeof: Symbol.for("react.server.reference"), $$id: 'createNoteAction'}
  return <button onClick={() => onClick()}>Create Empty Note</button>
}
```

Để biết thêm, xem tài liệu về [`"use server"`](/reference/rsc/use-server).


### Import Server Functions từ Client Components {/*importing-server-functions-from-client-components*/}

Client Components có thể import Server Functions từ các file sử dụng directive `"use server"`:

```js [[1, 3, "createNote"]]
"use server";

export async function createNote() {
  await db.notes.create();
}

```

Khi bundler build Client Component `EmptyNote`, nó sẽ tạo một tham chiếu đến hàm `createNote` trong bundle. Khi `button` được nhấn, React sẽ gửi một request đến server để thực thi hàm `createNote` sử dụng tham chiếu được cung cấp:

```js [[1, 2, "createNote"], [1, 5, "createNote"], [1, 7, "createNote"]]
"use client";
import {createNote} from './actions';

function EmptyNote() {
  console.log(createNote);
  // {$$typeof: Symbol.for("react.server.reference"), $$id: 'createNote'}
  <button onClick={() => createNote()} />
}
```

Để biết thêm, xem tài liệu về [`"use server"`](/reference/rsc/use-server).

### Server Functions với Actions {/*server-functions-with-actions*/}

Server Functions có thể được gọi từ Actions trên client:

```js [[1, 3, "updateName"]]
"use server";

export async function updateName(name) {
  if (!name) {
    return {error: 'Name is required'};
  }
  await db.users.updateName(name);
}
```

```js [[1, 3, "updateName"], [1, 13, "updateName"], [2, 11, "submitAction"],  [2, 23, "submitAction"]]
"use client";

import {updateName} from './actions';

function UpdateName() {
  const [name, setName] = useState('');
  const [error, setError] = useState(null);

  const [isPending, startTransition] = useTransition();

  const submitAction = async () => {
    startTransition(async () => {
      const {error} = await updateName(name);
      if (error) {
        setError(error);
      } else {
        setName('');
      }
    })
  }

  return (
    <form action={submitAction}>
      <input type="text" name="name" disabled={isPending}/>
      {error && <span>Failed: {error}</span>}
    </form>
  )
}
```

Điều này cho phép bạn truy cập trạng thái `isPending` của Server Function bằng cách bọc nó trong một Action trên client.

Để biết thêm, xem tài liệu về [Gọi Server Function bên ngoài `<form>`](/reference/rsc/use-server#calling-a-server-function-outside-of-form)

### Server Functions với Form Actions {/*using-server-functions-with-form-actions*/}

Server Functions hoạt động với các tính năng Form mới trong React 19.

Bạn có thể truyền một Server Function cho Form để tự động gửi form đến server:


```js [[1, 3, "updateName"], [1, 7, "updateName"]]
"use client";

import {updateName} from './actions';

function UpdateName() {
  return (
    <form action={updateName}>
      <input type="text" name="name" />
    </form>
  )
}
```

Khi việc gửi Form thành công, React sẽ tự động reset form. Bạn có thể thêm `useActionState` để truy cập trạng thái đang chờ, phản hồi cuối cùng, hoặc để hỗ trợ progressive enhancement.

Để biết thêm, xem tài liệu về [Server Functions trong Forms](/reference/rsc/use-server#server-functions-in-forms).

### Server Functions với `useActionState` {/*server-functions-with-use-action-state*/}

Bạn có thể gọi Server Functions với `useActionState` cho trường hợp phổ biến khi bạn chỉ cần truy cập trạng thái đang chờ và phản hồi cuối cùng của action:

```js [[1, 3, "updateName"], [1, 6, "updateName"], [2, 6, "submitAction"], [2, 9, "submitAction"]]
"use client";

import {updateName} from './actions';

function UpdateName() {
  const [state, submitAction, isPending] = useActionState(updateName, {error: null});

  return (
    <form action={submitAction}>
      <input type="text" name="name" disabled={isPending}/>
      {state.error && <span>Failed: {state.error}</span>}
    </form>
  );
}
```

Khi sử dụng `useActionState` với Server Functions, React cũng sẽ tự động phát lại các lần gửi form được nhập trước khi hydration hoàn tất. Điều này có nghĩa là người dùng có thể tương tác với ứng dụng của bạn ngay cả trước khi ứng dụng đã hydrate.

Để biết thêm, xem tài liệu về [`useActionState`](/reference/react/useActionState).

### Progressive enhancement với `useActionState` {/*progressive-enhancement-with-useactionstate*/}

Server Functions cũng hỗ trợ progressive enhancement với tham số thứ ba của `useActionState`.

```js [[1, 3, "updateName"], [1, 6, "updateName"], [2, 6, "/name/update"], [3, 6, "submitAction"], [3, 9, "submitAction"]]
"use client";

import {updateName} from './actions';

function UpdateName() {
  const [, submitAction] = useActionState(updateName, null, `/name/update`);

  return (
    <form action={submitAction}>
      ...
    </form>
  );
}
```

Khi <CodeStep step={2}>permalink</CodeStep> được cung cấp cho `useActionState`, React sẽ chuyển hướng đến URL được cung cấp nếu form được gửi trước khi JavaScript bundle tải xong.

Để biết thêm, xem tài liệu về [`useActionState`](/reference/react/useActionState).
