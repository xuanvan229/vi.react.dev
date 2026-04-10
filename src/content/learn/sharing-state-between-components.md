---
title: Chia sẻ State giữa các Component
---

<Intro>

Đôi khi, bạn muốn state của hai component luôn thay đổi cùng nhau. Để làm điều đó, hãy xóa state khỏi cả hai, di chuyển nó lên component cha chung gần nhất, rồi truyền xuống qua props. Điều này được gọi là *nâng state lên,* và đây là một trong những việc phổ biến nhất bạn sẽ làm khi viết code React.

</Intro>

<YouWillLearn>

- Cách chia sẻ state giữa các component bằng cách nâng nó lên
- Component có kiểm soát và component không có kiểm soát là gì

</YouWillLearn>

## Nâng state lên qua ví dụ {/*lifting-state-up-by-example*/}

Trong ví dụ này, component cha `Accordion` render hai `Panel` riêng biệt:

* `Accordion`
  - `Panel`
  - `Panel`

Mỗi component `Panel` có một state boolean `isActive` xác định nội dung của nó có hiển thị hay không.

Nhấn nút Show cho cả hai panel:

<Sandpack>

```js
import { useState } from 'react';

function Panel({ title, children }) {
  const [isActive, setIsActive] = useState(false);
  return (
    <section className="panel">
      <h3>{title}</h3>
      {isActive ? (
        <p>{children}</p>
      ) : (
        <button onClick={() => setIsActive(true)}>
          Show
        </button>
      )}
    </section>
  );
}

export default function Accordion() {
  return (
    <>
      <h2>Almaty, Kazakhstan</h2>
      <Panel title="About">
        With a population of about 2 million, Almaty is Kazakhstan's largest city. From 1929 to 1997, it was its capital city.
      </Panel>
      <Panel title="Etymology">
        The name comes from <span lang="kk-KZ">алма</span>, the Kazakh word for "apple" and is often translated as "full of apples". In fact, the region surrounding Almaty is thought to be the ancestral home of the apple, and the wild <i lang="la">Malus sieversii</i> is considered a likely candidate for the ancestor of the modern domestic apple.
      </Panel>
    </>
  );
}
```

```css
h3, p { margin: 5px 0px; }
.panel {
  padding: 10px;
  border: 1px solid #aaa;
}
```

</Sandpack>

Chú ý cách nhấn nút của một panel không ảnh hưởng đến panel kia--chúng độc lập với nhau.

<DiagramGroup>

<Diagram name="sharing_state_child" height={367} width={477} alt="Diagram showing a tree of three components, one parent labeled Accordion and two children labeled Panel. Both Panel components contain isActive with value false.">

Ban đầu, state `isActive` của mỗi `Panel` là `false`, vì vậy cả hai đều xuất hiện ở trạng thái thu gọn

</Diagram>

<Diagram name="sharing_state_child_clicked" height={367} width={480} alt="The same diagram as the previous, with the isActive of the first child Panel component highlighted indicating a click with the isActive value set to true. The second Panel component still contains value false." >

Nhấn nút của `Panel` nào sẽ chỉ cập nhật state `isActive` của `Panel` đó

</Diagram>

</DiagramGroup>

**Nhưng bây giờ giả sử bạn muốn thay đổi để chỉ một panel được mở rộng tại một thời điểm.** Với thiết kế đó, mở rộng panel thứ hai nên thu gọn panel thứ nhất. Làm thế nào để làm điều đó?

Để phối hợp hai panel này, bạn cần "nâng state lên" component cha theo ba bước:

1. **Xóa** state khỏi các component con.
2. **Truyền** dữ liệu cố định từ component cha chung.
3. **Thêm** state vào component cha chung và truyền nó xuống cùng với các event handler.

Điều này sẽ cho phép component `Accordion` phối hợp cả hai `Panel` và chỉ mở rộng một cái tại một thời điểm.

### Bước 1: Xóa state khỏi các component con {/*step-1-remove-state-from-the-child-components*/}

Bạn sẽ trao quyền kiểm soát `isActive` của `Panel` cho component cha của nó. Điều này có nghĩa là component cha sẽ truyền `isActive` vào `Panel` dưới dạng prop thay thế. Bắt đầu bằng cách **xóa dòng này** khỏi component `Panel`:

