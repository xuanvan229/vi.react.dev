---
title: <StrictMode>
---


<Intro>

`<StrictMode>` cho phép bạn tìm thấy các lỗi phổ biến trong các component của bạn sớm trong quá trình phát triển.


```js
<StrictMode>
  <App />
</StrictMode>
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `<StrictMode>` {/*strictmode*/}

Sử dụng `StrictMode` để bật các hành vi phát triển bổ sung và cảnh báo cho cây component bên trong:

```js
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

[Xem thêm ví dụ bên dưới.](#usage)

Strict Mode bật các hành vi chỉ dành cho môi trường phát triển sau:

- Các component của bạn sẽ [re-render thêm một lần](#fixing-bugs-found-by-double-rendering-in-development) để tìm lỗi gây ra bởi rendering không pure.
- Các component của bạn sẽ [chạy lại Effect thêm một lần](#fixing-bugs-found-by-re-running-effects-in-development) để tìm lỗi gây ra bởi việc thiếu Effect cleanup.
- Các component của bạn sẽ [chạy lại ref callback thêm một lần](#fixing-bugs-found-by-re-running-ref-callbacks-in-development) để tìm lỗi gây ra bởi việc thiếu ref cleanup.
- Các component của bạn sẽ [được kiểm tra xem có sử dụng API đã bị deprecated không.](#fixing-deprecation-warnings-enabled-by-strict-mode)

#### Props {/*props*/}

`StrictMode` không chấp nhận props nào.

#### Lưu ý {/*caveats*/}

* Không có cách nào để tắt Strict Mode bên trong một cây được bọc trong `<StrictMode>`. Điều này cho bạn sự đảm bảo rằng tất cả các component bên trong `<StrictMode>` đều được kiểm tra. Nếu hai nhóm làm việc trên một sản phẩm không đồng ý về việc liệu các kiểm tra có hữu ích hay không, họ cần đạt được sự đồng thuận hoặc di chuyển `<StrictMode>` xuống trong cây.

---

## Cách sử dụng {/*usage*/}

### Bật Strict Mode cho toàn bộ ứng dụng {/*enabling-strict-mode-for-entire-app*/}

Strict Mode bật các kiểm tra chỉ dành cho môi trường phát triển cho toàn bộ cây component bên trong component `<StrictMode>`. Các kiểm tra này giúp bạn tìm thấy các lỗi phổ biến trong các component của bạn sớm trong quá trình phát triển.


Để bật Strict Mode cho toàn bộ ứng dụng, bọc component gốc của bạn với `<StrictMode>` khi bạn render nó:

```js {6,8}
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

Chúng tôi khuyến nghị bọc toàn bộ ứng dụng của bạn trong Strict Mode, đặc biệt là đối với các ứng dụng mới tạo. Nếu bạn sử dụng framework gọi [`createRoot`](/reference/react-dom/client/createRoot) thay cho bạn, hãy kiểm tra tài liệu của nó để biết cách bật Strict Mode.

Mặc dù các kiểm tra Strict Mode **chỉ chạy trong môi trường phát triển,** chúng giúp bạn tìm thấy các lỗi đã tồn tại trong code của bạn nhưng có thể khó tái tạo một cách đáng tin cậy trong production. Strict Mode cho phép bạn sửa lỗi trước khi người dùng báo cáo chúng.

<Note>

Strict Mode bật các kiểm tra sau trong môi trường phát triển:

- Các component của bạn sẽ [re-render thêm một lần](#fixing-bugs-found-by-double-rendering-in-development) để tìm lỗi gây ra bởi rendering không pure.
- Các component của bạn sẽ [chạy lại Effect thêm một lần](#fixing-bugs-found-by-re-running-effects-in-development) để tìm lỗi gây ra bởi việc thiếu Effect cleanup.
- Các component của bạn sẽ [chạy lại ref callback thêm một lần](#fixing-bugs-found-by-re-running-ref-callbacks-in-development) để tìm lỗi gây ra bởi việc thiếu ref cleanup.
- Các component của bạn sẽ [được kiểm tra xem có sử dụng API đã bị deprecated không.](#fixing-deprecation-warnings-enabled-by-strict-mode)

**Tất cả các kiểm tra này chỉ dành cho môi trường phát triển và không ảnh hưởng đến bản build production.**

</Note>

---

### Bật Strict Mode cho một phần của ứng dụng {/*enabling-strict-mode-for-a-part-of-the-app*/}

Bạn cũng có thể bật Strict Mode cho bất kỳ phần nào của ứng dụng:

```js {7,12}
import { StrictMode } from 'react';

function App() {
  return (
    <>
      <Header />
      <StrictMode>
        <main>
          <Sidebar />
          <Content />
        </main>
      </StrictMode>
      <Footer />
    </>
  );
}
```

Trong ví dụ này, các kiểm tra Strict Mode sẽ không chạy đối với các component `Header` và `Footer`. Tuy nhiên, chúng sẽ chạy trên `Sidebar` và `Content`, cũng như tất cả các component bên trong chúng, bất kể sâu đến đâu.

<Note>

Khi `StrictMode` được bật cho một phần của ứng dụng, React sẽ chỉ bật các hành vi có thể xảy ra trong production. Ví dụ, nếu `<StrictMode>` không được bật ở root của ứng dụng, nó sẽ không [chạy lại Effect thêm một lần](#fixing-bugs-found-by-re-running-effects-in-development) khi mount lần đầu, vì điều này sẽ khiến các effect con kích hoạt gấp đôi mà không có effect cha, điều không thể xảy ra trong production.

</Note>

---

### Sửa lỗi được tìm thấy bởi double rendering trong môi trường phát triển {/*fixing-bugs-found-by-double-rendering-in-development*/}

[React giả định rằng mỗi component bạn viết là một hàm pure.](/learn/keeping-components-pure) Điều này có nghĩa là các React component bạn viết phải luôn trả về cùng JSX với cùng đầu vào (props, state, và context).

Các component vi phạm quy tắc này hoạt động không thể đoán trước và gây ra lỗi. Để giúp bạn tìm thấy code không pure vô tình, Strict Mode gọi một số hàm của bạn (chỉ những hàm phải pure) **hai lần trong môi trường phát triển.** Điều này bao gồm:

- Phần thân hàm component của bạn (chỉ logic cấp cao nhất, vì vậy điều này không bao gồm code bên trong event handler)
- Các hàm bạn truyền cho [`useState`](/reference/react/useState), [hàm `set`](/reference/react/useState#setstate), [`useMemo`](/reference/react/useMemo), hoặc [`useReducer`](/reference/react/useReducer)
- Một số phương thức class component như [`constructor`](/reference/react/Component#constructor), [`render`](/reference/react/Component#render), [`shouldComponentUpdate`](/reference/react/Component#shouldcomponentupdate) ([xem danh sách đầy đủ](https://reactjs.org/docs/strict-mode.html#detecting-unexpected-side-effects))

Nếu một hàm là pure, chạy nó hai lần không thay đổi hành vi của nó vì một hàm pure tạo ra cùng kết quả mỗi lần. Tuy nhiên, nếu một hàm không pure (ví dụ, nó mutate dữ liệu nó nhận), chạy nó hai lần có xu hướng dễ nhận ra (đó là điều làm cho nó không pure!) Điều này giúp bạn phát hiện và sửa lỗi sớm.

**Đây là một ví dụ để minh họa cách double rendering trong Strict Mode giúp bạn tìm thấy lỗi sớm.**

Component `StoryTray` này nhận một mảng `stories` và thêm một mục "Create Story" cuối cùng vào cuối:

<Sandpack>

```js src/index.js
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
root.render(<App />);
```

```js src/App.js
import { useState } from 'react';
import StoryTray from './StoryTray.js';

let initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  let [stories, setStories] = useState(initialStories)
  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <StoryTray stories={stories} />
    </div>
  );
}
```

```js src/StoryTray.js active
export default function StoryTray({ stories }) {
  const items = stories;
  items.push({ id: 'create', label: 'Create Story' });
  return (
    <ul>
      {items.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
    </ul>
  );
}
```

```css
ul {
  margin: 0;
  list-style-type: none;
  height: 100%;
  display: flex;
  flex-wrap: wrap;
  padding: 10px;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

</Sandpack>

Có một lỗi trong code ở trên. Tuy nhiên, rất dễ bỏ sót vì output ban đầu có vẻ đúng.

Lỗi này sẽ trở nên rõ ràng hơn nếu component `StoryTray` re-render nhiều lần. Ví dụ, hãy làm cho `StoryTray` re-render với màu nền khác mỗi khi bạn di chuột qua nó:

<Sandpack>

```js src/index.js
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

```js src/App.js
import { useState } from 'react';
import StoryTray from './StoryTray.js';

let initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  let [stories, setStories] = useState(initialStories)
  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <StoryTray stories={stories} />
    </div>
  );
}
```

```js src/StoryTray.js active
import { useState } from 'react';

export default function StoryTray({ stories }) {
  const [isHover, setIsHover] = useState(false);
  const items = stories;
  items.push({ id: 'create', label: 'Create Story' });
  return (
    <ul
      onPointerEnter={() => setIsHover(true)}
      onPointerLeave={() => setIsHover(false)}
      style={{
        backgroundColor: isHover ? '#ddd' : '#fff'
      }}
    >
      {items.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
    </ul>
  );
}
```

```css
ul {
  margin: 0;
  list-style-type: none;
  height: 100%;
  display: flex;
  flex-wrap: wrap;
  padding: 10px;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

</Sandpack>

Lưu ý cách mỗi lần bạn di chuột qua component `StoryTray`, "Create Story" được thêm vào danh sách lại. Ý định của code là thêm nó một lần vào cuối. Nhưng `StoryTray` trực tiếp sửa đổi mảng `stories` từ props. Mỗi lần `StoryTray` render, nó thêm "Create Story" lại vào cuối cùng mảng. Nói cách khác, `StoryTray` không phải là hàm pure--chạy nó nhiều lần tạo ra kết quả khác nhau.

Để sửa vấn đề này, bạn có thể tạo một bản sao của mảng, và sửa đổi bản sao đó thay vì mảng gốc:

```js {2}
export default function StoryTray({ stories }) {
  const items = stories.slice(); // Clone the array
  // ✅ Good: Pushing into a new array
  items.push({ id: 'create', label: 'Create Story' });
```

Điều này sẽ [làm cho hàm `StoryTray` trở thành pure.](/learn/keeping-components-pure) Mỗi lần nó được gọi, nó chỉ sửa đổi một bản sao mới của mảng, và sẽ không ảnh hưởng đến bất kỳ đối tượng hoặc biến bên ngoài nào. Điều này giải quyết lỗi, nhưng bạn phải làm cho component re-render thường xuyên hơn trước khi rõ ràng rằng có gì đó sai với hành vi của nó.

**Trong ví dụ gốc, lỗi không rõ ràng. Bây giờ hãy bọc code gốc (có lỗi) trong `<StrictMode>`:**

<Sandpack>

```js src/index.js
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

```js src/App.js
import { useState } from 'react';
import StoryTray from './StoryTray.js';

let initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  let [stories, setStories] = useState(initialStories)
  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <StoryTray stories={stories} />
    </div>
  );
}
```

```js src/StoryTray.js active
export default function StoryTray({ stories }) {
  const items = stories;
  items.push({ id: 'create', label: 'Create Story' });
  return (
    <ul>
      {items.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
    </ul>
  );
}
```

```css
ul {
  margin: 0;
  list-style-type: none;
  height: 100%;
  display: flex;
  flex-wrap: wrap;
  padding: 10px;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

</Sandpack>

**Strict Mode *luôn* gọi hàm rendering của bạn hai lần, vì vậy bạn có thể thấy lỗi ngay lập tức** ("Create Story" xuất hiện hai lần). Điều này cho phép bạn nhận ra những lỗi như vậy sớm trong quá trình. Khi bạn sửa component để render trong Strict Mode, bạn *cũng* sửa nhiều lỗi production có thể xảy ra trong tương lai như chức năng hover trước đó:

<Sandpack>

```js src/index.js
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById('root'));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

```js src/App.js
import { useState } from 'react';
import StoryTray from './StoryTray.js';

let initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  let [stories, setStories] = useState(initialStories)
  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <StoryTray stories={stories} />
    </div>
  );
}
```

```js src/StoryTray.js active
import { useState } from 'react';

export default function StoryTray({ stories }) {
  const [isHover, setIsHover] = useState(false);
  const items = stories.slice(); // Clone the array
  items.push({ id: 'create', label: 'Create Story' });
  return (
    <ul
      onPointerEnter={() => setIsHover(true)}
      onPointerLeave={() => setIsHover(false)}
      style={{
        backgroundColor: isHover ? '#ddd' : '#fff'
      }}
    >
      {items.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
    </ul>
  );
}
```

```css
ul {
  margin: 0;
  list-style-type: none;
  height: 100%;
  display: flex;
  flex-wrap: wrap;
  padding: 10px;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

</Sandpack>

Không có Strict Mode, rất dễ bỏ sót lỗi cho đến khi bạn thêm nhiều re-render hơn. Strict Mode làm cho cùng lỗi đó xuất hiện ngay lập tức. Strict Mode giúp bạn tìm thấy lỗi trước khi bạn đẩy chúng đến nhóm và người dùng của bạn.

[Đọc thêm về việc giữ các component pure.](/learn/keeping-components-pure)

<Note>

Nếu bạn đã cài đặt [React DevTools](/learn/react-developer-tools), bất kỳ cuộc gọi `console.log` nào trong lần render thứ hai sẽ xuất hiện hơi mờ. React DevTools cũng cung cấp một cài đặt (tắt theo mặc định) để tắt chúng hoàn toàn.

</Note>

---

### Sửa lỗi được tìm thấy bởi việc chạy lại Effect trong môi trường phát triển {/*fixing-bugs-found-by-re-running-effects-in-development*/}

Strict Mode cũng có thể giúp tìm lỗi trong [Effects.](/learn/synchronizing-with-effects)

Mỗi Effect có một số code setup và có thể có một số code cleanup. Thông thường, React gọi setup khi component *mount* (được thêm vào màn hình) và gọi cleanup khi component *unmount* (bị xóa khỏi màn hình). React sau đó gọi cleanup và setup lại nếu các dependency của nó thay đổi kể từ lần render trước.

Khi Strict Mode được bật, React cũng sẽ chạy **một chu kỳ setup+cleanup bổ sung trong môi trường phát triển cho mỗi Effect.** Điều này có thể cảm thấy bất ngờ, nhưng nó giúp phát lộ các lỗi tinh tế khó bắt thủ công.

**Đây là một ví dụ để minh họa cách chạy lại Effect trong Strict Mode giúp bạn tìm thấy lỗi sớm.**

Hãy xem ví dụ này kết nối một component với chat:

<Sandpack>

```js src/index.js
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
root.render(<App />);
```

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';
const roomId = 'general';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
  }, []);
  return <h1>Welcome to the {roomId} room!</h1>;
}
```

```js src/chat.js
let connections = 0;

export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
      connections++;
      console.log('Active connections: ' + connections);
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
      connections--;
      console.log('Active connections: ' + connections);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

Có một vấn đề với code này, nhưng nó có thể không rõ ràng ngay lập tức.

Để làm cho vấn đề rõ ràng hơn, hãy triển khai một tính năng. Trong ví dụ bên dưới, `roomId` không được hardcode. Thay vào đó, người dùng có thể chọn `roomId` họ muốn kết nối từ một dropdown. Nhấp vào "Open chat" rồi chọn các phòng chat khác nhau từng cái một. Theo dõi số lượng kết nối đang hoạt động trong console:

<Sandpack>

```js src/index.js
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
root.render(<App />);
```

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
  }, [roomId]);

  return <h1>Welcome to the {roomId} room!</h1>;
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [show, setShow] = useState(false);
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <button onClick={() => setShow(!show)}>
        {show ? 'Close chat' : 'Open chat'}
      </button>
      {show && <hr />}
      {show && <ChatRoom roomId={roomId} />}
    </>
  );
}
```

```js src/chat.js
let connections = 0;

