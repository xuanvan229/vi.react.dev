---
title: Bảo Toàn và Đặt Lại State
---

<Intro>

State được cô lập giữa các component. React theo dõi state nào thuộc về component nào dựa trên vị trí của chúng trong cây UI. Bạn có thể kiểm soát thời điểm bảo toàn state và khi nào đặt lại nó giữa các lần render.

</Intro>

<YouWillLearn>

* Khi nào React chọn bảo toàn hoặc đặt lại state
* Cách buộc React đặt lại state của component
* Cách key và type ảnh hưởng đến việc state có được bảo toàn hay không

</YouWillLearn>

## State được gắn với một vị trí trong cây render {/*state-is-tied-to-a-position-in-the-tree*/}

React xây dựng [cây render](learn/understanding-your-ui-as-a-tree#the-render-tree) cho cấu trúc component trong UI của bạn.

Khi bạn gán state cho một component, bạn có thể nghĩ rằng state "sống" bên trong component đó. Nhưng thực ra state được lưu trữ bên trong React. React liên kết từng phần state mà nó đang giữ với đúng component dựa trên vị trí của component đó trong cây render.

Ở đây, chỉ có một thẻ JSX `<Counter />`, nhưng nó được render ở hai vị trí khác nhau:

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  const counter = <Counter />;
  return (
    <div>
      {counter}
      {counter}
    </div>
  );
}