```js
const [isActive, setIsActive] = useState(false);
```

Và thay vào đó, thêm `isActive` vào danh sách props của `Panel`:

```js
function Panel({ title, children, isActive }) {
```

Bây giờ component cha của `Panel` có thể *kiểm soát* `isActive` bằng cách [truyền nó xuống như một prop.](/learn/passing-props-to-a-component) Ngược lại, component `Panel` bây giờ *không có quyền kiểm soát* giá trị của `isActive`--đó là việc của component cha!

### Bước 2: Truyền dữ liệu cố định từ component cha chung {/*step-2-pass-hardcoded-data-from-the-common-parent*/}

Để nâng state lên, bạn phải xác định component cha chung gần nhất của *cả hai* component con mà bạn muốn phối hợp:

* `Accordion` *(component cha chung gần nhất)*
  - `Panel`
  - `Panel`

Trong ví dụ này, đó là component `Accordion`. Vì nó nằm phía trên cả hai panel và có thể kiểm soát props của chúng, nó sẽ trở thành "nguồn sự thật duy nhất" cho panel nào đang active. Hãy để component `Accordion` truyền giá trị cố định của `isActive` (ví dụ: `true`) cho cả hai panel:

<Sandpack>

```js
import { useState } from 'react';

export default function Accordion() {
  return (
    <>
      <h2>Almaty, Kazakhstan</h2>
      <Panel title="About" isActive={true}>
        With a population of about 2 million, Almaty is Kazakhstan's largest city. From 1929 to 1997, it was its capital city.
      </Panel>
      <Panel title="Etymology" isActive={true}>
        The name comes from <span lang="kk-KZ">алма</span>, the Kazakh word for "apple" and is often translated as "full of apples". In fact, the region surrounding Almaty is thought to be the ancestral home of the apple, and the wild <i lang="la">Malus sieversii</i> is considered a likely candidate for the ancestor of the modern domestic apple.
      </Panel>
    </>
  );
}

function Panel({ title, children, isActive }) {
  return (
    <section className="panel">
      <h3>{title}</h3>
      {isActive ? (
        <p>{children}</p>
      ) : (
        <button onClick={() => setIsActive(true)}>
          Show
        </button>
      )}
    </section>
  );
}
```

```css
h3, p { margin: 5px 0px; }
.panel {
  padding: 10px;
  border: 1px solid #aaa;
}
```

</Sandpack>

Thử chỉnh sửa các giá trị `isActive` cố định trong component `Accordion` và xem kết quả trên màn hình.

### Bước 3: Thêm state vào component cha chung {/*step-3-add-state-to-the-common-parent*/}

Nâng state lên thường thay đổi bản chất của những gì bạn lưu trữ dưới dạng state.

Trong trường hợp này, chỉ một panel nên active tại một thời điểm. Điều này có nghĩa là component cha chung `Accordion` cần theo dõi *panel nào* đang active. Thay vì giá trị `boolean`, nó có thể sử dụng một số làm chỉ mục của `Panel` active cho biến state:

```js
const [activeIndex, setActiveIndex] = useState(0);
```

Khi `activeIndex` là `0`, panel đầu tiên đang active, và khi là `1`, đó là panel thứ hai.

