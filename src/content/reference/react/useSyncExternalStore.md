---
title: useSyncExternalStore
---

<Intro>

`useSyncExternalStore` là một React Hook cho phép bạn subscribe đến một external store.

```js
const snapshot = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?)
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?)` {/*usesyncexternalstore*/}

Gọi `useSyncExternalStore` ở cấp cao nhất của component để đọc một giá trị từ một external data store.

```js
import { useSyncExternalStore } from 'react';
import { todosStore } from './todoStore.js';

function TodosApp() {
  const todos = useSyncExternalStore(todosStore.subscribe, todosStore.getSnapshot);
  // ...
}
```

Nó trả về snapshot của dữ liệu trong store. Bạn cần truyền hai hàm như đối số:

1. Hàm `subscribe` nên subscribe vào store và trả về một hàm unsubscribe.
2. Hàm `getSnapshot` nên đọc một snapshot của dữ liệu từ store.

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `subscribe`: Một hàm nhận một đối số `callback` và subscribe nó vào store. Khi store thay đổi, nó nên gọi `callback` được cung cấp, điều này sẽ khiến React gọi lại `getSnapshot` và (nếu cần) re-render component. Hàm `subscribe` nên trả về một hàm dọn dẹp subscription.

* `getSnapshot`: Một hàm trả về snapshot của dữ liệu trong store cần thiết bởi component. Trong khi store chưa thay đổi, các lời gọi lặp lại `getSnapshot` phải trả về cùng giá trị. Nếu store thay đổi và giá trị trả về khác nhau (so sánh bởi [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is)), React re-render component.

* **tùy chọn** `getServerSnapshot`: Một hàm trả về snapshot ban đầu của dữ liệu trong store. Nó sẽ chỉ được sử dụng trong quá trình server rendering và trong quá trình hydration nội dung được render từ server trên client. Server snapshot phải giống nhau giữa client và server, và thường được serialize và truyền từ server đến client. Nếu bạn bỏ qua đối số này, render component trên server sẽ ném ra lỗi.

#### Giá trị trả về {/*returns*/}

Snapshot hiện tại của store mà bạn có thể sử dụng trong logic rendering của mình.

#### Lưu ý {/*caveats*/}

* Store snapshot được trả về bởi `getSnapshot` phải là bất biến. Nếu store bên dưới có dữ liệu có thể thay đổi, hãy trả về một snapshot bất biến mới nếu dữ liệu đã thay đổi. Nếu không, hãy trả về snapshot đã được cache lần cuối.

* Nếu một hàm `subscribe` khác được truyền trong quá trình re-render, React sẽ re-subscribe vào store bằng hàm `subscribe` mới được truyền. Bạn có thể ngăn điều này bằng cách khai báo `subscribe` bên ngoài component.

* Nếu store bị mutate trong quá trình [cập nhật Transition không chặn](/reference/react/useTransition), React sẽ rơi lại thực hiện cập nhật đó như là chặn. Cụ thể, đối với mỗi cập nhật Transition, React sẽ gọi `getSnapshot` lần thứ hai ngay trước khi áp dụng các thay đổi vào DOM. Nếu nó trả về giá trị khác với khi được gọi ban đầu, React sẽ khởi động lại cập nhật từ đầu, lần này áp dụng nó như một cập nhật chặn, để đảm bảo rằng mọi component trên màn hình đều phản ánh cùng phiên bản của store.

* Không khuyến nghị _suspend_ một lần render dựa trên giá trị store được trả về bởi `useSyncExternalStore`. Lý do là các mutation cho external store không thể được đánh dấu là [cập nhật Transition không chặn](/reference/react/useTransition), vì vậy chúng sẽ kích hoạt [fallback `Suspense`](/reference/react/Suspense) gần nhất, thay thế nội dung đã được render trên màn hình bằng loading spinner, thường tạo ra trải nghiệm người dùng kém.

  Ví dụ, những điều sau đây không được khuyến khích:

  ```js
  const LazyProductDetailPage = lazy(() => import('./ProductDetailPage.js'));

  function ShoppingApp() {
    const selectedProductId = useSyncExternalStore(...);

    // ❌ Gọi `use` với Promise phụ thuộc vào `selectedProductId`
    const data = use(fetchItem(selectedProductId))

    // ❌ Render có điều kiện một lazy component dựa trên `selectedProductId`
    return selectedProductId != null ? <LazyProductDetailPage /> : <FeaturedProducts />;
  }
  ```

