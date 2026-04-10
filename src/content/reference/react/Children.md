---
title: Children
---

<Pitfall>

Việc sử dụng `Children` là không phổ biến và có thể dẫn đến code dễ hỏng. [Xem các lựa chọn thay thế phổ biến.](#alternatives)

</Pitfall>

<Intro>

`Children` cho phép bạn thao tác và biến đổi JSX mà bạn nhận được như [`children` prop.](/learn/passing-props-to-a-component#passing-jsx-as-children)

```js
const mappedChildren = Children.map(children, child =>
  <div className="Row">
    {child}
  </div>
);

```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `Children.count(children)` {/*children-count*/}

Gọi `Children.count(children)` để đếm số lượng children trong cấu trúc dữ liệu `children`.

```js src/RowList.js active
import { Children } from 'react';

function RowList({ children }) {
  return (
    <>
      <h1>Total rows: {Children.count(children)}</h1>
      ...
    </>
  );
}
```

[Xem thêm ví dụ bên dưới.](#counting-children)

#### Tham số {/*children-count-parameters*/}

* `children`: Giá trị của [`children` prop](/learn/passing-props-to-a-component#passing-jsx-as-children) được nhận bởi component của bạn.

#### Giá trị trả về {/*children-count-returns*/}

Số lượng node bên trong những `children` này.

#### Lưu ý {/*children-count-caveats*/}

- Các node rỗng (`null`, `undefined` và Booleans), strings, numbers và [React elements](/reference/react/createElement) đều được tính là các node riêng lẻ. Mảng không được tính là node riêng lẻ, nhưng children của chúng thì có. **Quá trình duyệt không đi sâu hơn vào React elements:** chúng không được render, và children của chúng không được duyệt. [Fragments](/reference/react/Fragment) không được duyệt.

---

### `Children.forEach(children, fn, thisArg?)` {/*children-foreach*/}

Gọi `Children.forEach(children, fn, thisArg?)` để chạy một số code cho mỗi child trong cấu trúc dữ liệu `children`.

```js src/RowList.js active
import { Children } from 'react';

function SeparatorList({ children }) {
  const result = [];
  Children.forEach(children, (child, index) => {
    result.push(child);
    result.push(<hr key={index} />);
  });
  // ...
```

[Xem thêm ví dụ bên dưới.](#running-some-code-for-each-child)

#### Tham số {/*children-foreach-parameters*/}

* `children`: Giá trị của [`children` prop](/learn/passing-props-to-a-component#passing-jsx-as-children) được nhận bởi component của bạn.
* `fn`: Hàm bạn muốn chạy cho mỗi child, tương tự như callback của [phương thức `forEach` trên mảng](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach). Nó sẽ được gọi với child như tham số đầu tiên và index của nó như tham số thứ hai. Index bắt đầu từ `0` và tăng lên trong mỗi lần gọi.
* **tùy chọn** `thisArg`: Giá trị [`this`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this) mà hàm `fn` nên được gọi với. Nếu bỏ qua, nó là `undefined`.

#### Giá trị trả về {/*children-foreach-returns*/}

`Children.forEach` trả về `undefined`.

#### Lưu ý {/*children-foreach-caveats*/}

- Các node rỗng (`null`, `undefined` và Booleans), strings, numbers và [React elements](/reference/react/createElement) đều được tính là các node riêng lẻ. Mảng không được tính là node riêng lẻ, nhưng children của chúng thì có. **Quá trình duyệt không đi sâu hơn vào React elements:** chúng không được render, và children của chúng không được duyệt. [Fragments](/reference/react/Fragment) không được duyệt.

---

### `Children.map(children, fn, thisArg?)` {/*children-map*/}

Gọi `Children.map(children, fn, thisArg?)` để map hoặc biến đổi mỗi child trong cấu trúc dữ liệu `children`.

```js src/RowList.js active
import { Children } from 'react';

function RowList({ children }) {
  return (
    <div className="RowList">
      {Children.map(children, child =>
        <div className="Row">
          {child}
        </div>
      )}
    </div>
  );
}
```

[Xem thêm ví dụ bên dưới.](#transforming-children)

#### Tham số {/*children-map-parameters*/}

* `children`: Giá trị của [`children` prop](/learn/passing-props-to-a-component#passing-jsx-as-children) được nhận bởi component của bạn.
* `fn`: Hàm mapping, tương tự như callback của [phương thức `map` trên mảng](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map). Nó sẽ được gọi với child như tham số đầu tiên và index của nó như tham số thứ hai. Index bắt đầu từ `0` và tăng lên trong mỗi lần gọi. Bạn cần trả về một React node từ hàm này. Đây có thể là một node rỗng (`null`, `undefined` hoặc Boolean), một string, một number, một React element, hoặc một mảng các React node khác.
* **tùy chọn** `thisArg`: Giá trị [`this`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this) mà hàm `fn` nên được gọi với. Nếu bỏ qua, nó là `undefined`.

#### Giá trị trả về {/*children-map-returns*/}

Nếu `children` là `null` hoặc `undefined`, trả về cùng giá trị đó.

Ngược lại, trả về một mảng phẳng gồm các node bạn đã trả về từ hàm `fn`. Mảng được trả về sẽ chứa tất cả các node bạn trả về ngoại trừ `null` và `undefined`.

#### Lưu ý {/*children-map-caveats*/}

- Các node rỗng (`null`, `undefined` và Booleans), strings, numbers và [React elements](/reference/react/createElement) đều được tính là các node riêng lẻ. Mảng không được tính là node riêng lẻ, nhưng children của chúng thì có. **Quá trình duyệt không đi sâu hơn vào React elements:** chúng không được render, và children của chúng không được duyệt. [Fragments](/reference/react/Fragment) không được duyệt.

- Nếu bạn trả về một element hoặc mảng các element có keys từ `fn`, **các keys của element được trả về sẽ tự động kết hợp với key của item gốc tương ứng từ `children`.** Khi bạn trả về nhiều element từ `fn` trong một mảng, các keys của chúng chỉ cần là duy nhất cục bộ với nhau.

---

### `Children.only(children)` {/*children-only*/}


Gọi `Children.only(children)` để xác nhận rằng `children` đại diện cho một React element duy nhất.

```js
function Box({ children }) {
  const element = Children.only(children);
  // ...
```

#### Tham số {/*children-only-parameters*/}

* `children`: Giá trị của [`children` prop](/learn/passing-props-to-a-component#passing-jsx-as-children) được nhận bởi component của bạn.

#### Giá trị trả về {/*children-only-returns*/}

Nếu `children` [là một element hợp lệ,](/reference/react/isValidElement) trả về element đó.

Ngược lại, ném ra một lỗi.

#### Lưu ý {/*children-only-caveats*/}

- Phương thức này luôn **ném ra nếu bạn truyền một mảng (chẳng hạn như giá trị trả về của `Children.map`) như `children`.** Nói cách khác, nó đảm bảo rằng `children` là một React element duy nhất, không phải là một mảng với một element duy nhất.

---

### `Children.toArray(children)` {/*children-toarray*/}

Gọi `Children.toArray(children)` để tạo một mảng từ cấu trúc dữ liệu `children`.

```js src/ReversedList.js active
import { Children } from 'react';

export default function ReversedList({ children }) {
  const result = Children.toArray(children);
  result.reverse();
  // ...
```

#### Tham số {/*children-toarray-parameters*/}

* `children`: Giá trị của [`children` prop](/learn/passing-props-to-a-component#passing-jsx-as-children) được nhận bởi component của bạn.

#### Giá trị trả về {/*children-toarray-returns*/}

Trả về một mảng phẳng các element trong `children`.

#### Lưu ý {/*children-toarray-caveats*/}

- Các node rỗng (`null`, `undefined` và Booleans) sẽ bị bỏ qua trong mảng được trả về. **Các keys của element được trả về sẽ được tính từ keys của element gốc và mức độ lồng nhau và vị trí của chúng.** Điều này đảm bảo rằng việc làm phẳng mảng không gây ra thay đổi trong hành vi.

---

## Cách sử dụng {/*usage*/}

### Biến đổi children {/*transforming-children*/}

Để biến đổi JSX children mà component của bạn [nhận được như `children` prop,](/learn/passing-props-to-a-component#passing-jsx-as-children) hãy gọi `Children.map`:

```js {6,10}
import { Children } from 'react';

function RowList({ children }) {
  return (
    <div className="RowList">
      {Children.map(children, child =>
        <div className="Row">
          {child}
        </div>
      )}
    </div>
  );
}
```

Trong ví dụ trên, `RowList` bọc mỗi child mà nó nhận được vào một container `<div className="Row">`. Ví dụ, giả sử component cha truyền ba thẻ `<p>` như `children` prop cho `RowList`:

```js
<RowList>
  <p>This is the first item.</p>
  <p>This is the second item.</p>
  <p>This is the third item.</p>
</RowList>
```

Sau đó, với việc triển khai `RowList` ở trên, kết quả render cuối cùng sẽ trông như thế này:

```js
<div className="RowList">
  <div className="Row">
    <p>This is the first item.</p>
  </div>
  <div className="Row">
    <p>This is the second item.</p>
  </div>
  <div className="Row">
    <p>This is the third item.</p>
  </div>
</div>
```

`Children.map` tương tự như [biến đổi mảng với `map()`.](/learn/rendering-lists) Sự khác biệt là cấu trúc dữ liệu `children` được coi là *mờ đục.* Điều này có nghĩa là ngay cả khi đôi khi nó là một mảng, bạn không nên giả định nó là một mảng hay bất kỳ loại dữ liệu cụ thể nào khác. Đây là lý do tại sao bạn nên sử dụng `Children.map` nếu bạn cần biến đổi nó.

<Sandpack>

```js
import RowList from './RowList.js';

export default function App() {
  return (
    <RowList>
      <p>This is the first item.</p>
      <p>This is the second item.</p>
      <p>This is the third item.</p>
    </RowList>
  );
}
```

```js src/RowList.js active
import { Children } from 'react';

export default function RowList({ children }) {
  return (
    <div className="RowList">
      {Children.map(children, child =>
        <div className="Row">
          {child}
        </div>
      )}
    </div>
  );
}
```

```css
.RowList {
  display: flex;
  flex-direction: column;
  border: 2px solid grey;
  padding: 5px;
}

.Row {
  border: 2px dashed black;
  padding: 5px;
  margin: 5px;
}
```

</Sandpack>

<DeepDive>

#### Tại sao children prop không phải lúc nào cũng là một mảng? {/*why-is-the-children-prop-not-always-an-array*/}

Trong React, `children` prop được coi là một cấu trúc dữ liệu *mờ đục*. Điều này có nghĩa là bạn không nên dựa vào cách nó được cấu trúc. Để biến đổi, lọc hoặc đếm children, bạn nên sử dụng các phương thức `Children`.

Trong thực tế, cấu trúc dữ liệu `children` thường được biểu diễn như một mảng bên trong. Tuy nhiên, nếu chỉ có một child duy nhất, thì React sẽ không tạo thêm mảng vì điều này sẽ dẫn đến tốn bộ nhớ không cần thiết. Miễn là bạn sử dụng các phương thức `Children` thay vì trực tiếp kiểm tra `children` prop, code của bạn sẽ không bị hỏng ngay cả khi React thay đổi cách cấu trúc dữ liệu thực sự được triển khai.

Ngay cả khi `children` là một mảng, `Children.map` có hành vi đặc biệt hữu ích. Ví dụ, `Children.map` kết hợp [keys](/learn/rendering-lists#keeping-list-items-in-order-with-key) trên các element được trả về với keys trên `children` bạn đã truyền cho nó. Điều này đảm bảo rằng JSX children gốc không "mất" keys ngay cả khi chúng bị bọc như trong ví dụ trên.

</DeepDive>

<Pitfall>

Cấu trúc dữ liệu `children` **không bao gồm đầu ra đã render** của các component bạn truyền như JSX. Trong ví dụ dưới đây, `children` được nhận bởi `RowList` chỉ chứa hai item thay vì ba:

1. `<p>This is the first item.</p>`
2. `<MoreRows />`

Đây là lý do tại sao chỉ có hai row wrappers được tạo ra trong ví dụ này:

<Sandpack>

```js
import RowList from './RowList.js';

export default function App() {
  return (
    <RowList>
      <p>This is the first item.</p>
      <MoreRows />
    </RowList>
  );
}

function MoreRows() {
  return (
    <>
      <p>This is the second item.</p>
      <p>This is the third item.</p>
    </>
  );
}
```

```js src/RowList.js
import { Children } from 'react';

export default function RowList({ children }) {
  return (
    <div className="RowList">
      {Children.map(children, child =>
        <div className="Row">
          {child}
        </div>
      )}
    </div>
  );
}
```

```css
.RowList {
  display: flex;
  flex-direction: column;
  border: 2px solid grey;
  padding: 5px;
}

.Row {
  border: 2px dashed black;
  padding: 5px;
  margin: 5px;
}
```

</Sandpack>

**Không có cách nào để lấy đầu ra đã render của một component bên trong** như `<MoreRows />` khi thao tác với `children`. Đây là lý do tại sao [thường tốt hơn khi sử dụng một trong các giải pháp thay thế.](#alternatives)

</Pitfall>

---

### Chạy một số code cho mỗi child {/*running-some-code-for-each-child*/}

Gọi `Children.forEach` để lặp qua mỗi child trong cấu trúc dữ liệu `children`. Nó không trả về bất kỳ giá trị nào và tương tự như [phương thức `forEach` trên mảng.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) Bạn có thể dùng nó để chạy logic tùy chỉnh như xây dựng mảng của riêng bạn.

<Sandpack>

```js
import SeparatorList from './SeparatorList.js';

export default function App() {
  return (
    <SeparatorList>
      <p>This is the first item.</p>
      <p>This is the second item.</p>
      <p>This is the third item.</p>
    </SeparatorList>
  );
}
```

```js src/SeparatorList.js active
import { Children } from 'react';

export default function SeparatorList({ children }) {
  const result = [];
  Children.forEach(children, (child, index) => {
    result.push(child);
    result.push(<hr key={index} />);
  });
  result.pop(); // Remove the last separator
  return result;
}
```

</Sandpack>

<Pitfall>

Như đã đề cập trước đó, không có cách nào để lấy đầu ra đã render của một component bên trong khi thao tác với `children`. Đây là lý do tại sao [thường tốt hơn khi sử dụng một trong các giải pháp thay thế.](#alternatives)

</Pitfall>

---

### Đếm children {/*counting-children*/}

Gọi `Children.count(children)` để tính số lượng children.

<Sandpack>

```js
import RowList from './RowList.js';

export default function App() {
  return (
    <RowList>
      <p>This is the first item.</p>
      <p>This is the second item.</p>
      <p>This is the third item.</p>
    </RowList>
  );
}
```

```js src/RowList.js active
import { Children } from 'react';

export default function RowList({ children }) {
  return (
    <div className="RowList">
      <h1 className="RowListHeader">
        Total rows: {Children.count(children)}
      </h1>
      {Children.map(children, child =>
        <div className="Row">
          {child}
        </div>
      )}
    </div>
  );
}
```

```css
.RowList {
  display: flex;
  flex-direction: column;
  border: 2px solid grey;
  padding: 5px;
}

.RowListHeader {
  padding-top: 5px;
  font-size: 25px;
  font-weight: bold;
  text-align: center;
}

.Row {
  border: 2px dashed black;
  padding: 5px;
  margin: 5px;
}
```

</Sandpack>

<Pitfall>

Như đã đề cập trước đó, không có cách nào để lấy đầu ra đã render của một component bên trong khi thao tác với `children`. Đây là lý do tại sao [thường tốt hơn khi sử dụng một trong các giải pháp thay thế.](#alternatives)

</Pitfall>

---

### Chuyển đổi children thành mảng {/*converting-children-to-an-array*/}

Gọi `Children.toArray(children)` để biến cấu trúc dữ liệu `children` thành một mảng JavaScript thông thường. Điều này cho phép bạn thao tác mảng với các phương thức mảng tích hợp như [`filter`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter), [`sort`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort), hoặc [`reverse`.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse)

<Sandpack>

```js
import ReversedList from './ReversedList.js';

export default function App() {
  return (
    <ReversedList>
      <p>This is the first item.</p>
      <p>This is the second item.</p>
      <p>This is the third item.</p>
    </ReversedList>
  );
}
```

```js src/ReversedList.js active
import { Children } from 'react';

export default function ReversedList({ children }) {
  const result = Children.toArray(children);
  result.reverse();
  return result;
}
```

</Sandpack>

<Pitfall>

Như đã đề cập trước đó, không có cách nào để lấy đầu ra đã render của một component bên trong khi thao tác với `children`. Đây là lý do tại sao [thường tốt hơn khi sử dụng một trong các giải pháp thay thế.](#alternatives)

</Pitfall>

---

## Các lựa chọn thay thế {/*alternatives*/}

<Note>

Phần này mô tả các lựa chọn thay thế cho API `Children` (với chữ `C` in hoa) được import như thế này:

```js
import { Children } from 'react';
```

Đừng nhầm lẫn nó với [việc sử dụng `children` prop](/learn/passing-props-to-a-component#passing-jsx-as-children) (chữ `c` thường), điều này là tốt và được khuyến khích.

</Note>

### Hiển thị nhiều component {/*exposing-multiple-components*/}

Việc thao tác children với các phương thức `Children` thường dẫn đến code dễ hỏng. Khi bạn truyền children cho một component trong JSX, bạn thường không mong đợi component thao tác hoặc biến đổi các children riêng lẻ.

Khi có thể, hãy tránh sử dụng các phương thức `Children`. Ví dụ, nếu bạn muốn mỗi child của `RowList` được bọc trong `<div className="Row">`, hãy export một component `Row` và bọc thủ công mỗi row vào nó như thế này:

<Sandpack>

```js
import { RowList, Row } from './RowList.js';

export default function App() {
  return (
    <RowList>
      <Row>
        <p>This is the first item.</p>
      </Row>
      <Row>
        <p>This is the second item.</p>
      </Row>
      <Row>
        <p>This is the third item.</p>
      </Row>
    </RowList>
  );
}
```

```js src/RowList.js
export function RowList({ children }) {
  return (
    <div className="RowList">
      {children}
    </div>
  );
}

export function Row({ children }) {
  return (
    <div className="Row">
      {children}
    </div>
  );
}
```

```css
.RowList {
  display: flex;
  flex-direction: column;
  border: 2px solid grey;
  padding: 5px;
}

.Row {
  border: 2px dashed black;
  padding: 5px;
  margin: 5px;
}
```

</Sandpack>

Không giống như sử dụng `Children.map`, cách tiếp cận này không bọc mỗi child tự động. **Tuy nhiên, cách tiếp cận này có lợi ích đáng kể so với [ví dụ trước với `Children.map`](#transforming-children) vì nó hoạt động ngay cả khi bạn tiếp tục tách thêm các component.** Ví dụ, nó vẫn hoạt động nếu bạn tách component `MoreRows` của riêng bạn:

<Sandpack>

```js
import { RowList, Row } from './RowList.js';

export default function App() {
  return (
    <RowList>
      <Row>
        <p>This is the first item.</p>
      </Row>
      <MoreRows />
    </RowList>
  );
}

function MoreRows() {
  return (
    <>
      <Row>
        <p>This is the second item.</p>
      </Row>
      <Row>
        <p>This is the third item.</p>
      </Row>
    </>
  );
}
```

```js src/RowList.js
export function RowList({ children }) {
  return (
    <div className="RowList">
      {children}
    </div>
  );
}

export function Row({ children }) {
  return (
    <div className="Row">
      {children}
    </div>
  );
}
```

```css
.RowList {
  display: flex;
  flex-direction: column;
  border: 2px solid grey;
  padding: 5px;
}

.Row {
  border: 2px dashed black;
  padding: 5px;
  margin: 5px;
}
```

</Sandpack>

Điều này sẽ không hoạt động với `Children.map` vì nó sẽ "thấy" `<MoreRows />` như một child duy nhất (và một row duy nhất).

---

### Nhận một mảng objects như một prop {/*accepting-an-array-of-objects-as-a-prop*/}

Bạn cũng có thể truyền tường minh một mảng như một prop. Ví dụ, `RowList` này nhận một mảng `rows` như một prop:

<Sandpack>

```js
import { RowList, Row } from './RowList.js';

export default function App() {
  return (
    <RowList rows={[
      { id: 'first', content: <p>This is the first item.</p> },
      { id: 'second', content: <p>This is the second item.</p> },
      { id: 'third', content: <p>This is the third item.</p> }
    ]} />
  );
}
```

```js src/RowList.js
export function RowList({ rows }) {
  return (
    <div className="RowList">
      {rows.map(row => (
        <div className="Row" key={row.id}>
          {row.content}
        </div>
      ))}
    </div>
  );
}
```

```css
.RowList {
  display: flex;
  flex-direction: column;
  border: 2px solid grey;
  padding: 5px;
}

.Row {
  border: 2px dashed black;
  padding: 5px;
  margin: 5px;
}
```

</Sandpack>

Vì `rows` là một mảng JavaScript thông thường, component `RowList` có thể sử dụng các phương thức mảng tích hợp như [`map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) trên nó.

Mẫu này đặc biệt hữu ích khi bạn muốn có thể truyền thêm thông tin như dữ liệu có cấu trúc cùng với children. Trong ví dụ dưới đây, component `TabSwitcher` nhận một mảng objects như `tabs` prop:

<Sandpack>

```js
import TabSwitcher from './TabSwitcher.js';

export default function App() {
  return (
    <TabSwitcher tabs={[
      {
        id: 'first',
        header: 'First',
        content: <p>This is the first item.</p>
      },
      {
        id: 'second',
        header: 'Second',
        content: <p>This is the second item.</p>
      },
      {
        id: 'third',
        header: 'Third',
        content: <p>This is the third item.</p>
      }
    ]} />
  );
}
```

```js src/TabSwitcher.js
import { useState } from 'react';

export default function TabSwitcher({ tabs }) {
  const [selectedId, setSelectedId] = useState(tabs[0].id);
  const selectedTab = tabs.find(tab => tab.id === selectedId);
  return (
    <>
      {tabs.map(tab => (
        <button
          key={tab.id}
          onClick={() => setSelectedId(tab.id)}
        >
          {tab.header}
        </button>
      ))}
      <hr />
      <div key={selectedId}>
        <h3>{selectedTab.header}</h3>
        {selectedTab.content}
      </div>
    </>
  );
}
```

</Sandpack>

Không giống như việc truyền children như JSX, cách tiếp cận này cho phép bạn liên kết một số dữ liệu thêm như `header` với mỗi item. Vì bạn đang làm việc trực tiếp với `tabs`, và nó là một mảng, bạn không cần các phương thức `Children`.

---

### Gọi một render prop để tùy chỉnh việc render {/*calling-a-render-prop-to-customize-rendering*/}

Thay vì tạo ra JSX cho mỗi item duy nhất, bạn cũng có thể truyền một hàm trả về JSX và gọi hàm đó khi cần. Trong ví dụ này, component `App` truyền một hàm `renderContent` cho component `TabSwitcher`. Component `TabSwitcher` gọi `renderContent` chỉ cho tab đã chọn:

<Sandpack>

```js
import TabSwitcher from './TabSwitcher.js';

export default function App() {
  return (
    <TabSwitcher
      tabIds={['first', 'second', 'third']}
      getHeader={tabId => {
        return tabId[0].toUpperCase() + tabId.slice(1);
      }}
      renderContent={tabId => {
        return <p>This is the {tabId} item.</p>;
      }}
    />
  );
}
```

```js src/TabSwitcher.js
import { useState } from 'react';

export default function TabSwitcher({ tabIds, getHeader, renderContent }) {
  const [selectedId, setSelectedId] = useState(tabIds[0]);
  return (
    <>
      {tabIds.map((tabId) => (
        <button
          key={tabId}
          onClick={() => setSelectedId(tabId)}
        >
          {getHeader(tabId)}
        </button>
      ))}
      <hr />
      <div key={selectedId}>
        <h3>{getHeader(selectedId)}</h3>
        {renderContent(selectedId)}
      </div>
    </>
  );
}
```

</Sandpack>

Một prop như `renderContent` được gọi là *render prop* vì nó là một prop chỉ định cách render một phần của giao diện người dùng. Tuy nhiên, không có gì đặc biệt về nó: nó là một prop thông thường tình cờ là một hàm.

Render props là các hàm, vì vậy bạn có thể truyền thông tin cho chúng. Ví dụ, component `RowList` này truyền `id` và `index` của mỗi row cho render prop `renderRow`, sử dụng `index` để tô sáng các row chẵn:

<Sandpack>

```js
import { RowList, Row } from './RowList.js';

export default function App() {
  return (
    <RowList
      rowIds={['first', 'second', 'third']}
      renderRow={(id, index) => {
        return (
          <Row isHighlighted={index % 2 === 0}>
            <p>This is the {id} item.</p>
          </Row>
        );
      }}
    />
  );
}
```

```js src/RowList.js
import { Fragment } from 'react';

export function RowList({ rowIds, renderRow }) {
  return (
    <div className="RowList">
      <h1 className="RowListHeader">
        Total rows: {rowIds.length}
      </h1>
      {rowIds.map((rowId, index) =>
        <Fragment key={rowId}>
          {renderRow(rowId, index)}
        </Fragment>
      )}
    </div>
  );
}

export function Row({ children, isHighlighted }) {
  return (
    <div className={[
      'Row',
      isHighlighted ? 'RowHighlighted' : ''
    ].join(' ')}>
      {children}
    </div>
  );
}
```

```css
.RowList {
  display: flex;
  flex-direction: column;
  border: 2px solid grey;
  padding: 5px;
}

.RowListHeader {
  padding-top: 5px;
  font-size: 25px;
  font-weight: bold;
  text-align: center;
}

.Row {
  border: 2px dashed black;
  padding: 5px;
  margin: 5px;
}

.RowHighlighted {
  background: #ffa;
}
```

</Sandpack>

Đây là một ví dụ khác về cách các component cha và con có thể hợp tác mà không cần thao tác children.

---

## Xử lý sự cố {/*troubleshooting*/}

### Tôi truyền một component tùy chỉnh, nhưng các phương thức `Children` không hiển thị kết quả render của nó {/*i-pass-a-custom-component-but-the-children-methods-dont-show-its-render-result*/}

Giả sử bạn truyền hai children cho `RowList` như thế này:

```js
<RowList>
  <p>First item</p>
  <MoreRows />
</RowList>
```

Nếu bạn thực hiện `Children.count(children)` bên trong `RowList`, bạn sẽ nhận được `2`. Ngay cả khi `MoreRows` render 10 items khác nhau, hoặc nếu nó trả về `null`, `Children.count(children)` vẫn sẽ là `2`. Từ góc độ của `RowList`, nó chỉ "thấy" JSX mà nó đã nhận. Nó không "thấy" nội bộ của component `MoreRows`.

Hạn chế này làm cho việc tách một component trở nên khó khăn. Đây là lý do tại sao [các lựa chọn thay thế](#alternatives) được ưu tiên hơn so với việc sử dụng `Children`.
