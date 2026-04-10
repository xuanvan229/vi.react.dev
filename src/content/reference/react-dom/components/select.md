---
title: "<select>"
---

<Intro>

Component [`<select>` có sẵn trong trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select) cho phép bạn render một hộp chọn với các tùy chọn.

```js
<select>
  <option value="someOption">Some option</option>
  <option value="otherOption">Other option</option>
</select>
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `<select>` {/*select*/}

Để hiển thị một hộp chọn, render component [`<select>` có sẵn trong trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select).

```js
<select>
  <option value="someOption">Some option</option>
  <option value="otherOption">Other option</option>
</select>
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Props {/*props*/}

`<select>` hỗ trợ tất cả [common element props.](/reference/react-dom/components/common#common-props)

Bạn có thể [làm cho hộp chọn được kiểm soát](#controlling-a-select-box-with-a-state-variable) bằng cách truyền prop `value`:

* `value`: A string (or an array of strings for [`multiple={true}`](#enabling-multiple-selection)). Controls which option is selected. Every value string match the `value` of some `<option>` nested inside the `<select>`.

Khi bạn truyền `value`, bạn cũng phải truyền một handler `onChange` cập nhật giá trị được truyền.

Nếu `<select>` của bạn là uncontrolled, bạn có thể truyền prop `defaultValue` thay thế:

* `defaultValue`: A string (or an array of strings for [`multiple={true}`](#enabling-multiple-selection)). Specifies [the initially selected option.](#providing-an-initially-selected-option)

Các props `<select>` này liên quan đến cả hộp chọn không được kiểm soát và được kiểm soát:

* [`autoComplete`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select#autocomplete): A string. Specifies one of the possible [autocomplete behaviors.](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/autocomplete#values)
* [`autoFocus`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select#autofocus): A boolean. If `true`, React will focus the element on mount.
* `children`: `<select>` accepts [`<option>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option), [`<optgroup>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/optgroup), and [`<datalist>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/datalist) components as children. You can also pass your own components as long as they eventually render one of the allowed components. If you pass your own components that eventually render `<option>` tags, each `<option>` you render must have a `value`.
* [`disabled`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select#disabled): A boolean. If `true`, the select box will not be interactive and will appear dimmed.
* [`form`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select#form): A string. Specifies the `id` of the `<form>` this select box belongs to. If omitted, it's the closest parent form.
* [`multiple`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select#multiple): A boolean. If `true`, the browser allows [multiple selection.](#enabling-multiple-selection)
* [`name`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select#name): A string. Specifies the name for this select box that's [submitted with the form.](#reading-the-select-box-value-when-submitting-a-form)
* `onChange`: An [`Event` handler](/reference/react-dom/components/common#event-handler) function. Required for [controlled select boxes.](#controlling-a-select-box-with-a-state-variable) Fires immediately when the user picks a different option. Behaves like the browser [`input` event.](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/input_event)
* `onChangeCapture`: A version of `onChange` that fires in the [capture phase.](/learn/responding-to-events#capture-phase-events)
* [`onInput`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/input_event): An [`Event` handler](/reference/react-dom/components/common#event-handler) function. Fires immediately when the value is changed by the user. For historical reasons, in React it is idiomatic to use `onChange` instead which works similarly.
* `onInputCapture`: A version of `onInput` that fires in the [capture phase.](/learn/responding-to-events#capture-phase-events)
* [`onInvalid`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement/invalid_event): An [`Event` handler](/reference/react-dom/components/common#event-handler) function. Fires if an input fails validation on form submit. Unlike the built-in `invalid` event, the React `onInvalid` event bubbles.
* `onInvalidCapture`: A version of `onInvalid` that fires in the [capture phase.](/learn/responding-to-events#capture-phase-events)
* [`required`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select#required): A boolean. If `true`, the value must be provided for the form to submit.
* [`size`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select#size): A number. For `multiple={true}` selects, specifies the preferred number of initially visible items.

#### Lưu ý {/*caveats*/}

- Khác với HTML, việc truyền thuộc tính `selected` cho `<option>` không được hỗ trợ. Thay vào đó, hãy sử dụng [`<select defaultValue>`](#providing-an-initially-selected-option) cho hộp chọn uncontrolled và [`<select value>`](#controlling-a-select-box-with-a-state-variable) cho hộp chọn controlled.
- Nếu hộp chọn nhận prop `value`, nó sẽ được [coi là controlled.](#controlling-a-select-box-with-a-state-variable)
- Một hộp chọn không thể vừa controlled vừa uncontrolled cùng một lúc.
- Một hộp chọn không thể chuyển đổi giữa controlled và uncontrolled trong suốt vòng đời của nó.
- Mỗi controlled hộp chọn cần một event handler `onChange` đồng bộ cập nhật giá trị backing của nó.

---

## Cách sử dụng {/*usage*/}

### Hiển thị hộp chọn với các tùy chọn {/*displaying-a-select-box-with-options*/}

Render một `<select>` với danh sách các component `<option>` bên trong để hiển thị hộp chọn. Đặt cho mỗi `<option>` một `value` đại diện cho dữ liệu sẽ được gửi cùng form.

<Sandpack>

```js
export default function FruitPicker() {
  return (
    <label>
      Pick a fruit:
      <select name="selectedFruit">
        <option value="apple">Apple</option>
        <option value="banana">Banana</option>
        <option value="orange">Orange</option>
      </select>
    </label>
  );
}
```

```css
select { margin: 5px; }
```

</Sandpack>

---

### Cung cấp nhãn cho hộp chọn {/*providing-a-label-for-a-select-box*/}

Thông thường, bạn sẽ đặt mỗi `<select>` bên trong một thẻ [`<label>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/label). Điều này cho trình duyệt biết nhãn này được liên kết với hộp chọn đó. Khi người dùng nhấp vào nhãn, trình duyệt sẽ tự động focus vào hộp chọn. Điều này cũng rất quan trọng cho khả năng trợ năng: trình đọc màn hình sẽ thông báo chú thích nhãn khi người dùng focus vào hộp chọn.

Nếu bạn không thể lồng `<select>` vào một `<label>`, hãy liên kết chúng bằng cách truyền cùng ID cho `<select id>` và [`<label htmlFor>`.](https://developer.mozilla.org/en-US/docs/Web/API/HTMLLabelElement/htmlFor) Để tránh xung đột giữa nhiều instance của một component, hãy tạo ID đó với [`useId`.](/reference/react/useId)

<Sandpack>

```js
import { useId } from 'react';

export default function Form() {
  const vegetableSelectId = useId();
  return (
    <>
      <label>
        Pick a fruit:
        <select name="selectedFruit">
          <option value="apple">Apple</option>
          <option value="banana">Banana</option>
          <option value="orange">Orange</option>
        </select>
      </label>
      <hr />
      <label htmlFor={vegetableSelectId}>
        Pick a vegetable:
      </label>
      <select id={vegetableSelectId} name="selectedVegetable">
        <option value="cucumber">Cucumber</option>
        <option value="corn">Corn</option>
        <option value="tomato">Tomato</option>
      </select>
    </>
  );
}
```

```css
select { margin: 5px; }
```

</Sandpack>


---

### Cung cấp tùy chọn được chọn ban đầu {/*providing-an-initially-selected-option*/}

Theo mặc định, trình duyệt sẽ chọn `<option>` đầu tiên trong danh sách. Để chọn một tùy chọn khác theo mặc định, hãy truyền `value` của `<option>` đó như `defaultValue` cho phần tử `<select>`.

<Sandpack>

```js
export default function FruitPicker() {
  return (
    <label>
      Pick a fruit:
      <select name="selectedFruit" defaultValue="orange">
        <option value="apple">Apple</option>
        <option value="banana">Banana</option>
        <option value="orange">Orange</option>
      </select>
    </label>
  );
}
```

```css
select { margin: 5px; }
```

</Sandpack>

<Pitfall>

Khác với HTML, việc truyền thuộc tính `selected` cho một `<option>` riêng lẻ không được hỗ trợ.

</Pitfall>

---

### Bật chọn nhiều {/*enabling-multiple-selection*/}

Truyền `multiple={true}` vào `<select>` để cho phép người dùng chọn nhiều tùy chọn. Trong trường hợp đó, nếu bạn cũng chỉ định `defaultValue` để chọn các tùy chọn được chọn ban đầu, nó phải là một mảng.

<Sandpack>

```js
export default function FruitPicker() {
  return (
    <label>
      Pick some fruits:
      <select
        name="selectedFruit"
        defaultValue={['orange', 'banana']}
        multiple={true}
      >
        <option value="apple">Apple</option>
        <option value="banana">Banana</option>
        <option value="orange">Orange</option>
      </select>
    </label>
  );
}
```

```css
select { display: block; margin-top: 10px; width: 200px; }
```

</Sandpack>

---

### Đọc giá trị hộp chọn khi gửi form {/*reading-the-select-box-value-when-submitting-a-form*/}

Thêm một [`<form>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form) xung quanh hộp chọn của bạn với một [`<button type="submit">`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button) bên trong. Nó sẽ gọi event handler `<form onSubmit>` của bạn. Theo mặc định, trình duyệt sẽ gửi dữ liệu form đến URL hiện tại và tải lại trang. Bạn có thể ghi đè hành vi đó bằng cách gọi `e.preventDefault()`. Đọc dữ liệu form với [`new FormData(e.target)`](https://developer.mozilla.org/en-US/docs/Web/API/FormData).
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
    // You can generate a URL out of it, as the browser does by default:
    console.log(new URLSearchParams(formData).toString());
    // You can work with it as a plain object.
    const formJson = Object.fromEntries(formData.entries());
    console.log(formJson); // (!) This doesn't include multiple select values
    // Or you can get an array of name-value pairs.
    console.log([...formData.entries()]);
  }

  return (
    <form method="post" onSubmit={handleSubmit}>
      <label>
        Pick your favorite fruit:
        <select name="selectedFruit" defaultValue="orange">
          <option value="apple">Apple</option>
          <option value="banana">Banana</option>
          <option value="orange">Orange</option>
        </select>
      </label>
      <label>
        Pick all your favorite vegetables:
        <select
          name="selectedVegetables"
          multiple={true}
          defaultValue={['corn', 'tomato']}
        >
          <option value="cucumber">Cucumber</option>
          <option value="corn">Corn</option>
          <option value="tomato">Tomato</option>
        </select>
      </label>
      <hr />
      <button type="reset">Reset</button>
      <button type="submit">Submit</button>
    </form>
  );
}
```

```css
label, select { display: block; }
label { margin-bottom: 20px; }
```

</Sandpack>

<Note>

Đặt `name` cho `<select>` của bạn, ví dụ `<select name="selectedFruit" />`. `name` bạn chỉ định sẽ được sử dụng như một key trong dữ liệu form, ví dụ `{ selectedFruit: "orange" }`.

Nếu bạn sử dụng `<select multiple={true}>`, [`FormData`](https://developer.mozilla.org/en-US/docs/Web/API/FormData) bạn đọc từ form sẽ bao gồm mỗi giá trị được chọn như một cặp tên-giá trị riêng biệt. Hãy xem kỹ các log console trong ví dụ trên.

</Note>

<Pitfall>

Theo mặc định, *bất kỳ* `<button>` nào bên trong `<form>` sẽ gửi nó. Điều này có thể gây bất ngờ! Nếu bạn có component React `Button` tùy chỉnh của riêng mình, hãy xem xét trả về [`<button type="button">`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/button) thay vì `<button>`. Sau đó, để rõ ràng, hãy sử dụng `<button type="submit">` cho các nút *được* dự định gửi form.

</Pitfall>

---

### Kiểm soát hộp chọn với biến state {/*controlling-a-select-box-with-a-state-variable*/}

Một hộp chọn như `<select />` là *uncontrolled.* Ngay cả khi bạn [truyền giá trị được chọn ban đầu](#providing-an-initially-selected-option) như `<select defaultValue="orange" />`, JSX của bạn chỉ chỉ định giá trị ban đầu, không phải giá trị ngay bây giờ.

**Để render một hộp chọn *controlled*, hãy truyền prop `value` cho nó.** React sẽ buộc hộp chọn luôn có `value` bạn đã truyền. Thường thì bạn sẽ kiểm soát hộp chọn bằng cách khai báo một [biến state:](/reference/react/useState)

```js {2,6,7}
function FruitPicker() {
  const [selectedFruit, setSelectedFruit] = useState('orange'); // Declare a state variable...
  // ...
  return (
    <select
      value={selectedFruit} // ...force the select's value to match the state variable...
      onChange={e => setSelectedFruit(e.target.value)} // ... and update the state variable on any change!
    >
      <option value="apple">Apple</option>
      <option value="banana">Banana</option>
      <option value="orange">Orange</option>
    </select>
  );
}
```

Điều này hữu ích nếu bạn muốn re-render một phần của UI để phản hồi mỗi lần chọn.

<Sandpack>

```js
import { useState } from 'react';

export default function FruitPicker() {
  const [selectedFruit, setSelectedFruit] = useState('orange');
  const [selectedVegs, setSelectedVegs] = useState(['corn', 'tomato']);
  return (
    <>
      <label>
        Pick a fruit:
        <select
          value={selectedFruit}
          onChange={e => setSelectedFruit(e.target.value)}
        >
          <option value="apple">Apple</option>
          <option value="banana">Banana</option>
          <option value="orange">Orange</option>
        </select>
      </label>
      <hr />
      <label>
        Pick all your favorite vegetables:
        <select
          multiple={true}
          value={selectedVegs}
          onChange={e => {
            const options = [...e.target.selectedOptions];
            const values = options.map(option => option.value);
            setSelectedVegs(values);
          }}
        >
          <option value="cucumber">Cucumber</option>
          <option value="corn">Corn</option>
          <option value="tomato">Tomato</option>
        </select>
      </label>
      <hr />
      <p>Your favorite fruit: {selectedFruit}</p>
      <p>Your favorite vegetables: {selectedVegs.join(', ')}</p>
    </>
  );
}
```

```css
select { margin-bottom: 10px; display: block; }
```

</Sandpack>

<Pitfall>

**If you pass `value` without `onChange`, it will be impossible to select an option.** When you control a select box by passing some `value` to it, you *force* it to always have the value you passed. So if you pass a state variable as a `value` but forget to update that state variable synchronously during the `onChange` event handler, React will revert the select box after every keystroke back to the `value` that you specified.

Khác với HTML, việc truyền thuộc tính `selected` cho một `<option>` riêng lẻ không được hỗ trợ.

</Pitfall>
