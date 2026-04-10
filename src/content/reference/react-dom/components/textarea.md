---
title: "<textarea>"
---

<Intro>

Component [`<textarea>` có sẵn trong trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea) cho phép bạn render một text input nhiều dòng.

```js
<textarea />
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `<textarea>` {/*textarea*/}

Để hiển thị một text area, render component [`<textarea>` có sẵn trong trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea).

```js
<textarea name="postContent" />
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Props {/*props*/}

`<textarea>` hỗ trợ tất cả [common element props.](/reference/react-dom/components/common#common-props)

Bạn có thể [làm cho text area được kiểm soát](#controlling-a-text-area-with-a-state-variable) bằng cách truyền prop `value`:

* `value`: A string. Controls the text inside the text area.

Khi bạn truyền `value`, bạn cũng phải truyền một handler `onChange` cập nhật giá trị được truyền.

Nếu `<textarea>` của bạn là uncontrolled, bạn có thể truyền prop `defaultValue` thay thế:

* `defaultValue`: A string. Specifies [the initial value](#providing-an-initial-value-for-a-text-area) for a text area.

Các props `<textarea>` này liên quan đến cả text area không được kiểm soát và được kiểm soát:

* [`autoComplete`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#autocomplete): Either `'on'` or `'off'`. Specifies the autocomplete behavior.
* [`autoFocus`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#autofocus): A boolean. If `true`, React will focus the element on mount.
* `children`: `<textarea>` does not accept children. To set the initial value, use `defaultValue`.
* [`cols`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#cols): A number. Specifies the default width in average character widths. Defaults to `20`.
* [`disabled`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#disabled): A boolean. If `true`, the input will not be interactive and will appear dimmed.
* [`form`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#form): A string. Specifies the `id` of the `<form>` this input belongs to. If omitted, it's the closest parent form.
* [`maxLength`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#maxlength): A number. Specifies the maximum length of text.
* [`minLength`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#minlength): A number. Specifies the minimum length of text.
* [`name`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#name): A string. Specifies the name for this input that's [submitted with the form.](#reading-the-textarea-value-when-submitting-a-form)
* `onChange`: An [`Event` handler](/reference/react-dom/components/common#event-handler) function. Required for [controlled text areas.](#controlling-a-text-area-with-a-state-variable) Fires immediately when the input's value is changed by the user (for example, it fires on every keystroke). Behaves like the browser [`input` event.](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/input_event)
* `onChangeCapture`: A version of `onChange` that fires in the [capture phase.](/learn/responding-to-events#capture-phase-events)
* [`onInput`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/input_event): An [`Event` handler](/reference/react-dom/components/common#event-handler) function. Fires immediately when the value is changed by the user. For historical reasons, in React it is idiomatic to use `onChange` instead which works similarly.
* `onInputCapture`: A version of `onInput` that fires in the [capture phase.](/learn/responding-to-events#capture-phase-events)
* [`onInvalid`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement/invalid_event): An [`Event` handler](/reference/react-dom/components/common#event-handler) function. Fires if an input fails validation on form submit. Unlike the built-in `invalid` event, the React `onInvalid` event bubbles.
* `onInvalidCapture`: A version of `onInvalid` that fires in the [capture phase.](/learn/responding-to-events#capture-phase-events)
* [`onSelect`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLTextAreaElement/select_event): An [`Event` handler](/reference/react-dom/components/common#event-handler) function. Fires after the selection inside the `<textarea>` changes. React extends the `onSelect` event to also fire for empty selection and on edits (which may affect the selection).
* `onSelectCapture`: A version of `onSelect` that fires in the [capture phase.](/learn/responding-to-events#capture-phase-events)
* [`placeholder`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#placeholder): A string. Displayed in a dimmed color when the text area value is empty.
* [`readOnly`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#readonly): A boolean. If `true`, the text area is not editable by the user.
* [`required`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#required): A boolean. If `true`, the value must be provided for the form to submit.
* [`rows`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#rows): A number. Specifies the default height in average character heights. Defaults to `2`.
* [`wrap`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#wrap): Either `'hard'`, `'soft'`, or `'off'`. Specifies how the text should be wrapped when submitting a form.

#### Lưu ý {/*caveats*/}

- Truyền children như `<textarea>something</textarea>` không được phép. [Sử dụng `defaultValue` cho nội dung ban đầu.](#providing-an-initial-value-for-a-text-area)
- Nếu một text area nhận một prop `value` là chuỗi, nó sẽ được [coi là controlled.](#controlling-a-text-area-with-a-state-variable)
- Một text area không thể vừa controlled vừa uncontrolled cùng một lúc.
- Một text area không thể chuyển đổi giữa controlled và uncontrolled trong suốt vòng đời của nó.
- Mỗi controlled text area cần một event handler `onChange` đồng bộ cập nhật giá trị backing của nó.

---

## Cách sử dụng {/*usage*/}

### Hiển thị text area {/*displaying-a-text-area*/}

Render `<textarea>` để hiển thị một text area. Bạn có thể chỉ định kích thước mặc định của nó với các thuộc tính [`rows`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#rows) và [`cols`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#cols), nhưng theo mặc định người dùng sẽ có thể thay đổi kích thước nó. Để tắt thay đổi kích thước, bạn có thể chỉ định `resize: none` trong CSS.

<Sandpack>

```js
export default function NewPost() {
  return (
    <label>
      Write your post:
      <textarea name="postContent" rows={4} cols={40} />
    </label>
  );
}
```

```css
input { margin-left: 5px; }
textarea { margin-top: 10px; }
label { margin: 10px; }
label, textarea { display: block; }
```

</Sandpack>

---

### Cung cấp nhãn cho text area {/*providing-a-label-for-a-text-area*/}

Thông thường, bạn sẽ đặt mỗi `<textarea>` bên trong một thẻ [`<label>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/label). Điều này cho trình duyệt biết nhãn này được liên kết với text area đó. Khi người dùng nhấp vào nhãn, trình duyệt sẽ focus vào text area. Điều này cũng rất quan trọng cho khả năng trợ năng: trình đọc màn hình sẽ thông báo chú thích nhãn khi người dùng focus vào text area.

Nếu bạn không thể lồng `<textarea>` vào một `<label>`, hãy liên kết chúng bằng cách truyền cùng ID cho `<textarea id>` và [`<label htmlFor>`.](https://developer.mozilla.org/en-US/docs/Web/API/HTMLLabelElement/htmlFor) Để tránh xung đột giữa các instance của một component, hãy tạo ID đó với [`useId`.](/reference/react/useId)

<Sandpack>

```js
import { useId } from 'react';

export default function Form() {
  const postTextAreaId = useId();
  return (
    <>
      <label htmlFor={postTextAreaId}>
        Write your post:
      </label>
      <textarea
        id={postTextAreaId}
        name="postContent"
        rows={4}
        cols={40}
      />
    </>
  );
}
```

```css
input { margin: 5px; }
```

</Sandpack>

---

### Cung cấp giá trị ban đầu cho text area {/*providing-an-initial-value-for-a-text-area*/}

Bạn có thể tùy chọn chỉ định giá trị ban đầu cho text area. Truyền nó như chuỗi `defaultValue`.

<Sandpack>

```js
export default function EditPost() {
  return (
    <label>
      Edit your post:
      <textarea
        name="postContent"
        defaultValue="I really enjoyed biking yesterday!"
        rows={4}
        cols={40}
      />
    </label>
  );
}
```

```css
input { margin-left: 5px; }
textarea { margin-top: 10px; }
label { margin: 10px; }
label, textarea { display: block; }
```

</Sandpack>

<Pitfall>

Khác với HTML, việc truyền văn bản ban đầu như `<textarea>Some content</textarea>` không được hỗ trợ.

</Pitfall>

---

### Đọc giá trị text area khi gửi form {/*reading-the-text-area-value-when-submitting-a-form*/}

Thêm một [`<form>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form) xung quanh textarea của bạn với một [`<button type="submit">`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button) bên trong. Nó sẽ gọi event handler `<form onSubmit>` của bạn. Theo mặc định, trình duyệt sẽ gửi dữ liệu form đến URL hiện tại và tải lại trang. Bạn có thể ghi đè hành vi đó bằng cách gọi `e.preventDefault()`. Đọc dữ liệu form với [`new FormData(e.target)`](https://developer.mozilla.org/en-US/docs/Web/API/FormData).
<Sandpack>

```js
export default function EditPost() {
  function handleSubmit(e) {
    // Prevent the browser from reloading the page
    e.preventDefault();

    // Read the form data
    const form = e.target;
    const formData = new FormData(form);

    // You can pass formData as a fetch body directly:
    fetch('/some-api', { method: form.method, body: formData });

    // Or you can work with it as a plain object:
    const formJson = Object.fromEntries(formData.entries());
    console.log(formJson);
  }

  return (
    <form method="post" onSubmit={handleSubmit}>
      <label>
        Post title: <input name="postTitle" defaultValue="Biking" />
      </label>
      <label>
        Edit your post:
        <textarea
          name="postContent"
          defaultValue="I really enjoyed biking yesterday!"
          rows={4}
          cols={40}
        />
      </label>
      <hr />
      <button type="reset">Reset edits</button>
      <button type="submit">Save post</button>
    </form>
  );
}
```

```css
label { display: block; }
input { margin: 5px; }
```

</Sandpack>

<Note>

Đặt `name` cho `<textarea>` của bạn, ví dụ `<textarea name="postContent" />`. `name` bạn chỉ định sẽ được sử dụng như một key trong dữ liệu form, ví dụ `{ postContent: "Your post" }`.

</Note>

<Pitfall>

Theo mặc định, *bất kỳ* `<button>` nào bên trong `<form>` sẽ gửi nó. Điều này có thể gây bất ngờ! Nếu bạn có component React `Button` tùy chỉnh của riêng mình, hãy xem xét trả về [`<button type="button">`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/button) thay vì `<button>`. Sau đó, để rõ ràng, hãy sử dụng `<button type="submit">` cho các nút *được* dự định gửi form.

</Pitfall>

---

### Kiểm soát text area với biến state {/*controlling-a-text-area-with-a-state-variable*/}

Một text area như `<textarea />` là *uncontrolled.* Ngay cả khi bạn [truyền giá trị ban đầu](#providing-an-initial-value-for-a-text-area) như `<textarea defaultValue="Initial text" />`, JSX của bạn chỉ chỉ định giá trị ban đầu, không phải giá trị ngay bây giờ.

**Để render một text area *controlled*, hãy truyền prop `value` cho nó.** React sẽ buộc text area luôn có `value` bạn đã truyền. Thường thì bạn sẽ kiểm soát text area bằng cách khai báo một [biến state:](/reference/react/useState)

```js {2,6,7}
function NewPost() {
  const [postContent, setPostContent] = useState(''); // Declare a state variable...
  // ...
  return (
    <textarea
      value={postContent} // ...force the input's value to match the state variable...
      onChange={e => setPostContent(e.target.value)} // ... and update the state variable on any edits!
    />
  );
}
```

Điều này hữu ích nếu bạn muốn re-render một phần của UI để phản hồi mỗi lần gõ phím.

<Sandpack>

```js
import { useState } from 'react';
import MarkdownPreview from './MarkdownPreview.js';

export default function MarkdownEditor() {
  const [postContent, setPostContent] = useState('_Hello,_ **Markdown**!');
  return (
    <>
      <label>
        Enter some markdown:
        <textarea
          value={postContent}
          onChange={e => setPostContent(e.target.value)}
        />
      </label>
      <hr />
      <MarkdownPreview markdown={postContent} />
    </>
  );
}
```

```js src/MarkdownPreview.js
import { Remarkable } from 'remarkable';

const md = new Remarkable();

export default function MarkdownPreview({ markdown }) {
  const renderedHTML = md.render(markdown);
  return <div dangerouslySetInnerHTML={{__html: renderedHTML}} />;
}
```

```json package.json
{
  "dependencies": {
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
textarea { display: block; margin-top: 5px; margin-bottom: 10px; }
```

</Sandpack>

<Pitfall>

**Nếu bạn truyền `value` mà không có `onChange`, sẽ không thể gõ vào text area.** Khi bạn kiểm soát text area bằng cách truyền một `value` cho nó, bạn *buộc* nó luôn có giá trị bạn đã truyền. Vì vậy, nếu bạn truyền một biến state như là `value` nhưng quên cập nhật biến state đó một cách đồng bộ trong event handler `onChange`, React sẽ hoàn nguyên text area sau mỗi lần gõ phím về `value` mà bạn đã chỉ định.

</Pitfall>

---

## Xử lý sự cố {/*troubleshooting*/}

### Text area của tôi không cập nhật khi tôi gõ vào nó {/*my-text-area-doesnt-update-when-i-type-into-it*/}

Nếu bạn render một text area với `value` nhưng không có `onChange`, bạn sẽ thấy lỗi trong console:

```js
// 🔴 Bug: controlled text area with no onChange handler
<textarea value={something} />
```

<ConsoleBlock level="error">

You provided a `value` prop to a form field without an `onChange` handler. This will render a read-only field. If the field should be mutable use `defaultValue`. Otherwise, set either `onChange` or `readOnly`.

</ConsoleBlock>

Như thông báo lỗi gợi ý, nếu bạn chỉ muốn [chỉ định giá trị *ban đầu*,](#providing-an-initial-value-for-a-text-area) hãy truyền `defaultValue` thay thế:

```js
// ✅ Good: uncontrolled text area with an initial value
<textarea defaultValue={something} />
```

Nếu bạn muốn [kiểm soát text area này với một biến state,](#controlling-a-text-area-with-a-state-variable) hãy chỉ định một handler `onChange`:

```js
// ✅ Good: controlled text area with onChange
<textarea value={something} onChange={e => setSomething(e.target.value)} />
```

Nếu giá trị cố ý chỉ đọc, hãy thêm prop `readOnly` để tắt lỗi:

```js
// ✅ Good: readonly controlled text area without on change
<textarea value={something} readOnly={true} />
```

---

### Con trỏ text area của tôi nhảy về đầu trên mỗi lần gõ phím {/*my-text-area-caret-jumps-to-the-beginning-on-every-keystroke*/}

Nếu bạn [kiểm soát một text area,](#controlling-a-text-area-with-a-state-variable) bạn phải cập nhật biến state của nó thành giá trị của text area từ DOM trong khi `onChange`.

Bạn không thể cập nhật nó sang thứ gì khác ngoài `e.target.value`:

```js
function handleChange(e) {
  // 🔴 Bug: updating an input to something other than e.target.value
  setFirstName(e.target.value.toUpperCase());
}
```

You also can't update it asynchronously:

```js
function handleChange(e) {
  // 🔴 Bug: updating an input asynchronously
  setTimeout(() => {
    setFirstName(e.target.value);
  }, 100);
}
```

Để sửa code của bạn, hãy cập nhật nó đồng bộ thành `e.target.value`:

```js
function handleChange(e) {
  // ✅ Updating a controlled input to e.target.value synchronously
  setFirstName(e.target.value);
}
```

Nếu điều này không sửa vấn đề, có thể text area bị xóa và thêm lại từ DOM trên mỗi lần gõ phím. Điều này có thể xảy ra nếu bạn vô tình [reset state](/learn/preserving-and-resetting-state) trên mỗi lần re-render. Ví dụ, điều này có thể xảy ra nếu text area hoặc một trong các cha của nó luôn nhận thuộc tính `key` khác, hoặc nếu bạn lồng các định nghĩa component (không được phép trong React và khiến component "bên trong" remount trên mỗi lần render).

---

### Tôi gặp lỗi: "A component is changing an uncontrolled input to be controlled" {/*im-getting-an-error-a-component-is-changing-an-uncontrolled-input-to-be-controlled*/}


Nếu bạn cung cấp `value` cho component, nó phải luôn là một chuỗi trong suốt vòng đời của nó.

Bạn không thể truyền `value={undefined}` trước và sau đó truyền `value="some string"` vì React sẽ không biết bạn muốn component là uncontrolled hay controlled. Một controlled component nên luôn nhận một chuỗi `value`, không phải `null` hoặc `undefined`.

Nếu `value` của bạn đến từ API hoặc biến state, nó có thể được khởi tạo thành `null` hoặc `undefined`. Trong trường hợp đó, hãy đặt nó thành chuỗi rỗng (`''`) ban đầu, hoặc truyền `value={someValue ?? ''}` để đảm bảo `value` là một chuỗi.
