---
title: Server Components
---

<Intro>

Server Components là một loại Component mới được render trước, trước khi đóng gói, trong một môi trường tách biệt khỏi ứng dụng client hoặc server SSR của bạn.

</Intro>

Môi trường tách biệt này chính là "server" trong React Server Components. Server Components có thể chạy một lần vào thời điểm build trên server CI của bạn, hoặc chúng có thể được chạy cho mỗi request bằng một web server.

<InlineToc />

<Note>

#### Làm thế nào để tôi xây dựng hỗ trợ cho Server Components? {/*how-do-i-build-support-for-server-components*/}

Mặc dù React Server Components trong React 19 đã ổn định và sẽ không bị thay đổi đáng kể giữa các phiên bản minor, các API nền tảng được sử dụng để triển khai bundler hoặc framework cho React Server Components không tuân theo semver và có thể thay đổi giữa các phiên bản minor trong React 19.x.

Để hỗ trợ React Server Components với tư cách là bundler hoặc framework, chúng tôi khuyến nghị ghim vào một phiên bản React cụ thể, hoặc sử dụng bản Canary. Chúng tôi sẽ tiếp tục làm việc với các bundler và framework để ổn định các API được sử dụng để triển khai React Server Components trong tương lai.

</Note>

### Server Components không cần Server {/*server-components-without-a-server*/}
Server components có thể chạy tại thời điểm build để đọc từ hệ thống tệp hoặc lấy nội dung tĩnh, vì vậy không cần web server. Ví dụ, bạn có thể muốn đọc dữ liệu tĩnh từ hệ thống quản lý nội dung.

Nếu không có Server Components, việc lấy dữ liệu tĩnh trên client bằng Effect là phổ biến:
```js
// bundle.js
import marked from 'marked'; // 35.9K (11.2K gzipped)
import sanitizeHtml from 'sanitize-html'; // 206K (63.3K gzipped)

function Page({page}) {
  const [content, setContent] = useState('');
  // LƯU Ý: tải *sau* lần render trang đầu tiên.
  useEffect(() => {
    fetch(`/api/content/${page}`).then((data) => {
      setContent(data.content);
    });
  }, [page]);

  return <div>{sanitizeHtml(marked(content))}</div>;
}
```
```js
// api.js
app.get(`/api/content/:page`, async (req, res) => {
  const page = req.params.page;
  const content = await file.readFile(`${page}.md`);
  res.send({content});
});
```

Pattern này có nghĩa là người dùng cần tải xuống và phân tích thêm 75K (gzipped) thư viện, và đợi một request thứ hai để lấy dữ liệu sau khi trang tải, chỉ để render nội dung tĩnh sẽ không thay đổi trong suốt vòng đời của trang.

Với Server Components, bạn có thể render các component này một lần tại thời điểm build:

```js
import marked from 'marked'; // Không được bao gồm trong bundle
import sanitizeHtml from 'sanitize-html'; // Không được bao gồm trong bundle

async function Page({page}) {
  // LƯU Ý: tải *trong khi* render, khi ứng dụng được build.
  const content = await file.readFile(`${page}.md`);

  return <div>{sanitizeHtml(marked(content))}</div>;
}
```

Đầu ra đã render sau đó có thể được server-side render (SSR) thành HTML và tải lên CDN. Khi ứng dụng tải, client sẽ không thấy component `Page` gốc, hay các thư viện đắt đỏ để render markdown. Client chỉ thấy đầu ra đã render:

```js
<div><!-- html cho markdown --></div>
```

Điều này có nghĩa là nội dung hiển thị ngay trong lần tải trang đầu tiên, và bundle không bao gồm các thư viện đắt đỏ cần thiết để render nội dung tĩnh.

<Note>

Bạn có thể nhận thấy rằng Server Component ở trên là một hàm async:

```js
async function Page({page}) {
  //...
}
```

Async Components là một tính năng mới của Server Components cho phép bạn `await` trong render.