Nhấn nút "Show" trong `Panel` nào cũng cần thay đổi chỉ mục active trong `Accordion`. Một `Panel` không thể đặt state `activeIndex` trực tiếp vì nó được định nghĩa bên trong `Accordion`. Component `Accordion` cần *cho phép rõ ràng* component `Panel` thay đổi state của nó bằng cách [truyền một event handler xuống như một prop](/learn/responding-to-events#passing-event-handlers-as-props):

```js
<>
  <Panel
    isActive={activeIndex === 0}
    onShow={() => setActiveIndex(0)}
  >
    ...
  </Panel>
  <Panel
    isActive={activeIndex === 1}
    onShow={() => setActiveIndex(1)}
  >
    ...
  </Panel>
</>
```

`<button>` bên trong `Panel` bây giờ sẽ sử dụng prop `onShow` làm event handler click của nó:

<Sandpack>

```js
import { useState } from 'react';

export default function Accordion() {
  const [activeIndex, setActiveIndex] = useState(0);
  return (
    <>
      <h2>Almaty, Kazakhstan</h2>
      <Panel
        title="About"
        isActive={activeIndex === 0}
        onShow={() => setActiveIndex(0)}
      >
        With a population of about 2 million, Almaty is Kazakhstan's largest city. From 1929 to 1997, it was its capital city.
      </Panel>
      <Panel
        title="Etymology"
        isActive={activeIndex === 1}
        onShow={() => setActiveIndex(1)}
      >
        The name comes from <span lang="kk-KZ">алма</span>, the Kazakh word for "apple" and is often translated as "full of apples". In fact, the region surrounding Almaty is thought to be the ancestral home of the apple, and the wild <i lang="la">Malus sieversii</i> is considered a likely candidate for the ancestor of the modern domestic apple.
      </Panel>
    </>
  );
}

function Panel({
  title,
  children,
  isActive,
  onShow
}) {
  return (
    <section className="panel">
      <h3>{title}</h3>
      {isActive ? (
        <p>{children}</p>
      ) : (
        <button onClick={onShow}>
          Show
        </button>
      )}
    </section>
  );
}
```

```css
h3, p { margin: 5px 0px; }
.panel {
  padding: 10px;
  border: 1px solid #aaa;
}
```

</Sandpack>

Việc nâng state lên đã hoàn tất! Việc di chuyển state vào component cha chung cho phép bạn phối hợp hai panel. Sử dụng chỉ mục active thay vì hai cờ "is shown" đảm bảo rằng chỉ một panel active tại một thời điểm. Và việc truyền event handler xuống component con cho phép component con thay đổi state của component cha.

<DiagramGroup>

<Diagram name="sharing_state_parent" height={385} width={487} alt="Diagram showing a tree of three components, one parent labeled Accordion and two children labeled Panel. Accordion contains an activeIndex value of zero which turns into isActive value of true passed to the first Panel, and isActive value of false passed to the second Panel." >

Ban đầu, `activeIndex` của `Accordion` là `0`, vì vậy `Panel` đầu tiên nhận `isActive = true`

</Diagram>

<Diagram name="sharing_state_parent_clicked" height={385} width={521} alt="The same diagram as the previous, with the activeIndex value of the parent Accordion component highlighted indicating a click with the value changed to one. The flow to both of the children Panel components is also highlighted, and the isActive value passed to each child is set to the opposite: false for the first Panel and true for the second one." >

Khi state `activeIndex` của `Accordion` thay đổi thành `1`, `Panel` thứ hai nhận `isActive = true` thay thế

</Diagram>

</DiagramGroup>

<DeepDive>

#### Component có kiểm soát và component không có kiểm soát {/*controlled-and-uncontrolled-components*/}

Thông thường người ta gọi một component có một số state cục bộ là "không có kiểm soát". Ví dụ, component `Panel` ban đầu với biến state `isActive` là không có kiểm soát vì component cha của nó không thể ảnh hưởng đến việc panel có active hay không.

Ngược lại, bạn có thể nói một component là "có kiểm soát" khi thông tin quan trọng trong nó được điều khiển bởi props thay vì state cục bộ của nó. Điều này cho phép component cha chỉ định đầy đủ hành vi của nó. Component `Panel` cuối cùng với prop `isActive` được kiểm soát bởi component `Accordion`.

Các component không có kiểm soát dễ sử dụng hơn trong các component cha của chúng vì chúng yêu cầu ít cấu hình hơn. Nhưng chúng kém linh hoạt hơn khi bạn muốn phối hợp chúng với nhau. Các component có kiểm soát có tính linh hoạt tối đa, nhưng chúng yêu cầu các component cha cấu hình đầy đủ chúng bằng props.

Trong thực tế, "có kiểm soát" và "không có kiểm soát" không phải là các thuật ngữ kỹ thuật nghiêm ngặt--mỗi component thường có sự kết hợp giữa state cục bộ và props. Tuy nhiên, đây là một cách hữu ích để nói về cách các component được thiết kế và những khả năng chúng cung cấp.

Khi viết một component, hãy xem xét thông tin nào trong nó nên được kiểm soát (qua props), và thông tin nào nên không được kiểm soát (qua state). Nhưng bạn luôn có thể thay đổi suy nghĩ và tái cấu trúc sau này.

</DeepDive>

## Một nguồn sự thật duy nhất cho mỗi state {/*a-single-source-of-truth-for-each-state*/}

Trong một ứng dụng React, nhiều component sẽ có state riêng của chúng. Một số state có thể "sống" gần các leaf component (các component ở cuối cây) như inputs. Các state khác có thể "sống" gần đỉnh của ứng dụng hơn. Ví dụ, ngay cả các thư viện routing phía client thường được triển khai bằng cách lưu trữ route hiện tại trong state React, và truyền nó xuống qua props!

**Với mỗi phần state duy nhất, bạn sẽ chọn component "sở hữu" nó.** Nguyên tắc này còn được gọi là có ["nguồn sự thật duy nhất".](https://en.wikipedia.org/wiki/Single_source_of_truth) Nó không có nghĩa là tất cả state sống ở một nơi--mà là với _mỗi_ phần state, có một _component cụ thể_ giữ thông tin đó. Thay vì sao chép state chia sẻ giữa các component, hãy *nâng nó lên* component cha chia sẻ chung của chúng, và *truyền nó xuống* các component con cần nó.

Ứng dụng của bạn sẽ thay đổi khi bạn làm việc với nó. Thông thường bạn sẽ di chuyển state xuống hoặc ngược lên trong khi bạn vẫn đang tìm hiểu nơi mỗi phần state "sống". Đây đều là một phần của quy trình!

Để xem điều này cảm giác như thế nào trong thực tế với nhiều component hơn, hãy đọc [Tư duy trong React.](/learn/thinking-in-react)

<Recap>

* Khi bạn muốn phối hợp hai component, hãy di chuyển state của chúng lên component cha chung.
* Sau đó truyền thông tin xuống qua props từ component cha chung.
* Cuối cùng, truyền các event handler xuống để các component con có thể thay đổi state của component cha.
* Hữu ích khi xem xét các component là "có kiểm soát" (được điều khiển bởi props) hoặc "không có kiểm soát" (được điều khiển bởi state).

</Recap>

<Challenges>

#### Các input đồng bộ {/*synced-inputs*/}

Hai input này là độc lập. Làm cho chúng đồng bộ: chỉnh sửa một input nên cập nhật input kia với cùng văn bản, và ngược lại.

<Hint>

Bạn cần nâng state của chúng lên component cha.

</Hint>

<Sandpack>

```js
import { useState } from 'react';

export default function SyncedInputs() {
  return (
    <>
      <Input label="First input" />
      <Input label="Second input" />
    </>
  );
}

function Input({ label }) {
  const [text, setText] = useState('');

  function handleChange(e) {
    setText(e.target.value);
  }

  return (
    <label>
      {label}
      {' '}
      <input
        value={text}
        onChange={handleChange}
      />
    </label>
  );
}
```

```css
input { margin: 5px; }
label { display: block; }
```

</Sandpack>

<Solution>

Di chuyển biến state `text` vào component cha cùng với handler `handleChange`. Sau đó truyền chúng xuống dưới dạng props cho cả hai component `Input`. Điều này sẽ giữ chúng đồng bộ.

<Sandpack>

```js
import { useState } from 'react';

export default function SyncedInputs() {
  const [text, setText] = useState('');

  function handleChange(e) {
    setText(e.target.value);
  }

  return (
    <>
      <Input
        label="First input"
        value={text}
        onChange={handleChange}
      />
      <Input
        label="Second input"
        value={text}
        onChange={handleChange}
      />
    </>
  );
}

function Input({ label, value, onChange }) {
  return (
    <label>
      {label}
      {' '}
      <input
        value={value}
        onChange={onChange}
      />
    </label>
  );
}
```

```css
input { margin: 5px; }
label { display: block; }
```

</Sandpack>

</Solution>

#### Lọc một danh sách {/*filtering-a-list*/}

Trong ví dụ này, `SearchBar` có state `query` riêng điều khiển text input. Component cha `FilterableList` của nó hiển thị một `List` các mục, nhưng nó không tính đến search query.

Sử dụng hàm `filterItems(foods, query)` để lọc danh sách theo search query. Để kiểm tra các thay đổi của bạn, hãy xác minh rằng việc nhập "s" vào input lọc danh sách xuống còn "Sushi", "Shish kebab", và "Dim sum".

Lưu ý rằng `filterItems` đã được triển khai và import nên bạn không cần tự viết nó!

<Hint>

Bạn sẽ muốn xóa state `query` và handler `handleChange` khỏi `SearchBar`, và di chuyển chúng vào `FilterableList`. Sau đó truyền chúng xuống `SearchBar` dưới dạng props `query` và `onChange`.

</Hint>

<Sandpack>

```js
import { useState } from 'react';
import { foods, filterItems } from './data.js';

export default function FilterableList() {
  return (
    <>
      <SearchBar />
      <hr />
      <List items={foods} />
    </>
  );
}

function SearchBar() {
  const [query, setQuery] = useState('');

  function handleChange(e) {
    setQuery(e.target.value);
  }

  return (
    <label>
      Search:{' '}
      <input
        value={query}
        onChange={handleChange}
      />
    </label>
  );
}

function List({ items }) {
  return (
    <table>
      <tbody>
        {items.map(food => (
          <tr key={food.id}>
            <td>{food.name}</td>
            <td>{food.description}</td>
          </tr>
        ))}
      </tbody>
    </table>
  );
}
```

```js src/data.js
export function filterItems(items, query) {
  query = query.toLowerCase();
  return items.filter(item =>
    item.name.split(' ').some(word =>
      word.toLowerCase().startsWith(query)
    )
  );
}

export const foods = [{
  id: 0,
  name: 'Sushi',
  description: 'Sushi is a traditional Japanese dish of prepared vinegared rice'
}, {
  id: 1,
  name: 'Dal',
  description: 'The most common way of preparing dal is in the form of a soup to which onions, tomatoes and various spices may be added'
}, {
  id: 2,
  name: 'Pierogi',
  description: 'Pierogi are filled dumplings made by wrapping unleavened dough around a savoury or sweet filling and cooking in boiling water'
}, {
  id: 3,
  name: 'Shish kebab',
  description: 'Shish kebab is a popular meal of skewered and grilled cubes of meat.'
}, {
  id: 4,
  name: 'Dim sum',
  description: 'Dim sum is a large range of small dishes that Cantonese people traditionally enjoy in restaurants for breakfast and lunch'
}];
```

</Sandpack>

<Solution>

Nâng state `query` lên component `FilterableList`. Gọi `filterItems(foods, query)` để lấy danh sách đã lọc và truyền nó xuống `List`. Bây giờ việc thay đổi query input được phản ánh trong danh sách:

<Sandpack>

```js
import { useState } from 'react';
import { foods, filterItems } from './data.js';

export default function FilterableList() {
  const [query, setQuery] = useState('');
  const results = filterItems(foods, query);

  function handleChange(e) {
    setQuery(e.target.value);
  }

  return (
    <>
      <SearchBar
        query={query}
        onChange={handleChange}
      />
      <hr />
      <List items={results} />
    </>
  );
}

function SearchBar({ query, onChange }) {
  return (
    <label>
      Search:{' '}
      <input
        value={query}
        onChange={onChange}
      />
    </label>
  );
}

function List({ items }) {
  return (
    <table>
      <tbody>
        {items.map(food => (
          <tr key={food.id}>
            <td>{food.name}</td>
            <td>{food.description}</td>
          </tr>
        ))}
      </tbody>
    </table>
  );
}
```

```js src/data.js
export function filterItems(items, query) {
  query = query.toLowerCase();
  return items.filter(item =>
    item.name.split(' ').some(word =>
      word.toLowerCase().startsWith(query)
    )
  );
}

export const foods = [{
  id: 0,
  name: 'Sushi',
  description: 'Sushi is a traditional Japanese dish of prepared vinegared rice'
}, {
  id: 1,
  name: 'Dal',
  description: 'The most common way of preparing dal is in the form of a soup to which onions, tomatoes and various spices may be added'
}, {
  id: 2,
  name: 'Pierogi',
  description: 'Pierogi are filled dumplings made by wrapping unleavened dough around a savoury or sweet filling and cooking in boiling water'
}, {
  id: 3,
  name: 'Shish kebab',
  description: 'Shish kebab is a popular meal of skewered and grilled cubes of meat.'
}, {
  id: 4,
  name: 'Dim sum',
  description: 'Dim sum is a large range of small dishes that Cantonese people traditionally enjoy in restaurants for breakfast and lunch'
}];
```

</Sandpack>

</Solution>

</Challenges>
