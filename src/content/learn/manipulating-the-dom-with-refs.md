---
title: 'Thao tác DOM với Refs'
---

<Intro>

React tự động cập nhật [DOM](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) để khớp với output render của bạn, nên các component của bạn thường không cần thao tác với nó. Tuy nhiên, đôi khi bạn có thể cần truy cập các phần tử DOM được quản lý bởi React--ví dụ, để focus vào một node, cuộn đến nó, hoặc đo kích thước và vị trí của nó. Không có cách tích hợp sẵn để làm những điều đó trong React, vì vậy bạn sẽ cần một *ref* đến DOM node.

</Intro>

<YouWillLearn>

- Cách truy cập DOM node được quản lý bởi React với thuộc tính `ref`
- Thuộc tính JSX `ref` liên quan đến Hook `useRef` như thế nào
- Cách truy cập DOM node của component khác
- Trong trường hợp nào thì an toàn khi sửa đổi DOM được quản lý bởi React

</YouWillLearn>

## Lấy ref đến node {/*getting-a-ref-to-the-node*/}

Để truy cập DOM node được quản lý bởi React, trước tiên, hãy import Hook `useRef`:

```js
import { useRef } from 'react';
```

Sau đó, dùng nó để khai báo ref bên trong component của bạn:

```js
const myRef = useRef(null);
```

Cuối cùng, truyền ref của bạn như thuộc tính `ref` vào thẻ JSX mà bạn muốn lấy DOM node:

```js
<div ref={myRef}>
```

Hook `useRef` trả về một đối tượng với một thuộc tính duy nhất gọi là `current`. Ban đầu, `myRef.current` sẽ là `null`. Khi React tạo DOM node cho `<div>` này, React sẽ đặt tham chiếu đến node này vào `myRef.current`. Sau đó bạn có thể truy cập DOM node này từ [event handlers](/learn/responding-to-events) và dùng các [browser APIs](https://developer.mozilla.org/docs/Web/API/Element) tích hợp sẵn được định nghĩa trên nó.

```js
// Bạn có thể dùng bất kỳ browser API nào, ví dụ:
myRef.current.scrollIntoView();
```

### Ví dụ: Focus vào text input {/*example-focusing-a-text-input*/}

Trong ví dụ này, nhấp vào nút sẽ focus vào input:

<Sandpack>

```js
import { useRef } from 'react';

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

</Sandpack>

Để triển khai điều này:

1. Khai báo `inputRef` với Hook `useRef`.
2. Truyền nó như `<input ref={inputRef}>`. Điều này cho React biết **đặt DOM node của `<input>` này vào `inputRef.current`.**
3. Trong hàm `handleClick`, đọc DOM node input từ `inputRef.current` và gọi [`focus()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus) trên nó với `inputRef.current.focus()`.
4. Truyền event handler `handleClick` vào `<button>` với `onClick`.

Trong khi thao tác DOM là trường hợp sử dụng phổ biến nhất cho refs, Hook `useRef` có thể được dùng để lưu trữ những thứ khác bên ngoài React, như timer IDs. Tương tự như state, refs vẫn tồn tại giữa các lần render. Refs giống như các biến state không kích hoạt re-render khi bạn thiết lập chúng. Đọc về refs trong [Tham chiếu Giá trị với Refs.](/learn/referencing-values-with-refs)

### Ví dụ: Cuộn đến một phần tử {/*example-scrolling-to-an-element*/}

Bạn có thể có nhiều hơn một ref trong một component. Trong ví dụ này, có một carousel của ba hình ảnh. Mỗi nút căn giữa một hình ảnh bằng cách gọi phương thức [`scrollIntoView()`](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView) của browser trên DOM node tương ứng:

<Sandpack>