---

## Cách sử dụng {/*usage*/}

### Subscribe vào external store {/*subscribing-to-an-external-store*/}

Hầu hết các React component của bạn sẽ chỉ đọc dữ liệu từ [props,](/learn/passing-props-to-a-component) [state,](/reference/react/useState) và [context.](/reference/react/useContext) Tuy nhiên, đôi khi một component cần đọc một số dữ liệu từ một store bên ngoài React thay đổi theo thời gian. Điều này bao gồm:

* Các thư viện quản lý state bên thứ ba giữ state bên ngoài React.
* Các browser API hiển thị một giá trị có thể thay đổi và các sự kiện để subscribe vào các thay đổi của nó.

Gọi `useSyncExternalStore` ở cấp cao nhất của component để đọc một giá trị từ một external data store.

```js [[1, 5, "todosStore.subscribe"], [2, 5, "todosStore.getSnapshot"], [3, 5, "todos", 0]]
import { useSyncExternalStore } from 'react';
import { todosStore } from './todoStore.js';

function TodosApp() {
  const todos = useSyncExternalStore(todosStore.subscribe, todosStore.getSnapshot);
  // ...
}
```

Nó trả về <CodeStep step={3}>snapshot</CodeStep> của dữ liệu trong store. Bạn cần truyền hai hàm như đối số:

1. <CodeStep step={1}>Hàm `subscribe`</CodeStep> nên subscribe vào store và trả về một hàm unsubscribe.
2. <CodeStep step={2}>Hàm `getSnapshot`</CodeStep> nên đọc một snapshot của dữ liệu từ store.

React sẽ sử dụng các hàm này để giữ component của bạn subscribe vào store và re-render nó khi có thay đổi.

Ví dụ, trong sandbox bên dưới, `todosStore` được triển khai như một external store lưu trữ dữ liệu bên ngoài React. Component `TodosApp` kết nối với external store đó bằng Hook `useSyncExternalStore`.

<Sandpack>

```js
import { useSyncExternalStore } from 'react';
import { todosStore } from './todoStore.js';

export default function TodosApp() {
  const todos = useSyncExternalStore(todosStore.subscribe, todosStore.getSnapshot);
  return (
    <>
      <button onClick={() => todosStore.addTodo()}>Add todo</button>
      <hr />
      <ul>
        {todos.map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </>
  );
}
```

```js src/todoStore.js
// This is an example of a third-party store
// that you might need to integrate with React.

// If your app is fully built with React,
// we recommend using React state instead.

let nextId = 0;
let todos = [{ id: nextId++, text: 'Todo #1' }];
let listeners = [];

export const todosStore = {
  addTodo() {
    todos = [...todos, { id: nextId++, text: 'Todo #' + nextId }]
    emitChange();
  },
  subscribe(listener) {
    listeners = [...listeners, listener];
    return () => {
      listeners = listeners.filter(l => l !== listener);
    };
  },
  getSnapshot() {
    return todos;
  }
};

function emitChange() {
  for (let listener of listeners) {
    listener();
  }
}
```

</Sandpack>

<Note>

Khi có thể, chúng tôi khuyến nghị sử dụng React state được tích hợp sẵn với [`useState`](/reference/react/useState) và [`useReducer`](/reference/react/useReducer) thay thế. API `useSyncExternalStore` chủ yếu hữu ích nếu bạn cần tích hợp với code non-React hiện có.

</Note>

---

### Subscribe vào một browser API {/*subscribing-to-a-browser-api*/}

Một lý do khác để thêm `useSyncExternalStore` là khi bạn muốn subscribe vào một số giá trị do trình duyệt hiển thị thay đổi theo thời gian. Ví dụ, giả sử bạn muốn component của bạn hiển thị liệu kết nối mạng có đang hoạt động hay không. Trình duyệt hiển thị thông tin này qua một thuộc tính gọi là [`navigator.onLine`.](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/onLine)

Giá trị này có thể thay đổi mà React không biết, vì vậy bạn nên đọc nó với `useSyncExternalStore`.

