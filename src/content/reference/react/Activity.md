---
title: <Activity>
---

<Intro>

`<Activity>` cho phép bạn ẩn và khôi phục UI cùng state nội bộ của các component con.

```js
<Activity mode={visibility}>
  <Sidebar />
</Activity>
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `<Activity>` {/*activity*/}

Bạn có thể sử dụng Activity để ẩn một phần ứng dụng của mình:

```js [[1, 1, "\\"hidden\\""], [2, 2, "<Sidebar />"], [3, 1, "\\"visible\\""]]
<Activity mode={isShowingSidebar ? "visible" : "hidden"}>
  <Sidebar />
</Activity>
```

Khi một Activity boundary ở trạng thái <CodeStep step={1}>hidden</CodeStep>, React sẽ ẩn <CodeStep step={2}>các component con</CodeStep> bằng thuộc tính CSS `display: "none"`. Nó cũng sẽ hủy các Effect của chúng, dọn dẹp mọi subscription đang hoạt động.

Trong khi bị ẩn, các component con vẫn re-render để phản hồi props mới, mặc dù ở mức ưu tiên thấp hơn so với phần còn lại của nội dung.

Khi boundary trở nên <CodeStep step={3}>visible</CodeStep> trở lại, React sẽ hiển thị lại các component con với state trước đó được khôi phục, và tạo lại các Effect của chúng.

Theo cách này, Activity có thể được coi là một cơ chế để render "hoạt động nền". Thay vì loại bỏ hoàn toàn nội dung có khả năng sẽ hiển thị lại, bạn có thể sử dụng Activity để duy trì và khôi phục UI và state nội bộ của nội dung đó, đồng thời đảm bảo rằng nội dung bị ẩn của bạn không có side effect không mong muốn.

[Xem thêm ví dụ bên dưới.](#usage)

#### Props {/*props*/}

* `children`: UI bạn muốn hiện và ẩn.
* `mode`: Một giá trị chuỗi là `’visible’` hoặc `’hidden’`. Nếu bỏ qua, mặc định là `’visible’`.

#### Lưu ý {/*caveats*/}

- Nếu một Activity được render bên trong một [ViewTransition](/reference/react/ViewTransition), và nó trở nên visible do một cập nhật gây ra bởi [startTransition](/reference/react/startTransition), nó sẽ kích hoạt animation `enter` của ViewTransition. Nếu nó bị ẩn, nó sẽ kích hoạt animation `exit` của nó.
- Một Activity *hidden* chỉ render văn bản sẽ không render gì cả thay vì render văn bản bị ẩn, vì không có phần tử DOM tương ứng để áp dụng thay đổi visibility. Ví dụ, `<Activity mode="hidden"><ComponentThatJustReturnsText /></Activity>` sẽ không tạo ra bất kỳ output nào trong DOM cho `const ComponentThatJustReturnsText = () => "Hello, World!"`. `<Activity mode="visible"><ComponentThatJustReturnsText /></Activity>` sẽ render văn bản hiển thị.

---

## Cách sử dụng {/*usage*/}

### Khôi phục state của các component bị ẩn {/*restoring-the-state-of-hidden-components*/}

Trong React, khi bạn muốn hiển thị hoặc ẩn một component có điều kiện, bạn thường mount hoặc unmount nó dựa trên điều kiện đó:

```jsx
{isShowingSidebar && (
  <Sidebar />
)}
```

Nhưng việc unmount một component sẽ hủy state nội bộ của nó, điều này không phải lúc nào cũng là điều bạn muốn.

Khi bạn ẩn một component bằng Activity boundary thay thế, React sẽ "lưu" state của nó để sử dụng sau:

```jsx
<Activity mode={isShowingSidebar ? "visible" : "hidden"}>
  <Sidebar />
</Activity>
```

Điều này cho phép ẩn và sau đó khôi phục các component ở trạng thái chúng đã có trước đó.

Ví dụ sau có một sidebar với một phần có thể mở rộng. Bạn có thể nhấn "Overview" để hiển thị ba mục con bên dưới nó. Khu vực ứng dụng chính cũng có một nút ẩn và hiển thị sidebar.

Hãy thử mở rộng phần Overview, sau đó chuyển đổi sidebar đóng rồi mở:

<Sandpack>

```js src/App.js active
import { useState } from 'react';
import Sidebar from './Sidebar.js';

