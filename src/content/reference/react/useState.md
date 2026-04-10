---
title: useState
---

<Intro>

`useState` là một React Hook cho phép bạn thêm một [biến state](/learn/state-a-components-memory) vào component của mình.

```js
const [state, setState] = useState(initialState)
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `useState(initialState)` {/*usestate*/}

Gọi `useState` ở cấp cao nhất của component để khai báo một [biến state.](/learn/state-a-components-memory)

```js
import { useState } from 'react';

function MyComponent() {
  const [age, setAge] = useState(28);
  const [name, setName] = useState('Taylor');
  const [todos, setTodos] = useState(() => createTodos());
  // ...
```

Quy ước là đặt tên biến state như `[something, setSomething]` sử dụng [array destructuring.](https://javascript.info/destructuring-assignment)

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `initialState`: Giá trị bạn muốn state có ban đầu. Nó có thể là giá trị của bất kỳ kiểu nào, nhưng có một hành vi đặc biệt cho các hàm. Đối số này bị bỏ qua sau lần render đầu tiên.
  * Nếu bạn truyền một hàm như `initialState`, nó sẽ được coi là _hàm khởi tạo_. Nó phải là pure, không có đối số, và nên trả về một giá trị của bất kỳ kiểu nào. React sẽ gọi hàm khởi tạo của bạn khi khởi tạo component, và lưu trữ giá trị trả về của nó làm state ban đầu. [Xem ví dụ bên dưới.](#avoiding-recreating-the-initial-state)

#### Giá trị trả về {/*returns*/}

`useState` trả về một mảng với chính xác hai giá trị:

1. State hiện tại. Trong lần render đầu tiên, nó sẽ khớp với `initialState` bạn đã truyền.
2. [Hàm `set`](#setstate) cho phép bạn cập nhật state sang giá trị khác và kích hoạt re-render.

#### Lưu ý {/*caveats*/}

* `useState` là một Hook, vì vậy bạn chỉ có thể gọi nó **ở cấp cao nhất của component** hoặc các Hook của riêng bạn. Bạn không thể gọi nó bên trong vòng lặp hoặc điều kiện. Nếu bạn cần điều đó, hãy tách một component mới và chuyển state vào đó.
* Trong Strict Mode, React sẽ **gọi hàm khởi tạo của bạn hai lần** để [giúp bạn tìm ra các tạp chất vô tình.](#my-initializer-or-updater-function-runs-twice) Đây là hành vi chỉ có trong development và không ảnh hưởng đến production. Nếu hàm khởi tạo của bạn là pure (như nó nên là), điều này sẽ không ảnh hưởng đến hành vi. Kết quả từ một trong các lần gọi sẽ bị bỏ qua.

---

### Hàm `set`, như `setSomething(nextState)` {/*setstate*/}

Hàm `set` được trả về bởi `useState` cho phép bạn cập nhật state sang giá trị khác và kích hoạt re-render. Bạn có thể truyền trạng thái tiếp theo trực tiếp, hoặc một hàm tính toán nó từ trạng thái trước đó:

```js
const [name, setName] = useState('Edward');

function handleClick() {
  setName('Taylor');
  setAge(a => a + 1);
  // ...
```

#### Tham số {/*setstate-parameters*/}

* `nextState`: Giá trị bạn muốn state là. Nó có thể là giá trị của bất kỳ kiểu nào, nhưng có một hành vi đặc biệt cho các hàm.
  * Nếu bạn truyền một hàm như `nextState`, nó sẽ được coi là _hàm updater_. Nó phải là pure, chỉ nhận pending state làm đối số duy nhất của nó, và nên trả về state tiếp theo. React sẽ đưa hàm updater của bạn vào hàng đợi và re-render component của bạn. Trong lần render tiếp theo, React sẽ tính toán state tiếp theo bằng cách áp dụng tất cả các updater đã xếp hàng cho state trước đó. [Xem ví dụ bên dưới.](#updating-state-based-on-the-previous-state)

#### Giá trị trả về {/*setstate-returns*/}

Hàm `set` không có giá trị trả về.

#### Lưu ý {/*setstate-caveats*/}

* Hàm `set` **chỉ cập nhật biến state cho lần render *tiếp theo***. Nếu bạn đọc biến state sau khi gọi hàm `set`, [bạn vẫn sẽ nhận được giá trị cũ](#ive-updated-the-state-but-logging-gives-me-the-old-value) đã có trên màn hình trước khi bạn gọi.

* Nếu giá trị mới bạn cung cấp giống với `state` hiện tại, được xác định bởi so sánh [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is), React sẽ **bỏ qua việc re-render component và các con của nó.** Đây là một tối ưu hóa. Mặc dù trong một số trường hợp React vẫn có thể cần gọi component của bạn trước khi bỏ qua các con, nó sẽ không ảnh hưởng đến code của bạn.

* React [gom nhóm các cập nhật state.](/learn/queueing-a-series-of-state-updates) Nó cập nhật màn hình **sau khi tất cả các event handler đã chạy** và đã gọi các hàm `set` của chúng. Điều này ngăn chặn nhiều re-render trong một sự kiện duy nhất. Trong trường hợp hiếm khi bạn cần buộc React cập nhật màn hình sớm hơn, ví dụ để truy cập DOM, bạn có thể sử dụng [`flushSync`.](/reference/react-dom/flushSync)

* Hàm `set` có identity ổn định, vì vậy bạn sẽ thường thấy nó bị bỏ qua khỏi các dependency của Effect, nhưng bao gồm nó sẽ không gây ra Effect chạy lại. Nếu linter cho phép bạn bỏ qua một dependency mà không có lỗi, thì việc làm đó là an toàn. [Tìm hiểu thêm về việc loại bỏ các dependency của Effect.](/learn/removing-effect-dependencies#move-dynamic-objects-and-functions-inside-your-effect)

* Việc gọi hàm `set` *trong quá trình render* chỉ được phép từ bên trong component đang render. React sẽ loại bỏ output của nó và ngay lập tức cố gắng render lại với state mới. Pattern này hiếm khi cần thiết, nhưng bạn có thể dùng nó để **lưu trữ thông tin từ các lần render trước**. [Xem ví dụ bên dưới.](#storing-information-from-previous-renders)

* Trong Strict Mode, React sẽ **gọi hàm updater của bạn hai lần** để [giúp bạn tìm ra các tạp chất vô tình.](#my-initializer-or-updater-function-runs-twice) Đây là hành vi chỉ có trong development và không ảnh hưởng đến production. Nếu hàm updater của bạn là pure (như nó nên là), điều này sẽ không ảnh hưởng đến hành vi. Kết quả từ một trong các lần gọi sẽ bị bỏ qua.

---

## Cách sử dụng {/*usage*/}

### Thêm state vào component {/*adding-state-to-a-component*/}

Gọi `useState` ở cấp cao nhất của component để khai báo một hoặc nhiều [biến state.](/learn/state-a-components-memory)

```js [[1, 4, "age"], [2, 4, "setAge"], [3, 4, "42"], [1, 5, "name"], [2, 5, "setName"], [3, 5, "'Taylor'"]]
import { useState } from 'react';

function MyComponent() {
  const [age, setAge] = useState(42);
  const [name, setName] = useState('Taylor');
  // ...
```

Quy ước là đặt tên biến state như `[something, setSomething]` sử dụng [array destructuring.](https://javascript.info/destructuring-assignment)

`useState` trả về một mảng với chính xác hai mục:

1. <CodeStep step={1}>State hiện tại</CodeStep> của biến state này, ban đầu được đặt thành <CodeStep step={3}>state ban đầu</CodeStep> bạn đã cung cấp.
2. <CodeStep step={2}>Hàm `set`</CodeStep> cho phép bạn thay đổi nó thành bất kỳ giá trị nào khác để phản hồi tương tác.

Để cập nhật những gì trên màn hình, hãy gọi hàm `set` với một state tiếp theo:

```js [[2, 2, "setName"]]
function handleClick() {
  setName('Robin');
}
```

React sẽ lưu trữ state tiếp theo, render lại component của bạn với các giá trị mới, và cập nhật UI.

<Pitfall>

Việc gọi hàm `set` [**không** thay đổi state hiện tại trong code đang thực thi](#ive-updated-the-state-but-logging-gives-me-the-old-value):

```js {3}
function handleClick() {
  setName('Robin');
  console.log(name); // Vẫn là "Taylor"!
}
```

Nó chỉ ảnh hưởng đến những gì `useState` sẽ trả về bắt đầu từ lần render *tiếp theo*.

</Pitfall>

<Recipes titleText="Các ví dụ cơ bản về useState" titleId="examples-basic">

#### Bộ đếm (số) {/*counter-number*/}

Trong ví dụ này, biến state `count` giữ một số. Nhấp vào nút để tăng nó.

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      You pressed me {count} times
    </button>
  );
}
```

</Sandpack>

<Solution />

#### Ô nhập văn bản (string) {/*text-field-string*/}

Trong ví dụ này, biến state `text` giữ một chuỗi. Khi bạn gõ, `handleChange` đọc giá trị input mới nhất từ phần tử DOM input của trình duyệt, và gọi `setText` để cập nhật state. Điều này cho phép bạn hiển thị `text` hiện tại bên dưới.

<Sandpack>

```js
import { useState } from 'react';

export default function MyInput() {
  const [text, setText] = useState('hello');

  function handleChange(e) {
    setText(e.target.value);
  }

  return (
    <>
      <input value={text} onChange={handleChange} />
      <p>You typed: {text}</p>
      <button onClick={() => setText('hello')}>
        Reset
      </button>
    </>
  );
}
```

</Sandpack>

<Solution />

#### Hộp kiểm (boolean) {/*checkbox-boolean*/}

Trong ví dụ này, biến state `liked` giữ một boolean. Khi bạn nhấp vào input, `setLiked` cập nhật biến state `liked` với trạng thái có được check hay không của hộp kiểm trình duyệt. Biến `liked` được dùng để render văn bản bên dưới hộp kiểm.

<Sandpack>

```js
import { useState } from 'react';

export default function MyCheckbox() {
  const [liked, setLiked] = useState(true);

  function handleChange(e) {
    setLiked(e.target.checked);
  }

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={liked}
          onChange={handleChange}
        />
        I liked this
      </label>
      <p>You {liked ? 'liked' : 'did not like'} this.</p>
    </>
  );
}
```

</Sandpack>

<Solution />

#### Form (hai biến) {/*form-two-variables*/}

Bạn có thể khai báo nhiều hơn một biến state trong cùng một component. Mỗi biến state hoàn toàn độc lập.

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [name, setName] = useState('Taylor');
  const [age, setAge] = useState(42);

  return (
    <>
      <input
        value={name}
        onChange={e => setName(e.target.value)}
      />
      <button onClick={() => setAge(age + 1)}>
        Increment age
      </button>
      <p>Hello, {name}. You are {age}.</p>
    </>
  );
}
```

```css
button { display: block; margin-top: 10px; }
```

</Sandpack>

<Solution />

</Recipes>

---

### Cập nhật state dựa trên state trước đó {/*updating-state-based-on-the-previous-state*/}

Giả sử `age` là `42`. Handler này gọi `setAge(age + 1)` ba lần:

```js
function handleClick() {
  setAge(age + 1); // setAge(42 + 1)
  setAge(age + 1); // setAge(42 + 1)
  setAge(age + 1); // setAge(42 + 1)
}
```

Tuy nhiên, sau một lần click, `age` chỉ là `43` chứ không phải `45`! Điều này vì việc gọi hàm `set` [không cập nhật](/learn/state-as-a-snapshot) biến state `age` trong code đang chạy. Vì vậy mỗi lần gọi `setAge(age + 1)` trở thành `setAge(43)`.

Để giải quyết vấn đề này, **bạn có thể truyền một *hàm updater*** cho `setAge` thay vì state tiếp theo:

```js [[1, 2, "a", 0], [2, 2, "a + 1"], [1, 3, "a", 0], [2, 3, "a + 1"], [1, 4, "a", 0], [2, 4, "a + 1"]]
function handleClick() {
  setAge(a => a + 1); // setAge(42 => 43)
  setAge(a => a + 1); // setAge(43 => 44)
  setAge(a => a + 1); // setAge(44 => 45)
}
```

Ở đây, `a => a + 1` là hàm updater của bạn. Nó nhận <CodeStep step={1}>pending state</CodeStep> và tính toán <CodeStep step={2}>state tiếp theo</CodeStep> từ nó.

React đưa các hàm updater của bạn vào [hàng đợi.](/learn/queueing-a-series-of-state-updates) Sau đó, trong lần render tiếp theo, nó sẽ gọi chúng theo cùng thứ tự:

1. `a => a + 1` sẽ nhận `42` làm pending state và trả về `43` làm state tiếp theo.
1. `a => a + 1` sẽ nhận `43` làm pending state và trả về `44` làm state tiếp theo.
1. `a => a + 1` sẽ nhận `44` làm pending state và trả về `45` làm state tiếp theo.

Không có cập nhật nào khác trong hàng đợi, vì vậy React sẽ lưu trữ `45` làm state hiện tại cuối cùng.

Theo quy ước, thông thường đặt tên đối số pending state theo chữ cái đầu của tên biến state, như `a` cho `age`. Tuy nhiên, bạn cũng có thể gọi nó là `prevAge` hoặc bất cứ thứ gì bạn thấy rõ ràng hơn.

React có thể [gọi các updater của bạn hai lần](#my-initializer-or-updater-function-runs-twice) trong development để kiểm tra rằng chúng là [pure.](/learn/keeping-components-pure)

<DeepDive>

#### Việc dùng updater có phải luôn được ưu tiên không? {/*is-using-an-updater-always-preferred*/}

Bạn có thể nghe khuyến nghị luôn viết code như `setAge(a => a + 1)` nếu state bạn đang đặt được tính toán từ state trước đó. Không có hại gì cả, nhưng cũng không phải lúc nào cũng cần thiết.

Trong hầu hết các trường hợp, không có sự khác biệt giữa hai cách tiếp cận này. React luôn đảm bảo rằng với các hành động người dùng có chủ ý, như click, biến state `age` sẽ được cập nhật trước lần click tiếp theo. Điều này có nghĩa là không có rủi ro nào để event handler của click nhìn thấy `age` "cũ" ở đầu event handler.

Tuy nhiên, nếu bạn thực hiện nhiều cập nhật trong cùng một sự kiện, updaters có thể hữu ích. Chúng cũng hữu ích nếu việc truy cập biến state trực tiếp gây khó khăn (bạn có thể gặp điều này khi tối ưu hóa re-render).

Nếu bạn thích tính nhất quán hơn là cú pháp dài dòng hơn một chút, việc luôn viết updater nếu state bạn đang đặt được tính toán từ state trước đó là hợp lý. Nếu nó được tính toán từ state trước đó của một biến state *khác*, bạn có thể muốn kết hợp chúng thành một object và [sử dụng reducer.](/learn/extracting-state-logic-into-a-reducer)

</DeepDive>

<Recipes titleText="Sự khác biệt giữa việc truyền updater và truyền state tiếp theo trực tiếp" titleId="examples-updater">

#### Truyền hàm updater {/*passing-the-updater-function*/}

Ví dụ này truyền hàm updater, vì vậy nút "+3" hoạt động.

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [age, setAge] = useState(42);

  function increment() {
    setAge(a => a + 1);
  }

  return (
    <>
      <h1>Your age: {age}</h1>
      <button onClick={() => {
        increment();
        increment();
        increment();
      }}>+3</button>
      <button onClick={() => {
        increment();
      }}>+1</button>
    </>
  );
}
```

```css
button { display: block; margin: 10px; font-size: 20px; }
h1 { display: block; margin: 10px; }
```

</Sandpack>

<Solution />

#### Truyền state tiếp theo trực tiếp {/*passing-the-next-state-directly*/}

Ví dụ này **không** truyền hàm updater, vì vậy nút "+3" **không hoạt động như dự định**.

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [age, setAge] = useState(42);

  function increment() {
    setAge(age + 1);
  }

  return (
    <>
      <h1>Your age: {age}</h1>
      <button onClick={() => {
        increment();
        increment();
        increment();
      }}>+3</button>
      <button onClick={() => {
        increment();
      }}>+1</button>
    </>
  );
}
```

```css
button { display: block; margin: 10px; font-size: 20px; }
h1 { display: block; margin: 10px; }
```

</Sandpack>

<Solution />

</Recipes>

---

### Cập nhật objects và arrays trong state {/*updating-objects-and-arrays-in-state*/}

Bạn có thể đặt objects và arrays vào state. Trong React, state được coi là read-only, vì vậy **bạn nên *thay thế* nó thay vì *thay đổi trực tiếp* các objects hiện có**. Ví dụ, nếu bạn có một object `form` trong state, đừng thay đổi trực tiếp nó:

```js
// 🚩 Đừng thay đổi trực tiếp object trong state như thế này:
form.firstName = 'Taylor';
```

Thay vào đó, thay thế toàn bộ object bằng cách tạo một object mới:

```js
// ✅ Thay thế state bằng object mới
setForm({
  ...form,
  firstName: 'Taylor'
});
```

Đọc [cập nhật objects trong state](/learn/updating-objects-in-state) và [cập nhật arrays trong state](/learn/updating-arrays-in-state) để tìm hiểu thêm.

<Recipes titleText="Ví dụ về objects và arrays trong state" titleId="examples-objects">

#### Form (object) {/*form-object*/}

Trong ví dụ này, biến state `form` giữ một object. Mỗi input có change handler gọi `setForm` với state tiếp theo của toàn bộ form. Cú pháp spread `{ ...form }` đảm bảo object state được thay thế thay vì thay đổi trực tiếp.

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [form, setForm] = useState({
    firstName: 'Barbara',
    lastName: 'Hepworth',
    email: 'bhepworth@sculpture.com',
  });

  return (
    <>
      <label>
        First name:
        <input
          value={form.firstName}
          onChange={e => {
            setForm({
              ...form,
              firstName: e.target.value
            });
          }}
        />
      </label>
      <label>
        Last name:
        <input
          value={form.lastName}
          onChange={e => {
            setForm({
              ...form,
              lastName: e.target.value
            });
          }}
        />
      </label>
      <label>
        Email:
        <input
          value={form.email}
          onChange={e => {
            setForm({
              ...form,
              email: e.target.value
            });
          }}
        />
      </label>
      <p>
        {form.firstName}{' '}
        {form.lastName}{' '}
        ({form.email})
      </p>
    </>
  );
}
```

```css
label { display: block; }
input { margin-left: 5px; }
```

</Sandpack>

<Solution />

#### Form (nested object) {/*form-nested-object*/}

Trong ví dụ này, state được lồng nhau hơn. Khi bạn cập nhật state lồng nhau, bạn cần tạo bản sao của object bạn đang cập nhật, cũng như bất kỳ object nào "chứa" nó trên đường đi lên. Đọc [cập nhật object lồng nhau](/learn/updating-objects-in-state#updating-a-nested-object) để tìm hiểu thêm.

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [person, setPerson] = useState({
    name: 'Niki de Saint Phalle',
    artwork: {
      title: 'Blue Nana',
      city: 'Hamburg',
      image: 'https://react.dev/images/docs/scientists/Sd1AgUOm.jpg',
    }
  });

  function handleNameChange(e) {
    setPerson({
      ...person,
      name: e.target.value
    });
  }

  function handleTitleChange(e) {
    setPerson({
      ...person,
      artwork: {
        ...person.artwork,
        title: e.target.value
      }
    });
  }

  function handleCityChange(e) {
    setPerson({
      ...person,
      artwork: {
        ...person.artwork,
        city: e.target.value
      }
    });
  }

  function handleImageChange(e) {
    setPerson({
      ...person,
      artwork: {
        ...person.artwork,
        image: e.target.value
      }
    });
  }

  return (
    <>
      <label>
        Name:
        <input
          value={person.name}
          onChange={handleNameChange}
        />
      </label>
      <label>
        Title:
        <input
          value={person.artwork.title}
          onChange={handleTitleChange}
        />
      </label>
      <label>
        City:
        <input
          value={person.artwork.city}
          onChange={handleCityChange}
        />
      </label>
      <label>
        Image:
        <input
          value={person.artwork.image}
          onChange={handleImageChange}
        />
      </label>
      <p>
        <i>{person.artwork.title}</i>
        {' by '}
        {person.name}
        <br />
        (located in {person.artwork.city})
      </p>
      <img
        src={person.artwork.image}
        alt={person.artwork.title}
      />
    </>
  );
}
```

```css
label { display: block; }
input { margin-left: 5px; margin-bottom: 5px; }
img { width: 200px; height: 200px; }
```

</Sandpack>

<Solution />

#### Danh sách (array) {/*list-array*/}

Trong ví dụ này, biến state `todos` giữ một array. Mỗi button handler gọi `setTodos` với phiên bản tiếp theo của array đó. Cú pháp spread `[...todos]`, `todos.map()` và `todos.filter()` đảm bảo array state được thay thế thay vì thay đổi trực tiếp.

<Sandpack>

```js src/App.js
import { useState } from 'react';
import AddTodo from './AddTodo.js';
import TaskList from './TaskList.js';

let nextId = 3;
const initialTodos = [
  { id: 0, title: 'Buy milk', done: true },
  { id: 1, title: 'Eat tacos', done: false },
  { id: 2, title: 'Brew tea', done: false },
];

export default function TaskApp() {
  const [todos, setTodos] = useState(initialTodos);

  function handleAddTodo(title) {
    setTodos([
      ...todos,
      {
        id: nextId++,
        title: title,
        done: false
      }
    ]);
  }

  function handleChangeTodo(nextTodo) {
    setTodos(todos.map(t => {
      if (t.id === nextTodo.id) {
        return nextTodo;
      } else {
        return t;
      }
    }));
  }

  function handleDeleteTodo(todoId) {
    setTodos(
      todos.filter(t => t.id !== todoId)
    );
  }

  return (
    <>
      <AddTodo
        onAddTodo={handleAddTodo}
      />
      <TaskList
        todos={todos}
        onChangeTodo={handleChangeTodo}
        onDeleteTodo={handleDeleteTodo}
      />
    </>
  );
}
```

```js src/AddTodo.js
import { useState } from 'react';

export default function AddTodo({ onAddTodo }) {
  const [title, setTitle] = useState('');
  return (
    <>
      <input
        placeholder="Add todo"
        value={title}
        onChange={e => setTitle(e.target.value)}
      />
      <button onClick={() => {
        setTitle('');
        onAddTodo(title);
      }}>Add</button>
    </>
  )
}
```

```js src/TaskList.js
import { useState } from 'react';

export default function TaskList({
  todos,
  onChangeTodo,
  onDeleteTodo
}) {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>
          <Task
            todo={todo}
            onChange={onChangeTodo}
            onDelete={onDeleteTodo}
          />
        </li>
      ))}
    </ul>
  );
}

