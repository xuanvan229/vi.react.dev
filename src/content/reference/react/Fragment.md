---
title: <Fragment> (<>...</>)
---

<Intro>

`<Fragment>`, thường được sử dụng qua cú pháp `<>...</>`, cho phép bạn nhóm các element mà không cần một node bao bọc.

<Canary> Fragments cũng có thể nhận refs, cho phép tương tác với các node DOM bên dưới mà không cần thêm các element bao bọc. Xem tham chiếu và cách sử dụng bên dưới.</Canary>

```js
<>
  <OneChild />
  <AnotherChild />
</>
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `<Fragment>` {/*fragment*/}

Bọc các element trong `<Fragment>` để nhóm chúng lại với nhau trong các tình huống bạn cần một element duy nhất. Việc nhóm các element trong `Fragment` không có tác động đến DOM kết quả; nó giống như khi các element không được nhóm. Thẻ JSX rỗng `<></>` là viết tắt của `<Fragment></Fragment>` trong hầu hết các trường hợp.

#### Props {/*props*/}

- **tùy chọn** `key`: Các Fragment được khai báo với cú pháp `<Fragment>` tường minh có thể có [keys.](/learn/rendering-lists#keeping-list-items-in-order-with-key)
- <CanaryBadge />  **tùy chọn** `ref`: Một đối tượng ref (ví dụ từ [`useRef`](/reference/react/useRef)) hoặc [hàm callback](/reference/react-dom/components/common#ref-callback). React cung cấp một `FragmentInstance` làm giá trị ref với các phương thức để tương tác với các node DOM được bọc bởi Fragment.

### <CanaryBadge /> FragmentInstance {/*fragmentinstance*/}

Khi bạn truyền ref cho một fragment, React cung cấp một đối tượng `FragmentInstance` với các phương thức để tương tác với các node DOM được bọc bởi fragment:

**Các phương thức xử lý sự kiện:**
- `addEventListener(type, listener, options?)`: Thêm một event listener cho tất cả các DOM con cấp một của Fragment.
- `removeEventListener(type, listener, options?)`: Xóa một event listener khỏi tất cả các DOM con cấp một của Fragment.
- `dispatchEvent(event)`: Gửi một event đến một con ảo của Fragment để gọi bất kỳ listener nào đã thêm và có thể bubble lên DOM cha.

**Các phương thức layout:**
- `compareDocumentPosition(otherNode)`: So sánh vị trí tài liệu của Fragment với một node khác.
  - Nếu Fragment có con, giá trị `compareDocumentPosition` native sẽ được trả về.
  - Fragment rỗng sẽ cố gắng so sánh vị trí trong cây React và bao gồm `Node.DOCUMENT_POSITION_IMPLEMENTATION_SPECIFIC`.
  - Các element có mối quan hệ khác nhau trong cây React và cây DOM do portaling hoặc các insertion khác là `Node.DOCUMENT_POSITION_IMPLEMENTATION_SPECIFIC`.
- `getClientRects()`: Trả về một mảng phẳng các đối tượng `DOMRect` đại diện cho các hình chữ nhật bao quanh của tất cả các con.
- `getRootNode()`: Trả về node gốc chứa node DOM cha của Fragment.

**Các phương thức quản lý focus:**
- `focus(options?)`: Focus vào node DOM có thể focus đầu tiên trong Fragment. Focus được thử trên các con lồng nhau theo chiều sâu.
- `focusLast(options?)`: Focus vào node DOM có thể focus cuối cùng trong Fragment. Focus được thử trên các con lồng nhau theo chiều sâu.
- `blur()`: Xóa focus nếu `document.activeElement` nằm trong Fragment.

**Các phương thức observer:**
- `observeUsing(observer)`: Bắt đầu observe các con DOM của Fragment với một IntersectionObserver hoặc ResizeObserver.
- `unobserveUsing(observer)`: Dừng observe các con DOM của Fragment với observer đã chỉ định.

#### Lưu ý {/*caveats*/}

- Nếu bạn muốn truyền `key` cho một Fragment, bạn không thể sử dụng cú pháp `<>...</>`. Bạn phải import `Fragment` từ `'react'` một cách tường minh và render `<Fragment key={yourKey}>...</Fragment>`.

- React không [reset state](/learn/preserving-and-resetting-state) khi bạn chuyển từ render `<><Child /></>` sang `[<Child />]` hoặc ngược lại, hoặc khi bạn chuyển từ render `<><Child /></>` sang `<Child />` và ngược lại. Điều này chỉ hoạt động ở một cấp sâu: ví dụ, chuyển từ `<><><Child /></></>` sang `<Child />` sẽ reset state. Xem ngữ nghĩa chính xác [tại đây.](https://gist.github.com/clemmy/b3ef00f9507909429d8aa0d3ee4f986b)

- <CanaryBadge /> Nếu bạn muốn truyền `ref` cho một Fragment, bạn không thể sử dụng cú pháp `<>...</>`. Bạn phải import `Fragment` từ `'react'` một cách tường minh và render `<Fragment ref={yourRef}>...</Fragment>`.

---

## Cách sử dụng {/*usage*/}

### Trả về nhiều element {/*returning-multiple-elements*/}

Sử dụng `Fragment`, hoặc cú pháp tương đương `<>...</>`, để nhóm nhiều element lại với nhau. Bạn có thể sử dụng nó để đặt nhiều element vào bất kỳ nơi nào mà một element duy nhất có thể đặt được. Ví dụ, một component chỉ có thể trả về một element, nhưng bằng cách sử dụng Fragment, bạn có thể nhóm nhiều element lại với nhau và trả về chúng như một nhóm:

```js {3,6}
function Post() {
  return (
    <>
      <PostTitle />
      <PostBody />
    </>
  );
}
```

Fragments rất hữu ích vì việc nhóm các element với Fragment không ảnh hưởng đến layout hoặc style, khác với việc bạn bọc các element trong một container khác như một element DOM. Nếu bạn kiểm tra ví dụ này với công cụ trình duyệt, bạn sẽ thấy tất cả các node DOM `<h1>` và `<article>` xuất hiện như các anh em cùng cấp mà không có wrapper bao quanh:

<Sandpack>

```js
export default function Blog() {
  return (
    <>
      <Post title="An update" body="It's been a while since I posted..." />
      <Post title="My new blog" body="I am starting a new blog!" />
    </>
  )
}

