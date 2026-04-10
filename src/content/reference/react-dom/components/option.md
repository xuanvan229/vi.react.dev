---
title: "<option>"
---

<Intro>

Component [`<option>` có sẵn trong trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option) cho phép bạn render một tùy chọn bên trong hộp [`<select>`](/reference/react-dom/components/select).

```js
<select>
  <option value="someOption">Some option</option>
  <option value="otherOption">Other option</option>
</select>
```

</Intro>

<InlineToc />

---

## Reference {/*reference*/}

### `<option>` {/*option*/}

Component [`<option>` có sẵn trong trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option) cho phép bạn render một tùy chọn bên trong hộp [`<select>`](/reference/react-dom/components/select).

```js
<select>
  <option value="someOption">Some option</option>
  <option value="otherOption">Other option</option>
</select>
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Props {/*props*/}

`<option>` hỗ trợ tất cả [common element props.](/reference/react-dom/components/common#common-props)

Ngoài ra, `<option>` hỗ trợ các props này:

* [`disabled`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option#disabled): A boolean. If `true`, the option will not be selectable and will appear dimmed.
* [`label`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option#label): A string. Specifies the meaning of the option. If not specified, the text inside the option is used.
* [`value`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option#value): The value to be used [when submitting the parent `<select>` in a form](/reference/react-dom/components/select#reading-the-select-box-value-when-submitting-a-form) if this option is selected.

#### Lưu ý {/*caveats*/}

* React không hỗ trợ thuộc tính `selected` trên `<option>`. Thay vào đó, hãy truyền `value` của tùy chọn này cho [`<select defaultValue>`](/reference/react-dom/components/select#providing-an-initially-selected-option) cha cho hộp chọn uncontrolled, hoặc [`<select value>`](/reference/react-dom/components/select#controlling-a-select-box-with-a-state-variable) cho hộp chọn controlled.

---

## Cách sử dụng {/*usage*/}

### Hiển thị hộp chọn với các tùy chọn {/*displaying-a-select-box-with-options*/}

Render một `<select>` với danh sách các component `<option>` bên trong để hiển thị hộp chọn. Đặt cho mỗi `<option>` một `value` đại diện cho dữ liệu sẽ được gửi cùng form.

[Đọc thêm về việc hiển thị `<select>` với danh sách các component `<option>`.](/reference/react-dom/components/select)

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

