---
title: Render có điều kiện
---

<Intro>

Các component của bạn thường cần hiển thị những thứ khác nhau tùy thuộc vào các điều kiện khác nhau. Trong React, bạn có thể render JSX có điều kiện bằng cách sử dụng cú pháp JavaScript như câu lệnh `if`, `&&`, và toán tử `? :`.

</Intro>

<YouWillLearn>

* Cách trả về JSX khác nhau tùy thuộc vào điều kiện
* Cách bao gồm hoặc loại trừ một phần JSX có điều kiện
* Các cú pháp tắt điều kiện phổ biến mà bạn sẽ gặp trong các codebase React

</YouWillLearn>

## Trả về JSX có điều kiện {/*conditionally-returning-jsx*/}

Giả sử bạn có một component `PackingList` render nhiều `Item`, có thể được đánh dấu là đã đóng gói hoặc chưa:

<Sandpack>

```js
function Item({ name, isPacked }) {
  return <li className="item">{name}</li>;
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item
          isPacked={true}
          name="Space suit"
        />
        <Item
          isPacked={true}
          name="Helmet with a golden leaf"
        />
        <Item
          isPacked={false}
          name="Photo of Tam"
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

Lưu ý rằng một số component `Item` có prop `isPacked` được đặt thành `true` thay vì `false`. Bạn muốn thêm dấu check (✅) cho các item đã đóng gói nếu `isPacked={true}`.

Bạn có thể viết điều này bằng câu lệnh [`if`/`else`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else) như sau:

```js
if (isPacked) {
  return <li className="item">{name} ✅</li>;
}
return <li className="item">{name}</li>;
```

Nếu prop `isPacked` là `true`, đoạn code này **trả về một cây JSX khác.** Với thay đổi này, một số item sẽ có dấu check ở cuối:

<Sandpack>

```js
function Item({ name, isPacked }) {
  if (isPacked) {
    return <li className="item">{name} ✅</li>;
  }
  return <li className="item">{name}</li>;
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item
          isPacked={true}
          name="Space suit"
        />
        <Item
          isPacked={true}
          name="Helmet with a golden leaf"
        />
        <Item
          isPacked={false}
          name="Photo of Tam"
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

Thử chỉnh sửa những gì được trả về trong mỗi trường hợp, và xem kết quả thay đổi như thế nào!

Lưu ý cách bạn tạo logic rẽ nhánh với câu lệnh `if` và `return` của JavaScript. Trong React, luồng điều khiển (như điều kiện) được xử lý bởi JavaScript.

### Trả về không gì với `null` có điều kiện {/*conditionally-returning-nothing-with-null*/}

Trong một số tình huống, bạn sẽ không muốn render bất kỳ thứ gì cả. Ví dụ, giả sử bạn không muốn hiển thị các item đã đóng gói. Một component phải trả về một thứ gì đó. Trong trường hợp này, bạn có thể trả về `null`:

```js
if (isPacked) {
  return null;
}
return <li className="item">{name}</li>;
```

Nếu `isPacked` là true, component sẽ không trả về gì, `null`. Ngược lại, nó sẽ trả về JSX để render.

<Sandpack>

```js
function Item({ name, isPacked }) {
  if (isPacked) {
    return null;
  }
  return <li className="item">{name}</li>;
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item
          isPacked={true}
          name="Space suit"
        />
        <Item
          isPacked={true}
          name="Helmet with a golden leaf"
        />
        <Item
          isPacked={false}
          name="Photo of Tam"
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

Trong thực tế, trả về `null` từ một component không phổ biến vì nó có thể làm ngạc nhiên một lập trình viên đang cố render nó. Thường thì, bạn sẽ bao gồm hoặc loại trừ component có điều kiện trong JSX của component cha. Đây là cách làm điều đó!

## Bao gồm JSX có điều kiện {/*conditionally-including-jsx*/}

Trong ví dụ trước, bạn đã kiểm soát cây JSX nào (nếu có!) được trả về bởi component. Bạn có thể đã nhận thấy một số trùng lặp trong kết quả render:

```js
<li className="item">{name} ✅</li>
```

rất giống với

```js
<li className="item">{name}</li>
```

Cả hai nhánh điều kiện đều trả về `<li className="item">...</li>`:

```js
if (isPacked) {
  return <li className="item">{name} ✅</li>;
}
return <li className="item">{name}</li>;
```

Mặc dù sự trùng lặp này không có hại, nhưng nó có thể làm code của bạn khó bảo trì hơn. Nếu bạn muốn thay đổi `className` thì sao? Bạn sẽ phải làm điều đó ở hai chỗ trong code! Trong tình huống như vậy, bạn có thể bao gồm một ít JSX có điều kiện để làm code [DRY (Don't Repeat Yourself).](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)

### Toán tử điều kiện (ternary) (`? :`) {/*conditional-ternary-operator--*/}

JavaScript có một cú pháp gọn gàng để viết biểu thức điều kiện -- [toán tử điều kiện](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator) hay "toán tử ternary".

Thay vì viết như này:

```js
if (isPacked) {
  return <li className="item">{name} ✅</li>;
}
return <li className="item">{name}</li>;
```

Bạn có thể viết như này:

```js
return (
  <li className="item">
    {isPacked ? name + ' ✅' : name}
  </li>
);
```

Bạn có thể đọc như là *"nếu `isPacked` là true, thì (`?`) render `name + ' ✅'`, ngược lại (`:`) render `name`"*.

<DeepDive>

#### Hai ví dụ này có tương đương hoàn toàn không? {/*are-these-two-examples-fully-equivalent*/}

Nếu bạn đến từ nền lập trình hướng đối tượng, bạn có thể cho rằng hai ví dụ trên hơi khác nhau vì một trong số chúng có thể tạo hai "instance" khác nhau của `<li>`. Nhưng các element JSX không phải là "instance" vì chúng không giữ bất kỳ state nội bộ nào và không phải là các node DOM thật. Chúng là những mô tả nhẹ, giống như bản thiết kế. Vậy nên hai ví dụ này, thực tế, *hoàn toàn tương đương*. [Bảo toàn và Reset State](/learn/preserving-and-resetting-state) đi sâu vào chi tiết cách hoạt động này.

</DeepDive>

Bây giờ giả sử bạn muốn bọc văn bản của item đã hoàn thành vào một thẻ HTML khác, như `<del>` để gạch ngang nó. Bạn có thể thêm nhiều dòng mới và dấu ngoặc để dễ lồng thêm JSX trong mỗi trường hợp:

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {isPacked ? (
        <del>
          {name + ' ✅'}
        </del>
      ) : (
        name
      )}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item
          isPacked={true}
          name="Space suit"
        />
        <Item
          isPacked={true}
          name="Helmet with a golden leaf"
        />
        <Item
          isPacked={false}
          name="Photo of Tam"
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

Kiểu này hoạt động tốt cho các điều kiện đơn giản, nhưng hãy sử dụng vừa phải. Nếu các component của bạn trở nên lộn xộn với quá nhiều markup điều kiện lồng nhau, hãy xem xét trích xuất các component con để dọn dẹp. Trong React, markup là một phần của code, vì vậy bạn có thể sử dụng các công cụ như biến và hàm để sắp xếp các biểu thức phức tạp.

### Toán tử AND logic (`&&`) {/*logical-and-operator-*/}

Một cú pháp tắt phổ biến khác bạn sẽ gặp là [toán tử AND logic (`&&`) của JavaScript.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND#:~:text=The%20logical%20AND%20(%20%26%26%20)%20operator,it%20returns%20a%20Boolean%20value.) Bên trong các component React, nó thường xuất hiện khi bạn muốn render một số JSX khi điều kiện là true, **hoặc không render gì ngược lại.** Với `&&`, bạn có thể render dấu check có điều kiện chỉ khi `isPacked` là `true`:

```js
return (
  <li className="item">
    {name} {isPacked && '✅'}
  </li>
);
```

Bạn có thể đọc như là *"nếu `isPacked`, thì (`&&`) render dấu check, ngược lại, không render gì"*.

Đây là nó trong thực tế:

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {name} {isPacked && '✅'}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item
          isPacked={true}
          name="Space suit"
        />
        <Item
          isPacked={true}
          name="Helmet with a golden leaf"
        />
        <Item
          isPacked={false}
          name="Photo of Tam"
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

Một [biểu thức && của JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND) trả về giá trị của vế phải (trong trường hợp của chúng ta, dấu check) nếu vế trái (điều kiện của chúng ta) là `true`. Nhưng nếu điều kiện là `false`, toàn bộ biểu thức trở thành `false`. React coi `false` là một "lỗ hổng" trong cây JSX, giống như `null` hoặc `undefined`, và không render bất kỳ thứ gì ở vị trí đó.


<Pitfall>

**Đừng đặt số ở vế trái của `&&`.**

Để kiểm tra điều kiện, JavaScript tự động chuyển đổi vế trái thành boolean. Tuy nhiên, nếu vế trái là `0`, thì toàn bộ biểu thức nhận giá trị đó (`0`), và React sẽ vui vẻ render `0` thay vì không render gì.

Ví dụ, một lỗi phổ biến là viết code như `messageCount && <p>New messages</p>`. Dễ giả rằng nó không render gì khi `messageCount` là `0`, nhưng thực tế nó render số `0`!

Để sửa, hãy làm cho vế trái thành boolean: `messageCount > 0 && <p>New messages</p>`.

</Pitfall>

### Gán JSX vào biến có điều kiện {/*conditionally-assigning-jsx-to-a-variable*/}

Khi các cú pháp tắt cản trở việc viết code bình thường, hãy thử sử dụng câu lệnh `if` và một biến. Bạn có thể gán lại các biến được khai báo với [`let`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let), vì vậy hãy bắt đầu bằng cách cung cấp nội dung mặc định bạn muốn hiển thị, tên:

```js
let itemContent = name;
```

Sử dụng câu lệnh `if` để gán lại biểu thức JSX cho `itemContent` nếu `isPacked` là `true`:

```js
if (isPacked) {
  itemContent = name + " ✅";
}
```

[Dấu ngoặc nhọn mở "cửa sổ vào JavaScript".](/learn/javascript-in-jsx-with-curly-braces#using-curly-braces-a-window-into-the-javascript-world) Nhúng biến với dấu ngoặc nhọn trong cây JSX được trả về, lồng biểu thức đã tính toán trước đó bên trong JSX:

```js
<li className="item">
  {itemContent}
</li>
```

Kiểu này là dài dòng nhất, nhưng cũng linh hoạt nhất. Đây là nó trong thực tế:

<Sandpack>

```js
function Item({ name, isPacked }) {
  let itemContent = name;
  if (isPacked) {
    itemContent = name + " ✅";
  }
  return (
    <li className="item">
      {itemContent}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item
          isPacked={true}
          name="Space suit"
        />
        <Item
          isPacked={true}
          name="Helmet with a golden leaf"
        />
        <Item
          isPacked={false}
          name="Photo of Tam"
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

Như trước, điều này không chỉ hoạt động cho văn bản, mà còn cho bất kỳ JSX tùy ý nào:

<Sandpack>

```js
function Item({ name, isPacked }) {
  let itemContent = name;
  if (isPacked) {
    itemContent = (
      <del>
        {name + " ✅"}
      </del>
    );
  }
  return (
    <li className="item">
      {itemContent}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item
          isPacked={true}
          name="Space suit"
        />
        <Item
          isPacked={true}
          name="Helmet with a golden leaf"
        />
        <Item
          isPacked={false}
          name="Photo of Tam"
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

Nếu bạn chưa quen với JavaScript, sự đa dạng của các kiểu này có thể có vẻ choáng ngợp lúc đầu. Tuy nhiên, học chúng sẽ giúp bạn đọc và viết bất kỳ code JavaScript nào -- và không chỉ các component React! Chọn cách bạn thích để bắt đầu, và sau đó tham khảo lại tài liệu này nếu bạn quên cách các cách khác hoạt động.

<Recap>

* Trong React, bạn kiểm soát logic rẽ nhánh bằng JavaScript.
* Bạn có thể trả về biểu thức JSX có điều kiện với câu lệnh `if`.
* Bạn có thể lưu một số JSX vào biến có điều kiện và sau đó bao gồm nó bên trong JSX khác bằng cách sử dụng dấu ngoặc nhọn.
* Trong JSX, `{cond ? <A /> : <B />}` có nghĩa là *"nếu `cond`, render `<A />`, ngược lại `<B />`"*.
* Trong JSX, `{cond && <A />}` có nghĩa là *"nếu `cond`, render `<A />`, ngược lại không gì"*.
* Các cú pháp tắt là phổ biến, nhưng bạn không bắt buộc phải sử dụng chúng nếu bạn thích `if` bình thường.

</Recap>



<Challenges>

#### Hiển thị biểu tượng cho các item chưa hoàn thành với `? :` {/*show-an-icon-for-incomplete-items-with--*/}

Sử dụng toán tử điều kiện (`cond ? a : b`) để render ❌ nếu `isPacked` không phải `true`.

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {name} {isPacked && '✅'}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item
          isPacked={true}
          name="Space suit"
        />
        <Item
          isPacked={true}
          name="Helmet with a golden leaf"
        />
        <Item
          isPacked={false}
          name="Photo of Tam"
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

<Solution>

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {name} {isPacked ? '✅' : '❌'}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item
          isPacked={true}
          name="Space suit"
        />
        <Item
          isPacked={true}
          name="Helmet with a golden leaf"
        />
        <Item
          isPacked={false}
          name="Photo of Tam"
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

</Solution>

#### Hiển thị mức độ quan trọng của item với `&&` {/*show-the-item-importance-with-*/}

Trong ví dụ này, mỗi `Item` nhận một prop số `importance`. Sử dụng toán tử `&&` để render "_(Importance: X)_" in nghiêng, nhưng chỉ cho các item có mức độ quan trọng khác không. Danh sách item của bạn nên trông như thế này:

* Space suit _(Importance: 9)_
* Helmet with a golden leaf
* Photo of Tam _(Importance: 6)_

Đừng quên thêm khoảng trắng giữa hai nhãn!

<Sandpack>

```js
function Item({ name, importance }) {
  return (
    <li className="item">
      {name}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item
          importance={9}
          name="Space suit"
        />
        <Item
          importance={0}
          name="Helmet with a golden leaf"
        />
        <Item
          importance={6}
          name="Photo of Tam"
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

<Solution>

Đây là cách làm:

<Sandpack>

```js
function Item({ name, importance }) {
  return (
    <li className="item">
      {name}
      {importance > 0 && ' '}
      {importance > 0 &&
        <i>(Importance: {importance})</i>
      }
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item
          importance={9}
          name="Space suit"
        />
        <Item
          importance={0}
          name="Helmet with a golden leaf"
        />
        <Item
          importance={6}
          name="Photo of Tam"
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

Lưu ý rằng bạn phải viết `importance > 0 && ...` thay vì `importance && ...` để nếu `importance` là `0`, `0` không được render làm kết quả!

Trong giải pháp này, hai điều kiện riêng biệt được sử dụng để chèn khoảng trắng giữa tên và nhãn mức độ quan trọng. Ngoài ra, bạn có thể sử dụng Fragment với khoảng trắng đầu: `importance > 0 && <> <i>...</i></>` hoặc thêm khoảng trắng ngay bên trong `<i>`:  `importance > 0 && <i> ...</i>`.

</Solution>

#### Refactor chuỗi `? :` thành `if` và biến {/*refactor-a-series-of---to-if-and-variables*/}

Component `Drink` này sử dụng chuỗi điều kiện `? :` để hiển thị thông tin khác nhau tùy thuộc vào prop `name` là `"tea"` hay `"coffee"`. Vấn đề là thông tin về mỗi loại đồ uống bị trải ra nhiều điều kiện. Refactor code này để sử dụng một câu lệnh `if` duy nhất thay vì ba điều kiện `? :`.

<Sandpack>

```js
function Drink({ name }) {
  return (
    <section>
      <h1>{name}</h1>
      <dl>
        <dt>Part of plant</dt>
        <dd>{name === 'tea' ? 'leaf' : 'bean'}</dd>
        <dt>Caffeine content</dt>
        <dd>{name === 'tea' ? '15–70 mg/cup' : '80–185 mg/cup'}</dd>
        <dt>Age</dt>
        <dd>{name === 'tea' ? '4,000+ years' : '1,000+ years'}</dd>
      </dl>
    </section>
  );
}

export default function DrinkList() {
  return (
    <div>
      <Drink name="tea" />
      <Drink name="coffee" />
    </div>
  );
}
```

</Sandpack>

Sau khi bạn đã refactor code để sử dụng `if`, bạn có ý tưởng nào để đơn giản hóa thêm không?

<Solution>

Có nhiều cách để làm điều này, nhưng đây là một điểm bắt đầu:

<Sandpack>

```js
function Drink({ name }) {
  let part, caffeine, age;
  if (name === 'tea') {
    part = 'leaf';
    caffeine = '15–70 mg/cup';
    age = '4,000+ years';
  } else if (name === 'coffee') {
    part = 'bean';
    caffeine = '80–185 mg/cup';
    age = '1,000+ years';
  }
  return (
    <section>
      <h1>{name}</h1>
      <dl>
        <dt>Part of plant</dt>
        <dd>{part}</dd>
        <dt>Caffeine content</dt>
        <dd>{caffeine}</dd>
        <dt>Age</dt>
        <dd>{age}</dd>
      </dl>
    </section>
  );
}

export default function DrinkList() {
  return (
    <div>
      <Drink name="tea" />
      <Drink name="coffee" />
    </div>
  );
}
```

</Sandpack>

Ở đây thông tin về mỗi loại đồ uống được nhóm lại với nhau thay vì bị trải ra nhiều điều kiện. Điều này giúp dễ dàng thêm nhiều loại đồ uống trong tương lai.

Một giải pháp khác là loại bỏ điều kiện hoàn toàn bằng cách chuyển thông tin vào các object:

<Sandpack>

```js
const drinks = {
  tea: {
    part: 'leaf',
    caffeine: '15–70 mg/cup',
    age: '4,000+ years'
  },
  coffee: {
    part: 'bean',
    caffeine: '80–185 mg/cup',
    age: '1,000+ years'
  }
};

function Drink({ name }) {
  const info = drinks[name];
  return (
    <section>
      <h1>{name}</h1>
      <dl>
        <dt>Part of plant</dt>
        <dd>{info.part}</dd>
        <dt>Caffeine content</dt>
        <dd>{info.caffeine}</dd>
        <dt>Age</dt>
        <dd>{info.age}</dd>
      </dl>
    </section>
  );
}

export default function DrinkList() {
  return (
    <div>
      <Drink name="tea" />
      <Drink name="coffee" />
    </div>
  );
}
```

</Sandpack>

</Solution>

</Challenges>