```js
import { useRef } from 'react';

export default function CatFriends() {
  const firstCatRef = useRef(null);
  const secondCatRef = useRef(null);
  const thirdCatRef = useRef(null);

  function handleScrollToFirstCat() {
    firstCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  function handleScrollToSecondCat() {
    secondCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  function handleScrollToThirdCat() {
    thirdCatRef.current.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  return (
    <>
      <nav>
        <button onClick={handleScrollToFirstCat}>
          Neo
        </button>
        <button onClick={handleScrollToSecondCat}>
          Millie
        </button>
        <button onClick={handleScrollToThirdCat}>
          Bella
        </button>
      </nav>
      <div>
        <ul>
          <li>
            <img
              src="https://placecats.com/neo/300/200"
              alt="Neo"
              ref={firstCatRef}
            />
          </li>
          <li>
            <img
              src="https://placecats.com/millie/200/200"
              alt="Millie"
              ref={secondCatRef}
            />
          </li>
          <li>
            <img
              src="https://placecats.com/bella/199/200"
              alt="Bella"
              ref={thirdCatRef}
            />
          </li>
        </ul>
      </div>
    </>
  );
}
```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}
```

</Sandpack>

<DeepDive>

#### Cách quản lý danh sách refs bằng ref callback {/*how-to-manage-a-list-of-refs-using-a-ref-callback*/}

Trong các ví dụ trên, có một số lượng refs được định nghĩa trước. Tuy nhiên, đôi khi bạn có thể cần ref cho mỗi mục trong danh sách, và bạn không biết bạn sẽ có bao nhiêu. Điều gì đó như thế này **sẽ không hoạt động**:

```js
<ul>
  {items.map((item) => {
    // Không hoạt động!
    const ref = useRef(null);
    return <li ref={ref} />;
  })}
</ul>
```

Điều này vì **Hooks chỉ được gọi ở cấp độ trên cùng của component.** Bạn không thể gọi `useRef` trong vòng lặp, trong điều kiện, hoặc bên trong lời gọi `map()`.

Một cách có thể giải quyết vấn đề này là lấy một ref duy nhất đến phần tử cha của chúng, và sau đó dùng các phương thức thao tác DOM như [`querySelectorAll`](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelectorAll) để "tìm" các node con riêng lẻ từ nó. Tuy nhiên, điều này dễ vỡ và có thể bị hỏng nếu cấu trúc DOM của bạn thay đổi.

Một giải pháp khác là **truyền một hàm vào thuộc tính `ref`.** Đây gọi là [`ref` callback.](/reference/react-dom/components/common#ref-callback) React sẽ gọi ref callback của bạn với DOM node khi đến lúc thiết lập ref, và gọi hàm cleanup được trả về từ callback khi đến lúc xóa nó. Điều này cho phép bạn duy trì mảng của riêng mình hoặc một [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map), và truy cập bất kỳ ref nào bằng chỉ số hoặc một loại ID nào đó.

Ví dụ này cho thấy cách bạn có thể dùng cách tiếp cận này để cuộn đến một node tùy ý trong một danh sách dài:

<Sandpack>

```js
import { useRef, useState } from "react";

