---
title: useImperativeHandle
---

<Intro>

`useImperativeHandle` là một React Hook cho phép bạn tùy chỉnh handle được hiển thị như một [ref.](/learn/manipulating-the-dom-with-refs)

```js
useImperativeHandle(ref, createHandle, dependencies?)
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `useImperativeHandle(ref, createHandle, dependencies?)` {/*useimperativehandle*/}

Gọi `useImperativeHandle` ở cấp cao nhất của component để tùy chỉnh ref handle mà nó hiển thị:

```js
import { useImperativeHandle } from 'react';

function MyInput({ ref }) {
  useImperativeHandle(ref, () => {
    return {
      // ... các method của bạn ...
    };
  }, []);
  // ...
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `ref`: `ref` mà bạn nhận được như một prop cho component `MyInput`.

* `createHandle`: Một hàm không nhận đối số và trả về ref handle mà bạn muốn hiển thị. Ref handle đó có thể có bất kỳ kiểu nào. Thông thường, bạn sẽ trả về một object với các method bạn muốn hiển thị.

* **tùy chọn** `dependencies`: Danh sách tất cả các giá trị reactive được tham chiếu bên trong code `createHandle`. Các giá trị reactive bao gồm props, state, và tất cả các biến và hàm được khai báo trực tiếp bên trong thân component của bạn. Nếu bộ linter của bạn được [cấu hình cho React](/learn/editor-setup#linting), nó sẽ xác minh rằng mọi giá trị reactive được chỉ định chính xác như một dependency. Danh sách dependency phải có số lượng mục cố định và được viết inline như `[dep1, dep2, dep3]`. React sẽ so sánh mỗi dependency với giá trị trước đó của nó bằng cách sử dụng phép so sánh [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is). Nếu một lần re-render dẫn đến thay đổi một số dependency, hoặc nếu bạn bỏ qua đối số này, hàm `createHandle` của bạn sẽ thực thi lại, và handle mới được tạo ra sẽ được gán cho ref.

<Note>

Bắt đầu từ React 19, [`ref` có sẵn như một prop.](/blog/2024/12/05/react-19#ref-as-a-prop) Trong React 18 và các phiên bản trước, cần phải lấy `ref` từ [`forwardRef`.](/reference/react/forwardRef)

</Note>

#### Giá trị trả về {/*returns*/}

`useImperativeHandle` trả về `undefined`.

---

## Cách sử dụng {/*usage*/}

### Hiển thị một ref handle tùy chỉnh cho component cha {/*exposing-a-custom-ref-handle-to-the-parent-component*/}

Để hiển thị một DOM node cho phần tử cha, hãy truyền prop `ref` vào node.

```js {2}
function MyInput({ ref }) {
  return <input ref={ref} />;
};
```

Với code trên, [một ref đến `MyInput` sẽ nhận `<input>` DOM node.](/learn/manipulating-the-dom-with-refs) Tuy nhiên, bạn có thể hiển thị một giá trị tùy chỉnh thay thế. Để tùy chỉnh handle được hiển thị, hãy gọi `useImperativeHandle` ở cấp cao nhất của component:

```js {4-8}
import { useImperativeHandle } from 'react';

function MyInput({ ref }) {
  useImperativeHandle(ref, () => {
    return {
      // ... các method của bạn ...
    };
  }, []);

  return <input />;
};
```

Lưu ý rằng trong code trên, `ref` không còn được truyền cho `<input>` nữa.

Ví dụ, giả sử bạn không muốn hiển thị toàn bộ `<input>` DOM node, nhưng bạn muốn hiển thị hai method của nó: `focus` và `scrollIntoView`. Để làm điều này, hãy giữ DOM trình duyệt thực trong một ref riêng biệt. Sau đó sử dụng `useImperativeHandle` để hiển thị một handle chỉ có các method mà bạn muốn component cha gọi:

```js {7-14}
import { useRef, useImperativeHandle } from 'react';

function MyInput({ ref }) {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => {
    return {
      focus() {
        inputRef.current.focus();
      },
      scrollIntoView() {
        inputRef.current.scrollIntoView();
      },
    };
  }, []);

  return <input ref={inputRef} />;
};
```

Bây giờ, nếu component cha nhận được một ref đến `MyInput`, nó sẽ có thể gọi các method `focus` và `scrollIntoView` trên đó. Tuy nhiên, nó sẽ không có quyền truy cập đầy đủ vào `<input>` DOM node bên dưới.

<Sandpack>

```js
import { useRef } from 'react';
import MyInput from './MyInput.js';

export default function Form() {
  const ref = useRef(null);

  function handleClick() {
    ref.current.focus();
    // This won't work because the DOM node isn't exposed:
    // ref.current.style.opacity = 0.5;
  }

  return (
    <form>
      <MyInput placeholder="Enter your name" ref={ref} />
      <button type="button" onClick={handleClick}>
        Edit
      </button>
    </form>
  );
}
```

```js src/MyInput.js
import { useRef, useImperativeHandle } from 'react';

function MyInput({ ref, ...props }) {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => {
    return {
      focus() {
        inputRef.current.focus();
      },
      scrollIntoView() {
        inputRef.current.scrollIntoView();
      },
    };
  }, []);

  return <input {...props} ref={inputRef} />;
};

export default MyInput;
```

```css
input {
  margin: 5px;
}
```

</Sandpack>

---

### Hiển thị các method imperative của riêng bạn {/*exposing-your-own-imperative-methods*/}

Các method bạn hiển thị qua imperative handle không nhất thiết phải khớp hoàn toàn với các DOM method. Ví dụ, component `Post` này hiển thị method `scrollAndFocusAddComment` qua một imperative handle. Điều này cho phép `Page` cha cuộn danh sách bình luận *và* focus vào ô nhập liệu khi bạn nhấp vào nút:

<Sandpack>

```js
import { useRef } from 'react';
import Post from './Post.js';

export default function Page() {
  const postRef = useRef(null);

  function handleClick() {
    postRef.current.scrollAndFocusAddComment();
  }

  return (
    <>
      <button onClick={handleClick}>
        Write a comment
      </button>
      <Post ref={postRef} />
    </>
  );
}
```

```js src/Post.js
import { useRef, useImperativeHandle } from 'react';
import CommentList from './CommentList.js';
import AddComment from './AddComment.js';

function Post({ ref }) {
  const commentsRef = useRef(null);
  const addCommentRef = useRef(null);

  useImperativeHandle(ref, () => {
    return {
      scrollAndFocusAddComment() {
        commentsRef.current.scrollToBottom();
        addCommentRef.current.focus();
      }
    };
  }, []);

  return (
    <>
      <article>
        <p>Welcome to my blog!</p>
      </article>
      <CommentList ref={commentsRef} />
      <AddComment ref={addCommentRef} />
    </>
  );
};

export default Post;
```


```js src/CommentList.js
import { useRef, useImperativeHandle } from 'react';

function CommentList({ ref }) {
  const divRef = useRef(null);

  useImperativeHandle(ref, () => {
    return {
      scrollToBottom() {
        const node = divRef.current;
        node.scrollTop = node.scrollHeight;
      }
    };
  }, []);

  let comments = [];
  for (let i = 0; i < 50; i++) {
    comments.push(<p key={i}>Comment #{i}</p>);
  }

  return (
    <div className="CommentList" ref={divRef}>
      {comments}
    </div>
  );
}

export default CommentList;
```

```js src/AddComment.js
import { useRef, useImperativeHandle } from 'react';

function AddComment({ ref }) {
  return <input placeholder="Add comment..." ref={ref} />;
}

export default AddComment;
```

```css
.CommentList {
  height: 100px;
  overflow: scroll;
  border: 1px solid black;
  margin-top: 20px;
  margin-bottom: 20px;
}
```

</Sandpack>

<Pitfall>

**Đừng lạm dụng refs.** Bạn chỉ nên sử dụng refs cho các hành vi *imperative* mà bạn không thể diễn đạt như props: ví dụ, cuộn đến một node, focus vào một node, kích hoạt animation, chọn text, v.v.

**Nếu bạn có thể diễn đạt điều gì đó như một prop, bạn không nên sử dụng ref.** Ví dụ, thay vì hiển thị một imperative handle như `{ open, close }` từ component `Modal`, tốt hơn là nhận `isOpen` như một prop như `<Modal isOpen={isOpen} />`. [Effects](/learn/synchronizing-with-effects) có thể giúp bạn hiển thị các hành vi imperative qua props.

</Pitfall>