export default function App() {
  const [isShowingSidebar, setIsShowingSidebar] = useState(true);

  return (
    <>
      {isShowingSidebar && (
        <Sidebar />
      )}

      <main>
        <button onClick={() => setIsShowingSidebar(!isShowingSidebar)}>
          Toggle sidebar
        </button>
        <h1>Main content</h1>
      </main>
    </>
  );
}
```

```js src/Sidebar.js
import { useState } from 'react';

export default function Sidebar() {
  const [isExpanded, setIsExpanded] = useState(false)

  return (
    <nav>
      <button onClick={() => setIsExpanded(!isExpanded)}>
        Overview
        <span className={`indicator ${isExpanded ? 'down' : 'right'}`}>
          &#9650;
        </span>
      </button>

      {isExpanded && (
        <ul>
          <li>Section 1</li>
          <li>Section 2</li>
          <li>Section 3</li>
        </ul>
      )}
    </nav>
  );
}
```

```css
body { height: 275px; margin: 0; }
#root {
  display: flex;
  gap: 10px;
  height: 100%;
}
nav {
  padding: 10px;
  background: #eee;
  font-size: 14px;
  height: 100%;
}
main {
  padding: 10px;
}
p {
  margin: 0;
}
h1 {
  margin-top: 10px;
}
.indicator {
  margin-left: 4px;
  display: inline-block;
  rotate: 90deg;
}
.indicator.down {
  rotate: 180deg;
}
```

</Sandpack>

Phần Overview luôn bắt đầu ở trạng thái thu gọn. Vì chúng ta unmount sidebar khi `isShowingSidebar` chuyển sang `false`, tất cả state nội bộ của nó bị mất.

Đây là trường hợp sử dụng hoàn hảo cho Activity. Chúng ta có thể giữ nguyên state nội bộ của sidebar, ngay cả khi ẩn nó về mặt trực quan.

Hãy thay thế conditional rendering của sidebar bằng một Activity boundary:

```jsx {7,9}
// Before
{isShowingSidebar && (
  <Sidebar />
)}

// After
<Activity mode={isShowingSidebar ? 'visible' : 'hidden'}>
  <Sidebar />
</Activity>
```

và xem hành vi mới:

<Sandpack>

```js src/App.js active
import { Activity, useState } from 'react';

import Sidebar from './Sidebar.js';

export default function App() {
  const [isShowingSidebar, setIsShowingSidebar] = useState(true);

  return (
    <>
      <Activity mode={isShowingSidebar ? 'visible' : 'hidden'}>
        <Sidebar />
      </Activity>

      <main>
        <button onClick={() => setIsShowingSidebar(!isShowingSidebar)}>
          Toggle sidebar
        </button>
        <h1>Main content</h1>
      </main>
    </>
  );
}
```

```js src/Sidebar.js
import { useState } from 'react';

export default function Sidebar() {
  const [isExpanded, setIsExpanded] = useState(false)

  return (
    <nav>
      <button onClick={() => setIsExpanded(!isExpanded)}>
        Overview
        <span className={`indicator ${isExpanded ? 'down' : 'right'}`}>
          &#9650;
        </span>
      </button>

      {isExpanded && (
        <ul>
          <li>Section 1</li>
          <li>Section 2</li>
          <li>Section 3</li>
        </ul>
      )}
    </nav>
  );
}
```

```css
body { height: 275px; margin: 0; }
#root {
  display: flex;
  gap: 10px;
  height: 100%;
}
nav {
  padding: 10px;
  background: #eee;
  font-size: 14px;
  height: 100%;
}
main {
  padding: 10px;
}
p {
  margin: 0;
}
h1 {
  margin-top: 10px;
}
.indicator {
  margin-left: 4px;
  display: inline-block;
  rotate: 90deg;
}
.indicator.down {
  rotate: 180deg;
}
```

</Sandpack>

State nội bộ của sidebar hiện đã được khôi phục, mà không cần thay đổi gì trong implementation của nó.

---

### Khôi phục DOM của các component bị ẩn {/*restoring-the-dom-of-hidden-components*/}

Vì Activity boundaries ẩn các component con của chúng bằng `display: none`, DOM của các component con cũng được giữ lại khi bị ẩn. Điều này làm cho chúng rất phù hợp để duy trì trạng thái tạm thời trong các phần của UI mà người dùng có khả năng tương tác lại.

Trong ví dụ này, tab Contact có một `<textarea>` nơi người dùng có thể nhập tin nhắn. Nếu bạn nhập một số văn bản, chuyển sang tab Home, sau đó chuyển lại tab Contact, tin nhắn nháp sẽ bị mất:

<Sandpack>

```js src/App.js
import { useState } from 'react';
import TabButton from './TabButton.js';
import Home from './Home.js';
import Contact from './Contact.js';

