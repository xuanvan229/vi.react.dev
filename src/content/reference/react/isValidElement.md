---
title: isValidElement
---

<Intro>

`isValidElement` kiểm tra xem một giá trị có phải là một React element hay không.

```js
const isElement = isValidElement(value)
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `isValidElement(value)` {/*isvalidelement*/}

Gọi `isValidElement(value)` để kiểm tra xem `value` có phải là một React element hay không.

```js
import { isValidElement, createElement } from 'react';

// ✅ React elements
console.log(isValidElement(<p />)); // true
console.log(isValidElement(createElement('p'))); // true

// ❌ Not React elements
console.log(isValidElement(25)); // false
console.log(isValidElement('Hello')); // false
console.log(isValidElement({ age: 42 })); // false
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `value`: Giá trị `value` bạn muốn kiểm tra. Nó có thể là bất kỳ giá trị nào thuộc bất kỳ kiểu nào.

#### Giá trị trả về {/*returns*/}

`isValidElement` trả về `true` nếu `value` là một React element. Nếu không, nó trả về `false`.

#### Lưu ý {/*caveats*/}

* **Chỉ [các JSX tag](/learn/writing-markup-with-jsx) và các object được trả về bởi [`createElement`](/reference/react/createElement) mới được coi là React element.** Ví dụ, mặc dù một số như `42` là một React *node* hợp lệ (và có thể được trả về từ một component), nó không phải là một React element hợp lệ. Mảng và portal được tạo bằng [`createPortal`](/reference/react-dom/createPortal) cũng *không* được coi là React element.

---

## Cách sử dụng {/*usage*/}

### Kiểm tra xem một thứ có phải là React element không {/*checking-if-something-is-a-react-element*/}

Gọi `isValidElement` để kiểm tra xem một giá trị nào đó có phải là *React element* hay không.

React element là:

- Các giá trị được tạo ra bằng cách viết một [JSX tag](/learn/writing-markup-with-jsx)
- Các giá trị được tạo ra bằng cách gọi [`createElement`](/reference/react/createElement)

Đối với React element, `isValidElement` trả về `true`:

```js
import { isValidElement, createElement } from 'react';

// ✅ JSX tags are React elements
console.log(isValidElement(<p />)); // true
console.log(isValidElement(<MyComponent />)); // true

// ✅ Values returned by createElement are React elements
console.log(isValidElement(createElement('p'))); // true
console.log(isValidElement(createElement(MyComponent))); // true
```

Bất kỳ giá trị nào khác, chẳng hạn như chuỗi, số, hoặc các object và mảng tùy ý, đều không phải React element.

Đối với chúng, `isValidElement` trả về `false`:

```js
// ❌ These are *not* React elements
console.log(isValidElement(null)); // false
console.log(isValidElement(25)); // false
console.log(isValidElement('Hello')); // false
console.log(isValidElement({ age: 42 })); // false
console.log(isValidElement([<div />, <div />])); // false
console.log(isValidElement(MyComponent)); // false
```

Rất hiếm khi cần dùng `isValidElement`. Nó hữu ích nhất khi bạn đang gọi một API khác *chỉ* chấp nhận element (như [`cloneElement`](/reference/react/cloneElement) làm) và bạn muốn tránh lỗi khi đối số của bạn không phải là React element.

Trừ khi bạn có lý do rất cụ thể để thêm kiểm tra `isValidElement`, bạn có thể sẽ không cần nó.

<DeepDive>

#### React element so với React node {/*react-elements-vs-react-nodes*/}

Khi bạn viết một component, bạn có thể trả về bất kỳ loại *React node* nào từ nó:

```js
function MyComponent() {
  // ... you can return any React node ...
}
```

Một React node có thể là:

- Một React element được tạo như `<div />` hoặc `createElement('div')`
- Một portal được tạo bằng [`createPortal`](/reference/react-dom/createPortal)
- Một chuỗi
- Một số
- `true`, `false`, `null`, hoặc `undefined` (không được hiển thị)
- Một mảng các React node khác

**Lưu ý rằng `isValidElement` kiểm tra xem đối số có phải là *React element* hay không, không phải xem nó có phải là React node hay không.** Ví dụ, `42` không phải là một React element hợp lệ. Tuy nhiên, nó là một React node hoàn toàn hợp lệ:

```js
function MyComponent() {
  return 42; // It's ok to return a number from component
}
```

Đây là lý do tại sao bạn không nên dùng `isValidElement` như một cách để kiểm tra xem một thứ có thể được render hay không.

</DeepDive>
