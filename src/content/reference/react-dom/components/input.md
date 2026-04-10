---
title: "<input>"
---

<Intro>

Component [`<input>` có sẵn trong trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input) cho phép bạn render các loại input form khác nhau.

```js
<input />
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `<input>` {/*input*/}

Để hiển thị một input, render component [`<input>` có sẵn trong trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input).

```js
<input name="myInput" />
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Props {/*props*/}

`<input>` hỗ trợ tất cả [common element props.](/reference/react-dom/components/common#common-props)

- [`formAction`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#formaction): Một chuỗi hoặc hàm. Ghi đè `<form action>` cha cho `type="submit"` và `type="image"`. Khi một URL được truyền cho `action`, form sẽ hoạt động như một form HTML chuẩn. Khi một hàm được truyền cho `formAction`, hàm đó sẽ xử lý việc gửi form. Xem [`<form action>`](/reference/react-dom/components/form#props).

Bạn có thể [làm cho input được kiểm soát](#controlling-an-input-with-a-state-variable) bằng cách truyền một trong các props này:

* [`checked`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement#checked): A boolean. For a checkbox input or a radio button, controls whether it is selected.
* [`value`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement#value): A string. For a text input, controls its text. (For a radio button, specifies its form data.)

Khi bạn truyền một trong hai, bạn cũng phải truyền một handler `onChange` cập nhật giá trị được truyền.

Các props `<input>` này chỉ liên quan đến các input không được kiểm soát:

* [`defaultChecked`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement#defaultChecked): A boolean. Specifies [the initial value](#providing-an-initial-value-for-an-input) for `type="checkbox"` and `type="radio"` inputs.
* [`defaultValue`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement#defaultValue): A string. Specifies [the initial value](#providing-an-initial-value-for-an-input) for a text input.

Các props `<input>` này liên quan đến cả input không được kiểm soát và được kiểm soát:

* [`accept`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#accept): A string. Specifies which filetypes are accepted by a `type="file"` input.
* [`alt`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#alt): A string. Specifies the alternative image text for a `type="image"` input.
* [`capture`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#capture): A string. Specifies the media (microphone, video, or camera) captured by a `type="file"` input.
* [`autoComplete`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#autocomplete): A string. Specifies one of the possible [autocomplete behaviors.](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/autocomplete#values)
* [`autoFocus`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#autofocus): A boolean. If `true`, React will focus the element on mount.
* [`dirname`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#dirname): A string. Specifies the form field name for the element's directionality.
* [`disabled`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#disabled): A boolean. If `true`, the input will not be interactive and will appear dimmed.
* `children`: `<input>` does not accept children.
* [`form`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#form): A string. Specifies the `id` of the `<form>` this input belongs to. If omitted, it's the closest parent form.
* [`formAction`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#formaction): A string. Overrides the parent `<form action>` for `type="submit"` and `type="image"`.
* [`formEnctype`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#formenctype): A string. Overrides the parent `<form enctype>` for `type="submit"` and `type="image"`.
* [`formMethod`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#formmethod): A string. Overrides the parent `<form method>` for `type="submit"` and `type="image"`.
* [`formNoValidate`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#formnovalidate): A string. Overrides the parent `<form noValidate>` for `type="submit"` and `type="image"`.
* [`formTarget`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#formtarget): A string. Overrides the parent `<form target>` for `type="submit"` and `type="image"`.
* [`height`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#height): A string. Specifies the image height for `type="image"`.
* [`list`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#list): A string. Specifies the `id` of the `<datalist>` with the autocomplete options.
* [`max`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#max): A number. Specifies the maximum value of numerical and datetime inputs.
* [`maxLength`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#maxlength): A number. Specifies the maximum length of text and other inputs.
* [`min`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#min): A number. Specifies the minimum value of numerical and datetime inputs.
* [`minLength`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#minlength): A number. Specifies the minimum length of text and other inputs.
* [`multiple`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#multiple): A boolean. Specifies whether multiple values are allowed for `<type="file"` and `type="email"`.
* [`name`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#name): A string. Specifies the name for this input that's [submitted with the form.](#reading-the-input-values-when-submitting-a-form)
* `onChange`: An [`Event` handler](/reference/react-dom/components/common#event-handler) function. Required for [controlled inputs.](#controlling-an-input-with-a-state-variable) Fires immediately when the input's value is changed by the user (for example, it fires on every keystroke). Behaves like the browser [`input` event.](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/input_event)
* `onChangeCapture`: A version of `onChange` that fires in the [capture phase.](/learn/responding-to-events#capture-phase-events)
* [`onInput`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/input_event): An [`Event` handler](/reference/react-dom/components/common#event-handler) function. Fires immediately when the value is changed by the user. For historical reasons, in React it is idiomatic to use `onChange` instead which works similarly.
* `onInputCapture`: A version of `onInput` that fires in the [capture phase.](/learn/responding-to-events#capture-phase-events)
* [`onInvalid`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement/invalid_event): An [`Event` handler](/reference/react-dom/components/common#event-handler) function. Fires if an input fails validation on form submit. Unlike the built-in `invalid` event, the React `onInvalid` event bubbles.
* `onInvalidCapture`: A version of `onInvalid` that fires in the [capture phase.](/learn/responding-to-events#capture-phase-events)
* [`onSelect`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement/select_event): An [`Event` handler](/reference/react-dom/components/common#event-handler) function. Fires after the selection inside the `<input>` changes. React extends the `onSelect` event to also fire for empty selection and on edits (which may affect the selection).
* `onSelectCapture`: A version of `onSelect` that fires in the [capture phase.](/learn/responding-to-events#capture-phase-events)
* [`pattern`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#pattern): A string. Specifies the pattern that the `value` must match.
* [`placeholder`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#placeholder): A string. Displayed in a dimmed color when the input value is empty.
* [`readOnly`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#readonly): A boolean. If `true`, the input is not editable by the user.
* [`required`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#required): A boolean. If `true`, the value must be provided for the form to submit.
* [`size`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#size): A number. Similar to setting width, but the unit depends on the control.
* [`src`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#src): A string. Specifies the image source for a `type="image"` input.
* [`step`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#step): A positive number or an `'any'` string. Specifies the distance between valid values.
* [`type`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#type): A string. One of the [input types.](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#input_types)
* [`width`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#width):  A string. Specifies the image width for a `type="image"` input.

#### Lưu ý {/*caveats*/}

- Checkbox cần `checked` (hoặc `defaultChecked`), không phải `value` (hoặc `defaultValue`).
- Nếu một text input nhận một prop `value` là chuỗi, nó sẽ được [coi là controlled.](#controlling-an-input-with-a-state-variable)
- Nếu một checkbox hoặc radio button nhận một prop `checked` là boolean, nó sẽ được [coi là controlled.](#controlling-an-input-with-a-state-variable)
- Một input không thể vừa controlled vừa uncontrolled cùng một lúc.
- Một input không thể chuyển đổi giữa controlled và uncontrolled trong suốt vòng đời của nó.
- Mỗi controlled input cần một event handler `onChange` đồng bộ cập nhật giá trị backing của nó.

---

## Cách sử dụng {/*usage*/}

### Hiển thị các loại input khác nhau {/*displaying-inputs-of-different-types*/}

Để hiển thị một input, render một component `<input>`. Theo mặc định, nó sẽ là một text input. Bạn có thể truyền `type="checkbox"` cho checkbox, `type="radio"` cho radio button, [hoặc một trong các loại input khác.](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#input_types)

<Sandpack>

```js
export default function MyForm() {
  return (
    <>
      <label>
        Text input: <input name="myInput" />
      </label>
      <hr />
      <label>
        Checkbox: <input type="checkbox" name="myCheckbox" />
      </label>
      <hr />
      <p>
        Radio buttons:
        <label>
          <input type="radio" name="myRadio" value="option1" />
          Option 1
        </label>
        <label>
          <input type="radio" name="myRadio" value="option2" />
          Option 2
        </label>
        <label>
          <input type="radio" name="myRadio" value="option3" />
          Option 3
        </label>
      </p>
    </>
  );
}
```

```css
label { display: block; }
input { margin: 5px; }
```

</Sandpack>

---

### Cung cấp nhãn cho input {/*providing-a-label-for-an-input*/}

Thông thường, bạn sẽ đặt mỗi `<input>` bên trong một thẻ [`<label>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/label). Điều này cho trình duyệt biết nhãn này được liên kết với input đó. Khi người dùng nhấp vào nhãn, trình duyệt sẽ tự động focus vào input. Điều này cũng rất quan trọng cho khả năng trợ năng: trình đọc màn hình sẽ thông báo chú thích nhãn khi người dùng focus vào input liên kết.

Nếu bạn không thể lồng `<input>` vào một `<label>`, hãy liên kết chúng bằng cách truyền cùng ID cho `<input id>` và [`<label htmlFor>`.](https://developer.mozilla.org/en-US/docs/Web/API/HTMLLabelElement/htmlFor) Để tránh xung đột giữa nhiều instance của một component, hãy tạo ID đó với [`useId`.](/reference/react/useId)

<Sandpack>

```js
import { useId } from 'react';

export default function Form() {
  const ageInputId = useId();
  return (
    <>
      <label>
        Your first name:
        <input name="firstName" />
      </label>
      <hr />
      <label htmlFor={ageInputId}>Your age:</label>
      <input id={ageInputId} name="age" type="number" />
    </>
  );
}
```

```css
input { margin: 5px; }
```

</Sandpack>

---

### Cung cấp giá trị ban đầu cho input {/*providing-an-initial-value-for-an-input*/}

Bạn có thể tùy chọn chỉ định giá trị ban đầu cho bất kỳ input nào. Truyền nó như chuỗi `defaultValue` cho text inputs. Checkbox và radio button nên chỉ định giá trị ban đầu với boolean `defaultChecked` thay vào đó.

<Sandpack>

```js
export default function MyForm() {
  return (
    <>
      <label>
        Text input: <input name="myInput" defaultValue="Some initial value" />
      </label>
      <hr />
      <label>
        Checkbox: <input type="checkbox" name="myCheckbox" defaultChecked={true} />
      </label>
      <hr />
      <p>
        Radio buttons:
        <label>
          <input type="radio" name="myRadio" value="option1" />
          Option 1
        </label>
        <label>
          <input
            type="radio"
            name="myRadio"
            value="option2"
            defaultChecked={true}
          />
          Option 2
        </label>
        <label>
          <input type="radio" name="myRadio" value="option3" />
          Option 3
        </label>
      </p>
    </>
  );
}
```

```css
label { display: block; }
input { margin: 5px; }
```

</Sandpack>

---

### Đọc giá trị input khi gửi form {/*reading-the-input-values-when-submitting-a-form*/}

Thêm một [`<form>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form) xung quanh các input của bạn với một [`<button type="submit">`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button) bên trong. Nó sẽ gọi event handler `<form onSubmit>` của bạn. Theo mặc định, trình duyệt sẽ gửi dữ liệu form đến URL hiện tại và tải lại trang. Bạn có thể ghi đè hành vi đó bằng cách gọi `e.preventDefault()`. Đọc dữ liệu form với [`new FormData(e.target)`](https://developer.mozilla.org/en-US/docs/Web/API/FormData).
<Sandpack>

```js
export default function MyForm() {
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
        Text input: <input name="myInput" defaultValue="Some initial value" />
      </label>
      <hr />
      <label>
        Checkbox: <input type="checkbox" name="myCheckbox" defaultChecked={true} />
      </label>
      <hr />
      <p>
        Radio buttons:
        <label><input type="radio" name="myRadio" value="option1" /> Option 1</label>
        <label><input type="radio" name="myRadio" value="option2" defaultChecked={true} /> Option 2</label>
        <label><input type="radio" name="myRadio" value="option3" /> Option 3</label>
      </p>
      <hr />
      <button type="reset">Reset form</button>
      <button type="submit">Submit form</button>
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

Đặt `name` cho mỗi `<input>`, ví dụ `<input name="firstName" defaultValue="Taylor" />`. `name` bạn chỉ định sẽ được sử dụng như một key trong dữ liệu form, ví dụ `{ firstName: "Taylor" }`.

</Note>

<Pitfall>

Theo mặc định, một `<button>` bên trong `<form>` không có thuộc tính `type` sẽ gửi nó. Điều này có thể gây bất ngờ! Nếu bạn có component React `Button` tùy chỉnh của riêng mình, hãy xem xét sử dụng [`<button type="button">`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button) thay vì `<button>` (không có type). Sau đó, để rõ ràng, hãy sử dụng `<button type="submit">` cho các nút *được* dự định gửi form.

</Pitfall>

---

### Kiểm soát input với biến state {/*controlling-an-input-with-a-state-variable*/}

Một input như `<input />` là *uncontrolled.* Ngay cả khi bạn [truyền giá trị ban đầu](#providing-an-initial-value-for-an-input) như `<input defaultValue="Initial text" />`, JSX của bạn chỉ chỉ định giá trị ban đầu. Nó không kiểm soát giá trị nên là gì ngay bây giờ.

**Để render một input *controlled*, hãy truyền prop `value` cho nó (hoặc `checked` cho checkbox và radio).** React sẽ buộc input luôn có `value` bạn đã truyền. Thường thì bạn sẽ làm điều này bằng cách khai báo một [biến state:](/reference/react/useState)

```js {2,6,7}
function Form() {
  const [firstName, setFirstName] = useState(''); // Declare a state variable...
  // ...
  return (
    <input
      value={firstName} // ...force the input's value to match the state variable...
      onChange={e => setFirstName(e.target.value)} // ... and update the state variable on any edits!
    />
  );
}
```

Một controlled input có ý nghĩa nếu bạn cần state dù sao--ví dụ, để re-render UI của bạn trên mỗi lần chỉnh sửa:

```js {2,9}
function Form() {
  const [firstName, setFirstName] = useState('');
  return (
    <>
      <label>
        First name:
        <input value={firstName} onChange={e => setFirstName(e.target.value)} />
      </label>
      {firstName !== '' && <p>Your name is {firstName}.</p>}
      ...
```

Nó cũng hữu ích nếu bạn muốn cung cấp nhiều cách để điều chỉnh state input (ví dụ, bằng cách nhấp vào nút):

```js {3-4,10-11,14}
function Form() {
  // ...
  const [age, setAge] = useState('');
  const ageAsNumber = Number(age);
  return (
    <>
      <label>
        Age:
        <input
          value={age}
          onChange={e => setAge(e.target.value)}
          type="number"
        />
        <button onClick={() => setAge(ageAsNumber + 10)}>
          Add 10 years
        </button>
```

Giá trị `value` bạn truyền cho các controlled component không nên là `undefined` hoặc `null`. Nếu bạn cần giá trị ban đầu là rỗng (chẳng hạn với trường `firstName` bên dưới), hãy khởi tạo biến state của bạn với chuỗi rỗng (`''`).

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [firstName, setFirstName] = useState('');
  const [age, setAge] = useState('20');
  const ageAsNumber = Number(age);
  return (
    <>
      <label>
        First name:
        <input
          value={firstName}
          onChange={e => setFirstName(e.target.value)}
        />
      </label>
      <label>
        Age:
        <input
          value={age}
          onChange={e => setAge(e.target.value)}
          type="number"
        />
        <button onClick={() => setAge(ageAsNumber + 10)}>
          Add 10 years
        </button>
      </label>
      {firstName !== '' &&
        <p>Your name is {firstName}.</p>
      }
      {ageAsNumber > 0 &&
        <p>Your age is {ageAsNumber}.</p>
      }
    </>
  );
}
```

```css
label { display: block; }
input { margin: 5px; }
p { font-weight: bold; }
```

</Sandpack>

<Pitfall>

**Nếu bạn truyền `value` mà không có `onChange`, sẽ không thể gõ vào input.** Khi bạn kiểm soát input bằng cách truyền một `value` cho nó, bạn *buộc* nó luôn có giá trị bạn đã truyền. Vì vậy, nếu bạn truyền một biến state như là `value` nhưng quên cập nhật biến state đó một cách đồng bộ trong event handler `onChange`, React sẽ hoàn nguyên input sau mỗi lần gõ phím về `value` mà bạn đã chỉ định.

</Pitfall>

---

### Tối ưu hóa re-rendering trên mỗi lần gõ phím {/*optimizing-re-rendering-on-every-keystroke*/}

Khi bạn sử dụng controlled input, bạn đặt state trên mỗi lần gõ phím. Nếu component chứa state của bạn re-render một cây lớn, điều này có thể chậm. Có một vài cách bạn có thể tối ưu hóa hiệu suất re-rendering.

Ví dụ, giả sử bạn bắt đầu với một form re-render tất cả nội dung trang trên mỗi lần gõ phím:

```js {5-8}
function App() {
  const [firstName, setFirstName] = useState('');
  return (
    <>
      <form>
        <input value={firstName} onChange={e => setFirstName(e.target.value)} />
      </form>
      <PageContent />
    </>
  );
}
```

Vì `<PageContent />` không phụ thuộc vào state input, bạn có thể di chuyển state input vào component riêng của nó:

```js {4,10-17}
function App() {
  return (
    <>
      <SignupForm />
      <PageContent />
    </>
  );
}

function SignupForm() {
  const [firstName, setFirstName] = useState('');
  return (
    <form>
      <input value={firstName} onChange={e => setFirstName(e.target.value)} />
    </form>
  );
}
```

Điều này cải thiện đáng kể hiệu suất vì bây giờ chỉ `SignupForm` re-render trên mỗi lần gõ phím.

Nếu không có cách nào để tránh re-rendering (ví dụ, nếu `PageContent` phụ thuộc vào giá trị của search input), [`useDeferredValue`](/reference/react/useDeferredValue#deferring-re-rendering-for-a-part-of-the-ui) cho phép bạn giữ controlled input phản hồi ngay cả giữa một lần re-render lớn.

---

## Xử lý sự cố {/*troubleshooting*/}

### Text input của tôi không cập nhật khi tôi gõ vào nó {/*my-text-input-doesnt-update-when-i-type-into-it*/}

Nếu bạn render một input với `value` nhưng không có `onChange`, bạn sẽ thấy lỗi trong console:

```js
// 🔴 Bug: controlled text input with no onChange handler
<input value={something} />
```

<ConsoleBlock level="error">

You provided a `value` prop to a form field without an `onChange` handler. This will render a read-only field. If the field should be mutable use `defaultValue`. Otherwise, set either `onChange` or `readOnly`.

</ConsoleBlock>

Như thông báo lỗi gợi ý, nếu bạn chỉ muốn [chỉ định giá trị *ban đầu*,](#providing-an-initial-value-for-an-input) hãy truyền `defaultValue` thay thế:

```js
// ✅ Good: uncontrolled input with an initial value
<input defaultValue={something} />
```

Nếu bạn muốn [kiểm soát input này với một biến state,](#controlling-an-input-with-a-state-variable) hãy chỉ định một handler `onChange`:

```js
// ✅ Good: controlled input with onChange
<input value={something} onChange={e => setSomething(e.target.value)} />
```

Nếu giá trị cố ý chỉ đọc, hãy thêm prop `readOnly` để tắt lỗi:

```js
// ✅ Good: readonly controlled input without on change
<input value={something} readOnly={true} />
```

---

### Checkbox của tôi không cập nhật khi tôi nhấp vào nó {/*my-checkbox-doesnt-update-when-i-click-on-it*/}

Nếu bạn render một checkbox với `checked` nhưng không có `onChange`, bạn sẽ thấy lỗi trong console:

```js
// 🔴 Bug: controlled checkbox with no onChange handler
<input type="checkbox" checked={something} />
```

<ConsoleBlock level="error">

You provided a `checked` prop to a form field without an `onChange` handler. This will render a read-only field. If the field should be mutable use `defaultChecked`. Otherwise, set either `onChange` or `readOnly`.

</ConsoleBlock>

Như thông báo lỗi gợi ý, nếu bạn chỉ muốn [chỉ định giá trị *ban đầu*,](#providing-an-initial-value-for-an-input) hãy truyền `defaultChecked` thay thế:

```js
// ✅ Good: uncontrolled checkbox with an initial value
<input type="checkbox" defaultChecked={something} />
```

Nếu bạn muốn [kiểm soát checkbox này với một biến state,](#controlling-an-input-with-a-state-variable) hãy chỉ định một handler `onChange`:

```js
// ✅ Good: controlled checkbox with onChange
<input type="checkbox" checked={something} onChange={e => setSomething(e.target.checked)} />
```

<Pitfall>

Bạn cần đọc `e.target.checked` thay vì `e.target.value` cho checkbox.

</Pitfall>

Nếu checkbox cố ý chỉ đọc, hãy thêm prop `readOnly` để tắt lỗi:

```js
// ✅ Good: readonly controlled input without on change
<input type="checkbox" checked={something} readOnly={true} />
```

---

### Con trỏ input của tôi nhảy về đầu trên mỗi lần gõ phím {/*my-input-caret-jumps-to-the-beginning-on-every-keystroke*/}

Nếu bạn [kiểm soát một input,](#controlling-an-input-with-a-state-variable) bạn phải cập nhật biến state của nó thành giá trị của input từ DOM trong khi `onChange`.

Bạn không thể cập nhật nó sang thứ gì khác ngoài `e.target.value` (hoặc `e.target.checked` cho checkbox):

```js
function handleChange(e) {
  // 🔴 Bug: updating an input to something other than e.target.value
  setFirstName(e.target.value.toUpperCase());
}
```

Bạn cũng không thể cập nhật nó một cách không đồng bộ:

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

Nếu điều này không sửa vấn đề, có thể input bị xóa và thêm lại từ DOM trên mỗi lần gõ phím. Điều này có thể xảy ra nếu bạn vô tình [reset state](/learn/preserving-and-resetting-state) trên mỗi lần re-render, ví dụ nếu input hoặc một trong các cha của nó luôn nhận thuộc tính `key` khác, hoặc nếu bạn lồng các định nghĩa hàm component (không được hỗ trợ và khiến component "bên trong" luôn được coi là một cây khác).

---

### Tôi gặp lỗi: "A component is changing an uncontrolled input to be controlled" {/*im-getting-an-error-a-component-is-changing-an-uncontrolled-input-to-be-controlled*/}


Nếu bạn cung cấp `value` cho component, nó phải luôn là một chuỗi trong suốt vòng đời của nó.

Bạn không thể truyền `value={undefined}` trước và sau đó truyền `value="some string"` vì React sẽ không biết bạn muốn component là uncontrolled hay controlled. Một controlled component nên luôn nhận một chuỗi `value`, không phải `null` hoặc `undefined`.

Nếu `value` của bạn đến từ API hoặc biến state, nó có thể được khởi tạo thành `null` hoặc `undefined`. Trong trường hợp đó, hãy đặt nó thành chuỗi rỗng (`''`) ban đầu, hoặc truyền `value={someValue ?? ''}` để đảm bảo `value` là một chuỗi.

Tương tự, nếu bạn truyền `checked` cho một checkbox, hãy đảm bảo nó luôn là một boolean.