export default function App() {
  const [activeTab, setActiveTab] = useState('contact');

  return (
    <>
      <TabButton
        isActive={activeTab === 'home'}
        onClick={() => setActiveTab('home')}
      >
        Home
      </TabButton>
      <TabButton
        isActive={activeTab === 'contact'}
        onClick={() => setActiveTab('contact')}
      >
        Contact
      </TabButton>

      <hr />

      {activeTab === 'home' && <Home />}
      {activeTab === 'contact' && <Contact />}
    </>
  );
}
```

```js src/TabButton.js
export default function TabButton({ onClick, children, isActive }) {
  if (isActive) {
    return <b>{children}</b>
  }

  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}
```

```js src/Home.js
export default function Home() {
  return (
    <p>Welcome to my profile!</p>
  );
}
```

```js src/Contact.js active
export default function Contact() {
  return (
    <div>
      <p>Send me a message!</p>

      <textarea />

      <p>You can find me online here:</p>
      <ul>
        <li>admin@mysite.com</li>
        <li>+123456789</li>
      </ul>
    </div>
  );
}
```

```css
body { height: 275px; }
button { margin-right: 10px }
b { display: inline-block; margin-right: 10px; }
.pending { color: #777; }
```

</Sandpack>

Điều này là do chúng ta đang unmount hoàn toàn `Contact` trong `App`. Khi tab Contact unmount, state DOM nội bộ của phần tử `<textarea>` bị mất.

Nếu chúng ta chuyển sang sử dụng Activity boundary để hiện và ẩn tab đang hoạt động, chúng ta có thể giữ nguyên state DOM của mỗi tab. Hãy thử nhập văn bản và chuyển đổi tab lại, và bạn sẽ thấy tin nhắn nháp không còn bị đặt lại:

<Sandpack>

```js src/App.js active
import { Activity, useState } from 'react';
import TabButton from './TabButton.js';
import Home from './Home.js';
import Contact from './Contact.js';

export default function App() {
  const [activeTab, setActiveTab] = useState('contact');

  return (
    <>
      <TabButton
        isActive={activeTab === 'home'}
        onClick={() => setActiveTab('home')}
      >
        Home
      </TabButton>
      <TabButton
        isActive={activeTab === 'contact'}
        onClick={() => setActiveTab('contact')}
      >
        Contact
      </TabButton>

      <hr />

      <Activity mode={activeTab === 'home' ? 'visible' : 'hidden'}>
        <Home />
      </Activity>
      <Activity mode={activeTab === 'contact' ? 'visible' : 'hidden'}>
        <Contact />
      </Activity>
    </>
  );
}
```

```js src/TabButton.js
export default function TabButton({ onClick, children, isActive }) {
  if (isActive) {
    return <b>{children}</b>
  }

  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}
```

```js src/Home.js
export default function Home() {
  return (
    <p>Welcome to my profile!</p>
  );
}
```

```js src/Contact.js
export default function Contact() {
  return (
    <div>
      <p>Send me a message!</p>

      <textarea />

      <p>You can find me online here:</p>
      <ul>
        <li>admin@mysite.com</li>
        <li>+123456789</li>
      </ul>
    </div>
  );
}
```

```css
body { height: 275px; }
button { margin-right: 10px }
b { display: inline-block; margin-right: 10px; }
.pending { color: #777; }
```

</Sandpack>

Một lần nữa, Activity boundary cho phép chúng ta giữ nguyên state nội bộ của tab Contact mà không thay đổi implementation của nó.

---

### Pre-render nội dung có khả năng trở nên hiển thị {/*pre-rendering-content-thats-likely-to-become-visible*/}

Cho đến nay, chúng ta đã thấy cách Activity có thể ẩn một số nội dung mà người dùng đã tương tác, mà không loại bỏ trạng thái tạm thời của nội dung đó.

Nhưng Activity boundaries cũng có thể được sử dụng để _chuẩn bị_ nội dung mà người dùng chưa thấy lần đầu tiên:

```jsx [[1, 1, "\\"hidden\\""]]
<Activity mode="hidden">
  <SlowComponent />
</Activity>
```

Khi một Activity boundary ở trạng thái <CodeStep step={1}>hidden</CodeStep> trong lần render ban đầu, các component con của nó sẽ không hiển thị trên trang — nhưng chúng _vẫn sẽ được render_, mặc dù ở mức ưu tiên thấp hơn so với nội dung hiển thị, và không mount các Effect của chúng.

_Pre-rendering_ này cho phép các component con tải mọi code hoặc dữ liệu cần thiết trước, để sau này, khi Activity boundary trở nên visible, các component con có thể hiển thị nhanh hơn với thời gian tải giảm.

Hãy xem một ví dụ.

Trong demo này, tab Posts tải một số dữ liệu. Nếu bạn nhấn vào nó, bạn sẽ thấy Suspense fallback được hiển thị trong khi dữ liệu đang được tải:

<Sandpack>

```js src/App.js
import { useState, Suspense } from 'react';
import TabButton from './TabButton.js';
import Home from './Home.js';
import Posts from './Posts.js';

export default function App() {
  const [activeTab, setActiveTab] = useState('home');

  return (
    <>
      <TabButton
        isActive={activeTab === 'home'}
        onClick={() => setActiveTab('home')}
      >
        Home
      </TabButton>
      <TabButton
        isActive={activeTab === 'posts'}
        onClick={() => setActiveTab('posts')}
      >
        Posts
      </TabButton>

      <hr />

      <Suspense fallback={<h1>🌀 Loading...</h1>}>
        {activeTab === 'home' && <Home />}
        {activeTab === 'posts' && <Posts />}
      </Suspense>
    </>
  );
}
```

```js src/TabButton.js hidden
export default function TabButton({ onClick, children, isActive }) {
  if (isActive) {
    return <b>{children}</b>
  }

  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}
```

```js src/Home.js
export default function Home() {
  return (
    <p>Welcome to my profile!</p>
  );
}
```

```js src/Posts.js
import { use } from 'react';
import { fetchData } from './data.js';

export default function Posts() {
  const posts = use(fetchData('/posts'));

  return (
    <ul className="items">
      {posts.map(post =>
        <li className="item" key={post.id}>
          {post.title}
        </li>
      )}
    </ul>
  );
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url.startsWith('/posts')) {
    return await getPosts();
  } else {
    throw Error('Not implemented');
  }
}

async function getPosts() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 1000);
  });
  let posts = [];
  for (let i = 0; i < 10; i++) {
    posts.push({
      id: i,
      title: 'Post #' + (i + 1)
    });
  }
  return posts;
}
```

```css
body { height: 275px; }
button { margin-right: 10px }
b { display: inline-block; margin-right: 10px; }
.pending { color: #777; }
video { width: 300px; margin-top: 10px; aspect-ratio: 16/9; }
```

</Sandpack>

Điều này là do `App` không mount `Posts` cho đến khi tab của nó được kích hoạt.

Nếu chúng ta cập nhật `App` để sử dụng Activity boundary để hiện và ẩn tab đang hoạt động, `Posts` sẽ được pre-render khi ứng dụng tải lần đầu, cho phép nó tải dữ liệu trước khi trở nên visible.

Hãy thử nhấp vào tab Posts bây giờ:

<Sandpack>

```js src/App.js
import { Activity, useState, Suspense } from 'react';
import TabButton from './TabButton.js';
import Home from './Home.js';
import Posts from './Posts.js';

export default function App() {
  const [activeTab, setActiveTab] = useState('home');

  return (
    <>
      <TabButton
        isActive={activeTab === 'home'}
        onClick={() => setActiveTab('home')}
      >
        Home
      </TabButton>
      <TabButton
        isActive={activeTab === 'posts'}
        onClick={() => setActiveTab('posts')}
      >
        Posts
      </TabButton>

      <hr />

      <Suspense fallback={<h1>🌀 Loading...</h1>}>
        <Activity mode={activeTab === 'home' ? 'visible' : 'hidden'}>
          <Home />
        </Activity>
        <Activity mode={activeTab === 'posts' ? 'visible' : 'hidden'}>
          <Posts />
        </Activity>
      </Suspense>
    </>
  );
}
```

```js src/TabButton.js hidden
export default function TabButton({ onClick, children, isActive }) {
  if (isActive) {
    return <b>{children}</b>
  }

  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}
```

```js src/Home.js
export default function Home() {
  return (
    <p>Welcome to my profile!</p>
  );
}
```

```js src/Posts.js
import { use } from 'react';
import { fetchData } from './data.js';

export default function Posts() {
  const posts = use(fetchData('/posts'));

  return (
    <ul className="items">
      {posts.map(post =>
        <li className="item" key={post.id}>
          {post.title}
        </li>
      )}
    </ul>
  );
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url.startsWith('/posts')) {
    return await getPosts();
  } else {
    throw Error('Not implemented');
  }
}