function Task({ todo, onChange, onDelete }) {
  const [isEditing, setIsEditing] = useState(false);
  let todoContent;
  if (isEditing) {
    todoContent = (
      <>
        <input
          value={todo.title}
          onChange={e => {
            onChange({
              ...todo,
              title: e.target.value
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    todoContent = (
      <>
        {todo.title}
        <button onClick={() => setIsEditing(true)}>
          Edit
        </button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={todo.done}
        onChange={e => {
          onChange({
            ...todo,
            done: e.target.checked
          });
        }}
      />
      {todoContent}
      <button onClick={() => onDelete(todo.id)}>
        Delete
      </button>
    </label>
  );
}
```

```css
button { margin: 5px; }
li { list-style-type: none; }
ul, li { margin: 0; padding: 0; }
```

</Sandpack>

<Solution />

#### Viết logic cập nhật ngắn gọn với Immer {/*writing-concise-update-logic-with-immer*/}

Nếu việc cập nhật arrays và objects mà không thay đổi trực tiếp cảm thấy tẻ nhạt, bạn có thể sử dụng thư viện như [Immer](https://github.com/immerjs/use-immer) để giảm code lặp lại. Immer cho phép bạn viết code ngắn gọn như thể bạn đang thay đổi trực tiếp objects, nhưng bên dưới nó thực hiện các cập nhật bất biến:

<Sandpack>

```js
import { useState } from 'react';
import { useImmer } from 'use-immer';

let nextId = 3;
const initialList = [
  { id: 0, title: 'Big Bellies', seen: false },
  { id: 1, title: 'Lunar Landscape', seen: false },
  { id: 2, title: 'Terracotta Army', seen: true },
];

export default function BucketList() {
  const [list, updateList] = useImmer(initialList);

  function handleToggle(artworkId, nextSeen) {
    updateList(draft => {
      const artwork = draft.find(a =>
        a.id === artworkId
      );
      artwork.seen = nextSeen;
    });
  }

  return (
    <>
      <h1>Art Bucket List</h1>
      <h2>My list of art to see:</h2>
      <ItemList
        artworks={list}
        onToggle={handleToggle} />
    </>
  );
}

function ItemList({ artworks, onToggle }) {
  return (
    <ul>
      {artworks.map(artwork => (
        <li key={artwork.id}>
          <label>
            <input
              type="checkbox"
              checked={artwork.seen}
              onChange={e => {
                onToggle(
                  artwork.id,
                  e.target.checked
                );
              }}
            />
            {artwork.title}
          </label>
        </li>
      ))}
    </ul>
  );
}
```

```json package.json
{
  "dependencies": {
    "immer": "1.7.3",
    "react": "latest",
    "react-dom": "latest",
    "react-scripts": "latest",
    "use-immer": "0.5.1"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

</Sandpack>

<Solution />

</Recipes>

---

### Tránh tạo lại state ban đầu {/*avoiding-recreating-the-initial-state*/}

React lưu state ban đầu một lần và bỏ qua nó trong các lần render tiếp theo.

```js
function TodoList() {
  const [todos, setTodos] = useState(createInitialTodos());
  // ...
```

Mặc dù kết quả của `createInitialTodos()` chỉ được sử dụng cho lần render đầu tiên, bạn vẫn gọi hàm này trong mọi lần render. Điều này có thể lãng phí nếu nó đang tạo các mảng lớn hoặc thực hiện các tính toán tốn kém.

Để giải quyết, bạn có thể **truyền nó như hàm _khởi tạo_** cho `useState` thay thế:

```js
function TodoList() {
  const [todos, setTodos] = useState(createInitialTodos);
  // ...
```

Lưu ý rằng bạn đang truyền `createInitialTodos`, đó là *bản thân hàm*, chứ không phải `createInitialTodos()`, là kết quả của việc gọi nó. Nếu bạn truyền một hàm cho `useState`, React chỉ gọi nó trong quá trình khởi tạo.

React có thể [gọi các initializer của bạn hai lần](#my-initializer-or-updater-function-runs-twice) trong development để kiểm tra rằng chúng là [pure.](/learn/keeping-components-pure)

<Recipes titleText="Sự khác biệt giữa việc truyền initializer và truyền state ban đầu trực tiếp" titleId="examples-initializer">

#### Truyền hàm initializer {/*passing-the-initializer-function*/}

Ví dụ này truyền hàm initializer, vì vậy hàm `createInitialTodos` chỉ chạy trong quá trình khởi tạo. Nó không chạy khi component re-render, chẳng hạn như khi bạn gõ vào input.

<Sandpack>

```js
import { useState } from 'react';

function createInitialTodos() {
  const initialTodos = [];
  for (let i = 0; i < 50; i++) {
    initialTodos.push({
      id: i,
      text: 'Item ' + (i + 1)
    });
  }
  return initialTodos;
}

export default function TodoList() {
  const [todos, setTodos] = useState(createInitialTodos);
  const [text, setText] = useState('');

  return (
    <>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        setTodos([{
          id: todos.length,
          text: text
        }, ...todos]);
      }}>Add</button>
      <ul>
        {todos.map(item => (
          <li key={item.id}>
            {item.text}
          </li>
        ))}
      </ul>
    </>
  );
}
```

</Sandpack>

<Solution />

#### Truyền state ban đầu trực tiếp {/*passing-the-initial-state-directly*/}

Ví dụ này **không** truyền hàm initializer, vì vậy hàm `createInitialTodos` chạy trong mọi lần render, chẳng hạn như khi bạn gõ vào input. Không có sự khác biệt nào có thể quan sát được trong hành vi, nhưng code này kém hiệu quả hơn.

<Sandpack>

```js
import { useState } from 'react';

function createInitialTodos() {
  const initialTodos = [];
  for (let i = 0; i < 50; i++) {
    initialTodos.push({
      id: i,
      text: 'Item ' + (i + 1)
    });
  }
  return initialTodos;
}

export default function TodoList() {
  const [todos, setTodos] = useState(createInitialTodos());
  const [text, setText] = useState('');

  return (
    <>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        setTodos([{
          id: todos.length,
          text: text
        }, ...todos]);
      }}>Add</button>
      <ul>
        {todos.map(item => (
          <li key={item.id}>
            {item.text}
          </li>
        ))}
      </ul>
    </>
  );
}
```

</Sandpack>

<Solution />

</Recipes>

---

### Reset state với key {/*resetting-state-with-a-key*/}

Bạn thường gặp thuộc tính `key` khi [render danh sách.](/learn/rendering-lists) Tuy nhiên, nó còn phục vụ một mục đích khác.

Bạn có thể **reset state của component bằng cách truyền `key` khác cho component.** Trong ví dụ này, nút Reset thay đổi biến state `version`, mà chúng ta truyền như `key` cho `Form`. Khi `key` thay đổi, React tạo lại component `Form` (và tất cả các con của nó) từ đầu, vì vậy state của nó được reset.

Đọc [bảo tồn và reset state](/learn/preserving-and-resetting-state) để tìm hiểu thêm.

<Sandpack>

```js src/App.js
import { useState } from 'react';

export default function App() {
  const [version, setVersion] = useState(0);

  function handleReset() {
    setVersion(version + 1);
  }

  return (
    <>
      <button onClick={handleReset}>Reset</button>
      <Form key={version} />
    </>
  );
}

function Form() {
  const [name, setName] = useState('Taylor');

  return (
    <>
      <input
        value={name}
        onChange={e => setName(e.target.value)}
      />
      <p>Hello, {name}.</p>
    </>
  );
}
```

```css
button { display: block; margin-bottom: 20px; }
```

</Sandpack>

---

### Lưu trữ thông tin từ các lần render trước {/*storing-information-from-previous-renders*/}

Thông thường, bạn sẽ cập nhật state trong event handlers. Tuy nhiên, trong các trường hợp hiếm gặp, bạn có thể muốn điều chỉnh state để phản hồi render -- ví dụ, bạn có thể muốn thay đổi một biến state khi một prop thay đổi.

Trong hầu hết các trường hợp, bạn không cần điều này:

* **Nếu giá trị bạn cần có thể được tính toán hoàn toàn từ props hiện tại hoặc state khác, [hãy loại bỏ hoàn toàn state thừa đó.](/learn/choosing-the-state-structure#avoid-redundant-state)** Nếu bạn lo lắng về việc tính toán lại quá nhiều lần, [`useMemo` Hook](/reference/react/useMemo) có thể giúp ích.
* Nếu bạn muốn reset toàn bộ state của cây component, [hãy truyền `key` khác cho component của bạn.](#resetting-state-with-a-key)
* Nếu có thể, hãy cập nhật tất cả state liên quan trong các event handlers.

Trong trường hợp hiếm gặp khi không có cách nào trong số này áp dụng được, có một pattern bạn có thể dùng để cập nhật state dựa trên các giá trị đã được render cho đến nay, bằng cách gọi hàm `set` khi component của bạn đang render.

Đây là một ví dụ. Component `CountLabel` này hiển thị prop `count` được truyền vào cho nó:

```js src/CountLabel.js
export default function CountLabel({ count }) {
  return <h1>{count}</h1>
}
```

Giả sử bạn muốn hiển thị liệu bộ đếm có *tăng hay giảm* kể từ lần thay đổi cuối cùng. Prop `count` không cho bạn biết điều này -- bạn cần theo dõi giá trị trước đó của nó. Thêm biến state `prevCount` để theo dõi nó. Thêm một biến state khác gọi là `trend` để giữ liệu count có tăng hay giảm. So sánh `prevCount` với `count`, và nếu chúng không bằng nhau, hãy cập nhật cả `prevCount` và `trend`. Bây giờ bạn có thể hiển thị cả prop count hiện tại và *cách nó thay đổi kể từ lần render cuối cùng*.

<Sandpack>

```js src/App.js
import { useState } from 'react';
import CountLabel from './CountLabel.js';

export default function App() {
  const [count, setCount] = useState(0);
  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
      <button onClick={() => setCount(count - 1)}>
        Decrement
      </button>
      <CountLabel count={count} />
    </>
  );
}
```

```js src/CountLabel.js active
import { useState } from 'react';

export default function CountLabel({ count }) {
  const [prevCount, setPrevCount] = useState(count);
  const [trend, setTrend] = useState(null);
  if (prevCount !== count) {
    setPrevCount(count);
    setTrend(count > prevCount ? 'increasing' : 'decreasing');
  }
  return (
    <>
      <h1>{count}</h1>
      {trend && <p>The count is {trend}</p>}
    </>
  );
}
```

```css
button { margin-bottom: 10px; }
```

</Sandpack>

Lưu ý rằng nếu bạn gọi hàm `set` trong quá trình render, nó phải nằm trong một điều kiện như `prevCount !== count`, và phải có một lời gọi như `setPrevCount(count)` bên trong điều kiện. Nếu không, component của bạn sẽ re-render trong vòng lặp cho đến khi crash. Ngoài ra, bạn chỉ có thể cập nhật state của component *đang render* như thế này. Việc gọi hàm `set` của *component khác* trong quá trình render là một lỗi. Cuối cùng, lời gọi `set` của bạn vẫn nên [cập nhật state mà không thay đổi trực tiếp](#updating-objects-and-arrays-in-state) -- điều này không có nghĩa là bạn có thể phá vỡ các quy tắc khác của [hàm pure.](/learn/keeping-components-pure)

Pattern này có thể khó hiểu và thường tốt nhất là tránh nó. Tuy nhiên, nó tốt hơn so với cập nhật state trong effect. Khi bạn gọi hàm `set` trong quá trình render, React sẽ re-render component ngay sau khi component của bạn thoát với câu lệnh `return`, và trước khi render các con. Bằng cách này, các con không cần render hai lần. Phần còn lại của hàm component của bạn vẫn sẽ thực thi (và kết quả sẽ bị loại bỏ). Nếu điều kiện của bạn nằm dưới tất cả các lời gọi Hook, bạn có thể thêm `return;` sớm để khởi động lại render sớm hơn.

---

## Xử lý sự cố {/*troubleshooting*/}

### Tôi đã cập nhật state, nhưng log cho tôi giá trị cũ {/*ive-updated-the-state-but-logging-gives-me-the-old-value*/}

Việc gọi hàm `set` **không thay đổi state trong code đang chạy**:

```js {4,5,8}
function handleClick() {
  console.log(count);  // 0

  setCount(count + 1); // Yêu cầu re-render với 1
  console.log(count);  // Vẫn là 0!

  setTimeout(() => {
    console.log(count); // Cũng là 0!
  }, 5000);
}
```

Điều này vì [state hoạt động như một snapshot.](/learn/state-as-a-snapshot) Cập nhật state yêu cầu một render khác với giá trị state mới, nhưng không ảnh hưởng đến biến JavaScript `count` trong event handler đang chạy của bạn.

Nếu bạn cần sử dụng state tiếp theo, bạn có thể lưu nó trong một biến trước khi truyền nó cho hàm `set`:

```js
const nextCount = count + 1;
setCount(nextCount);

console.log(count);     // 0
console.log(nextCount); // 1
```

---

### Tôi đã cập nhật state, nhưng màn hình không cập nhật {/*ive-updated-the-state-but-the-screen-doesnt-update*/}

React sẽ **bỏ qua cập nhật của bạn nếu state tiếp theo bằng state trước đó,** được xác định bởi so sánh [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is). Điều này thường xảy ra khi bạn thay đổi trực tiếp một object hoặc array trong state:

```js
obj.x = 10;  // 🚩 Sai: thay đổi trực tiếp object hiện có
setObj(obj); // 🚩 Không làm gì cả
```

Bạn đã thay đổi trực tiếp object `obj` hiện có và truyền nó lại cho `setObj`, vì vậy React đã bỏ qua cập nhật. Để sửa điều này, bạn cần đảm bảo rằng bạn luôn [_thay thế_ objects và arrays trong state thay vì _thay đổi trực tiếp_ chúng](#updating-objects-and-arrays-in-state):

```js
// ✅ Đúng: tạo object mới
setObj({
  ...obj,
  x: 10
});
```

---

### Tôi nhận được lỗi: "Too many re-renders" {/*im-getting-an-error-too-many-re-renders*/}

Bạn có thể nhận được lỗi: `Too many re-renders. React limits the number of renders to prevent an infinite loop.` Thông thường, điều này có nghĩa là bạn đang đặt state vô điều kiện *trong quá trình render*, vì vậy component của bạn vào vòng lặp: render, đặt state (gây ra render), render, đặt state (gây ra render), v.v. Rất thường xuyên, điều này được gây ra bởi sai lầm trong việc chỉ định event handler:

```js {1-2}
// 🚩 Sai: gọi handler trong quá trình render
return <button onClick={handleClick()}>Click me</button>

// ✅ Đúng: truyền event handler xuống
return <button onClick={handleClick}>Click me</button>

// ✅ Đúng: truyền hàm inline xuống
return <button onClick={(e) => handleClick(e)}>Click me</button>
```

Nếu bạn không thể tìm ra nguyên nhân của lỗi này, hãy nhấp vào mũi tên bên cạnh lỗi trong console và tìm qua JavaScript stack để tìm lời gọi hàm `set` cụ thể chịu trách nhiệm cho lỗi.

---

### Hàm khởi tạo hoặc updater của tôi chạy hai lần {/*my-initializer-or-updater-function-runs-twice*/}

Trong [Strict Mode](/reference/react/StrictMode), React sẽ gọi một số hàm của bạn hai lần thay vì một lần:

```js {2,5-6,11-12}
function TodoList() {
  // Hàm component này sẽ chạy hai lần cho mỗi lần render.

  const [todos, setTodos] = useState(() => {
    // Hàm initializer này sẽ chạy hai lần trong quá trình khởi tạo.
    return createTodos();
  });

  function handleClick() {
    setTodos(prevTodos => {
      // Hàm updater này sẽ chạy hai lần cho mỗi lần click.
      return [...prevTodos, createTodo()];
    });
  }
  // ...
```

Điều này là dự kiến và không nên làm hỏng code của bạn.

Hành vi **chỉ có trong development** này giúp bạn [giữ cho components pure.](/learn/keeping-components-pure) React sử dụng kết quả của một trong các lần gọi, và bỏ qua kết quả của lần gọi kia. Miễn là các hàm component, initializer, và updater của bạn là pure, điều này sẽ không ảnh hưởng đến logic của bạn. Tuy nhiên, nếu chúng vô tình không pure, điều này giúp bạn nhận thấy các sai lầm.

Ví dụ, hàm updater không pure này thay đổi trực tiếp một array trong state:

```js {2,3}
setTodos(prevTodos => {
  // 🚩 Sai lầm: thay đổi trực tiếp state
  prevTodos.push(createTodo());
});
```

Vì React gọi hàm updater của bạn hai lần, bạn sẽ thấy todo được thêm hai lần, vì vậy bạn sẽ biết rằng có một sai lầm. Trong ví dụ này, bạn có thể sửa sai lầm bằng cách [thay thế array thay vì thay đổi trực tiếp nó](#updating-objects-and-arrays-in-state):

```js {2,3}
setTodos(prevTodos => {
  // ✅ Đúng: thay thế bằng state mới
  return [...prevTodos, createTodo()];
});
```

Bây giờ hàm updater này là pure, việc gọi nó thêm một lần không tạo ra sự khác biệt trong hành vi. Đây là lý do tại sao React gọi nó hai lần giúp bạn tìm ra các sai lầm. **Chỉ các hàm component, initializer, và updater cần phải pure.** Event handlers không cần phải pure, vì vậy React sẽ không bao giờ gọi event handlers của bạn hai lần.

Đọc [giữ cho components pure](/learn/keeping-components-pure) để tìm hiểu thêm.

---

### Tôi đang cố gắng đặt state thành một hàm, nhưng nó được gọi thay thế {/*im-trying-to-set-state-to-a-function-but-it-gets-called-instead*/}

Bạn không thể đặt một hàm vào state như thế này:

```js
const [fn, setFn] = useState(someFunction);

function handleClick() {
  setFn(someOtherFunction);
}
```

Vì bạn đang truyền một hàm, React giả định rằng `someFunction` là [hàm khởi tạo](#avoiding-recreating-the-initial-state), và `someOtherFunction` là [hàm updater](#updating-state-based-on-the-previous-state), vì vậy nó cố gắng gọi chúng và lưu kết quả. Để thực sự *lưu trữ* một hàm, bạn phải đặt `() =>` trước chúng trong cả hai trường hợp. Sau đó React sẽ lưu trữ các hàm bạn truyền.

```js {1,4}
const [fn, setFn] = useState(() => someFunction);

function handleClick() {
  setFn(() => someOtherFunction);
}
```