Xem [Async components với Server Components](#async-components-with-server-components) bên dưới.

</Note>

### Server Components với Server {/*server-components-with-a-server*/}
Server Components cũng có thể chạy trên web server trong quá trình xử lý request cho một trang, cho phép bạn truy cập tầng dữ liệu mà không cần xây dựng API. Chúng được render trước khi ứng dụng của bạn được đóng gói, và có thể truyền dữ liệu và JSX dưới dạng props cho Client Components.

Nếu không có Server Components, việc lấy dữ liệu động trên client trong Effect là phổ biến:

```js
// bundle.js
function Note({id}) {
  const [note, setNote] = useState('');
  // LƯU Ý: tải *sau* lần render đầu tiên.
  useEffect(() => {
    fetch(`/api/notes/${id}`).then(data => {
      setNote(data.note);
    });
  }, [id]);

  return (
    <div>
      <Author id={note.authorId} />
      <p>{note}</p>
    </div>
  );
}

function Author({id}) {
  const [author, setAuthor] = useState('');
  // LƯU Ý: tải *sau khi* Note render.
  // Gây ra waterfall client-server tốn kém.
  useEffect(() => {
    fetch(`/api/authors/${id}`).then(data => {
      setAuthor(data.author);
    });
  }, [id]);

  return <span>By: {author.name}</span>;
}
```
```js
// api
import db from './database';

app.get(`/api/notes/:id`, async (req, res) => {
  const note = await db.notes.get(id);
  res.send({note});
});

app.get(`/api/authors/:id`, async (req, res) => {
  const author = await db.authors.get(id);
  res.send({author});
});
```

Với Server Components, bạn có thể đọc dữ liệu và render nó trong component:

```js
import db from './database';

async function Note({id}) {
  // LƯU Ý: tải *trong khi* render.
  const note = await db.notes.get(id);
  return (
    <div>
      <Author id={note.authorId} />
      <p>{note}</p>
    </div>
  );
}

async function Author({id}) {
  // LƯU Ý: tải *sau* Note,
  // nhưng nhanh nếu dữ liệu cùng vị trí.
  const author = await db.authors.get(id);
  return <span>By: {author.name}</span>;
}
```

Bundler sau đó kết hợp dữ liệu, các Server Components đã render và các Client Components động thành một bundle. Tùy chọn, bundle đó sau đó có thể được server-side render (SSR) để tạo HTML ban đầu cho trang. Khi trang tải, trình duyệt không thấy các component `Note` và `Author` gốc; chỉ đầu ra đã render được gửi đến client:

```js
<div>
  <span>By: The React Team</span>
  <p>React 19 is...</p>
</div>
```

Server Components có thể được làm động bằng cách lấy lại chúng từ server, nơi chúng có thể truy cập dữ liệu và render lại. Kiến trúc ứng dụng mới này kết hợp mô hình tư duy "request/response" đơn giản của Multi-Page Apps tập trung vào server với tính tương tác liền mạch của Single-Page Apps tập trung vào client, mang lại cho bạn điều tốt nhất của cả hai thế giới.

### Thêm tính tương tác vào Server Components {/*adding-interactivity-to-server-components*/}

Server Components không được gửi đến trình duyệt, vì vậy chúng không thể sử dụng các API tương tác như `useState`. Để thêm tính tương tác vào Server Components, bạn có thể kết hợp chúng với Client Component sử dụng directive `"use client"`.

<Note>

#### Không có directive cho Server Components. {/*there-is-no-directive-for-server-components*/}

Một hiểu lầm phổ biến là Server Components được ký hiệu bằng `"use server"`, nhưng không có directive cho Server Components. Directive `"use server"` được sử dụng cho Server Functions.

Để biết thêm thông tin, xem tài liệu về [Directives](/reference/rsc/directives).

</Note>


Trong ví dụ sau, Server Component `Notes` import Client Component `Expandable` sử dụng state để chuyển đổi trạng thái `expanded`:
```js
// Server Component
import Expandable from './Expandable';

async function Notes() {
  const notes = await db.notes.getAll();
  return (
    <div>
      {notes.map(note => (
        <Expandable key={note.id}>
          <p note={note} />
        </Expandable>
      ))}
    </div>
  )
}
```
```js
// Client Component
"use client"

export default function Expandable({children}) {
  const [expanded, setExpanded] = useState(false);
  return (
    <div>
      <button
        onClick={() => setExpanded(!expanded)}
      >
        Toggle
      </button>
      {expanded && children}
    </div>
  )
}
```

Điều này hoạt động bằng cách đầu tiên render `Notes` như một Server Component, sau đó hướng dẫn bundler tạo bundle cho Client Component `Expandable`. Trong trình duyệt, Client Components sẽ thấy đầu ra của Server Components được truyền dưới dạng props:

```js
<head>
  <!-- bundle cho Client Components -->
  <script src="bundle.js" />
</head>
<body>
  <div>
    <Expandable key={1}>
      <p>this is the first note</p>
    </Expandable>
    <Expandable key={2}>
      <p>this is the second note</p>
    </Expandable>
    <!--...-->
  </div>
</body>
```

### Async components với Server Components {/*async-components-with-server-components*/}

Server Components giới thiệu một cách mới để viết Components sử dụng async/await. Khi bạn `await` trong một async component, React sẽ tạm dừng và đợi promise được resolve trước khi tiếp tục render. Điều này hoạt động xuyên ranh giới server/client với hỗ trợ streaming cho Suspense.

Bạn thậm chí có thể tạo một promise trên server, và await nó trên client:

```js
// Server Component
import db from './database';

async function Page({id}) {
  // Sẽ suspend Server Component.
  const note = await db.notes.get(id);

  // LƯU Ý: không await, sẽ bắt đầu ở đây và await trên client.
  const commentsPromise = db.comments.get(note.id);
  return (
    <div>
      {note}
      <Suspense fallback={<p>Đang tải bình luận...</p>}>
        <Comments commentsPromise={commentsPromise} />
      </Suspense>
    </div>
  );
}
```

```js
// Client Component
"use client";
import {use} from 'react';

function Comments({commentsPromise}) {
  // LƯU Ý: điều này sẽ tiếp tục promise từ server.
  // Nó sẽ suspend cho đến khi dữ liệu có sẵn.
  const comments = use(commentsPromise);
  return comments.map(comment => <p>{comment}</p>);
}
```

Nội dung `note` là dữ liệu quan trọng để trang render, vì vậy chúng ta `await` nó trên server. Các bình luận nằm bên dưới phần hiển thị và có độ ưu tiên thấp hơn, vì vậy chúng ta bắt đầu promise trên server, và đợi nó trên client với API `use`. Điều này sẽ Suspend trên client, mà không chặn nội dung `note` khỏi việc render.

Vì async components không được hỗ trợ trên client, chúng ta await promise bằng `use`.
