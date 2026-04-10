---
title: lazy
---

<Intro>

`lazy` cho phép bạn trì hoãn việc tải code của component cho đến khi nó được render lần đầu tiên.

```js
const SomeComponent = lazy(load)
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `lazy(load)` {/*lazy*/}

Gọi `lazy` bên ngoài các component của bạn để khai báo một React component được tải lười biếng:

```js
import { lazy } from 'react';

const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));
```

[Xem thêm ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `load`: Một hàm trả về một [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) hoặc một *thenable* khác (một object giống Promise với phương thức `then`). React sẽ không gọi `load` cho đến lần đầu tiên bạn cố gắng render component được trả về. Sau khi React gọi `load` lần đầu tiên, nó sẽ chờ cho đến khi nó resolve, và sau đó render giá trị `.default` đã resolve như một React component. Cả Promise được trả về và giá trị đã resolve của Promise đều sẽ được cache, vì vậy React sẽ không gọi `load` nhiều hơn một lần. Nếu Promise reject, React sẽ `throw` lý do reject cho Error Boundary gần nhất xử lý.

#### Giá trị trả về {/*returns*/}

`lazy` trả về một React component mà bạn có thể render trong cây của mình. Trong khi code cho component lazy vẫn đang tải, việc cố gắng render nó sẽ *suspend.* Sử dụng [`<Suspense>`](/reference/react/Suspense) để hiển thị chỉ báo tải trong khi nó đang tải.

---

### Hàm `load` {/*load*/}

#### Tham số {/*load-parameters*/}

`load` không nhận tham số nào.

#### Giá trị trả về {/*load-returns*/}

Bạn cần trả về một [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) hoặc một *thenable* khác (một object giống Promise với phương thức `then`). Cuối cùng nó cần resolve thành một object mà thuộc tính `.default` của nó là một loại React component hợp lệ, chẳng hạn như một hàm, component [`memo`](/reference/react/memo), hoặc component [`forwardRef`](/reference/react/forwardRef).

---

## Cách sử dụng {/*usage*/}

### Tải lười biếng các component với Suspense {/*suspense-for-code-splitting*/}

Thông thường, bạn import các component bằng khai báo [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) tĩnh:

```js
import MarkdownPreview from './MarkdownPreview.js';
```

Để trì hoãn việc tải code của component này cho đến khi nó được render lần đầu tiên, hãy thay thế import này bằng:

```js
import { lazy } from 'react';

const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));
```

Code này dựa vào [dynamic `import()`,](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import) có thể yêu cầu hỗ trợ từ bundler hoặc framework của bạn. Sử dụng mẫu này yêu cầu component lazy bạn đang import phải được export như `default` export.

Bây giờ code của component của bạn tải theo yêu cầu, bạn cũng cần chỉ định những gì nên hiển thị trong khi nó đang tải. Bạn có thể làm điều này bằng cách bọc component lazy hoặc bất kỳ cha nào của nó vào một ranh giới [`<Suspense>`](/reference/react/Suspense):

```js {1,4}
<Suspense fallback={<Loading />}>
  <h2>Preview</h2>
  <MarkdownPreview />
</Suspense>
```

Trong ví dụ này, code cho `MarkdownPreview` sẽ không được tải cho đến khi bạn cố gắng render nó. Nếu `MarkdownPreview` chưa tải, `Loading` sẽ được hiển thị thay thế. Thử tick vào checkbox:

<Sandpack>

```js src/App.js
import { useState, Suspense, lazy } from 'react';
import Loading from './Loading.js';

const MarkdownPreview = lazy(() => delayForDemo(import('./MarkdownPreview.js')));

export default function MarkdownEditor() {
  const [showPreview, setShowPreview] = useState(false);
  const [markdown, setMarkdown] = useState('Hello, **world**!');
  return (
    <>
      <textarea value={markdown} onChange={e => setMarkdown(e.target.value)} />
      <label>
        <input type="checkbox" checked={showPreview} onChange={e => setShowPreview(e.target.checked)} />
        Show preview
      </label>
      <hr />
      {showPreview && (
        <Suspense fallback={<Loading />}>
          <h2>Preview</h2>
          <MarkdownPreview markdown={markdown} />
        </Suspense>
      )}
    </>
  );
}

// Thêm độ trễ cố định để bạn có thể thấy trạng thái tải
function delayForDemo(promise) {
  return new Promise(resolve => {
    setTimeout(resolve, 2000);
  }).then(() => promise);
}
```

```js src/Loading.js
export default function Loading() {
  return <p><i>Loading...</i></p>;
}
```

```js src/MarkdownPreview.js
import { Remarkable } from 'remarkable';

const md = new Remarkable();

export default function MarkdownPreview({ markdown }) {
  return (
    <div
      className="content"
      dangerouslySetInnerHTML={{__html: md.render(markdown)}}
    />
  );
}
```

```json package.json hidden
{
  "dependencies": {
    "immer": "1.7.3",
    "react": "latest",
    "react-dom": "latest",
    "react-scripts": "latest",
    "remarkable": "2.0.1"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```css
label {
  display: block;
}

input, textarea {
  margin-bottom: 10px;
}

body {
  min-height: 200px;
}
```

</Sandpack>

Demo này tải với độ trễ nhân tạo. Lần tiếp theo bạn untick và tick lại checkbox, `Preview` sẽ được cache, vì vậy sẽ không có trạng thái tải. Để thấy trạng thái tải lại, hãy nhấn "Reset" trên sandbox.

[Tìm hiểu thêm về cách quản lý trạng thái tải với Suspense.](/reference/react/Suspense)

---

## Xử lý sự cố {/*troubleshooting*/}

### State của component `lazy` của tôi bị reset không mong đợi {/*my-lazy-components-state-gets-reset-unexpectedly*/}

Đừng khai báo các component `lazy` *bên trong* các component khác:

```js {4-5}
import { lazy } from 'react';

function Editor() {
  // 🔴 Xấu: Điều này sẽ làm tất cả state bị reset khi re-render
  const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));
  // ...
}
```

Thay vào đó, hãy luôn khai báo chúng ở cấp cao nhất của module của bạn:

```js {3-4}
import { lazy } from 'react';

// ✅ Tốt: Khai báo các component lazy bên ngoài các component của bạn
const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));

function Editor() {
  // ...
}
```
