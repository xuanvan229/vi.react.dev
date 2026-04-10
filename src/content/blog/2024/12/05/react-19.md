---
title: "React v19"
author: The React Team
date: 2024/12/05
description: React 19 hiện đã có trên npm! Trong bài viết này, chúng tôi sẽ cung cấp tổng quan về các tính năng mới trong React 19 và cách bạn có thể áp dụng chúng.
---

December 05, 2024 by [The React Team](/community/team)

---
<Note>

### React 19 hiện đã ổn định! {/*react-19-is-now-stable*/}

Các bổ sung kể từ bài viết này được chia sẻ ban đầu với React 19 RC vào tháng 4:

- **Pre-warming cho suspended trees**: xem [Cải thiện cho Suspense](/blog/2024/04/25/react-19-upgrade-guide#improvements-to-suspense).
- **React DOM static APIs**: xem [React DOM Static APIs mới](#new-react-dom-static-apis).

_Ngày của bài viết này đã được cập nhật để phản ánh ngày phát hành ổn định._

</Note>

<Intro>

React v19 hiện đã có trên npm!

</Intro>

Trong [Hướng dẫn nâng cấp React 19](/blog/2024/04/25/react-19-upgrade-guide), chúng tôi đã chia sẻ các hướng dẫn từng bước để nâng cấp ứng dụng của bạn lên React 19. Trong bài viết này, chúng tôi sẽ cung cấp tổng quan về các tính năng mới trong React 19 và cách bạn có thể áp dụng chúng.

- [Có gì mới trong React 19](#whats-new-in-react-19)
- [Cải thiện trong React 19](#improvements-in-react-19)
- [Cách nâng cấp](#how-to-upgrade)

Để xem danh sách các thay đổi phá vỡ, hãy xem [Hướng dẫn nâng cấp](/blog/2024/04/25/react-19-upgrade-guide).

---

## Có gì mới trong React 19 {/*whats-new-in-react-19*/}

### Actions {/*actions*/}

Một trường hợp sử dụng phổ biến trong các ứng dụng React là thực hiện mutation dữ liệu và sau đó cập nhật state để phản hồi. Ví dụ, khi người dùng gửi form để thay đổi tên của họ, bạn sẽ thực hiện yêu cầu API, và sau đó xử lý phản hồi. Trong quá khứ, bạn sẽ cần xử lý các trạng thái pending, lỗi, optimistic updates, và các yêu cầu tuần tự thủ công.

For example, you could handle the pending and error state in `useState`:

```js
// Before Actions
function UpdateName({}) {
  const [name, setName] = useState("");
  const [error, setError] = useState(null);
  const [isPending, setIsPending] = useState(false);

  const handleSubmit = async () => {
    setIsPending(true);
    const error = await updateName(name);
    setIsPending(false);
    if (error) {
      setError(error);
      return;
    }
    redirect("/path");
  };

  return (
    <div>
      <input value={name} onChange={(event) => setName(event.target.value)} />
      <button onClick={handleSubmit} disabled={isPending}>
        Update
      </button>
      {error && <p>{error}</p>}
    </div>
  );
}
```

Trong React 19, chúng tôi đang thêm hỗ trợ cho việc sử dụng các hàm async trong transitions để tự động xử lý các trạng thái pending, lỗi, form, và optimistic updates.

Ví dụ, bạn có thể dùng `useTransition` để xử lý trạng thái pending cho bạn:

```js
// Using pending state from Actions
function UpdateName({}) {
  const [name, setName] = useState("");
  const [error, setError] = useState(null);
  const [isPending, startTransition] = useTransition();

  const handleSubmit = () => {
    startTransition(async () => {
      const error = await updateName(name);
      if (error) {
        setError(error);
        return;
      }
      redirect("/path");
    })
  };

  return (
    <div>
      <input value={name} onChange={(event) => setName(event.target.value)} />
      <button onClick={handleSubmit} disabled={isPending}>
        Update
      </button>
      {error && <p>{error}</p>}
    </div>
  );
}
```

Async transition sẽ ngay lập tức đặt trạng thái `isPending` thành true, thực hiện các yêu cầu async, và chuyển `isPending` thành false sau khi bất kỳ transitions nào. Điều này cho phép bạn giữ cho UI hiện tại phản hồi và tương tác trong khi dữ liệu đang thay đổi.

<Note>

#### Theo quy ước, các hàm sử dụng async transitions được gọi là "Actions". {/*by-convention-functions-that-use-async-transitions-are-called-actions*/}

Actions tự động quản lý việc gửi dữ liệu cho bạn:

- **Trạng thái pending**: Actions cung cấp trạng thái pending bắt đầu ở đầu yêu cầu và tự động reset khi cập nhật trạng thái cuối cùng được commit.
- **Optimistic updates**: Actions hỗ trợ hook [`useOptimistic`](#new-hook-optimistic-updates) mới để bạn có thể hiển thị phản hồi tức thì cho người dùng trong khi các yêu cầu đang được gửi.
- **Xử lý lỗi**: Actions cung cấp xử lý lỗi để bạn có thể hiển thị Error Boundaries khi yêu cầu thất bại, và tự động hoàn nguyên optimistic updates về giá trị gốc của chúng.
- **Forms**: Các phần tử `<form>` hiện hỗ trợ truyền hàm vào props `action` và `formAction`. Truyền hàm vào props `action` sử dụng Actions theo mặc định và reset form tự động sau khi gửi.

</Note>

Xây dựng trên Actions, React 19 giới thiệu [`useOptimistic`](#new-hook-optimistic-updates) để quản lý optimistic updates, và một hook mới [`React.useActionState`](#new-hook-useactionstate) để xử lý các trường hợp phổ biến cho Actions. Trong `react-dom` chúng tôi đang thêm [`<form>` Actions](#form-actions) để tự động quản lý form và [`useFormStatus`](#new-hook-useformstatus) để hỗ trợ các trường hợp phổ biến cho Actions trong form.

Trong React 19, ví dụ trên có thể được đơn giản hóa thành:

```js
// Using <form> Actions and useActionState
function ChangeName({ name, setName }) {
  const [error, submitAction, isPending] = useActionState(
    async (previousState, formData) => {
      const error = await updateName(formData.get("name"));
      if (error) {
        return error;
      }
      redirect("/path");
      return null;
    },
    null,
  );

  return (
    <form action={submitAction}>
      <input type="text" name="name" />
      <button type="submit" disabled={isPending}>Update</button>
      {error && <p>{error}</p>}
    </form>
  );
}
```

Trong phần tiếp theo, chúng tôi sẽ phân tích từng tính năng Action mới trong React 19.

### Hook mới: `useActionState` {/*new-hook-useactionstate*/}

Để làm cho các trường hợp phổ biến dễ dàng hơn cho Actions, chúng tôi đã thêm một hook mới gọi là `useActionState`:

```js
const [error, submitAction, isPending] = useActionState(
  async (previousState, newName) => {
    const error = await updateName(newName);
    if (error) {
      // You can return any result of the action.
      // Here, we return only the error.
      return error;
    }

    // handle success
    return null;
  },
  null,
);
```

`useActionState` chấp nhận một hàm (là "Action"), và trả về một Action được bọc để gọi. Điều này hoạt động vì Actions có thể compose. Khi Action được bọc được gọi, `useActionState` sẽ trả về kết quả cuối cùng của Action dưới dạng `data`, và trạng thái pending của Action dưới dạng `pending`.

<Note>

`React.useActionState` trước đây được gọi là `ReactDOM.useFormState` trong các bản phát hành Canary, nhưng chúng tôi đã đổi tên nó và deprecated `useFormState`.

Xem [#28491](https://github.com/facebook/react/pull/28491) để biết thêm thông tin.

</Note>

Để biết thêm thông tin, hãy xem tài liệu cho [`useActionState`](/reference/react/useActionState).

### React DOM: `<form>` Actions {/*form-actions*/}

Actions cũng được tích hợp với các tính năng `<form>` mới của React 19 cho `react-dom`. Chúng tôi đã thêm hỗ trợ truyền hàm như props `action` và `formAction` của các phần tử `<form>`, `<input>`, và `<button>` để tự động gửi form với Actions:

```js [[1,1,"actionFunction"]]
<form action={actionFunction}>
```

Khi `<form>` Action thành công, React sẽ tự động reset form cho các uncontrolled component. Nếu bạn cần reset `<form>` thủ công, bạn có thể gọi React DOM API `requestFormReset` mới.

Để biết thêm thông tin, hãy xem tài liệu `react-dom` cho [`<form>`](/reference/react-dom/components/form), [`<input>`](/reference/react-dom/components/input), và `<button>`.

### React DOM: Hook mới: `useFormStatus` {/*new-hook-useformstatus*/}

Trong các hệ thống thiết kế, việc viết các component thiết kế cần truy cập thông tin về `<form>` mà chúng ở trong, mà không cần truyền props xuống component là phổ biến. Điều này có thể được thực hiện qua Context, nhưng để làm cho trường hợp phổ biến dễ dàng hơn, chúng tôi đã thêm hook mới `useFormStatus`:

```js [[1, 4, "pending"], [1, 5, "pending"]]
import {useFormStatus} from 'react-dom';

function DesignButton() {
  const {pending} = useFormStatus();
  return <button type="submit" disabled={pending} />
}
```

`useFormStatus` đọc trạng thái của `<form>` cha như thể form là một Context provider.

Để biết thêm thông tin, hãy xem tài liệu `react-dom` cho [`useFormStatus`](/reference/react-dom/hooks/useFormStatus).

### Hook mới: `useOptimistic` {/*new-hook-optimistic-updates*/}

Một mẫu UI phổ biến khác khi thực hiện mutation dữ liệu là hiển thị trạng thái cuối cùng một cách lạc quan trong khi yêu cầu async đang diễn ra. Trong React 19, chúng tôi đang thêm một hook mới gọi là `useOptimistic` để làm điều này dễ dàng hơn:

```js {2,6,13,19}
function ChangeName({currentName, onUpdateName}) {
  const [optimisticName, setOptimisticName] = useOptimistic(currentName);

  const submitAction = async formData => {
    const newName = formData.get("name");
    setOptimisticName(newName);
    const updatedName = await updateName(newName);
    onUpdateName(updatedName);
  };

  return (
    <form action={submitAction}>
      <p>Your name is: {optimisticName}</p>
      <p>
        <label>Change Name:</label>
        <input
          type="text"
          name="name"
          disabled={currentName !== optimisticName}
        />
      </p>
    </form>
  );
}
```

Hook `useOptimistic` sẽ ngay lập tức render `optimisticName` trong khi yêu cầu `updateName` đang diễn ra. Khi cập nhật hoàn tất hoặc có lỗi, React sẽ tự động chuyển lại về giá trị `currentName`.

Để biết thêm thông tin, hãy xem tài liệu cho [`useOptimistic`](/reference/react/useOptimistic).

### API mới: `use` {/*new-feature-use*/}

Trong React 19 chúng tôi đang giới thiệu một API mới để đọc tài nguyên trong render: `use`.

Ví dụ, bạn có thể đọc một promise với `use`, và React sẽ Suspend cho đến khi promise resolve:

```js {1,5}
import {use} from 'react';

function Comments({commentsPromise}) {
  // `use` will suspend until the promise resolves.
  const comments = use(commentsPromise);
  return comments.map(comment => <p key={comment.id}>{comment}</p>);
}

function Page({commentsPromise}) {
  // When `use` suspends in Comments,
  // this Suspense boundary will be shown.
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Comments commentsPromise={commentsPromise} />
    </Suspense>
  )
}
```

<Note>

#### `use` không hỗ trợ các promise được tạo trong render. {/*use-does-not-support-promises-created-in-render*/}

Nếu bạn cố gắng truyền một promise được tạo trong render cho `use`, React sẽ cảnh báo:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

A component was suspended by an uncached promise. Creating promises inside a Client Component or hook is not yet supported, except via a Suspense-compatible library or framework.

</ConsoleLogLine>

</ConsoleBlockMulti>

Để sửa, bạn cần truyền một promise từ thư viện hoặc framework hỗ trợ Suspense có cache cho promise. Trong tương lai chúng tôi có kế hoạch cung cấp các tính năng để cache promise trong render dễ dàng hơn.

</Note>

Bạn cũng có thể đọc context với `use`, cho phép bạn đọc Context có điều kiện như sau early return:

```js {1,11}
import {use} from 'react';
import ThemeContext from './ThemeContext'

function Heading({children}) {
  if (children == null) {
    return null;
  }

  // This would not work with useContext
  // because of the early return.
  const theme = use(ThemeContext);
  return (
    <h1 style={{color: theme.color}}>
      {children}
    </h1>
  );
}
```

API `use` chỉ có thể được gọi trong render, tương tự như hooks. Khác với hooks, `use` có thể được gọi có điều kiện. Trong tương lai chúng tôi có kế hoạch hỗ trợ nhiều cách hơn để sử dụng tài nguyên trong render với `use`.

Để biết thêm thông tin, hãy xem tài liệu cho [`use`](/reference/react/use).

## React DOM Static APIs mới {/*new-react-dom-static-apis*/}

Chúng tôi đã thêm hai API mới vào `react-dom/static` để tạo static site:
- [`prerender`](/reference/react-dom/static/prerender)
- [`prerenderToNodeStream`](/reference/react-dom/static/prerenderToNodeStream)

Các API mới này cải thiện so với `renderToString` bằng cách chờ dữ liệu tải để tạo HTML tĩnh. Chúng được thiết kế để hoạt động với môi trường streaming như Node.js Streams và Web Streams. Ví dụ, trong môi trường Web Stream, bạn có thể prerender một React tree thành HTML tĩnh với `prerender`:

```js
import { prerender } from 'react-dom/static';

async function handler(request) {
  const {prelude} = await prerender(<App />, {
    bootstrapScripts: ['/main.js']
  });
  return new Response(prelude, {
    headers: { 'content-type': 'text/html' },
  });
}
```

Prerender APIs sẽ chờ tất cả dữ liệu tải trước khi trả về HTML stream tĩnh. Các stream có thể được chuyển đổi thành chuỗi, hoặc được gửi với phản hồi streaming. Chúng không hỗ trợ streaming nội dung khi nó tải, được hỗ trợ bởi các [React DOM server rendering APIs](/reference/react-dom/server) hiện có.

Để biết thêm thông tin, hãy xem [React DOM Static APIs](/reference/react-dom/static).

## React Server Components {/*react-server-components*/}

### Server Components {/*server-components*/}

Server Components là một tùy chọn mới cho phép render component trước, trước khi bundling, trong một môi trường tách biệt khỏi ứng dụng client hoặc máy chủ SSR của bạn. Môi trường riêng biệt này là "server" trong React Server Components. Server Components có thể chạy một lần tại build time trên CI server của bạn, hoặc chúng có thể chạy cho mỗi yêu cầu sử dụng một web server.

React 19 bao gồm tất cả các tính năng React Server Components từ kênh Canary. Điều này có nghĩa là các thư viện được giao kèm với Server Components giờ đây có thể nhắm mục tiêu React 19 như một peer dependency với `react-server` [export condition](https://github.com/reactjs/rfcs/blob/main/text/0227-server-module-conventions.md#react-server-conditional-exports) để sử dụng trong các framework hỗ trợ [Full-stack React Architecture](/learn/creating-a-react-app#which-features-make-up-the-react-teams-full-stack-architecture-vision).


<Note>

#### Làm thế nào để xây dựng hỗ trợ cho Server Components? {/*how-do-i-build-support-for-server-components*/}

Trong khi React Server Components trong React 19 ổn định và sẽ không phá vỡ giữa các phiên bản minor, các API cơ bản được sử dụng để triển khai một bundler hoặc framework React Server Components không tuân theo semver và có thể phá vỡ giữa các phiên bản minor trong React 19.x.

Để hỗ trợ React Server Components như một bundler hoặc framework, chúng tôi khuyến nghị gắn với một phiên bản React cụ thể, hoặc sử dụng bản phát hành Canary. Chúng tôi sẽ tiếp tục làm việc với các bundler và framework để ổn định các API được sử dụng để triển khai React Server Components trong tương lai.

</Note>


Để biết thêm, hãy xem tài liệu cho [React Server Components](/reference/rsc/server-components).

### Server Actions {/*server-actions*/}

Server Actions cho phép Client Components gọi các hàm async được thực thi trên server.

Khi một Server Action được định nghĩa với directive `"use server"`, framework của bạn sẽ tự động tạo một tham chiếu đến server function và truyền tham chiếu đó cho Client Component. Khi hàm đó được gọi trên client, React sẽ gửi yêu cầu đến server để thực thi hàm, và trả về kết quả.

<Note>

#### Không có directive cho Server Components. {/*there-is-no-directive-for-server-components*/}

Một hiểu lầm phổ biến là Server Components được ký hiệu bằng `"use server"`, nhưng không có directive cho Server Components. Directive `"use server"` được sử dụng cho Server Actions.

Để biết thêm thông tin, hãy xem tài liệu cho [Directives](/reference/rsc/directives).

</Note>

Server Actions có thể được tạo trong Server Components và được truyền như props cho Client Components, hoặc chúng có thể được import và sử dụng trong Client Components.

Để biết thêm, hãy xem tài liệu cho [React Server Actions](/reference/rsc/server-actions).

## Cải thiện trong React 19 {/*improvements-in-react-19*/}

### `ref` như một prop {/*ref-as-a-prop*/}

Bắt đầu từ React 19, bạn có thể truy cập `ref` như một prop cho function component:

```js [[1, 1, "ref"], [1, 2, "ref", 45], [1, 6, "ref", 14]]
function MyInput({placeholder, ref}) {
  return <input placeholder={placeholder} ref={ref} />
}

//...
<MyInput ref={ref} />
```

Các function component mới sẽ không còn cần `forwardRef`, và chúng tôi sẽ xuất bản một codemod để tự động cập nhật các component của bạn để sử dụng prop `ref` mới. Trong các phiên bản tương lai chúng tôi sẽ deprecated và xóa `forwardRef`.

<Note>

`ref` được truyền cho các class không được truyền như props vì chúng tham chiếu đến instance của component.

</Note>

### Diffs cho lỗi hydration {/*diffs-for-hydration-errors*/}

Chúng tôi cũng đã cải thiện báo cáo lỗi cho lỗi hydration trong `react-dom`. Ví dụ, thay vì ghi nhiều lỗi trong DEV mà không có thông tin về mismatch:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Warning: Text content did not match. Server: "Server" Client: "Client"
{'  '}at span
{'  '}at App

</ConsoleLogLine>

<ConsoleLogLine level="error">

Warning: An error occurred during hydration. The server HTML was replaced with client content in \<div\>.

</ConsoleLogLine>

<ConsoleLogLine level="error">

Warning: Text content did not match. Server: "Server" Client: "Client"
{'  '}at span
{'  '}at App

</ConsoleLogLine>

<ConsoleLogLine level="error">

Warning: An error occurred during hydration. The server HTML was replaced with client content in \<div\>.

</ConsoleLogLine>

<ConsoleLogLine level="error">

Uncaught Error: Text content does not match server-rendered HTML.
{'  '}at checkForUnmatchedText
{'  '}...

</ConsoleLogLine>

</ConsoleBlockMulti>

Bây giờ chúng tôi ghi lại một thông điệp duy nhất với diff của mismatch:


<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Uncaught Error: Hydration failed because the server rendered HTML didn't match the client. As a result this tree will be regenerated on the client. This can happen if an SSR-ed Client Component used:{'\n'}
\- A server/client branch `if (typeof window !== 'undefined')`.
\- Variable input such as `Date.now()` or `Math.random()` which changes each time it's called.
\- Date formatting in a user's locale which doesn't match the server.
\- External changing data without sending a snapshot of it along with the HTML.
\- Invalid HTML tag nesting.{'\n'}
It can also happen if the client has a browser extension installed which messes with the HTML before React loaded.{'\n'}
https://react.dev/link/hydration-mismatch {'\n'}
{'  '}\<App\>
{'    '}\<span\>
{'+    '}Client
{'-    '}Server{'\n'}
{'  '}at throwOnHydrationMismatch
{'  '}...

</ConsoleLogLine>

</ConsoleBlockMulti>

### `<Context>` như một provider {/*context-as-a-provider*/}

Trong React 19, bạn có thể render `<Context>` như một provider thay vì `<Context.Provider>`:


```js {5,7}
const ThemeContext = createContext('');

function App({children}) {
  return (
    <ThemeContext value="dark">
      {children}
    </ThemeContext>
  );
}
```

Các Context provider mới có thể sử dụng `<Context>` và chúng tôi sẽ xuất bản một codemod để chuyển đổi các provider hiện có. Trong các phiên bản tương lai chúng tôi sẽ deprecated `<Context.Provider>`.

### Hàm cleanup cho refs {/*cleanup-functions-for-refs*/}

Chúng tôi hiện hỗ trợ trả về hàm cleanup từ các `ref` callback:

```js {7-9}
<input
  ref={(ref) => {
    // ref created

    // NEW: return a cleanup function to reset
    // the ref when element is removed from DOM.
    return () => {
      // ref cleanup
    };
  }}
/>
```

Khi component unmount, React sẽ gọi hàm cleanup được trả về từ `ref` callback. Điều này hoạt động cho DOM refs, refs đến class component, và `useImperativeHandle`.

<Note>

Trước đây, React sẽ gọi các hàm `ref` với `null` khi unmount component. Nếu `ref` của bạn trả về một hàm cleanup, React sẽ bỏ qua bước này.

Trong các phiên bản tương lai, chúng tôi sẽ deprecated việc gọi refs với `null` khi unmount component.

</Note>

Do việc giới thiệu các hàm cleanup cho ref, việc trả về bất cứ thứ gì khác từ `ref` callback giờ đây sẽ bị TypeScript từ chối. Cách sửa thường là ngừng sử dụng implicit returns, ví dụ:

```diff [[1, 1, "("], [1, 1, ")"], [2, 2, "{", 15], [2, 2, "}", 1]]
- <div ref={current => (instance = current)} />
+ <div ref={current => {instance = current}} />
```

Code gốc đã trả về instance của `HTMLDivElement` và TypeScript sẽ không biết liệu đây có _được cho là_ một hàm cleanup hay bạn không muốn trả về hàm cleanup.

Bạn có thể codemod mẫu này với [`no-implicit-ref-callback-return`](https://github.com/eps1lon/types-react-codemod/#no-implicit-ref-callback-return).

### Giá trị khởi tạo `useDeferredValue` {/*use-deferred-value-initial-value*/}

Chúng tôi đã thêm tùy chọn `initialValue` vào `useDeferredValue`:

```js [[1, 1, "deferredValue"], [1, 4, "deferredValue"], [2, 4, "''"]]
function Search({deferredValue}) {
  // On initial render the value is ''.
  // Then a re-render is scheduled with the deferredValue.
  const value = useDeferredValue(deferredValue, '');

  return (
    <Results query={value} />
  );
}
````

Khi <CodeStep step={2}>initialValue</CodeStep> được cung cấp, `useDeferredValue` sẽ trả về nó như `value` cho lần render đầu tiên của component, và lên lịch re-render trong nền với <CodeStep step={1}>deferredValue</CodeStep> được trả về.

Để biết thêm, hãy xem [`useDeferredValue`](/reference/react/useDeferredValue).

### Hỗ trợ Document Metadata {/*support-for-metadata-tags*/}

Trong HTML, các thẻ document metadata như `<title>`, `<link>`, và `<meta>` được dành riêng để đặt trong phần `<head>` của tài liệu. Trong React, component quyết định metadata nào phù hợp cho ứng dụng có thể rất xa nơi bạn render `<head>` hoặc React không render `<head>` chút nào. Trong quá khứ, các phần tử này cần được chèn thủ công trong một effect, hoặc bởi các thư viện như [`react-helmet`](https://github.com/nfl/react-helmet), và yêu cầu xử lý cẩn thận khi server rendering một ứng dụng React.

Trong React 19, chúng tôi đang thêm hỗ trợ để render các thẻ document metadata trong component một cách tự nhiên:

```js {5-8}
function BlogPost({post}) {
  return (
    <article>
      <h1>{post.title}</h1>
      <title>{post.title}</title>
      <meta name="author" content="Josh" />
      <link rel="author" href="https://twitter.com/joshcstory/" />
      <meta name="keywords" content={post.keywords} />
      <p>
        Eee equals em-see-squared...
      </p>
    </article>
  );
}
```

Khi React render component này, nó sẽ thấy các thẻ `<title>`, `<link>` và `<meta>`, và tự động đẩy chúng lên phần `<head>` của tài liệu. Bằng cách hỗ trợ các thẻ metadata này một cách tự nhiên, chúng tôi có thể đảm bảo chúng hoạt động với các ứng dụng chỉ client, streaming SSR, và Server Components.

<Note>

#### Bạn vẫn có thể muốn một thư viện Metadata {/*you-may-still-want-a-metadata-library*/}

Đối với các trường hợp sử dụng đơn giản, việc render Document Metadata như các thẻ có thể phù hợp, nhưng các thư viện có thể cung cấp các tính năng mạnh mẽ hơn như ghi đè metadata chung bằng metadata cụ thể dựa trên route hiện tại. Các tính năng này giúp các framework và thư viện như [`react-helmet`](https://github.com/nfl/react-helmet) hỗ trợ các thẻ metadata dễ dàng hơn, thay vì thay thế chúng.

</Note>

Để biết thêm thông tin, hãy xem tài liệu cho [`<title>`](/reference/react-dom/components/title), [`<link>`](/reference/react-dom/components/link), và [`<meta>`](/reference/react-dom/components/meta).

### Hỗ trợ stylesheets {/*support-for-stylesheets*/}

Stylesheets, cả liên kết ngoài (`<link rel="stylesheet" href="...">`) và inline (`<style>...</style>`), yêu cầu định vị cẩn thận trong DOM do các quy tắc ưu tiên style. Xây dựng khả năng stylesheet cho phép composability trong các component là khó, vì vậy người dùng thường kết thúc bằng cách tải tất cả các style của họ xa các component có thể phụ thuộc vào chúng, hoặc họ sử dụng một thư viện style đóng gói sự phức tạp này.

Trong React 19, chúng tôi đang giải quyết sự phức tạp này và cung cấp tích hợp sâu hơn vào Concurrent Rendering trên Client và Streaming Rendering trên Server với hỗ trợ tích hợp cho stylesheets. Nếu bạn cho React biết `precedence` của stylesheet, nó sẽ quản lý thứ tự chèn của stylesheet trong DOM và đảm bảo rằng stylesheet (nếu ngoài) được tải trước khi tiết lộ nội dung phụ thuộc vào các quy tắc style đó.

```js {4,5,17}
function ComponentOne() {
  return (
    <Suspense fallback="loading...">
      <link rel="stylesheet" href="foo" precedence="default" />
      <link rel="stylesheet" href="bar" precedence="high" />
      <article class="foo-class bar-class">
        {...}
      </article>
    </Suspense>
  )
}

function ComponentTwo() {
  return (
    <div>
      <p>{...}</p>
      <link rel="stylesheet" href="baz" precedence="default" />  <-- will be inserted between foo & bar
    </div>
  )
}
```

Trong quá trình Server Side Rendering, React sẽ bao gồm stylesheet trong `<head>`, đảm bảo rằng trình duyệt sẽ không vẽ cho đến khi nó được tải. Nếu stylesheet được phát hiện muộn sau khi chúng tôi đã bắt đầu streaming, React sẽ đảm bảo rằng stylesheet được chèn vào `<head>` trên client trước khi tiết lộ nội dung của Suspense boundary phụ thuộc vào stylesheet đó.

Trong quá trình Client Side Rendering, React sẽ đợi các stylesheet mới được render tải trước khi commit render. Nếu bạn render component này từ nhiều nơi trong ứng dụng của bạn, React sẽ chỉ bao gồm stylesheet một lần trong tài liệu:

```js {5}
function App() {
  return <>
    <ComponentOne />
    ...
    <ComponentOne /> // won't lead to a duplicate stylesheet link in the DOM
  </>
}
```

Đối với người dùng quen với việc tải stylesheet thủ công, đây là cơ hội để đặt các stylesheet đó cạnh các component phụ thuộc vào chúng, cho phép suy luận cục bộ tốt hơn và dễ dàng hơn để đảm bảo bạn chỉ tải các stylesheet mà bạn thực sự phụ thuộc.

Các thư viện style và tích hợp style với bundlers cũng có thể áp dụng khả năng mới này, vì vậy ngay cả khi bạn không trực tiếp render các stylesheet của mình, bạn vẫn có thể được hưởng lợi khi các công cụ của bạn được nâng cấp để sử dụng tính năng này.

Để biết thêm chi tiết, hãy đọc tài liệu cho [`<link>`](/reference/react-dom/components/link) và [`<style>`](/reference/react-dom/components/style).

### Hỗ trợ async scripts {/*support-for-async-scripts*/}

Trong HTML, các script thông thường (`<script src="...">`) và deferred script (`<script defer="" src="...">`) tải theo thứ tự tài liệu, làm cho việc render các loại script này sâu trong cây component trở nên khó khăn. Async scripts (`<script async="" src="...">`) tuy nhiên sẽ tải theo thứ tự tùy ý.

Trong React 19 chúng tôi đã bao gồm hỗ trợ tốt hơn cho async scripts bằng cách cho phép bạn render chúng ở bất kỳ đâu trong cây component của bạn, bên trong các component thực sự phụ thuộc vào script, mà không cần phải quản lý việc di chuyển và loại bỏ trùng lặp các instance script.

```js {4,15}
function MyComponent() {
  return (
    <div>
      <script async={true} src="..." />
      Hello World
    </div>
  )
}

function App() {
  <html>
    <body>
      <MyComponent>
      ...
      <MyComponent> // won't lead to duplicate script in the DOM
    </body>
  </html>
}
```

Trong tất cả môi trường rendering, async scripts sẽ được deduplication để React sẽ chỉ tải và thực thi script một lần ngay cả khi nó được render bởi nhiều component khác nhau.

Trong Server Side Rendering, async scripts sẽ được bao gồm trong `<head>` và được ưu tiên sau các tài nguyên quan trọng hơn chặn việc vẽ như stylesheets, fonts, và image preloads.

Để biết thêm chi tiết, hãy đọc tài liệu cho [`<script>`](/reference/react-dom/components/script).

### Hỗ trợ preloading tài nguyên {/*support-for-preloading-resources*/}

Trong quá trình tải tài liệu ban đầu và cập nhật phía client, việc cho Trình duyệt biết về các tài nguyên mà nó có thể cần tải sớm nhất có thể có thể có tác động đáng kể đến hiệu suất trang.

React 19 bao gồm một số API mới để tải và preload tài nguyên Trình duyệt để làm cho việc xây dựng trải nghiệm tuyệt vời không bị cản trở bởi việc tải tài nguyên không hiệu quả trở nên dễ dàng nhất có thể.

```js
import { prefetchDNS, preconnect, preload, preinit } from 'react-dom'
function MyComponent() {
  preinit('https://.../path/to/some/script.js', {as: 'script' }) // loads and executes this script eagerly
  preload('https://.../path/to/font.woff', { as: 'font' }) // preloads this font
  preload('https://.../path/to/stylesheet.css', { as: 'style' }) // preloads this stylesheet
  prefetchDNS('https://...') // when you may not actually request anything from this host
  preconnect('https://...') // when you will request something but aren't sure what
}
```
```html
<!-- the above would result in the following DOM/HTML -->
<html>
  <head>
    <!-- links/scripts are prioritized by their utility to early loading, not call order -->
    <link rel="prefetch-dns" href="https://...">
    <link rel="preconnect" href="https://...">
    <link rel="preload" as="font" href="https://.../path/to/font.woff">
    <link rel="preload" as="style" href="https://.../path/to/stylesheet.css">
    <script async="" src="https://.../path/to/some/script.js"></script>
  </head>
  <body>
    ...
  </body>
</html>
```

Các API này có thể được dùng để tối ưu hóa tải trang ban đầu bằng cách chuyển việc phát hiện các tài nguyên bổ sung như fonts ra khỏi việc tải stylesheet. Chúng cũng có thể làm cho các cập nhật client nhanh hơn bằng cách prefetch một danh sách các tài nguyên được sử dụng bởi một navigation dự kiến và sau đó eagerly preload các tài nguyên đó khi nhấp chuột hoặc ngay cả khi di chuột.

Để biết thêm chi tiết, hãy xem [Resource Preloading APIs](/reference/react-dom#resource-preloading-apis).

### Tương thích với script và extension bên thứ ba {/*compatibility-with-third-party-scripts-and-extensions*/}

Chúng tôi đã cải thiện hydration để tính đến các script bên thứ ba và extension trình duyệt.

Khi hydrating, nếu một phần tử render trên client không khớp với phần tử được tìm thấy trong HTML từ server, React sẽ ép client re-render để sửa nội dung. Trước đây, nếu một phần tử được chèn bởi các script bên thứ ba hoặc extension trình duyệt, nó sẽ kích hoạt lỗi mismatch và client render.

Trong React 19, các thẻ không mong đợi trong `<head>` và `<body>` sẽ được bỏ qua, tránh các lỗi mismatch. Nếu React cần re-render toàn bộ tài liệu do mismatch hydration không liên quan, nó sẽ giữ nguyên vị trí các stylesheet được chèn bởi các script bên thứ ba và extension trình duyệt.

### Báo cáo lỗi tốt hơn {/*error-handling*/}

Chúng tôi đã cải thiện xử lý lỗi trong React 19 để loại bỏ sự trùng lặp và cung cấp các tùy chọn để xử lý các lỗi được bắt và không được bắt. Ví dụ, khi có lỗi trong render được bắt bởi một Error Boundary, trước đây React sẽ throw lỗi hai lần (một lần cho lỗi gốc, sau đó một lần nữa sau khi thất bại trong việc tự động phục hồi), và sau đó gọi `console.error` với thông tin về nơi lỗi xảy ra.

Điều này dẫn đến ba lỗi cho mỗi lỗi được bắt:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Uncaught Error: hit
{'  '}at Throws
{'  '}at renderWithHooks
{'  '}...

</ConsoleLogLine>

<ConsoleLogLine level="error">

Uncaught Error: hit<span className="ms-2 text-gray-30">{'    <--'} Duplicate</span>
{'  '}at Throws
{'  '}at renderWithHooks
{'  '}...

</ConsoleLogLine>

<ConsoleLogLine level="error">

The above error occurred in the Throws component:
{'  '}at Throws
{'  '}at ErrorBoundary
{'  '}at App{'\n'}
React will try to recreate this component tree from scratch using the error boundary you provided, ErrorBoundary.

</ConsoleLogLine>

</ConsoleBlockMulti>

Trong React 19, chúng tôi ghi lại một lỗi duy nhất với tất cả thông tin lỗi được bao gồm:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Error: hit
{'  '}at Throws
{'  '}at renderWithHooks
{'  '}...{'\n'}
The above error occurred in the Throws component:
{'  '}at Throws
{'  '}at ErrorBoundary
{'  '}at App{'\n'}
React will try to recreate this component tree from scratch using the error boundary you provided, ErrorBoundary.
{'  '}at ErrorBoundary
{'  '}at App

</ConsoleLogLine>

</ConsoleBlockMulti>

Ngoài ra, chúng tôi đã thêm hai tùy chọn root mới để bổ sung cho `onRecoverableError`:

- `onCaughtError`: được gọi khi React bắt một lỗi trong Error Boundary.
- `onUncaughtError`: được gọi khi một lỗi được throw và không được bắt bởi Error Boundary.
- `onRecoverableError`: được gọi khi một lỗi được throw và tự động phục hồi.

Để biết thêm thông tin và ví dụ, hãy xem tài liệu cho [`createRoot`](/reference/react-dom/client/createRoot) và [`hydrateRoot`](/reference/react-dom/client/hydrateRoot).

### Hỗ trợ Custom Elements {/*support-for-custom-elements*/}

React 19 thêm hỗ trợ đầy đủ cho custom elements và vượt qua tất cả các bài kiểm tra trên [Custom Elements Everywhere](https://custom-elements-everywhere.com/).

Trong các phiên bản trước, việc sử dụng Custom Elements trong React đã khó khăn vì React xử lý các props không được nhận dạng như attributes thay vì properties. Trong React 19, chúng tôi đã thêm hỗ trợ cho properties hoạt động trên client và trong SSR với chiến lược sau:

- **Server Side Rendering**: props được truyền cho custom element sẽ render như attributes nếu kiểu của chúng là giá trị primitive như `string`, `number`, hoặc giá trị là `true`. Props với kiểu không phải primitive như `object`, `symbol`, `function`, hoặc giá trị `false` sẽ bị bỏ qua.
- **Client Side Rendering**: props khớp với property trên instance Custom Element sẽ được gán như properties, nếu không thì chúng sẽ được gán như attributes.

Cảm ơn [Joey Arhar](https://github.com/josepharhar) vì đã thúc đẩy thiết kế và triển khai hỗ trợ Custom Element trong React.


#### Cách nâng cấp {/*how-to-upgrade*/}
Xem [Hướng dẫn nâng cấp React 19](/blog/2024/04/25/react-19-upgrade-guide) để có hướng dẫn từng bước và danh sách đầy đủ các thay đổi phá vỡ và đáng chú ý.

_Lưu ý: bài viết này được xuất bản ban đầu vào 04/25/2024 và đã được cập nhật vào 12/05/2024 với bản phát hành ổn định._