export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
      connections++;
      console.log('Active connections: ' + connections);
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
      connections--;
      console.log('Active connections: ' + connections);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

Bạn sẽ nhận thấy rằng số lượng kết nối mở luôn tiếp tục tăng. Trong ứng dụng thực, điều này sẽ gây ra vấn đề về hiệu suất và mạng. Vấn đề là [Effect của bạn thiếu hàm cleanup:](/learn/synchronizing-with-effects#step-3-add-cleanup-if-needed)

```js {4}
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);
```

Bây giờ Effect của bạn "dọn dẹp" sau chính nó và hủy các kết nối đã lỗi thời, sự rò rỉ đã được giải quyết. Tuy nhiên, hãy chú ý rằng vấn đề không trở nên rõ ràng cho đến khi bạn thêm nhiều tính năng hơn (hộp select).

**Trong ví dụ gốc, lỗi không rõ ràng. Bây giờ hãy bọc code gốc (có lỗi) trong `<StrictMode>`:**

<Sandpack>

```js src/index.js
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';
const roomId = 'general';

export default function ChatRoom() {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
  }, []);
  return <h1>Welcome to the {roomId} room!</h1>;
}
```

```js src/chat.js
let connections = 0;

export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
      connections++;
      console.log('Active connections: ' + connections);
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
      connections--;
      console.log('Active connections: ' + connections);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

**Với Strict Mode, bạn ngay lập tức thấy rằng có một vấn đề** (số lượng kết nối đang hoạt động tăng lên 2). Strict Mode chạy một chu kỳ setup+cleanup bổ sung cho mỗi Effect. Effect này không có logic cleanup, vì vậy nó tạo ra một kết nối bổ sung nhưng không hủy nó. Đây là dấu hiệu cho thấy bạn đang thiếu hàm cleanup.

Strict Mode cho phép bạn nhận ra những lỗi như vậy sớm trong quá trình. Khi bạn sửa Effect bằng cách thêm hàm cleanup trong Strict Mode, bạn *cũng* sửa nhiều lỗi production có thể xảy ra trong tương lai như hộp select trước đó:

<Sandpack>

```js src/index.js
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);

  return <h1>Welcome to the {roomId} room!</h1>;
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [show, setShow] = useState(false);
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <button onClick={() => setShow(!show)}>
        {show ? 'Close chat' : 'Open chat'}
      </button>
      {show && <hr />}
      {show && <ChatRoom roomId={roomId} />}
    </>
  );
}
```

```js src/chat.js
let connections = 0;