async function getPosts() {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 1000);
  });
  let posts = [];
  for (let i = 0; i < 10; i++) {
    posts.push({
      id: i,
      title: 'Post #' + (i + 1)
    });
  }
  return posts;
}
```

```css
body { height: 275px; }
button { margin-right: 10px }
b { display: inline-block; margin-right: 10px; }
.pending { color: #777; }
video { width: 300px; margin-top: 10px; aspect-ratio: 16/9; }
```

</Sandpack>

`Posts` đã có thể chuẩn bị bản thân cho việc render nhanh hơn, nhờ vào Activity boundary bị ẩn.

---

Pre-rendering các component với Activity boundary bị ẩn là một cách mạnh mẽ để giảm thời gian tải cho các phần của UI mà người dùng có khả năng tương tác tiếp theo.

<Note>

**Chỉ các nguồn dữ liệu được Suspense hỗ trợ mới được tải trong quá trình pre-rendering.** Chúng bao gồm:

- Tải dữ liệu với các framework có Suspense như [Relay](https://relay.dev/docs/guided-tour/rendering/loading-states/) và [Next.js](https://nextjs.org/docs/app/building-your-application/routing/loading-ui-and-streaming#streaming-with-suspense)
- Lazy-loading code component với [`lazy`](/reference/react/lazy)
- Đọc giá trị của một Promise đã được cache với [`use`](/reference/react/use)

Activity **không** phát hiện dữ liệu được tải bên trong một Effect.

Cách chính xác để tải dữ liệu trong component `Posts` ở trên phụ thuộc vào framework của bạn. Nếu bạn sử dụng một framework có Suspense, bạn sẽ tìm thấy chi tiết trong tài liệu về tải dữ liệu của nó.

Tải dữ liệu với Suspense mà không sử dụng framework có quan điểm cụ thể chưa được hỗ trợ. Các yêu cầu để triển khai một nguồn dữ liệu có Suspense vẫn chưa ổn định và chưa được ghi lại. Một API chính thức để tích hợp các nguồn dữ liệu với Suspense sẽ được phát hành trong phiên bản React tương lai.

</Note>

---


### Tăng tốc tương tác trong quá trình tải trang {/*speeding-up-interactions-during-page-load*/}

React bao gồm một tối ưu hóa hiệu suất ẩn gọi là Selective Hydration. Nó hoạt động bằng cách hydrate HTML ban đầu của ứng dụng _theo từng phần_, cho phép một số component trở nên tương tác ngay cả khi các component khác trên trang chưa tải code hoặc dữ liệu của chúng.

Suspense boundaries tham gia vào Selective Hydration, vì chúng tự nhiên chia cây component của bạn thành các đơn vị độc lập với nhau:

```jsx
function Page() {
  return (
    <>
      <MessageComposer />

      <Suspense fallback="Loading chats...">
        <Chats />
      </Suspense>
    </>
  )
}
```

Ở đây, `MessageComposer` có thể được hydrate hoàn toàn trong quá trình render ban đầu của trang, ngay cả trước khi `Chats` được mount và bắt đầu tải dữ liệu của nó.

Vì vậy, bằng cách chia nhỏ cây component của bạn thành các đơn vị riêng biệt, Suspense cho phép React hydrate HTML được render server của ứng dụng theo từng phần, cho phép các phần của ứng dụng trở nên tương tác nhanh nhất có thể.

Nhưng còn những trang không sử dụng Suspense thì sao?

Hãy lấy ví dụ về tabs này:

```jsx
function Page() {
  const [activeTab, setActiveTab] = useState('home');

  return (
    <>
      <TabButton onClick={() => setActiveTab('home')}>
        Home
      </TabButton>
      <TabButton onClick={() => setActiveTab('video')}>
        Video
      </TabButton>

      {activeTab === 'home' && (
        <Home />
      )}
      {activeTab === 'video' && (
        <Video />
      )}
    </>
  )
}
```

Ở đây, React phải hydrate toàn bộ trang cùng một lúc. Nếu `Home` hoặc `Video` render chậm hơn, chúng có thể làm cho các nút tab cảm thấy không phản hồi trong quá trình hydration.

Thêm Suspense xung quanh tab đang hoạt động sẽ giải quyết vấn đề này:

```jsx {13,20}
function Page() {
  const [activeTab, setActiveTab] = useState('home');

  return (
    <>
      <TabButton onClick={() => setActiveTab('home')}>
        Home
      </TabButton>
      <TabButton onClick={() => setActiveTab('video')}>
        Video
      </TabButton>

      <Suspense fallback={<Placeholder />}>
        {activeTab === 'home' && (
          <Home />
        )}
        {activeTab === 'video' && (
          <Video />
        )}
      </Suspense>
    </>
  )
}
```

...nhưng nó cũng sẽ thay đổi UI, vì fallback `Placeholder` sẽ được hiển thị trong lần render ban đầu.

Thay vào đó, chúng ta có thể sử dụng Activity. Vì Activity boundaries hiện và ẩn các component con của chúng, chúng đã tự nhiên chia cây component thành các đơn vị độc lập. Và giống như Suspense, tính năng này cho phép chúng tham gia vào Selective Hydration.

Hãy cập nhật ví dụ của chúng ta để sử dụng Activity boundaries xung quanh tab đang hoạt động:

```jsx {13-18}
function Page() {
  const [activeTab, setActiveTab] = useState('home');

  return (
    <>
      <TabButton onClick={() => setActiveTab('home')}>
        Home
      </TabButton>
      <TabButton onClick={() => setActiveTab('video')}>
        Video
      </TabButton>

      <Activity mode={activeTab === "home" ? "visible" : "hidden"}>
        <Home />
      </Activity>
      <Activity mode={activeTab === "video" ? "visible" : "hidden"}>
        <Video />
      </Activity>
    </>
  )
}
```

Bây giờ HTML được render server ban đầu của chúng ta trông giống như trong phiên bản gốc, nhưng nhờ Activity, React có thể hydrate các nút tab trước, trước khi nó mount `Home` hoặc `Video`.

---

Do đó, ngoài việc ẩn và hiển thị nội dung, Activity boundaries giúp cải thiện hiệu suất của ứng dụng trong quá trình hydration bằng cách cho React biết các phần nào của trang có thể trở nên tương tác độc lập.

Và ngay cả khi trang của bạn không bao giờ ẩn một phần nội dung, bạn vẫn có thể thêm các Activity boundary luôn hiển thị để cải thiện hiệu suất hydration:

```jsx
function Page() {
  return (
    <>
      <Post />

      <Activity>
        <Comments />
      </Activity>
    </>
  );
}
```

---

## Xử lý sự cố {/*troubleshooting*/}

### Các component bị ẩn của tôi có side effect không mong muốn {/*my-hidden-components-have-unwanted-side-effects*/}

Một Activity boundary ẩn nội dung của nó bằng cách đặt `display: none` trên các component con và dọn dẹp tất cả Effect của chúng. Vì vậy, hầu hết các React component có hành vi tốt và dọn dẹp side effect đúng cách đã đủ mạnh để được ẩn bởi Activity.

Nhưng _có_ một số tình huống mà một component bị ẩn hoạt động khác với một component bị unmount. Đáng chú ý nhất là vì DOM của một component bị ẩn không bị hủy, bất kỳ side effect nào từ DOM đó sẽ tồn tại, ngay cả sau khi component bị ẩn.

Ví dụ, hãy xem thẻ `<video>`. Thông thường nó không cần bất kỳ cleanup nào, vì ngay cả khi bạn đang phát video, việc unmount thẻ sẽ dừng video và âm thanh phát trong trình duyệt. Hãy thử phát video rồi nhấn Home trong demo này:

<Sandpack>

```js src/App.js active
import { useState } from 'react';
import TabButton from './TabButton.js';
import Home from './Home.js';
import Video from './Video.js';

export default function App() {
  const [activeTab, setActiveTab] = useState('video');

  return (
    <>
      <TabButton
        isActive={activeTab === 'home'}
        onClick={() => setActiveTab('home')}
      >
        Home
      </TabButton>
      <TabButton
        isActive={activeTab === 'video'}
        onClick={() => setActiveTab('video')}
      >
        Video
      </TabButton>

      <hr />

      {activeTab === 'home' && <Home />}
      {activeTab === 'video' && <Video />}
    </>
  );
}
```

```js src/TabButton.js hidden
export default function TabButton({ onClick, children, isActive }) {
  if (isActive) {
    return <b>{children}</b>
  }

  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}
```

```js src/Home.js
export default function Home() {
  return (
    <p>Welcome to my profile!</p>
  );
}
```

```js src/Video.js
export default function Video() {
  return (
    <video
      // 'Big Buck Bunny' licensed under CC 3.0 by the Blender foundation. Hosted by archive.org
      src="https://archive.org/download/BigBuckBunny_124/Content/big_buck_bunny_720p_surround.mp4"
      controls
      playsInline
    />

  );
}
```

```css
body { height: 275px; }
button { margin-right: 10px }
b { display: inline-block; margin-right: 10px; }
.pending { color: #777; }
video { width: 300px; margin-top: 10px; aspect-ratio: 16/9; }
```

</Sandpack>

Video dừng phát như mong đợi.

Bây giờ, giả sử chúng ta muốn giữ lại timecode nơi người dùng đã xem lần cuối, để khi họ chuyển lại tab video, nó không bắt đầu lại từ đầu.

Đây là trường hợp sử dụng tuyệt vời cho Activity!

Hãy cập nhật `App` để ẩn tab không hoạt động bằng một Activity boundary bị ẩn thay vì unmount nó, và xem cách demo hoạt động lần này:

<Sandpack>

```js src/App.js active
import { Activity, useState } from 'react';
import TabButton from './TabButton.js';
import Home from './Home.js';
import Video from './Video.js';

export default function App() {
  const [activeTab, setActiveTab] = useState('video');

  return (
    <>
      <TabButton
        isActive={activeTab === 'home'}
        onClick={() => setActiveTab('home')}
      >
        Home
      </TabButton>
      <TabButton
        isActive={activeTab === 'video'}
        onClick={() => setActiveTab('video')}
      >
        Video
      </TabButton>

      <hr />

      <Activity mode={activeTab === 'home' ? 'visible' : 'hidden'}>
        <Home />
      </Activity>
      <Activity mode={activeTab === 'video' ? 'visible' : 'hidden'}>
        <Video />
      </Activity>
    </>
  );
}
```

```js src/TabButton.js hidden
export default function TabButton({ onClick, children, isActive }) {
  if (isActive) {
    return <b>{children}</b>
  }

  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}
```

```js src/Home.js
export default function Home() {
  return (
    <p>Welcome to my profile!</p>
  );
}
```

```js src/Video.js
export default function Video() {
  return (
    <video
      controls
      playsInline
      // 'Big Buck Bunny' licensed under CC 3.0 by the Blender foundation. Hosted by archive.org
      src="https://archive.org/download/BigBuckBunny_124/Content/big_buck_bunny_720p_surround.mp4"
    />

  );
}
```

```css
body { height: 275px; }
button { margin-right: 10px }
b { display: inline-block; margin-right: 10px; }
.pending { color: #777; }
video { width: 300px; margin-top: 10px; aspect-ratio: 16/9; }
```

</Sandpack>

Ôi không! Video và âm thanh tiếp tục phát ngay cả sau khi nó bị ẩn, vì phần tử `<video>` của tab vẫn còn trong DOM.

Để khắc phục điều này, chúng ta có thể thêm một Effect với hàm cleanup dừng video:

```jsx {2,4-10,14}
export default function VideoTab() {
  const ref = useRef();

  useLayoutEffect(() => {
    const videoRef = ref.current;

    return () => {
      videoRef.pause()
    }
  }, []);

  return (
    <video
      ref={ref}
      controls
      playsInline
      src="..."
    />

  );
}
```

Chúng ta gọi `useLayoutEffect` thay vì `useEffect` vì về mặt khái niệm code cleanup gắn liền với UI của component bị ẩn về mặt trực quan. Nếu chúng ta sử dụng một effect thông thường, code có thể bị trì hoãn bởi (chẳng hạn) một Suspense boundary đang re-suspend hoặc một View Transition.

Hãy xem hành vi mới. Thử phát video, chuyển sang tab Home, rồi quay lại tab Video:

<Sandpack>

```js src/App.js active
import { Activity, useState } from 'react';
import TabButton from './TabButton.js';
import Home from './Home.js';
import Video from './Video.js';

export default function App() {
  const [activeTab, setActiveTab] = useState('video');

  return (
    <>
      <TabButton
        isActive={activeTab === 'home'}
        onClick={() => setActiveTab('home')}
      >
        Home
      </TabButton>
      <TabButton
        isActive={activeTab === 'video'}
        onClick={() => setActiveTab('video')}
      >
        Video
      </TabButton>

      <hr />

      <Activity mode={activeTab === 'home' ? 'visible' : 'hidden'}>
        <Home />
      </Activity>
      <Activity mode={activeTab === 'video' ? 'visible' : 'hidden'}>
        <Video />
      </Activity>
    </>
  );
}
```

```js src/TabButton.js hidden
export default function TabButton({ onClick, children, isActive }) {
  if (isActive) {
    return <b>{children}</b>
  }

  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}
```

```js src/Home.js
export default function Home() {
  return (
    <p>Welcome to my profile!</p>
  );
}
```

```js src/Video.js
import { useRef, useLayoutEffect } from 'react';

export default function Video() {
  const ref = useRef();

  useLayoutEffect(() => {
    const videoRef = ref.current

    return () => {
      videoRef.pause()
    };
  }, [])

  return (
    <video
      ref={ref}
      controls
      playsInline
      // 'Big Buck Bunny' licensed under CC 3.0 by the Blender foundation. Hosted by archive.org
      src="https://archive.org/download/BigBuckBunny_124/Content/big_buck_bunny_720p_surround.mp4"
    />

  );
}
```

```css
body { height: 275px; }
button { margin-right: 10px }
b { display: inline-block; margin-right: 10px; }
.pending { color: #777; }
video { width: 300px; margin-top: 10px; aspect-ratio: 16/9; }
```

</Sandpack>

Hoạt động tốt! Hàm cleanup của chúng ta đảm bảo rằng video dừng phát nếu nó bị ẩn bởi Activity boundary, và thậm chí tốt hơn nữa, vì thẻ `<video>` không bao giờ bị hủy, timecode được giữ lại, và video không cần phải được khởi tạo lại hoặc tải xuống lại khi người dùng chuyển lại để tiếp tục xem.

Đây là một ví dụ tuyệt vời về việc sử dụng Activity để giữ trạng thái DOM tạm thời cho các phần của UI trở nên bị ẩn, nhưng người dùng có khả năng tương tác lại sớm.

---

Ví dụ của chúng ta minh họa rằng đối với một số thẻ nhất định như `<video>`, unmounting và hiding có hành vi khác nhau. Nếu một component render DOM có side effect, và bạn muốn ngăn side effect đó khi Activity boundary ẩn nó, hãy thêm một Effect với hàm return để dọn dẹp nó.

Các trường hợp phổ biến nhất của điều này sẽ đến từ các thẻ sau:

  - `<video>`
  - `<audio>`
  - `<iframe>`

Tuy nhiên, thông thường, hầu hết các React component của bạn đã đủ mạnh để được ẩn bởi Activity boundary. Và về mặt khái niệm, bạn nên nghĩ đến các Activity "hidden" như đang bị unmounted.

Để sớm phát hiện các Effect khác không có cleanup đúng cách, điều này quan trọng không chỉ cho Activity boundaries mà còn cho nhiều hành vi khác trong React, chúng tôi khuyến nghị sử dụng [`<StrictMode>`](/reference/react/StrictMode).

---


### Các component bị ẩn của tôi có Effect không chạy {/*my-hidden-components-have-effects-that-arent-running*/}

Khi một `<Activity>` ở trạng thái "hidden", tất cả Effect của các component con của nó được dọn dẹp. Về mặt khái niệm, các component con bị unmounted, nhưng React lưu state của chúng để sử dụng sau. Đây là một tính năng của Activity vì nó có nghĩa là các subscription sẽ không hoạt động đối với các phần ẩn của UI, giảm lượng công việc cần thiết cho nội dung ẩn.

Nếu bạn đang dựa vào việc mount một Effect để dọn dẹp side effect của component, hãy cấu trúc lại Effect để thực hiện công việc trong hàm cleanup được trả về thay thế.

Để sớm tìm thấy các Effect có vấn đề, chúng tôi khuyến nghị thêm [`<StrictMode>`](/reference/react/StrictMode) sẽ chủ động thực hiện unmount và mount Activity để bắt bất kỳ side effect không mong muốn nào.