function Post({ title, body }) {
  return (
    <>
      <PostTitle title={title} />
      <PostBody body={body} />
    </>
  );
}

function PostTitle({ title }) {
  return <h1>{title}</h1>
}

function PostBody({ body }) {
  return (
    <article>
      <p>{body}</p>
    </article>
  );
}
```

</Sandpack>

<DeepDive>

#### Làm thế nào để viết Fragment mà không dùng cú pháp đặc biệt? {/*how-to-write-a-fragment-without-the-special-syntax*/}

Ví dụ trên tương đương với việc import `Fragment` từ React:

```js {1,5,8}
import { Fragment } from 'react';

function Post() {
  return (
    <Fragment>
      <PostTitle />
      <PostBody />
    </Fragment>
  );
}
```

Thông thường bạn sẽ không cần điều này trừ khi bạn cần [truyền `key` cho `Fragment` của bạn.](#rendering-a-list-of-fragments)

</DeepDive>

---

### Gán nhiều element vào một biến {/*assigning-multiple-elements-to-a-variable*/}

Giống như bất kỳ element nào khác, bạn có thể gán các Fragment element vào biến, truyền chúng làm props, v.v.:

```js
function CloseDialog() {
  const buttons = (
    <>
      <OKButton />
      <CancelButton />
    </>
  );
  return (
    <AlertDialog buttons={buttons}>
      Are you sure you want to leave this page?
    </AlertDialog>
  );
}
```

---

### Nhóm element với văn bản {/*grouping-elements-with-text*/}

Bạn có thể sử dụng `Fragment` để nhóm văn bản với các component:

```js
function DateRangePicker({ start, end }) {
  return (
    <>
      From
      <DatePicker date={start} />
      to
      <DatePicker date={end} />
    </>
  );
}
```

---

### Render một danh sách Fragments {/*rendering-a-list-of-fragments*/}

Đây là tình huống bạn cần viết `Fragment` tường minh thay vì sử dụng cú pháp `<></>`. Khi bạn [render nhiều element trong một vòng lặp](/learn/rendering-lists), bạn cần gán một `key` cho mỗi element. Nếu các element trong vòng lặp là Fragments, bạn cần sử dụng cú pháp element JSX bình thường để cung cấp thuộc tính `key`:

```js {3,6}
function Blog() {
  return posts.map(post =>
    <Fragment key={post.id}>
      <PostTitle title={post.title} />
      <PostBody body={post.body} />
    </Fragment>
  );
}
```

Bạn có thể kiểm tra DOM để xác nhận rằng không có element bao bọc nào xung quanh các con của Fragment:

<Sandpack>

```js
import { Fragment } from 'react';