export function createConnection(serverUrl, roomId) {
  // A real implementation would actually connect to the server
  return {
    connect() {
      console.log('✅ Connecting to "' + roomId + '" room at ' + serverUrl + '...');
      connections++;
      console.log('Active connections: ' + connections);
    },
    disconnect() {
      console.log('❌ Disconnected from "' + roomId + '" room at ' + serverUrl);
      connections--;
      console.log('Active connections: ' + connections);
    }
  };
}
```

```css
input { display: block; margin-bottom: 20px; }
button { margin-left: 10px; }
```

</Sandpack>

Lưu ý rằng số lượng kết nối đang hoạt động trong console không còn tiếp tục tăng nữa.

Không có Strict Mode, rất dễ bỏ sót rằng Effect của bạn cần cleanup. Bằng cách chạy *setup → cleanup → setup* thay vì *setup* cho Effect của bạn trong môi trường phát triển, Strict Mode làm cho logic cleanup bị thiếu trở nên rõ ràng hơn.

[Đọc thêm về việc triển khai Effect cleanup.](/learn/synchronizing-with-effects#how-to-handle-the-effect-firing-twice-in-development)

---
### Sửa lỗi được tìm thấy bởi việc chạy lại ref callback trong môi trường phát triển {/*fixing-bugs-found-by-re-running-ref-callbacks-in-development*/}

Strict Mode cũng có thể giúp tìm lỗi trong [callback refs.](/learn/manipulating-the-dom-with-refs)

Mỗi callback `ref` có một số code setup và có thể có một số code cleanup. Thông thường, React gọi setup khi phần tử được *tạo* (được thêm vào DOM) và gọi cleanup khi phần tử bị *xóa* (bị xóa khỏi DOM).

Khi Strict Mode được bật, React cũng sẽ chạy **một chu kỳ setup+cleanup bổ sung trong môi trường phát triển cho mỗi callback `ref`.** Điều này có thể cảm thấy bất ngờ, nhưng nó giúp phát lộ các lỗi tinh tế khó bắt thủ công.

Hãy xem ví dụ này, cho phép bạn chọn một con vật và sau đó cuộn đến một trong số chúng. Lưu ý khi bạn chuyển từ "Cats" sang "Dogs", console log hiển thị rằng số lượng con vật trong danh sách tiếp tục tăng, và các nút "Scroll to" ngừng hoạt động:

<Sandpack>

```js src/index.js
import { createRoot } from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
// ❌ Not using StrictMode.
root.render(<App />);
```

```js src/App.js active
import { useRef, useState } from "react";

