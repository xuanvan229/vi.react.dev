---
title: useId
---

<Intro>

`useId` là một React Hook để tạo các ID duy nhất có thể được truyền vào các thuộc tính accessibility.

```js
const id = useId()
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `useId()` {/*useid*/}

Gọi `useId` ở cấp cao nhất của component để tạo một ID duy nhất:

```js
import { useId } from 'react';

function PasswordField() {
  const passwordHintId = useId();
  // ...
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

`useId` không nhận tham số nào.

#### Giá trị trả về {/*returns*/}

`useId` trả về một chuỗi ID duy nhất được liên kết với lời gọi `useId` cụ thể này trong component cụ thể này.

#### Lưu ý {/*caveats*/}

* `useId` là một Hook, vì vậy bạn chỉ có thể gọi nó **ở cấp cao nhất của component** hoặc các Hook của riêng bạn. Bạn không thể gọi nó bên trong vòng lặp hoặc điều kiện. Nếu bạn cần điều đó, hãy tách một component mới và chuyển state vào đó.

* `useId` **không nên được dùng để tạo cache keys** cho [use()](/reference/react/use). ID ổn định khi component được mount nhưng có thể thay đổi trong quá trình render. Cache keys nên được tạo từ dữ liệu của bạn.

* `useId` **không nên được dùng để tạo keys** trong danh sách. [Keys nên được tạo từ dữ liệu của bạn.](/learn/rendering-lists#where-to-get-your-key)

* `useId` hiện tại không thể được dùng trong [async Server Components](/reference/rsc/server-components#async-components-with-server-components).

---

## Cách sử dụng {/*usage*/}

<Pitfall>

**Không gọi `useId` để tạo keys trong danh sách.** [Keys nên được tạo từ dữ liệu của bạn.](/learn/rendering-lists#where-to-get-your-key)

</Pitfall>

### Tạo ID duy nhất cho các thuộc tính accessibility {/*generating-unique-ids-for-accessibility-attributes*/}

Gọi `useId` ở cấp cao nhất của component để tạo một ID duy nhất:

```js [[1, 4, "passwordHintId"]]
import { useId } from 'react';

function PasswordField() {
  const passwordHintId = useId();
  // ...
```

Sau đó bạn có thể truyền <CodeStep step={1}>ID được tạo ra</CodeStep> cho các thuộc tính khác nhau:

```js [[1, 2, "passwordHintId"], [1, 3, "passwordHintId"]]
<>
  <input type="password" aria-describedby={passwordHintId} />
  <p id={passwordHintId}>
</>
```

**Hãy xem một ví dụ để thấy khi nào điều này hữu ích.**

[Các thuộc tính accessibility HTML](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA) như [`aria-describedby`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-describedby) cho phép bạn chỉ định rằng hai thẻ có liên quan với nhau. Ví dụ, bạn có thể chỉ định rằng một phần tử (như một input) được mô tả bởi phần tử khác (như một đoạn văn).

Trong HTML thông thường, bạn sẽ viết như thế này:

```html {5,8}
<label>
  Password:
  <input
    type="password"
    aria-describedby="password-hint"
  />
</label>
<p id="password-hint">
  The password should contain at least 18 characters
</p>
```

Tuy nhiên, việc hardcode ID như thế này không phải là cách tốt trong React. Một component có thể được render nhiều hơn một lần trên trang--nhưng ID phải là duy nhất! Thay vì hardcode một ID, hãy tạo một ID duy nhất với `useId`:

```js {4,11,14}
import { useId } from 'react';

function PasswordField() {
  const passwordHintId = useId();
  return (
    <>
      <label>
        Password:
        <input
          type="password"
          aria-describedby={passwordHintId}
        />
      </label>
      <p id={passwordHintId}>
        The password should contain at least 18 characters
      </p>
    </>
  );
}
```

Bây giờ, dù `PasswordField` xuất hiện nhiều lần trên màn hình, các ID được tạo ra sẽ không bị trùng lặp.

<Sandpack>

```js
import { useId } from 'react';

function PasswordField() {
  const passwordHintId = useId();
  return (
    <>
      <label>
        Password:
        <input
          type="password"
          aria-describedby={passwordHintId}
        />
      </label>
      <p id={passwordHintId}>
        The password should contain at least 18 characters
      </p>
    </>
  );
}

export default function App() {
  return (
    <>
      <h2>Choose password</h2>
      <PasswordField />
      <h2>Confirm password</h2>
      <PasswordField />
    </>
  );
}
```

```css
input { margin: 5px; }
```

</Sandpack>

[Xem video này](https://www.youtube.com/watch?v=0dNzNcuEuOo) để thấy sự khác biệt trong trải nghiệm người dùng với các công nghệ hỗ trợ.

<Pitfall>

Với [server rendering](/reference/react-dom/server), **`useId` yêu cầu cây component giống hệt nhau trên server và client**. Nếu các cây bạn render trên server và client không khớp chính xác, các ID được tạo ra sẽ không khớp.

</Pitfall>

<DeepDive>

#### Tại sao useId tốt hơn bộ đếm tăng dần? {/*why-is-useid-better-than-an-incrementing-counter*/}

Bạn có thể tự hỏi tại sao `useId` tốt hơn việc tăng một biến global như `nextId++`.

Lợi ích chính của `useId` là React đảm bảo nó hoạt động với [server rendering.](/reference/react-dom/server) Trong quá trình server rendering, các component của bạn tạo ra HTML output. Sau đó, trên client, [hydration](/reference/react-dom/client/hydrateRoot) gắn các event handler của bạn vào HTML đã tạo. Để hydration hoạt động, client output phải khớp với HTML của server.

Điều này rất khó đảm bảo với bộ đếm tăng dần vì thứ tự mà các Client Components được hydrated có thể không khớp với thứ tự mà HTML server được emit ra. Bằng cách gọi `useId`, bạn đảm bảo rằng hydration sẽ hoạt động và output sẽ khớp giữa server và client.

Bên trong React, `useId` được tạo ra từ "đường dẫn cha" của component đang gọi. Đây là lý do tại sao, nếu cây client và server giống nhau, "đường dẫn cha" sẽ khớp bất kể thứ tự render.

</DeepDive>

---

### Tạo ID cho nhiều phần tử liên quan {/*generating-ids-for-several-related-elements*/}

Nếu bạn cần đặt ID cho nhiều phần tử liên quan, bạn có thể gọi `useId` để tạo một tiền tố chung cho chúng:

<Sandpack>

```js
import { useId } from 'react';

export default function Form() {
  const id = useId();
  return (
    <form>
      <label htmlFor={id + '-firstName'}>First Name:</label>
      <input id={id + '-firstName'} type="text" />
      <hr />
      <label htmlFor={id + '-lastName'}>Last Name:</label>
      <input id={id + '-lastName'} type="text" />
    </form>
  );
}
```

```css
input { margin: 5px; }
```

</Sandpack>

Điều này cho phép bạn tránh gọi `useId` cho mỗi phần tử đơn lẻ cần một ID duy nhất.

---

### Chỉ định tiền tố chung cho tất cả ID được tạo ra {/*specifying-a-shared-prefix-for-all-generated-ids*/}

Nếu bạn render nhiều ứng dụng React độc lập trên một trang, hãy truyền `identifierPrefix` làm một tùy chọn cho các lời gọi [`createRoot`](/reference/react-dom/client/createRoot#parameters) hoặc [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) của bạn. Điều này đảm bảo các ID được tạo ra bởi hai ứng dụng khác nhau không bao giờ bị trùng lặp vì mọi identifier được tạo bằng `useId` sẽ bắt đầu bằng tiền tố riêng biệt mà bạn đã chỉ định.

<Sandpack>

```html public/index.html
<!DOCTYPE html>
<html>
  <head><title>My app</title></head>
  <body>
    <div id="root1"></div>
    <div id="root2"></div>
  </body>
</html>
```

```js
import { useId } from 'react';

function PasswordField() {
  const passwordHintId = useId();
  console.log('Generated identifier:', passwordHintId)
  return (
    <>
      <label>
        Password:
        <input
          type="password"
          aria-describedby={passwordHintId}
        />
      </label>
      <p id={passwordHintId}>
        The password should contain at least 18 characters
      </p>
    </>
  );
}

export default function App() {
  return (
    <>
      <h2>Choose password</h2>
      <PasswordField />
    </>
  );
}
```

```js src/index.js active
import { createRoot } from 'react-dom/client';
import App from './App.js';
import './styles.css';

const root1 = createRoot(document.getElementById('root1'), {
  identifierPrefix: 'my-first-app-'
});
root1.render(<App />);

const root2 = createRoot(document.getElementById('root2'), {
  identifierPrefix: 'my-second-app-'
});
root2.render(<App />);
```

```css
#root1 {
  border: 5px solid blue;
  padding: 10px;
  margin: 5px;
}

#root2 {
  border: 5px solid green;
  padding: 10px;
  margin: 5px;
}

input { margin: 5px; }
```

</Sandpack>

---

### Sử dụng cùng tiền tố ID trên client và server {/*using-the-same-id-prefix-on-the-client-and-the-server*/}

Nếu bạn [render nhiều ứng dụng React độc lập trên cùng một trang](#specifying-a-shared-prefix-for-all-generated-ids), và một số ứng dụng này được render trên server, hãy đảm bảo rằng `identifierPrefix` bạn truyền vào lời gọi [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) trên phía client giống với `identifierPrefix` bạn truyền vào các [server API](/reference/react-dom/server) như [`renderToPipeableStream`.](/reference/react-dom/server/renderToPipeableStream)

```js
// Server
import { renderToPipeableStream } from 'react-dom/server';

const { pipe } = renderToPipeableStream(
  <App />,
  { identifierPrefix: 'react-app1' }
);
```

```js
// Client
import { hydrateRoot } from 'react-dom/client';

const domNode = document.getElementById('root');
const root = hydrateRoot(
  domNode,
  reactNode,
  { identifierPrefix: 'react-app1' }
);
```

Bạn không cần truyền `identifierPrefix` nếu chỉ có một ứng dụng React trên trang.