function Counter() {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
label {
  display: block;
  clear: both;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

Đây là cách chúng trông giống như một cây:

<DiagramGroup>

<Diagram name="preserving_state_tree" height={248} width={395} alt="Diagram of a tree of React components. The root node is labeled 'div' and has two children. Each of the children are labeled 'Counter' and both contain a state bubble labeled 'count' with value 0.">

Cây React

</Diagram>

</DiagramGroup>

**Đây là hai counter riêng biệt vì mỗi cái được render ở vị trí riêng của nó trong cây.** Thường bạn không cần phải nghĩ đến những vị trí này khi sử dụng React, nhưng hiểu cách hoạt động của nó có thể hữu ích.

Trong React, mỗi component trên màn hình có state hoàn toàn cô lập. Ví dụ, nếu bạn render hai component `Counter` cạnh nhau, mỗi cái sẽ có state `score` và `hover` độc lập của riêng mình.

Hãy thử click vào cả hai counter và nhận thấy chúng không ảnh hưởng lẫn nhau:

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  return (
    <div>
      <Counter />
      <Counter />
    </div>
  );
}

function Counter() {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

Như bạn có thể thấy, khi một counter được cập nhật, chỉ state của component đó được cập nhật:


<DiagramGroup>

<Diagram name="preserving_state_increment" height={248} width={441} alt="Diagram of a tree of React components. The root node is labeled 'div' and has two children. The left child is labeled 'Counter' and contains a state bubble labeled 'count' with value 0. The right child is labeled 'Counter' and contains a state bubble labeled 'count' with value 1. The state bubble of the right child is highlighted in yellow to indicate its value has updated.">

Cập nhật state

</Diagram>

</DiagramGroup>


React sẽ giữ state miễn là bạn render cùng một component ở cùng vị trí trong cây. Để thấy điều này, hãy tăng cả hai counter, sau đó xóa component thứ hai bằng cách bỏ tick ô "Render the second counter", rồi thêm lại bằng cách tick lại:

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  const [showB, setShowB] = useState(true);
  return (
    <div>
      <Counter />
      {showB && <Counter />}
      <label>
        <input
          type="checkbox"
          checked={showB}
          onChange={e => {
            setShowB(e.target.checked)
          }}
        />
        Render the second counter
      </label>
    </div>
  );
}

function Counter() {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
label {
  display: block;
  clear: both;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

Lưu ý cách ngay khi bạn dừng render counter thứ hai, state của nó biến mất hoàn toàn. Điều đó là vì khi React xóa một component, nó hủy state của nó.

<DiagramGroup>

<Diagram name="preserving_state_remove_component" height={253} width={422} alt="Diagram of a tree of React components. The root node is labeled 'div' and has two children. The left child is labeled 'Counter' and contains a state bubble labeled 'count' with value 0. The right child is missing, and in its place is a yellow 'poof' image, highlighting the component being deleted from the tree.">

Xóa một component

</Diagram>

</DiagramGroup>

Khi bạn tick "Render the second counter", một `Counter` thứ hai và state của nó được khởi tạo từ đầu (`score = 0`) và được thêm vào DOM.

<DiagramGroup>

<Diagram name="preserving_state_add_component" height={258} width={500} alt="Diagram of a tree of React components. The root node is labeled 'div' and has two children. The left child is labeled 'Counter' and contains a state bubble labeled 'count' with value 0. The right child is labeled 'Counter' and contains a state bubble labeled 'count' with value 0. The entire right child node is highlighted in yellow, indicating that it was just added to the tree.">

Thêm một component

</Diagram>

</DiagramGroup>

**React bảo toàn state của một component miễn là nó được render ở vị trí của nó trong cây UI.** Nếu nó bị xóa, hoặc một component khác được render ở cùng vị trí, React sẽ loại bỏ state của nó.

## Cùng component ở cùng vị trí bảo toàn state {/*same-component-at-the-same-position-preserves-state*/}

Trong ví dụ này, có hai thẻ `<Counter />` khác nhau:

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  const [isFancy, setIsFancy] = useState(false);
  return (
    <div>
      {isFancy ? (
        <Counter isFancy={true} />
      ) : (
        <Counter isFancy={false} />
      )}
      <label>
        <input
          type="checkbox"
          checked={isFancy}
          onChange={e => {
            setIsFancy(e.target.checked)
          }}
        />
        Use fancy styling
      </label>
    </div>
  );
}

function Counter({ isFancy }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }
  if (isFancy) {
    className += ' fancy';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
label {
  display: block;
  clear: both;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.fancy {
  border: 5px solid gold;
  color: #ff6767;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

Khi bạn tick hoặc bỏ tick ô checkbox, state của counter không bị reset. Dù `isFancy` là `true` hay `false`, bạn luôn có một `<Counter />` là con đầu tiên của `div` được trả về từ component gốc `App`:

<DiagramGroup>

<Diagram name="preserving_state_same_component" height={461} width={600} alt="Diagram with two sections separated by an arrow transitioning between them. Each section contains a layout of components with a parent labeled 'App' containing a state bubble labeled isFancy. This component has one child labeled 'div', which leads to a prop bubble containing isFancy (highlighted in purple) passed down to the only child. The last child is labeled 'Counter' and contains a state bubble with label 'count' and value 3 in both diagrams. In the left section of the diagram, nothing is highlighted and the isFancy parent state value is false. In the right section of the diagram, the isFancy parent state value has changed to true and it is highlighted in yellow, and so is the props bubble below, which has also changed its isFancy value to true.">

Cập nhật state `App` không reset `Counter` vì `Counter` vẫn ở cùng vị trí

</Diagram>

</DiagramGroup>


Đó là cùng một component ở cùng vị trí, vì vậy từ góc nhìn của React, đó là cùng một counter.

<Pitfall>

Hãy nhớ rằng **vị trí trong cây UI—không phải trong đánh dấu JSX—mới là điều quan trọng với React!** Component này có hai mệnh đề `return` với các thẻ JSX `<Counter />` khác nhau bên trong và bên ngoài `if`:

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  const [isFancy, setIsFancy] = useState(false);
  if (isFancy) {
    return (
      <div>
        <Counter isFancy={true} />
        <label>
          <input
            type="checkbox"
            checked={isFancy}
            onChange={e => {
              setIsFancy(e.target.checked)
            }}
          />
          Use fancy styling
        </label>
      </div>
    );
  }
  return (
    <div>
      <Counter isFancy={false} />
      <label>
        <input
          type="checkbox"
          checked={isFancy}
          onChange={e => {
            setIsFancy(e.target.checked)
          }}
        />
        Use fancy styling
      </label>
    </div>
  );
}

function Counter({ isFancy }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }
  if (isFancy) {
    className += ' fancy';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
label {
  display: block;
  clear: both;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.fancy {
  border: 5px solid gold;
  color: #ff6767;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

Bạn có thể mong đợi state sẽ reset khi bạn tick checkbox, nhưng nó không! Điều này là vì **cả hai thẻ `<Counter />` này được render ở cùng vị trí.** React không biết bạn đặt điều kiện ở đâu trong hàm của bạn. Tất cả những gì nó "nhìn thấy" là cây mà bạn trả về.

Trong cả hai trường hợp, component `App` trả về một `<div>` với `<Counter />` là con đầu tiên. Đối với React, hai counter này có cùng "địa chỉ": con đầu tiên của con đầu tiên của root. Đây là cách React khớp chúng giữa các lần render trước và sau, bất kể bạn cấu trúc logic như thế nào.

</Pitfall>

## Component khác ở cùng vị trí đặt lại state {/*different-components-at-the-same-position-reset-state*/}

Trong ví dụ này, việc tick checkbox sẽ thay thế `<Counter>` bằng một `<p>`:

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  const [isPaused, setIsPaused] = useState(false);
  return (
    <div>
      {isPaused ? (
        <p>See you later!</p>
      ) : (
        <Counter />
      )}
      <label>
        <input
          type="checkbox"
          checked={isPaused}
          onChange={e => {
            setIsPaused(e.target.checked)
          }}
        />
        Take a break
      </label>
    </div>
  );
}

function Counter() {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
label {
  display: block;
  clear: both;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

Ở đây, bạn chuyển đổi giữa các loại component _khác nhau_ ở cùng vị trí. Ban đầu, con đầu tiên của `<div>` chứa một `Counter`. Nhưng khi bạn đặt vào một `p`, React đã xóa `Counter` khỏi cây UI và hủy state của nó.

<DiagramGroup>

<Diagram name="preserving_state_diff_pt1" height={290} width={753} alt="Diagram with three sections, with an arrow transitioning each section in between. The first section contains a React component labeled 'div' with a single child labeled 'Counter' containing a state bubble labeled 'count' with value 3. The middle section has the same 'div' parent, but the child component has now been deleted, indicated by a yellow 'proof' image. The third section has the same 'div' parent again, now with a new child labeled 'p', highlighted in yellow.">

Khi `Counter` thay đổi thành `p`, `Counter` bị xóa và `p` được thêm vào

</Diagram>

</DiagramGroup>

<DiagramGroup>

<Diagram name="preserving_state_diff_pt2" height={290} width={753} alt="Diagram with three sections, with an arrow transitioning each section in between. The first section contains a React component labeled 'p'. The middle section has the same 'div' parent, but the child component has now been deleted, indicated by a yellow 'proof' image. The third section has the same 'div' parent again, now with a new child labeled 'Counter' containing a state bubble labeled 'count' with value 0, highlighted in yellow.">

Khi chuyển lại, `p` bị xóa và `Counter` được thêm vào

</Diagram>

</DiagramGroup>

Ngoài ra, **khi bạn render một component khác ở cùng vị trí, nó đặt lại state của toàn bộ cây con của nó.** Để xem cách hoạt động, hãy tăng counter rồi tick checkbox:

<Sandpack>

```js
import { useState } from 'react';

export default function App() {
  const [isFancy, setIsFancy] = useState(false);
  return (
    <div>
      {isFancy ? (
        <div>
          <Counter isFancy={true} />
        </div>
      ) : (
        <section>
          <Counter isFancy={false} />
        </section>
      )}
      <label>
        <input
          type="checkbox"
          checked={isFancy}
          onChange={e => {
            setIsFancy(e.target.checked)
          }}
        />
        Use fancy styling
      </label>
    </div>
  );
}

function Counter({ isFancy }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }
  if (isFancy) {
    className += ' fancy';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
label {
  display: block;
  clear: both;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
  float: left;
}

.fancy {
  border: 5px solid gold;
  color: #ff6767;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

State của counter bị reset khi bạn click checkbox. Mặc dù bạn render một `Counter`, nhưng con đầu tiên của `div` thay đổi từ `section` sang `div`. Khi `section` con bị xóa khỏi DOM, toàn bộ cây bên dưới nó (bao gồm `Counter` và state của nó) cũng bị hủy.

<DiagramGroup>

<Diagram name="preserving_state_diff_same_pt1" height={350} width={794} alt="Diagram with three sections, with an arrow transitioning each section in between. The first section contains a React component labeled 'div' with a single child labeled 'section', which has a single child labeled 'Counter' containing a state bubble labeled 'count' with value 3. The middle section has the same 'div' parent, but the child components have now been deleted, indicated by a yellow 'proof' image. The third section has the same 'div' parent again, now with a new child labeled 'div', highlighted in yellow, also with a new child labeled 'Counter' containing a state bubble labeled 'count' with value 0, all highlighted in yellow.">

Khi `section` thay đổi thành `div`, `section` bị xóa và `div` mới được thêm vào

</Diagram>

</DiagramGroup>

<DiagramGroup>

<Diagram name="preserving_state_diff_same_pt2" height={350} width={794} alt="Diagram with three sections, with an arrow transitioning each section in between. The first section contains a React component labeled 'div' with a single child labeled 'div', which has a single child labeled 'Counter' containing a state bubble labeled 'count' with value 0. The middle section has the same 'div' parent, but the child components have now been deleted, indicated by a yellow 'proof' image. The third section has the same 'div' parent again, now with a new child labeled 'section', highlighted in yellow, also with a new child labeled 'Counter' containing a state bubble labeled 'count' with value 0, all highlighted in yellow.">

Khi chuyển lại, `div` bị xóa và `section` mới được thêm vào

</Diagram>

</DiagramGroup>

Theo nguyên tắc chung, **nếu bạn muốn bảo toàn state giữa các lần render, cấu trúc của cây cần phải "khớp"** từ lần render này sang lần render khác. Nếu cấu trúc khác nhau, state sẽ bị hủy vì React hủy state khi nó xóa một component khỏi cây.

<Pitfall>

Đây là lý do tại sao bạn không nên lồng định nghĩa hàm component.

Ở đây, hàm component `MyTextField` được định nghĩa *bên trong* `MyComponent`:

<Sandpack>

```js {expectedErrors: {'react-compiler': [7]}}
import { useState } from 'react';

export default function MyComponent() {
  const [counter, setCounter] = useState(0);

  function MyTextField() {
    const [text, setText] = useState('');

    return (
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
    );
  }

  return (
    <>
      <MyTextField />
      <button onClick={() => {
        setCounter(counter + 1)
      }}>Clicked {counter} times</button>
    </>
  );
}
```

</Sandpack>


Mỗi lần bạn click button, state của input biến mất! Điều này là vì một hàm `MyTextField` *khác* được tạo ra cho mỗi lần render của `MyComponent`. Bạn đang render một component *khác* ở cùng vị trí, vì vậy React đặt lại tất cả state bên dưới. Điều này dẫn đến bugs và vấn đề hiệu suất. Để tránh vấn đề này, **luôn khai báo các hàm component ở cấp độ cao nhất, và đừng lồng định nghĩa của chúng.**

</Pitfall>

## Đặt lại state ở cùng vị trí {/*resetting-state-at-the-same-position*/}

Theo mặc định, React bảo toàn state của một component khi nó vẫn ở cùng vị trí. Thường thì đây chính xác là những gì bạn muốn, vì vậy nó có ý nghĩa là hành vi mặc định. Nhưng đôi khi, bạn có thể muốn đặt lại state của component. Hãy xem xét ứng dụng này cho phép hai người chơi theo dõi điểm của họ trong mỗi lượt:

<Sandpack>

```js
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA ? (
        <Counter person="Taylor" />
      ) : (
        <Counter person="Sarah" />
      )}
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}

function Counter({ person }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{person}'s score: {score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
h1 {
  font-size: 18px;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

Hiện tại, khi bạn thay đổi người chơi, điểm số được bảo toàn. Hai `Counter` xuất hiện ở cùng vị trí, vì vậy React coi chúng là *cùng một* `Counter` mà prop `person` đã thay đổi.

Nhưng về mặt khái niệm, trong ứng dụng này chúng phải là hai counter riêng biệt. Chúng có thể xuất hiện ở cùng chỗ trong UI, nhưng một cái là counter cho Taylor, và cái kia là counter cho Sarah.

Có hai cách để đặt lại state khi chuyển đổi giữa chúng:

1. Render các component ở các vị trí khác nhau
2. Gán mỗi component một danh tính rõ ràng với `key`


### Tùy chọn 1: Render một component ở các vị trí khác nhau {/*option-1-rendering-a-component-in-different-positions*/}

Nếu bạn muốn hai `Counter` này độc lập, bạn có thể render chúng ở hai vị trí khác nhau:

<Sandpack>

```js
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA &&
        <Counter person="Taylor" />
      }
      {!isPlayerA &&
        <Counter person="Sarah" />
      }
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}

function Counter({ person }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{person}'s score: {score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
h1 {
  font-size: 18px;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

* Ban đầu, `isPlayerA` là `true`. Vì vậy vị trí đầu tiên chứa state `Counter`, và vị trí thứ hai trống.
* Khi bạn click nút "Next player", vị trí đầu tiên xóa nhưng vị trí thứ hai bây giờ chứa một `Counter`.

<DiagramGroup>

<Diagram name="preserving_state_diff_position_p1" height={375} width={504} alt="Diagram with a tree of React components. The parent is labeled 'Scoreboard' with a state bubble labeled isPlayerA with value 'true'. The only child, arranged to the left, is labeled Counter with a state bubble labeled 'count' and value 0. All of the left child is highlighted in yellow, indicating it was added.">

Trạng thái ban đầu

</Diagram>

<Diagram name="preserving_state_diff_position_p2" height={375} width={504} alt="Diagram with a tree of React components. The parent is labeled 'Scoreboard' with a state bubble labeled isPlayerA with value 'false'. The state bubble is highlighted in yellow, indicating that it has changed. The left child is replaced with a yellow 'poof' image indicating that it has been deleted and there is a new child on the right, highlighted in yellow indicating that it was added. The new child is labeled 'Counter' and contains a state bubble labeled 'count' with value 0.">

Click "next"

</Diagram>

<Diagram name="preserving_state_diff_position_p3" height={375} width={504} alt="Diagram with a tree of React components. The parent is labeled 'Scoreboard' with a state bubble labeled isPlayerA with value 'true'. The state bubble is highlighted in yellow, indicating that it has changed. There is a new child on the left, highlighted in yellow indicating that it was added. The new child is labeled 'Counter' and contains a state bubble labeled 'count' with value 0. The right child is replaced with a yellow 'poof' image indicating that it has been deleted.">

Click "next" lần nữa

</Diagram>

</DiagramGroup>

State của mỗi `Counter` bị hủy mỗi lần nó bị xóa khỏi DOM. Đây là lý do tại sao chúng reset mỗi lần bạn click button.

Giải pháp này tiện lợi khi bạn chỉ có một vài component độc lập được render ở cùng chỗ. Trong ví dụ này, bạn chỉ có hai, vì vậy không phiền gì khi render cả hai riêng biệt trong JSX.

### Tùy chọn 2: Đặt lại state với key {/*option-2-resetting-state-with-a-key*/}

Cũng có một cách khác, tổng quát hơn để đặt lại state của component.

Bạn có thể đã thấy `key` khi [render danh sách.](/learn/rendering-lists#keeping-list-items-in-order-with-key) Key không chỉ dành cho danh sách! Bạn có thể dùng key để làm cho React phân biệt giữa bất kỳ component nào. Theo mặc định, React sử dụng thứ tự trong parent ("counter đầu tiên", "counter thứ hai") để phân biệt giữa các component. Nhưng key cho phép bạn nói với React rằng đây không chỉ là counter *đầu tiên*, hay counter *thứ hai*, mà là một counter cụ thể—ví dụ, counter của *Taylor*. Bằng cách này, React sẽ biết counter của *Taylor* bất kể nó xuất hiện ở đâu trong cây!

Trong ví dụ này, hai `<Counter />` không chia sẻ state mặc dù chúng xuất hiện ở cùng chỗ trong JSX:

<Sandpack>

```js
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA ? (
        <Counter key="Taylor" person="Taylor" />
      ) : (
        <Counter key="Sarah" person="Sarah" />
      )}
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}

function Counter({ person }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{person}'s score: {score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

```css
h1 {
  font-size: 18px;
}

.counter {
  width: 100px;
  text-align: center;
  border: 1px solid gray;
  border-radius: 4px;
  padding: 20px;
  margin: 0 20px 20px 0;
}

.hover {
  background: #ffffd8;
}
```

</Sandpack>

Chuyển đổi giữa Taylor và Sarah không bảo toàn state. Điều này là vì **bạn đã gán cho chúng các `key` khác nhau:**

```js
{isPlayerA ? (
  <Counter key="Taylor" person="Taylor" />
) : (
  <Counter key="Sarah" person="Sarah" />
)}
```

Chỉ định một `key` nói với React rằng hãy sử dụng chính `key` đó như một phần của vị trí, thay vì thứ tự của chúng trong parent. Đây là lý do tại sao, mặc dù bạn render chúng ở cùng chỗ trong JSX, React coi chúng là hai counter khác nhau, và vì vậy chúng sẽ không bao giờ chia sẻ state. Mỗi lần một counter xuất hiện trên màn hình, state của nó được tạo ra. Mỗi lần nó bị xóa, state của nó bị hủy. Chuyển đổi giữa chúng đặt lại state của chúng lặp đi lặp lại.

<Note>

Hãy nhớ rằng key không phải là duy nhất toàn cục. Chúng chỉ xác định vị trí *trong parent*.

</Note>

### Đặt lại form với key {/*resetting-a-form-with-a-key*/}

Đặt lại state với key đặc biệt hữu ích khi làm việc với form.

Trong ứng dụng chat này, component `<Chat>` chứa state ô nhập liệu:

<Sandpack>

```js src/App.js
import { useState } from 'react';
import Chat from './Chat.js';
import ContactList from './ContactList.js';

export default function Messenger() {
  const [to, setTo] = useState(contacts[0]);
  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedContact={to}
        onSelect={contact => setTo(contact)}
      />
      <Chat contact={to} />
    </div>
  )
}

const contacts = [
  { id: 0, name: 'Taylor', email: 'taylor@mail.com' },
  { id: 1, name: 'Alice', email: 'alice@mail.com' },
  { id: 2, name: 'Bob', email: 'bob@mail.com' }
];
```

```js src/ContactList.js
export default function ContactList({
  selectedContact,
  contacts,
  onSelect
}) {
  return (
    <section className="contact-list">
      <ul>
        {contacts.map(contact =>
          <li key={contact.id}>
            <button onClick={() => {
              onSelect(contact);
            }}>
              {contact.name}
            </button>
          </li>
        )}
      </ul>
    </section>
  );
}
```

```js src/Chat.js
import { useState } from 'react';

export default function Chat({ contact }) {
  const [text, setText] = useState('');
  return (
    <section className="chat">
      <textarea
        value={text}
        placeholder={'Chat to ' + contact.name}
        onChange={e => setText(e.target.value)}
      />
      <br />
      <button>Send to {contact.email}</button>
    </section>
  );
}
```

```css
.chat, .contact-list {
  float: left;
  margin-bottom: 20px;
}
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li button {
  width: 100px;
  padding: 10px;
  margin-right: 10px;
}
textarea {
  height: 150px;
}
```

</Sandpack>

Hãy thử nhập gì đó vào ô nhập liệu, rồi nhấn "Alice" hoặc "Bob" để chọn người nhận khác. Bạn sẽ nhận thấy rằng state của ô nhập liệu được bảo toàn vì `<Chat>` được render ở cùng vị trí trong cây.

**Trong nhiều ứng dụng, đây có thể là hành vi mong muốn, nhưng không phải trong ứng dụng chat!** Bạn không muốn để người dùng gửi tin nhắn mà họ đã nhập cho người sai do vô tình click. Để sửa lỗi này, hãy thêm `key`:

```js
<Chat key={to.id} contact={to} />
```

Điều này đảm bảo rằng khi bạn chọn người nhận khác, component `Chat` sẽ được tạo lại từ đầu, bao gồm bất kỳ state nào trong cây bên dưới nó. React cũng sẽ tạo lại các phần tử DOM thay vì tái sử dụng chúng.

Bây giờ việc chuyển đổi người nhận luôn xóa ô nhập liệu:

<Sandpack>

```js src/App.js
import { useState } from 'react';
import Chat from './Chat.js';
import ContactList from './ContactList.js';

export default function Messenger() {
  const [to, setTo] = useState(contacts[0]);
  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedContact={to}
        onSelect={contact => setTo(contact)}
      />
      <Chat key={to.id} contact={to} />
    </div>
  )
}

const contacts = [
  { id: 0, name: 'Taylor', email: 'taylor@mail.com' },
  { id: 1, name: 'Alice', email: 'alice@mail.com' },
  { id: 2, name: 'Bob', email: 'bob@mail.com' }
];
```

```js src/ContactList.js
export default function ContactList({
  selectedContact,
  contacts,
  onSelect
}) {
  return (
    <section className="contact-list">
      <ul>
        {contacts.map(contact =>
          <li key={contact.id}>
            <button onClick={() => {
              onSelect(contact);
            }}>
              {contact.name}
            </button>
          </li>
        )}
      </ul>
    </section>
  );
}
```

```js src/Chat.js
import { useState } from 'react';

export default function Chat({ contact }) {
  const [text, setText] = useState('');
  return (
    <section className="chat">
      <textarea
        value={text}
        placeholder={'Chat to ' + contact.name}
        onChange={e => setText(e.target.value)}
      />
      <br />
      <button>Send to {contact.email}</button>
    </section>
  );
}
```

```css
.chat, .contact-list {
  float: left;
  margin-bottom: 20px;
}
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li button {
  width: 100px;
  padding: 10px;
  margin-right: 10px;
}
textarea {
  height: 150px;
}
```

</Sandpack>

<DeepDive>

#### Bảo toàn state cho các component đã bị xóa {/*preserving-state-for-removed-components*/}

Trong ứng dụng chat thực tế, bạn có thể muốn khôi phục state ô nhập liệu khi người dùng chọn lại người nhận trước đó. Có một vài cách để giữ state "còn sống" cho một component không còn hiển thị nữa:

- Bạn có thể render _tất cả_ các cuộc chat thay vì chỉ cái hiện tại, nhưng ẩn tất cả các cuộc chat khác bằng CSS. Các cuộc chat sẽ không bị xóa khỏi cây, vì vậy state cục bộ của chúng sẽ được bảo toàn. Giải pháp này hoạt động tốt cho các UI đơn giản. Nhưng nó có thể rất chậm nếu các cây ẩn lớn và chứa nhiều nút DOM.
- Bạn có thể [đẩy state lên](/learn/sharing-state-between-components) và giữ tin nhắn chờ cho mỗi người nhận trong component cha. Bằng cách này, khi các component con bị xóa, điều đó không quan trọng, vì chính parent giữ thông tin quan trọng. Đây là giải pháp phổ biến nhất.
- Bạn cũng có thể sử dụng một nguồn khác ngoài React state. Ví dụ, bạn có thể muốn bản nháp tin nhắn tồn tại ngay cả khi người dùng vô tình đóng trang. Để thực hiện điều này, bạn có thể cho component `Chat` khởi tạo state của nó bằng cách đọc từ [`localStorage`](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage), và lưu các bản nháp ở đó.

Dù bạn chọn chiến lược nào, cuộc chat _với Alice_ về mặt khái niệm là khác với cuộc chat _với Bob_, vì vậy việc gán `key` cho cây `<Chat>` dựa trên người nhận hiện tại là hợp lý.

</DeepDive>

<Recap>

- React giữ state miễn là cùng một component được render ở cùng vị trí.
- State không được giữ trong các thẻ JSX. Nó được liên kết với vị trí cây mà bạn đặt JSX đó.
- Bạn có thể buộc một cây con đặt lại state của nó bằng cách gán cho nó một key khác.
- Đừng lồng định nghĩa component, nếu không bạn sẽ đặt lại state một cách vô tình.

</Recap>



<Challenges>

#### Sửa lỗi văn bản input biến mất {/*fix-disappearing-input-text*/}

Ví dụ này hiển thị một tin nhắn khi bạn nhấn button. Tuy nhiên, nhấn button cũng vô tình đặt lại ô nhập liệu. Tại sao điều này xảy ra? Hãy sửa nó để nhấn button không đặt lại văn bản ô nhập liệu.

<Sandpack>

```js src/App.js
import { useState } from 'react';

export default function App() {
  const [showHint, setShowHint] = useState(false);
  if (showHint) {
    return (
      <div>
        <p><i>Hint: Your favorite city?</i></p>
        <Form />
        <button onClick={() => {
          setShowHint(false);
        }}>Hide hint</button>
      </div>
    );
  }
  return (
    <div>
      <Form />
      <button onClick={() => {
        setShowHint(true);
      }}>Show hint</button>
    </div>
  );
}

function Form() {
  const [text, setText] = useState('');
  return (
    <textarea
      value={text}
      onChange={e => setText(e.target.value)}
    />
  );
}
```

```css
textarea { display: block; margin: 10px 0; }
```

</Sandpack>

<Solution>

Vấn đề là `Form` được render ở các vị trí khác nhau. Trong nhánh `if`, nó là con thứ hai của `<div>`, nhưng trong nhánh `else`, nó là con đầu tiên. Do đó, loại component ở mỗi vị trí thay đổi. Vị trí đầu tiên thay đổi giữa việc giữ `p` và `Form`, trong khi vị trí thứ hai thay đổi giữa việc giữ `Form` và `button`. React đặt lại state mỗi khi loại component thay đổi.

Giải pháp dễ nhất là gộp các nhánh lại để `Form` luôn render ở cùng vị trí:

<Sandpack>

```js src/App.js
import { useState } from 'react';

export default function App() {
  const [showHint, setShowHint] = useState(false);
  return (
    <div>
      {showHint &&
        <p><i>Hint: Your favorite city?</i></p>
      }
      <Form />
      {showHint ? (
        <button onClick={() => {
          setShowHint(false);
        }}>Hide hint</button>
      ) : (
        <button onClick={() => {
          setShowHint(true);
        }}>Show hint</button>
      )}
    </div>
  );
}

function Form() {
  const [text, setText] = useState('');
  return (
    <textarea
      value={text}
      onChange={e => setText(e.target.value)}
    />
  );
}
```

```css
textarea { display: block; margin: 10px 0; }
```

</Sandpack>

Về mặt kỹ thuật, bạn cũng có thể thêm `null` trước `<Form />` trong nhánh `else` để khớp với cấu trúc nhánh `if`:

<Sandpack>

```js src/App.js
import { useState } from 'react';

export default function App() {
  const [showHint, setShowHint] = useState(false);
  if (showHint) {
    return (
      <div>
        <p><i>Hint: Your favorite city?</i></p>
        <Form />
        <button onClick={() => {
          setShowHint(false);
        }}>Hide hint</button>
      </div>
    );
  }
  return (
    <div>
      {null}
      <Form />
      <button onClick={() => {
        setShowHint(true);
      }}>Show hint</button>
    </div>
  );
}

function Form() {
  const [text, setText] = useState('');
  return (
    <textarea
      value={text}
      onChange={e => setText(e.target.value)}
    />
  );
}
```

```css
textarea { display: block; margin: 10px 0; }
```

</Sandpack>

Bằng cách này, `Form` luôn là con thứ hai, vì vậy nó vẫn ở cùng vị trí và giữ state của nó. Nhưng cách tiếp cận này ít rõ ràng hơn và đưa ra rủi ro rằng người khác sẽ xóa `null` đó.

</Solution>

#### Hoán đổi hai trường form {/*swap-two-form-fields*/}

Form này cho phép bạn nhập tên và họ. Nó cũng có một checkbox kiểm soát trường nào đứng trước. Khi bạn tick checkbox, trường "Last name" sẽ xuất hiện trước trường "First name".

Nó gần như hoạt động, nhưng có một bug. Nếu bạn điền vào ô nhập "First name" và tick checkbox, văn bản sẽ vẫn ở ô nhập đầu tiên (bây giờ là "Last name"). Hãy sửa nó để văn bản ô nhập *cũng* di chuyển khi bạn đảo ngược thứ tự.

<Hint>

Có vẻ như đối với những trường này, vị trí của chúng trong parent không đủ. Có cách nào để nói với React cách khớp state giữa các lần render không?

</Hint>

<Sandpack>

```js src/App.js
import { useState } from 'react';

export default function App() {
  const [reverse, setReverse] = useState(false);
  let checkbox = (
    <label>
      <input
        type="checkbox"
        checked={reverse}
        onChange={e => setReverse(e.target.checked)}
      />
      Reverse order
    </label>
  );
  if (reverse) {
    return (
      <>
        <Field label="Last name" />
        <Field label="First name" />
        {checkbox}
      </>
    );
  } else {
    return (
      <>
        <Field label="First name" />
        <Field label="Last name" />
        {checkbox}
      </>
    );
  }
}

function Field({ label }) {
  const [text, setText] = useState('');
  return (
    <label>
      {label}:{' '}
      <input
        type="text"
        value={text}
        placeholder={label}
        onChange={e => setText(e.target.value)}
      />
    </label>
  );
}
```

```css
label { display: block; margin: 10px 0; }
```

</Sandpack>

<Solution>

Gán `key` cho cả hai component `<Field>` trong cả nhánh `if` và `else`. Điều này nói với React cách "khớp" đúng state cho mỗi `<Field>` ngay cả khi thứ tự của chúng trong parent thay đổi:

<Sandpack>

```js src/App.js
import { useState } from 'react';

export default function App() {
  const [reverse, setReverse] = useState(false);
  let checkbox = (
    <label>
      <input
        type="checkbox"
        checked={reverse}
        onChange={e => setReverse(e.target.checked)}
      />
      Reverse order
    </label>
  );
  if (reverse) {
    return (
      <>
        <Field key="lastName" label="Last name" />
        <Field key="firstName" label="First name" />
        {checkbox}
      </>
    );
  } else {
    return (
      <>
        <Field key="firstName" label="First name" />
        <Field key="lastName" label="Last name" />
        {checkbox}
      </>
    );
  }
}

function Field({ label }) {
  const [text, setText] = useState('');
  return (
    <label>
      {label}:{' '}
      <input
        type="text"
        value={text}
        placeholder={label}
        onChange={e => setText(e.target.value)}
      />
    </label>
  );
}
```

```css
label { display: block; margin: 10px 0; }
```

</Sandpack>

</Solution>

#### Đặt lại form chi tiết {/*reset-a-detail-form*/}

Đây là danh sách liên hệ có thể chỉnh sửa. Bạn có thể chỉnh sửa chi tiết của liên hệ được chọn rồi nhấn "Save" để cập nhật nó, hoặc "Reset" để hoàn tác các thay đổi.

Khi bạn chọn một liên hệ khác (ví dụ, Alice), state cập nhật nhưng form vẫn hiển thị chi tiết của liên hệ trước đó. Hãy sửa nó để form được reset khi liên hệ đã chọn thay đổi.

<Sandpack>

```js src/App.js
import { useState } from 'react';
import ContactList from './ContactList.js';
import EditContact from './EditContact.js';

export default function ContactManager() {
  const [
    contacts,
    setContacts
  ] = useState(initialContacts);
  const [
    selectedId,
    setSelectedId
  ] = useState(0);
  const selectedContact = contacts.find(c =>
    c.id === selectedId
  );

  function handleSave(updatedData) {
    const nextContacts = contacts.map(c => {
      if (c.id === updatedData.id) {
        return updatedData;
      } else {
        return c;
      }
    });
    setContacts(nextContacts);
  }

  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedId={selectedId}
        onSelect={id => setSelectedId(id)}
      />
      <hr />
      <EditContact
        initialData={selectedContact}
        onSave={handleSave}
      />
    </div>
  )
}

const initialContacts = [
  { id: 0, name: 'Taylor', email: 'taylor@mail.com' },
  { id: 1, name: 'Alice', email: 'alice@mail.com' },
  { id: 2, name: 'Bob', email: 'bob@mail.com' }
];
```

```js src/ContactList.js
export default function ContactList({
  contacts,
  selectedId,
  onSelect
}) {
  return (
    <section>
      <ul>
        {contacts.map(contact =>
          <li key={contact.id}>
            <button onClick={() => {
              onSelect(contact.id);
            }}>
              {contact.id === selectedId ?
                <b>{contact.name}</b> :
                contact.name
              }
            </button>
          </li>
        )}
      </ul>
    </section>
  );
}
```

```js src/EditContact.js
import { useState } from 'react';

export default function EditContact({ initialData, onSave }) {
  const [name, setName] = useState(initialData.name);
  const [email, setEmail] = useState(initialData.email);
  return (
    <section>
      <label>
        Name:{' '}
        <input
          type="text"
          value={name}
          onChange={e => setName(e.target.value)}
        />
      </label>
      <label>
        Email:{' '}
        <input
          type="email"
          value={email}
          onChange={e => setEmail(e.target.value)}
        />
      </label>
      <button onClick={() => {
        const updatedData = {
          id: initialData.id,
          name: name,
          email: email
        };
        onSave(updatedData);
      }}>
        Save
      </button>
      <button onClick={() => {
        setName(initialData.name);
        setEmail(initialData.email);
      }}>
        Reset
      </button>
    </section>
  );
}
```

```css
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li { display: inline-block; }
li button {
  padding: 10px;
}
label {
  display: block;
  margin: 10px 0;
}
button {
  margin-right: 10px;
  margin-bottom: 10px;
}
```

</Sandpack>

<Solution>

Gán `key={selectedId}` cho component `EditContact`. Bằng cách này, chuyển đổi giữa các liên hệ khác nhau sẽ reset form:

<Sandpack>

```js src/App.js
import { useState } from 'react';
import ContactList from './ContactList.js';
import EditContact from './EditContact.js';

export default function ContactManager() {
  const [
    contacts,
    setContacts
  ] = useState(initialContacts);
  const [
    selectedId,
    setSelectedId
  ] = useState(0);
  const selectedContact = contacts.find(c =>
    c.id === selectedId
  );

  function handleSave(updatedData) {
    const nextContacts = contacts.map(c => {
      if (c.id === updatedData.id) {
        return updatedData;
      } else {
        return c;
      }
    });
    setContacts(nextContacts);
  }

  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedId={selectedId}
        onSelect={id => setSelectedId(id)}
      />
      <hr />
      <EditContact
        key={selectedId}
        initialData={selectedContact}
        onSave={handleSave}
      />
    </div>
  )
}

const initialContacts = [
  { id: 0, name: 'Taylor', email: 'taylor@mail.com' },
  { id: 1, name: 'Alice', email: 'alice@mail.com' },
  { id: 2, name: 'Bob', email: 'bob@mail.com' }
];
```

```js src/ContactList.js
export default function ContactList({
  contacts,
  selectedId,
  onSelect
}) {
  return (
    <section>
      <ul>
        {contacts.map(contact =>
          <li key={contact.id}>
            <button onClick={() => {
              onSelect(contact.id);
            }}>
              {contact.id === selectedId ?
                <b>{contact.name}</b> :
                contact.name
              }
            </button>
          </li>
        )}
      </ul>
    </section>
  );
}
```

```js src/EditContact.js
import { useState } from 'react';

export default function EditContact({ initialData, onSave }) {
  const [name, setName] = useState(initialData.name);
  const [email, setEmail] = useState(initialData.email);
  return (
    <section>
      <label>
        Name:{' '}
        <input
          type="text"
          value={name}
          onChange={e => setName(e.target.value)}
        />
      </label>
      <label>
        Email:{' '}
        <input
          type="email"
          value={email}
          onChange={e => setEmail(e.target.value)}
        />
      </label>
      <button onClick={() => {
        const updatedData = {
          id: initialData.id,
          name: name,
          email: email
        };
        onSave(updatedData);
      }}>
        Save
      </button>
      <button onClick={() => {
        setName(initialData.name);
        setEmail(initialData.email);
      }}>
        Reset
      </button>
    </section>
  );
}
```

```css
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li { display: inline-block; }
li button {
  padding: 10px;
}
label {
  display: block;
  margin: 10px 0;
}
button {
  margin-right: 10px;
  margin-bottom: 10px;
}
```

</Sandpack>

</Solution>

#### Xóa ảnh trong khi đang tải {/*clear-an-image-while-its-loading*/}

Khi bạn nhấn "Next", trình duyệt bắt đầu tải ảnh tiếp theo. Tuy nhiên, vì nó được hiển thị trong cùng thẻ `<img>`, theo mặc định bạn vẫn sẽ thấy ảnh trước cho đến khi ảnh tiếp theo tải xong. Điều này có thể không mong muốn nếu quan trọng là văn bản luôn khớp với ảnh. Hãy thay đổi nó để ngay khi bạn nhấn "Next", ảnh trước sẽ ngay lập tức xóa đi.

<Hint>

Có cách nào để nói với React tạo lại DOM thay vì tái sử dụng nó không?

</Hint>

<Sandpack>

```js
import { useState } from 'react';

export default function Gallery() {
  const [index, setIndex] = useState(0);
  const hasNext = index < images.length - 1;

  function handleClick() {
    if (hasNext) {
      setIndex(index + 1);
    } else {
      setIndex(0);
    }
  }

  let image = images[index];
  return (
    <>
      <button onClick={handleClick}>
        Next
      </button>
      <h3>
        Image {index + 1} of {images.length}
      </h3>
      <img src={image.src} />
      <p>
        {image.place}
      </p>
    </>
  );
}

let images = [{
  place: 'Penang, Malaysia',
  src: 'https://react.dev/images/docs/scientists/FJeJR8M.jpg'
}, {
  place: 'Lisbon, Portugal',
  src: 'https://react.dev/images/docs/scientists/dB2LRbj.jpg'
}, {
  place: 'Bilbao, Spain',
  src: 'https://react.dev/images/docs/scientists/z08o2TS.jpg'
}, {
  place: 'Valparaíso, Chile',
  src: 'https://react.dev/images/docs/scientists/Y3utgTi.jpg'
}, {
  place: 'Schwyz, Switzerland',
  src: 'https://react.dev/images/docs/scientists/JBbMpWY.jpg'
}, {
  place: 'Prague, Czechia',
  src: 'https://react.dev/images/docs/scientists/QwUKKmF.jpg'
}, {
  place: 'Ljubljana, Slovenia',
  src: 'https://react.dev/images/docs/scientists/3aIiwfm.jpg'
}];
```

```css
img { width: 150px; height: 150px; }
```

</Sandpack>

<Solution>

Bạn có thể cung cấp `key` cho thẻ `<img>`. Khi `key` đó thay đổi, React sẽ tạo lại nút DOM `<img>` từ đầu. Điều này gây ra một khoảnh khắc nhấp nháy ngắn khi mỗi ảnh tải, vì vậy đây không phải là điều bạn muốn làm cho mọi ảnh trong ứng dụng. Nhưng nó có ý nghĩa nếu bạn muốn đảm bảo ảnh luôn khớp với văn bản.

<Sandpack>

```js
import { useState } from 'react';

export default function Gallery() {
  const [index, setIndex] = useState(0);
  const hasNext = index < images.length - 1;

  function handleClick() {
    if (hasNext) {
      setIndex(index + 1);
    } else {
      setIndex(0);
    }
  }

  let image = images[index];
  return (
    <>
      <button onClick={handleClick}>
        Next
      </button>
      <h3>
        Image {index + 1} of {images.length}
      </h3>
      <img key={image.src} src={image.src} />
      <p>
        {image.place}
      </p>
    </>
  );
}

let images = [{
  place: 'Penang, Malaysia',
  src: 'https://react.dev/images/docs/scientists/FJeJR8M.jpg'
}, {
  place: 'Lisbon, Portugal',
  src: 'https://react.dev/images/docs/scientists/dB2LRbj.jpg'
}, {
  place: 'Bilbao, Spain',
  src: 'https://react.dev/images/docs/scientists/z08o2TS.jpg'
}, {
  place: 'Valparaíso, Chile',
  src: 'https://react.dev/images/docs/scientists/Y3utgTi.jpg'
}, {
  place: 'Schwyz, Switzerland',
  src: 'https://react.dev/images/docs/scientists/JBbMpWY.jpg'
}, {
  place: 'Prague, Czechia',
  src: 'https://react.dev/images/docs/scientists/QwUKKmF.jpg'
}, {
  place: 'Ljubljana, Slovenia',
  src: 'https://react.dev/images/docs/scientists/3aIiwfm.jpg'
}];
```

```css
img { width: 150px; height: 150px; }
```

</Sandpack>

</Solution>

#### Sửa state bị đặt sai chỗ trong danh sách {/*fix-misplaced-state-in-the-list*/}

Trong danh sách này, mỗi `Contact` có state xác định xem "Show email" có được nhấn cho nó hay không. Nhấn "Show email" cho Alice, rồi tick ô checkbox "Show in reverse order". Bạn sẽ nhận thấy rằng email của _Taylor_ hiện được mở rộng, nhưng Alice—đã di chuyển xuống cuối—có vẻ bị thu lại.

Hãy sửa nó để trạng thái mở rộng được liên kết với mỗi liên hệ, bất kể thứ tự đã chọn.

<Sandpack>

```js src/App.js
import { useState } from 'react';
import Contact from './Contact.js';

export default function ContactList() {
  const [reverse, setReverse] = useState(false);

  const displayedContacts = [...contacts];
  if (reverse) {
    displayedContacts.reverse();
  }

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={reverse}
          onChange={e => {
            setReverse(e.target.checked)
          }}
        />{' '}
        Show in reverse order
      </label>
      <ul>
        {displayedContacts.map((contact, i) =>
          <li key={i}>
            <Contact contact={contact} />
          </li>
        )}
      </ul>
    </>
  );
}

const contacts = [
  { id: 0, name: 'Alice', email: 'alice@mail.com' },
  { id: 1, name: 'Bob', email: 'bob@mail.com' },
  { id: 2, name: 'Taylor', email: 'taylor@mail.com' }
];
```

```js src/Contact.js
import { useState } from 'react';

export default function Contact({ contact }) {
  const [expanded, setExpanded] = useState(false);
  return (
    <>
      <p><b>{contact.name}</b></p>
      {expanded &&
        <p><i>{contact.email}</i></p>
      }
      <button onClick={() => {
        setExpanded(!expanded);
      }}>
        {expanded ? 'Hide' : 'Show'} email
      </button>
    </>
  );
}
```

```css
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li {
  margin-bottom: 20px;
}
label {
  display: block;
  margin: 10px 0;
}
button {
  margin-right: 10px;
  margin-bottom: 10px;
}
```

</Sandpack>

<Solution>

Vấn đề là ví dụ này đang sử dụng index làm `key`:

```js
{displayedContacts.map((contact, i) =>
  <li key={i}>
```

Tuy nhiên, bạn muốn state được liên kết với _mỗi liên hệ cụ thể_.

Sử dụng ID liên hệ làm `key` thay thế để sửa vấn đề:

<Sandpack>

```js src/App.js
import { useState } from 'react';
import Contact from './Contact.js';

export default function ContactList() {
  const [reverse, setReverse] = useState(false);

  const displayedContacts = [...contacts];
  if (reverse) {
    displayedContacts.reverse();
  }

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={reverse}
          onChange={e => {
            setReverse(e.target.checked)
          }}
        />{' '}
        Show in reverse order
      </label>
      <ul>
        {displayedContacts.map(contact =>
          <li key={contact.id}>
            <Contact contact={contact} />
          </li>
        )}
      </ul>
    </>
  );
}

const contacts = [
  { id: 0, name: 'Alice', email: 'alice@mail.com' },
  { id: 1, name: 'Bob', email: 'bob@mail.com' },
  { id: 2, name: 'Taylor', email: 'taylor@mail.com' }
];
```

```js src/Contact.js
import { useState } from 'react';

export default function Contact({ contact }) {
  const [expanded, setExpanded] = useState(false);
  return (
    <>
      <p><b>{contact.name}</b></p>
      {expanded &&
        <p><i>{contact.email}</i></p>
      }
      <button onClick={() => {
        setExpanded(!expanded);
      }}>
        {expanded ? 'Hide' : 'Show'} email
      </button>
    </>
  );
}
```

```css
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li {
  margin-bottom: 20px;
}
label {
  display: block;
  margin: 10px 0;
}
button {
  margin-right: 10px;
  margin-bottom: 10px;
}
```

</Sandpack>

State được liên kết với vị trí cây. Một `key` cho phép bạn chỉ định một vị trí được đặt tên thay vì dựa vào thứ tự.

</Solution>

</Challenges>