export default function CatFriends() {
  const itemsRef = useRef([]);
  const [catList, setCatList] = useState(setupCatList);
  const [cat, setCat] = useState('neo');

  function scrollToCat(index) {
    const list = itemsRef.current;
    const {node} = list[index];
    node.scrollIntoView({
      behavior: "smooth",
      block: "nearest",
      inline: "center",
    });
  }

  const cats = catList.filter(c => c.type === cat)

  return (
    <>
      <nav>
        <button onClick={() => setCat('neo')}>Neo</button>
        <button onClick={() => setCat('millie')}>Millie</button>
      </nav>
      <hr />
      <nav>
        <span>Scroll to:</span>{cats.map((cat, index) => (
          <button key={cat.src} onClick={() => scrollToCat(index)}>
            {index}
          </button>
        ))}
      </nav>
      <div>
        <ul>
          {cats.map((cat) => (
            <li
              key={cat.src}
              ref={(node) => {
                const list = itemsRef.current;
                const item = {cat: cat, node};
                list.push(item);
                console.log(`✅ Adding cat to the map. Total cats: ${list.length}`);
                if (list.length > 10) {
                  console.log('❌ Too many cats in the list!');
                }
                return () => {
                  // 🚩 No cleanup, this is a bug!
                }
              }}
            >
              <img src={cat.src} />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

function setupCatList() {
  const catList = [];
  for (let i = 0; i < 10; i++) {
    catList.push({type: 'neo', src: "https://placecats.com/neo/320/240?" + i});
  }
  for (let i = 0; i < 10; i++) {
    catList.push({type: 'millie', src: "https://placecats.com/millie/320/240?" + i});
  }

  return catList;
}

```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}
```

</Sandpack>


**Đây là một lỗi production!** Vì ref callback không xóa con vật khỏi danh sách trong cleanup, danh sách con vật tiếp tục tăng. Đây là một memory leak có thể gây ra vấn đề về hiệu suất trong ứng dụng thực và phá vỡ hành vi của ứng dụng.

Vấn đề là ref callback không dọn dẹp sau chính nó:

```js {6-8}
<li
  ref={node => {
    const list = itemsRef.current;
    const item = {animal, node};
    list.push(item);
    return () => {
      // 🚩 No cleanup, this is a bug!
    }
  }}
</li>
```

Bây giờ hãy bọc code gốc (có lỗi) trong `<StrictMode>`:

<Sandpack>

```js src/index.js
import { createRoot } from 'react-dom/client';
import {StrictMode} from 'react';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
// ✅ Using StrictMode.
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

```js src/App.js active
import { useRef, useState } from "react";

export default function CatFriends() {
  const itemsRef = useRef([]);
  const [catList, setCatList] = useState(setupCatList);
  const [cat, setCat] = useState('neo');

  function scrollToCat(index) {
    const list = itemsRef.current;
    const {node} = list[index];
    node.scrollIntoView({
      behavior: "smooth",
      block: "nearest",
      inline: "center",
    });
  }

  const cats = catList.filter(c => c.type === cat)

  return (
    <>
      <nav>
        <button onClick={() => setCat('neo')}>Neo</button>
        <button onClick={() => setCat('millie')}>Millie</button>
      </nav>
      <hr />
      <nav>
        <span>Scroll to:</span>{cats.map((cat, index) => (
          <button key={cat.src} onClick={() => scrollToCat(index)}>
            {index}
          </button>
        ))}
      </nav>
      <div>
        <ul>
          {cats.map((cat) => (
            <li
              key={cat.src}
              ref={(node) => {
                const list = itemsRef.current;
                const item = {cat: cat, node};
                list.push(item);
                console.log(`✅ Adding cat to the map. Total cats: ${list.length}`);
                if (list.length > 10) {
                  console.log('❌ Too many cats in the list!');
                }
                return () => {
                  // 🚩 No cleanup, this is a bug!
                }
              }}
            >
              <img src={cat.src} />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

function setupCatList() {
  const catList = [];
  for (let i = 0; i < 10; i++) {
    catList.push({type: 'neo', src: "https://placecats.com/neo/320/240?" + i});
  }
  for (let i = 0; i < 10; i++) {
    catList.push({type: 'millie', src: "https://placecats.com/millie/320/240?" + i});
  }

  return catList;
}

```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}
```

</Sandpack>

**Với Strict Mode, bạn ngay lập tức thấy rằng có một vấn đề**. Strict Mode chạy một chu kỳ setup+cleanup bổ sung cho mỗi callback ref. Callback ref này không có logic cleanup, vì vậy nó thêm refs nhưng không xóa chúng. Đây là dấu hiệu cho thấy bạn đang thiếu hàm cleanup.

Strict Mode cho phép bạn sớm tìm thấy lỗi trong callback refs. Khi bạn sửa callback bằng cách thêm hàm cleanup trong Strict Mode, bạn *cũng* sửa nhiều lỗi production có thể xảy ra trong tương lai như lỗi "Scroll to" trước đó:

<Sandpack>

```js src/index.js
import { createRoot } from 'react-dom/client';
import {StrictMode} from 'react';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById("root"));
// ✅ Using StrictMode.
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

```js src/App.js active
import { useRef, useState } from "react";

export default function CatFriends() {
  const itemsRef = useRef([]);
  const [catList, setCatList] = useState(setupCatList);
  const [cat, setCat] = useState('neo');

  function scrollToCat(index) {
    const list = itemsRef.current;
    const {node} = list[index];
    node.scrollIntoView({
      behavior: "smooth",
      block: "nearest",
      inline: "center",
    });
  }

  const cats = catList.filter(c => c.type === cat)

  return (
    <>
      <nav>
        <button onClick={() => setCat('neo')}>Neo</button>
        <button onClick={() => setCat('millie')}>Millie</button>
      </nav>
      <hr />
      <nav>
        <span>Scroll to:</span>{cats.map((cat, index) => (
          <button key={cat.src} onClick={() => scrollToCat(index)}>
            {index}
          </button>
        ))}
      </nav>
      <div>
        <ul>
          {cats.map((cat) => (
            <li
              key={cat.src}
              ref={(node) => {
                const list = itemsRef.current;
                const item = {cat: cat, node};
                list.push(item);
                console.log(`✅ Adding cat to the map. Total cats: ${list.length}`);
                if (list.length > 10) {
                  console.log('❌ Too many cats in the list!');
                }
                return () => {
                  list.splice(list.indexOf(item), 1);
                  console.log(`❌ Removing cat from the map. Total cats: ${itemsRef.current.length}`);
                }
              }}
            >
              <img src={cat.src} />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

function setupCatList() {
  const catList = [];
  for (let i = 0; i < 10; i++) {
    catList.push({type: 'neo', src: "https://placecats.com/neo/320/240?" + i});
  }
  for (let i = 0; i < 10; i++) {
    catList.push({type: 'millie', src: "https://placecats.com/millie/320/240?" + i});
  }

  return catList;
}

```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}
```

</Sandpack>

Bây giờ khi mount lần đầu trong StrictMode, tất cả ref callback đều được setup, dọn dẹp, và setup lại:

```
...
✅ Adding animal to the map. Total animals: 10
...
❌ Removing animal from the map. Total animals: 0
...
✅ Adding animal to the map. Total animals: 10
```

**Đây là điều được mong đợi.** Strict Mode xác nhận rằng các ref callback được dọn dẹp đúng cách, vì vậy kích thước không bao giờ tăng trên lượng mong đợi. Sau khi sửa, không có memory leak nào, và tất cả các tính năng hoạt động như mong đợi.

Không có Strict Mode, rất dễ bỏ sót lỗi cho đến khi bạn nhấp vào ứng dụng để nhận thấy các tính năng bị hỏng. Strict Mode làm cho các lỗi xuất hiện ngay lập tức, trước khi bạn đẩy chúng vào production.

---
### Sửa cảnh báo deprecation được bật bởi Strict Mode {/*fixing-deprecation-warnings-enabled-by-strict-mode*/}

React cảnh báo nếu một số component ở bất kỳ đâu bên trong cây `<StrictMode>` sử dụng một trong những API đã deprecated sau:

* Các phương thức lifecycle class có tiền tố `UNSAFE_` như [`UNSAFE_componentWillMount`](/reference/react/Component#unsafe_componentwillmount). [Xem các thay thế.](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#migrating-from-legacy-lifecycles)

Các API này chủ yếu được sử dụng trong các [class component](/reference/react/Component) cũ hơn nên chúng hiếm khi xuất hiện trong các ứng dụng hiện đại.