```js
import { useSyncExternalStore } from 'react';

function ChatIndicator() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  // ...
}
```

Để triển khai hàm `getSnapshot`, hãy đọc giá trị hiện tại từ browser API:

```js
function getSnapshot() {
  return navigator.onLine;
}
```

Tiếp theo, bạn cần triển khai hàm `subscribe`. Ví dụ, khi `navigator.onLine` thay đổi, trình duyệt kích hoạt các sự kiện [`online`](https://developer.mozilla.org/en-US/docs/Web/API/Window/online_event) và [`offline`](https://developer.mozilla.org/en-US/docs/Web/API/Window/offline_event) trên object `window`. Bạn cần subscribe đối số `callback` vào các sự kiện tương ứng, và sau đó trả về một hàm dọn dẹp subscriptions:

```js
function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}
```

Bây giờ React biết cách đọc giá trị từ external API `navigator.onLine` và cách subscribe vào các thay đổi của nó. Ngắt kết nối thiết bị của bạn khỏi mạng và lưu ý rằng component re-render để phản hồi:

<Sandpack>

```js
import { useSyncExternalStore } from 'react';

export default function ChatIndicator() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}

function getSnapshot() {
  return navigator.onLine;
}

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}
```

</Sandpack>

---

### Trích xuất logic vào một custom Hook {/*extracting-the-logic-to-a-custom-hook*/}

Thông thường bạn sẽ không viết `useSyncExternalStore` trực tiếp trong các component của mình. Thay vào đó, bạn thường gọi nó từ custom Hook của riêng bạn. Điều này cho phép bạn sử dụng cùng một external store từ các component khác nhau.

Ví dụ, custom Hook `useOnlineStatus` này theo dõi liệu mạng có đang online hay không:

```js {3,6}
import { useSyncExternalStore } from 'react';

export function useOnlineStatus() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  return isOnline;
}

function getSnapshot() {
  // ...
}

function subscribe(callback) {
  // ...
}
```

Bây giờ các component khác nhau có thể gọi `useOnlineStatus` mà không lặp lại triển khai bên dưới:

<Sandpack>

```js
import { useOnlineStatus } from './useOnlineStatus.js';

function StatusBar() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}

function SaveButton() {
  const isOnline = useOnlineStatus();

  function handleSaveClick() {
    console.log('✅ Progress saved');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? 'Save progress' : 'Reconnecting...'}
    </button>
  );
}

export default function App() {
  return (
    <>
      <SaveButton />
      <StatusBar />
    </>
  );
}
```

```js src/useOnlineStatus.js
import { useSyncExternalStore } from 'react';

export function useOnlineStatus() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  return isOnline;
}

function getSnapshot() {
  return navigator.onLine;
}

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}
```

</Sandpack>

---

### Thêm hỗ trợ cho server rendering {/*adding-support-for-server-rendering*/}

Nếu ứng dụng React của bạn sử dụng [server rendering,](/reference/react-dom/server) các React component của bạn cũng sẽ chạy bên ngoài môi trường trình duyệt để tạo HTML ban đầu. Điều này tạo ra một số thách thức khi kết nối với external store:

- Nếu bạn kết nối với browser-only API, nó sẽ không hoạt động vì nó không tồn tại trên server.
- Nếu bạn kết nối với một third-party data store, bạn sẽ cần dữ liệu của nó khớp giữa server và client.

Để giải quyết những vấn đề này, hãy truyền hàm `getServerSnapshot` như đối số thứ ba cho `useSyncExternalStore`:

```js {4,12-14}
import { useSyncExternalStore } from 'react';

export function useOnlineStatus() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot);
  return isOnline;
}

function getSnapshot() {
  return navigator.onLine;
}

function getServerSnapshot() {
  return true; // Luôn hiển thị "Online" cho HTML được tạo từ server
}

function subscribe(callback) {
  // ...
}
```

Hàm `getServerSnapshot` tương tự như `getSnapshot`, nhưng nó chỉ chạy trong hai trường hợp:

- Nó chạy trên server khi tạo HTML.
- Nó chạy trên client trong quá trình [hydration](/reference/react-dom/client/hydrateRoot), tức là khi React lấy HTML từ server và làm cho nó tương tác.

Điều này cho phép bạn cung cấp giá trị snapshot ban đầu sẽ được sử dụng trước khi ứng dụng trở nên tương tác. Nếu không có giá trị ban đầu có ý nghĩa cho server rendering, hãy bỏ qua đối số này để [buộc rendering trên client.](/reference/react/Suspense#providing-a-fallback-for-server-errors-and-client-only-content)

<Note>

Đảm bảo rằng `getServerSnapshot` trả về chính xác cùng dữ liệu trên lần render client ban đầu như nó đã trả về trên server. Ví dụ, nếu `getServerSnapshot` trả về một số nội dung store đã được điền sẵn trên server, bạn cần chuyển nội dung này đến client. Một cách để làm điều này là phát ra một thẻ `<script>` trong quá trình server rendering đặt một global như `window.MY_STORE_DATA`, và đọc từ global đó trên client trong `getServerSnapshot`. External store của bạn nên cung cấp hướng dẫn về cách làm điều đó.

</Note>

---

## Xử lý sự cố {/*troubleshooting*/}

### Tôi nhận được lỗi: "The result of `getSnapshot` should be cached" {/*im-getting-an-error-the-result-of-getsnapshot-should-be-cached*/}

Lỗi này có nghĩa là hàm `getSnapshot` của bạn trả về một object mới mỗi lần được gọi, ví dụ:

```js {2-5}
function getSnapshot() {
  // 🔴 Đừng luôn trả về các object khác nhau từ getSnapshot
  return {
    todos: myStore.todos
  };
}
```

React sẽ re-render component nếu giá trị trả về `getSnapshot` khác với lần trước. Đây là lý do tại sao, nếu bạn luôn trả về một giá trị khác nhau, bạn sẽ vào vòng lặp vô hạn và nhận được lỗi này.

Object `getSnapshot` của bạn chỉ nên trả về một object khác nếu có gì đó thực sự đã thay đổi. Nếu store của bạn chứa dữ liệu bất biến, bạn có thể trả về dữ liệu đó trực tiếp:

```js {2-3}
function getSnapshot() {
  // ✅ Bạn có thể trả về dữ liệu bất biến
  return myStore.todos;
}
```

Nếu dữ liệu store của bạn có thể thay đổi, hàm `getSnapshot` của bạn nên trả về một snapshot bất biến của nó. Điều này có nghĩa là nó *cần* tạo các object mới, nhưng nó không nên làm điều này cho mỗi lần gọi. Thay vào đó, nó nên lưu trữ snapshot đã được tính toán cuối cùng, và trả về cùng snapshot như lần trước nếu dữ liệu trong store chưa thay đổi. Cách bạn xác định liệu dữ liệu có thể thay đổi có thay đổi hay không phụ thuộc vào mutable store của bạn.

---

### Hàm `subscribe` của tôi được gọi sau mỗi lần re-render {/*my-subscribe-function-gets-called-after-every-re-render*/}

Hàm `subscribe` này được định nghĩa *bên trong* một component nên nó khác nhau trên mỗi lần re-render:

```js {2-5}
function ChatIndicator() {
  // 🚩 Luôn là hàm khác nhau, vì vậy React sẽ re-subscribe trên mỗi lần re-render
  function subscribe() {
    // ...
  }

  const isOnline = useSyncExternalStore(subscribe, getSnapshot);

  // ...
}
```

React sẽ re-subscribe vào store của bạn nếu bạn truyền một hàm `subscribe` khác giữa các lần re-render. Nếu điều này gây ra vấn đề hiệu suất và bạn muốn tránh re-subscribe, hãy chuyển hàm `subscribe` ra ngoài:

```js {1-4}
// ✅ Luôn là cùng một hàm, vì vậy React sẽ không cần re-subscribe
function subscribe() {
  // ...
}

function ChatIndicator() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  // ...
}
```

Ngoài ra, hãy bọc `subscribe` vào [`useCallback`](/reference/react/useCallback) để chỉ re-subscribe khi một đối số nào đó thay đổi:

```js {2-5}
function ChatIndicator({ userId }) {
  // ✅ Cùng hàm miễn là userId không thay đổi
  const subscribe = useCallback(() => {
    // ...
  }, [userId]);

  const isOnline = useSyncExternalStore(subscribe, getSnapshot);

  // ...
}
```