export default function CatFriends() {
  const itemsRef = useRef(null);
  const [catList, setCatList] = useState(setupCatList);

  function scrollToCat(cat) {
    const map = getMap();
    const node = map.get(cat);
    node.scrollIntoView({
      behavior: "smooth",
      block: "nearest",
      inline: "center",
    });
  }

  function getMap() {
    if (!itemsRef.current) {
      // Initialize the Map on first usage.
      itemsRef.current = new Map();
    }
    return itemsRef.current;
  }

  return (
    <>
      <nav>
        <button onClick={() => scrollToCat(catList[0])}>Neo</button>
        <button onClick={() => scrollToCat(catList[5])}>Millie</button>
        <button onClick={() => scrollToCat(catList[8])}>Bella</button>
      </nav>
      <div>
        <ul>
          {catList.map((cat) => (
            <li
              key={cat.id}
              ref={(node) => {
                const map = getMap();
                map.set(cat, node);

                return () => {
                  map.delete(cat);
                };
              }}
            >
              <img src={cat.imageUrl} />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

function setupCatList() {
  const catCount = 10;
  const catList = new Array(catCount)
  for (let i = 0; i < catCount; i++) {
    let imageUrl = '';
    if (i < 5) {
      imageUrl = "https://placecats.com/neo/320/240";
    } else if (i < 8) {
      imageUrl = "https://placecats.com/millie/320/240";
    } else {
      imageUrl = "https://placecats.com/bella/320/240";
    }
    catList[i] = {
      id: i,
      imageUrl,
    };
  }
  return catList;
}

```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}
```

</Sandpack>

Trong ví dụ này, `itemsRef` không giữ một DOM node duy nhất. Thay vào đó, nó giữ một [Map](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Map) từ item ID đến DOM node. ([Refs có thể giữ bất kỳ giá trị nào!](/learn/referencing-values-with-refs)) [`ref` callback](/reference/react-dom/components/common#ref-callback) trên mỗi mục danh sách đảm bảo cập nhật Map:

```js
<li
  key={cat.id}
  ref={node => {
    const map = getMap();
    // Add to the Map
    map.set(cat, node);

    return () => {
      // Remove from the Map
      map.delete(cat);
    };
  }}
>
```

Điều này cho phép bạn đọc các DOM node riêng lẻ từ Map sau đó.

<Note>

Khi Strict Mode được bật, ref callbacks sẽ chạy hai lần trong development.

Đọc thêm về [cách này giúp tìm bug](/reference/react/StrictMode#fixing-bugs-found-by-re-running-ref-callbacks-in-development) trong callback refs.

</Note>

</DeepDive>

## Truy cập DOM node của component khác {/*accessing-another-components-dom-nodes*/}

<Pitfall>
Refs là lối thoát. Thao tác thủ công với DOM node của component _khác_ có thể làm code của bạn dễ vỡ.
</Pitfall>

Bạn có thể truyền refs từ component cha xuống component con [giống như bất kỳ prop nào khác](/learn/passing-props-to-a-component).

```js {3-4,9}
import { useRef } from 'react';

function MyInput({ ref }) {
  return <input ref={ref} />;
}

function MyForm() {
  const inputRef = useRef(null);
  return <MyInput ref={inputRef} />
}
```

Trong ví dụ trên, ref được tạo trong component cha, `MyForm`, và được truyền cho component con, `MyInput`. `MyInput` sau đó truyền ref cho `<input>`. Vì `<input>` là [component tích hợp sẵn](/reference/react-dom/components/common) React thiết lập thuộc tính `.current` của ref thành phần tử DOM `<input>`.

`inputRef` được tạo trong `MyForm` bây giờ trỏ đến phần tử DOM `<input>` được trả về bởi `MyInput`. Một click handler được tạo trong `MyForm` có thể truy cập `inputRef` và gọi `focus()` để thiết lập focus trên `<input>`.

<Sandpack>

```js
import { useRef } from 'react';

function MyInput({ ref }) {
  return <input ref={ref} />;
}

export default function MyForm() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

</Sandpack>

<DeepDive>

#### Expose một tập con của API với imperative handle {/*exposing-a-subset-of-the-api-with-an-imperative-handle*/}

Trong ví dụ trên, ref được truyền cho `MyInput` được chuyển tiếp đến phần tử DOM input gốc. Điều này cho phép component cha gọi `focus()` trên nó. Tuy nhiên, điều này cũng cho phép component cha làm điều gì đó khác--ví dụ, thay đổi CSS styles của nó. Trong những trường hợp không phổ biến, bạn có thể muốn hạn chế chức năng được expose. Bạn có thể làm điều đó với [`useImperativeHandle`](/reference/react/useImperativeHandle):

<Sandpack>

```js
import { useRef, useImperativeHandle } from "react";

function MyInput({ ref }) {
  const realInputRef = useRef(null);
  useImperativeHandle(ref, () => ({
    // Only expose focus and nothing else
    focus() {
      realInputRef.current.focus();
    },
  }));
  return <input ref={realInputRef} />;
};

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>Focus the input</button>
    </>
  );
}
```

</Sandpack>

Ở đây, `realInputRef` bên trong `MyInput` giữ DOM node input thực tế. Tuy nhiên, [`useImperativeHandle`](/reference/react/useImperativeHandle) chỉ dẫn React cung cấp đối tượng đặc biệt của riêng bạn như là giá trị của ref cho component cha. Vì vậy `inputRef.current` bên trong component `Form` sẽ chỉ có phương thức `focus`. Trong trường hợp này, "handle" ref không phải là DOM node, mà là đối tượng tùy chỉnh bạn tạo bên trong lời gọi [`useImperativeHandle`](/reference/react/useImperativeHandle).

</DeepDive>

## Khi nào React gắn refs {/*when-react-attaches-the-refs*/}

Trong React, mỗi lần cập nhật được chia thành [hai giai đoạn](/learn/render-and-commit#step-3-react-commits-changes-to-the-dom):

* Trong **render,** React gọi các component của bạn để tìm hiểu những gì nên ở trên màn hình.
* Trong **commit,** React áp dụng các thay đổi vào DOM.

Nói chung, bạn [không muốn](/learn/referencing-values-with-refs#best-practices-for-refs) truy cập refs trong quá trình render. Điều đó cũng áp dụng cho refs giữ DOM nodes. Trong lần render đầu tiên, các DOM node chưa được tạo ra, nên `ref.current` sẽ là `null`. Và trong quá trình render các bản cập nhật, các DOM node chưa được cập nhật. Vì vậy, còn quá sớm để đọc chúng.

React thiết lập `ref.current` trong quá trình commit. Trước khi cập nhật DOM, React thiết lập các giá trị `ref.current` bị ảnh hưởng thành `null`. Sau khi cập nhật DOM, React ngay lập tức thiết lập chúng thành các DOM node tương ứng.

**Thông thường, bạn sẽ truy cập refs từ event handlers.** Nếu bạn muốn làm gì đó với ref, nhưng không có sự kiện cụ thể nào để làm điều đó, bạn có thể cần Effect. Chúng ta sẽ thảo luận về Effects ở các trang tiếp theo.

<DeepDive>

#### Flush state updates đồng bộ với flushSync {/*flushing-state-updates-synchronously-with-flush-sync*/}

Xem xét code như thế này, thêm một todo mới và cuộn màn hình xuống con cuối cùng của danh sách. Lưu ý cách, vì một lý do nào đó, nó luôn cuộn đến todo *ngay trước* todo được thêm cuối cùng:

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function TodoList() {
  const listRef = useRef(null);
  const [text, setText] = useState('');
  const [todos, setTodos] = useState(
    initialTodos
  );

  function handleAdd() {
    const newTodo = { id: nextId++, text: text };
    setText('');
    setTodos([ ...todos, newTodo]);
    listRef.current.lastChild.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest'
    });
  }

  return (
    <>
      <button onClick={handleAdd}>
        Add
      </button>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <ul ref={listRef}>
        {todos.map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </>
  );
}

let nextId = 0;
let initialTodos = [];
for (let i = 0; i < 20; i++) {
  initialTodos.push({
    id: nextId++,
    text: 'Todo #' + (i + 1)
  });
}
```

</Sandpack>

Vấn đề nằm ở hai dòng này:

```js
setTodos([ ...todos, newTodo]);
listRef.current.lastChild.scrollIntoView();
```

Trong React, [các bản cập nhật state được đặt vào hàng đợi.](/learn/queueing-a-series-of-state-updates) Thông thường, đây là điều bạn muốn. Tuy nhiên, ở đây nó gây ra vấn đề vì `setTodos` không cập nhật DOM ngay lập tức. Vì vậy khi bạn cuộn danh sách đến phần tử cuối cùng của nó, todo chưa được thêm vào. Đây là lý do tại sao cuộn luôn "bị trễ" một mục.

Để khắc phục vấn đề này, bạn có thể buộc React cập nhật ("flush") DOM đồng bộ. Để làm điều này, import `flushSync` từ `react-dom` và **bọc bản cập nhật state** vào lời gọi `flushSync`:

```js
flushSync(() => {
  setTodos([ ...todos, newTodo]);
});
listRef.current.lastChild.scrollIntoView();
```

Điều này sẽ hướng dẫn React cập nhật DOM đồng bộ ngay sau khi code được bọc trong `flushSync` thực thi. Kết quả là, todo cuối cùng sẽ đã ở trong DOM vào thời điểm bạn cố cuộn đến nó:

<Sandpack>

```js
import { useState, useRef } from 'react';
import { flushSync } from 'react-dom';

export default function TodoList() {
  const listRef = useRef(null);
  const [text, setText] = useState('');
  const [todos, setTodos] = useState(
    initialTodos
  );

  function handleAdd() {
    const newTodo = { id: nextId++, text: text };
    flushSync(() => {
      setText('');
      setTodos([ ...todos, newTodo]);
    });
    listRef.current.lastChild.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest'
    });
  }

  return (
    <>
      <button onClick={handleAdd}>
        Add
      </button>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <ul ref={listRef}>
        {todos.map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </>
  );
}

let nextId = 0;
let initialTodos = [];
for (let i = 0; i < 20; i++) {
  initialTodos.push({
    id: nextId++,
    text: 'Todo #' + (i + 1)
  });
}
```

</Sandpack>

</DeepDive>

## Các thực hành tốt nhất cho thao tác DOM với refs {/*best-practices-for-dom-manipulation-with-refs*/}

Refs là lối thoát. Bạn chỉ nên dùng chúng khi bạn phải "bước ra ngoài React". Các ví dụ phổ biến bao gồm quản lý focus, vị trí cuộn, hoặc gọi các browser APIs mà React không expose.

Nếu bạn tuân thủ các hành động không phá hủy như focus và cuộn, bạn sẽ không gặp vấn đề gì. Tuy nhiên, nếu bạn cố **sửa đổi** DOM theo cách thủ công, bạn có thể gặp rủi ro xung đột với những thay đổi mà React đang thực hiện.

Để minh họa vấn đề này, ví dụ này bao gồm một thông báo chào mừng và hai nút. Nút đầu tiên chuyển đổi sự hiện diện của nó bằng cách sử dụng [conditional rendering](/learn/conditional-rendering) và [state](/learn/state-a-components-memory), như bạn thường làm trong React. Nút thứ hai sử dụng [`remove()` DOM API](https://developer.mozilla.org/en-US/docs/Web/API/Element/remove) để bắt buộc xóa nó khỏi DOM bên ngoài tầm kiểm soát của React.

Thử nhấn "Toggle with setState" vài lần. Thông báo sẽ biến mất và xuất hiện lại. Sau đó nhấn "Remove from the DOM". Điều này sẽ bắt buộc xóa nó. Cuối cùng, nhấn "Toggle with setState":

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function Counter() {
  const [show, setShow] = useState(true);
  const ref = useRef(null);

  return (
    <div>
      <button
        onClick={() => {
          setShow(!show);
        }}>
        Toggle with setState
      </button>
      <button
        onClick={() => {
          ref.current.remove();
        }}>
        Remove from the DOM
      </button>
      {show && <p ref={ref}>Hello world</p>}
    </div>
  );
}
```

```css
p,
button {
  display: block;
  margin: 10px;
}
```

</Sandpack>

Sau khi bạn đã xóa thủ công phần tử DOM, việc cố dùng `setState` để hiển thị lại nó sẽ dẫn đến sự cố. Điều này vì bạn đã thay đổi DOM, và React không biết cách tiếp tục quản lý nó một cách chính xác.

**Tránh thay đổi các DOM node được quản lý bởi React.** Sửa đổi, thêm con, hoặc xóa con từ các phần tử được quản lý bởi React có thể dẫn đến kết quả trực quan không nhất quán hoặc sự cố như trên.

Tuy nhiên, điều này không có nghĩa là bạn hoàn toàn không thể làm điều đó. Nó đòi hỏi sự cẩn thận. **Bạn có thể an toàn sửa đổi các phần của DOM mà React _không có lý do_ để cập nhật.** Ví dụ, nếu một số `<div>` luôn rỗng trong JSX, React sẽ không có lý do để chạm vào danh sách con của nó. Do đó, an toàn để thêm hoặc xóa các phần tử ở đó một cách thủ công.

<Recap>

- Refs là khái niệm chung, nhưng thường bạn sẽ dùng chúng để giữ các phần tử DOM.
- Bạn hướng dẫn React đặt DOM node vào `myRef.current` bằng cách truyền `<div ref={myRef}>`.
- Thông thường, bạn sẽ dùng refs cho các hành động không phá hủy như focus, cuộn, hoặc đo lường các phần tử DOM.
- Theo mặc định, component không expose các DOM node của nó. Bạn có thể chọn expose DOM node bằng cách dùng prop `ref`.
- Tránh thay đổi các DOM node được quản lý bởi React.
- Nếu bạn sửa đổi các DOM node được quản lý bởi React, hãy sửa đổi các phần mà React không có lý do để cập nhật.

</Recap>



<Challenges>

#### Phát và dừng video {/*play-and-pause-the-video*/}

Trong ví dụ này, nút chuyển đổi biến state để chuyển đổi giữa trạng thái đang phát và tạm dừng. Tuy nhiên, để thực sự phát hoặc tạm dừng video, việc chuyển đổi state là chưa đủ. Bạn cũng cần gọi [`play()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/play) và [`pause()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/pause) trên phần tử DOM cho `<video>`. Thêm ref vào nó, và làm cho nút hoạt động.

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function VideoPlayer() {
  const [isPlaying, setIsPlaying] = useState(false);

  function handleClick() {
    const nextIsPlaying = !isPlaying;
    setIsPlaying(nextIsPlaying);
  }

  return (
    <>
      <button onClick={handleClick}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <video width="250">
        <source
          src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
          type="video/mp4"
        />
      </video>
    </>
  )
}
```

```css
button { display: block; margin-bottom: 20px; }
```

</Sandpack>

Để có thêm thách thức, hãy giữ nút "Play" đồng bộ với việc video có đang phát hay không, ngay cả khi người dùng nhấp chuột phải vào video và phát bằng cách sử dụng controls media tích hợp của browser. Bạn có thể muốn lắng nghe `onPlay` và `onPause` trên video để làm điều đó.

<Solution>

Khai báo ref và đặt nó trên phần tử `<video>`. Sau đó gọi `ref.current.play()` và `ref.current.pause()` trong event handler tùy thuộc vào state tiếp theo.

<Sandpack>

```js
import { useState, useRef } from 'react';

export default function VideoPlayer() {
  const [isPlaying, setIsPlaying] = useState(false);
  const ref = useRef(null);

  function handleClick() {
    const nextIsPlaying = !isPlaying;
    setIsPlaying(nextIsPlaying);

    if (nextIsPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  }

  return (
    <>
      <button onClick={handleClick}>
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      <video
        width="250"
        ref={ref}
        onPlay={() => setIsPlaying(true)}
        onPause={() => setIsPlaying(false)}
      >
        <source
          src="https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4"
          type="video/mp4"
        />
      </video>
    </>
  )
}
```

```css
button { display: block; margin-bottom: 20px; }
```

</Sandpack>

Để xử lý các controls tích hợp sẵn của browser, bạn có thể thêm các handler `onPlay` và `onPause` vào phần tử `<video>` và gọi `setIsPlaying` từ chúng. Bằng cách này, nếu người dùng phát video bằng controls của browser, state sẽ điều chỉnh tương ứng.

</Solution>

#### Focus vào trường tìm kiếm {/*focus-the-search-field*/}

Làm cho việc nhấp vào nút "Search" đặt focus vào trường.

<Sandpack>

```js
export default function Page() {
  return (
    <>
      <nav>
        <button>Search</button>
      </nav>
      <input
        placeholder="Looking for something?"
      />
    </>
  );
}
```

```css
button { display: block; margin-bottom: 10px; }
```

</Sandpack>

<Solution>

Thêm ref vào input, và gọi `focus()` trên DOM node để focus nó:

<Sandpack>

```js
import { useRef } from 'react';

export default function Page() {
  const inputRef = useRef(null);
  return (
    <>
      <nav>
        <button onClick={() => {
          inputRef.current.focus();
        }}>
          Search
        </button>
      </nav>
      <input
        ref={inputRef}
        placeholder="Looking for something?"
      />
    </>
  );
}
```

```css
button { display: block; margin-bottom: 10px; }
```

</Sandpack>

</Solution>

#### Cuộn carousel hình ảnh {/*scrolling-an-image-carousel*/}

Carousel hình ảnh này có nút "Next" chuyển đổi hình ảnh đang hoạt động. Làm cho gallery cuộn ngang đến hình ảnh đang hoạt động khi nhấp. Bạn sẽ muốn gọi [`scrollIntoView()`](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView) trên DOM node của hình ảnh đang hoạt động:

```js
node.scrollIntoView({
  behavior: 'smooth',
  block: 'nearest',
  inline: 'center'
});
```

<Hint>

Bạn không cần có ref cho mỗi hình ảnh cho bài tập này. Sẽ đủ nếu có ref cho hình ảnh đang hoạt động hiện tại, hoặc cho chính danh sách. Dùng `flushSync` để đảm bảo DOM được cập nhật *trước* khi bạn cuộn.

</Hint>

<Sandpack>

```js
import { useState } from 'react';

export default function CatFriends() {
  const [index, setIndex] = useState(0);
  return (
    <>
      <nav>
        <button onClick={() => {
          if (index < catList.length - 1) {
            setIndex(index + 1);
          } else {
            setIndex(0);
          }
        }}>
          Next
        </button>
      </nav>
      <div>
        <ul>
          {catList.map((cat, i) => (
            <li key={cat.id}>
              <img
                className={
                  index === i ?
                    'active' :
                    ''
                }
                src={cat.imageUrl}
                alt={'Cat #' + cat.id}
              />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

const catCount = 10;
const catList = new Array(catCount);
for (let i = 0; i < catCount; i++) {
  const bucket = Math.floor(Math.random() * catCount) % 2;
  let imageUrl = '';
  switch (bucket) {
    case 0: {
      imageUrl = "https://placecats.com/neo/250/200";
      break;
    }
    case 1: {
      imageUrl = "https://placecats.com/millie/250/200";
      break;
    }
    case 2:
    default: {
      imageUrl = "https://placecats.com/bella/250/200";
      break;
    }
  }
  catList[i] = {
    id: i,
    imageUrl,
  };
}

```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}

img {
  padding: 10px;
  margin: -10px;
  transition: background 0.2s linear;
}

.active {
  background: rgba(0, 100, 150, 0.4);
}
```

</Sandpack>

<Solution>

Bạn có thể khai báo một `selectedRef`, và sau đó truyền nó có điều kiện chỉ cho hình ảnh hiện tại:

```js
<li ref={index === i ? selectedRef : null}>
```

Khi `index === i`, có nghĩa là hình ảnh đang được chọn, `<li>` sẽ nhận `selectedRef`. React sẽ đảm bảo rằng `selectedRef.current` luôn trỏ đến DOM node chính xác.

Lưu ý rằng lời gọi `flushSync` là cần thiết để buộc React cập nhật DOM trước khi cuộn. Nếu không, `selectedRef.current` sẽ luôn trỏ đến mục đã được chọn trước đó.

<Sandpack>

```js
import { useRef, useState } from 'react';
import { flushSync } from 'react-dom';

export default function CatFriends() {
  const selectedRef = useRef(null);
  const [index, setIndex] = useState(0);

  return (
    <>
      <nav>
        <button onClick={() => {
          flushSync(() => {
            if (index < catList.length - 1) {
              setIndex(index + 1);
            } else {
              setIndex(0);
            }
          });
          selectedRef.current.scrollIntoView({
            behavior: 'smooth',
            block: 'nearest',
            inline: 'center'
          });
        }}>
          Next
        </button>
      </nav>
      <div>
        <ul>
          {catList.map((cat, i) => (
            <li
              key={cat.id}
              ref={index === i ?
                selectedRef :
                null
              }
            >
              <img
                className={
                  index === i ?
                    'active'
                    : ''
                }
                src={cat.imageUrl}
                alt={'Cat #' + cat.id}
              />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

const catCount = 10;
const catList = new Array(catCount);
for (let i = 0; i < catCount; i++) {
  const bucket = Math.floor(Math.random() * catCount) % 2;
  let imageUrl = '';
  switch (bucket) {
    case 0: {
      imageUrl = "https://placecats.com/neo/250/200";
      break;
    }
    case 1: {
      imageUrl = "https://placecats.com/millie/250/200";
      break;
    }
    case 2:
    default: {
      imageUrl = "https://placecats.com/bella/250/200";
      break;
    }
  }
  catList[i] = {
    id: i,
    imageUrl,
  };
}

```

```css
div {
  width: 100%;
  overflow: hidden;
}

nav {
  text-align: center;
}

button {
  margin: .25rem;
}

ul,
li {
  list-style: none;
  white-space: nowrap;
}

li {
  display: inline;
  padding: 0.5rem;
}

img {
  padding: 10px;
  margin: -10px;
  transition: background 0.2s linear;
}

.active {
  background: rgba(0, 100, 150, 0.4);
}
```

</Sandpack>

</Solution>

#### Focus vào trường tìm kiếm với các component riêng biệt {/*focus-the-search-field-with-separate-components*/}

Làm cho việc nhấp vào nút "Search" đặt focus vào trường. Lưu ý rằng mỗi component được định nghĩa trong một file riêng biệt và không nên được di chuyển ra ngoài nó. Làm thế nào bạn kết nối chúng lại với nhau?

<Hint>

Bạn sẽ cần truyền `ref` như một prop để tham gia vào việc expose DOM node từ component của riêng bạn như `SearchInput`.

</Hint>

<Sandpack>

```js src/App.js
import SearchButton from './SearchButton.js';
import SearchInput from './SearchInput.js';

export default function Page() {
  return (
    <>
      <nav>
        <SearchButton />
      </nav>
      <SearchInput />
    </>
  );
}
```

```js src/SearchButton.js
export default function SearchButton() {
  return (
    <button>
      Search
    </button>
  );
}
```

```js src/SearchInput.js
export default function SearchInput() {
  return (
    <input
      placeholder="Looking for something?"
    />
  );
}
```

```css
button { display: block; margin-bottom: 10px; }
```

</Sandpack>

<Solution>

Bạn sẽ cần thêm prop `onClick` vào `SearchButton`, và làm cho `SearchButton` truyền nó xuống `<button>` của browser. Bạn cũng sẽ truyền ref xuống `<SearchInput>`, sẽ chuyển tiếp nó đến `<input>` thực sự và điền vào nó. Cuối cùng, trong click handler, bạn sẽ gọi `focus` trên DOM node được lưu trữ bên trong ref đó.

<Sandpack>

```js src/App.js
import { useRef } from 'react';
import SearchButton from './SearchButton.js';
import SearchInput from './SearchInput.js';

export default function Page() {
  const inputRef = useRef(null);
  return (
    <>
      <nav>
        <SearchButton onClick={() => {
          inputRef.current.focus();
        }} />
      </nav>
      <SearchInput ref={inputRef} />
    </>
  );
}
```

```js src/SearchButton.js
export default function SearchButton({ onClick }) {
  return (
    <button onClick={onClick}>
      Search
    </button>
  );
}
```

```js src/SearchInput.js
export default function SearchInput({ ref }) {
  return (
    <input
      ref={ref}
      placeholder="Looking for something?"
    />
  );
}
```

```css
button { display: block; margin-bottom: 10px; }
```

</Sandpack>

</Solution>

</Challenges>