const posts = [
  { id: 1, title: 'An update', body: "It's been a while since I posted..." },
  { id: 2, title: 'My new blog', body: 'I am starting a new blog!' }
];

export default function Blog() {
  return posts.map(post =>
    <Fragment key={post.id}>
      <PostTitle title={post.title} />
      <PostBody body={post.body} />
    </Fragment>
  );
}

function PostTitle({ title }) {
  return <h1>{title}</h1>
}

function PostBody({ body }) {
  return (
    <article>
      <p>{body}</p>
    </article>
  );
}
```

</Sandpack>

---

### <CanaryBadge /> Sử dụng Fragment refs để tương tác DOM {/*using-fragment-refs-for-dom-interaction*/}

Fragment refs cho phép bạn tương tác với các node DOM được bọc bởi Fragment mà không cần thêm các element wrapper bổ sung. Điều này hữu ích cho xử lý sự kiện, theo dõi khả năng hiển thị, quản lý focus, và thay thế các pattern đã lỗi thời như `ReactDOM.findDOMNode()`.

```js
import { Fragment } from 'react';

function ClickableFragment({ children, onClick }) {
  return (
    <Fragment ref={fragmentInstance => {
      fragmentInstance.addEventListener('click', handleClick);
      return () => fragmentInstance.removeEventListener('click', handleClick);
    }}>
      {children}
    </Fragment>
  );
}
```
---

### <CanaryBadge /> Theo dõi khả năng hiển thị với Fragment refs {/*tracking-visibility-with-fragment-refs*/}

Fragment refs hữu ích cho việc theo dõi khả năng hiển thị và intersection observation. Điều này cho phép bạn giám sát khi nội dung trở nên hiển thị mà không yêu cầu các Component con phải expose refs:

```js {19,21,31-34}
import { Fragment, useRef, useLayoutEffect } from 'react';

function VisibilityObserverFragment({ threshold = 0.5, onVisibilityChange, children }) {
  const fragmentRef = useRef(null);

  useLayoutEffect(() => {
    const observer = new IntersectionObserver(
      (entries) => {
        onVisibilityChange(entries.some(entry => entry.isIntersecting))
      },
      { threshold }
    );

    fragmentRef.current.observeUsing(observer);
    return () => fragmentRef.current.unobserveUsing(observer);
  }, [threshold, onVisibilityChange]);

  return (
    <Fragment ref={fragmentRef}>
      {children}
    </Fragment>
  );
}

function MyComponent() {
  const handleVisibilityChange = (isVisible) => {
    console.log('Component is', isVisible ? 'visible' : 'hidden');
  };

  return (
    <VisibilityObserverFragment onVisibilityChange={handleVisibilityChange}>
      <SomeThirdPartyComponent />
      <AnotherComponent />
    </VisibilityObserverFragment>
  );
}
```

Pattern này là một thay thế cho việc ghi log khả năng hiển thị dựa trên Effect, vốn là một anti-pattern trong hầu hết các trường hợp. Việc chỉ dựa vào Effects không đảm bảo rằng Component đã render là observable bởi người dùng.

---

### <CanaryBadge /> Quản lý focus với Fragment refs {/*focus-management-with-fragment-refs*/}

Fragment refs cung cấp các phương thức quản lý focus hoạt động trên tất cả các node DOM trong Fragment:

```js
import { Fragment, useRef } from 'react';

function FocusFragment({ children }) {
  return (
    <Fragment ref={(fragmentInstance) => fragmentInstance?.focus()}>
      {children}
    </Fragment>
  );
}
```

Phương thức `focus()` focus vào element có thể focus đầu tiên trong Fragment, trong khi `focusLast()` focus vào element có thể focus cuối cùng.
