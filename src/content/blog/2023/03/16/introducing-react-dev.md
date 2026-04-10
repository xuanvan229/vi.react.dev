---
title: "Giới thiệu react.dev"
author: Dan Abramov and Rachel Nabors
date: 2023/03/16
description: Hôm nay chúng tôi rất vui mừng được ra mắt react.dev, ngôi nhà mới của React và tài liệu của nó. Trong bài viết này, chúng tôi muốn đưa bạn tham quan trang web mới.
---

Ngày 16 tháng 3, 2023 bởi [Dan Abramov](https://bsky.app/profile/danabra.mov) và [Rachel Nabors](https://twitter.com/rachelnabors)

---

<Intro>

Hôm nay chúng tôi rất vui mừng được ra mắt [react.dev](https://react.dev), ngôi nhà mới của React và tài liệu của nó. Trong bài viết này, chúng tôi muốn đưa bạn tham quan trang web mới.

</Intro>

---

## tl;dr {/*tldr*/}

* Trang web React mới ([react.dev](https://react.dev)) dạy React hiện đại với function components và Hooks.
* Chúng tôi đã bao gồm các sơ đồ, hình minh họa, thử thách và hơn 600 ví dụ tương tác mới.
* Trang tài liệu React trước đây đã chuyển sang [legacy.reactjs.org](https://legacy.reactjs.org).

## Trang web mới, domain mới, trang chủ mới {/*new-site-new-domain-new-homepage*/}

Đầu tiên, một chút công việc vặt vãnh.

Để kỷ niệm việc ra mắt tài liệu mới và, quan trọng hơn, để phân tách rõ ràng nội dung cũ và mới, chúng tôi đã chuyển sang domain ngắn hơn [react.dev](https://react.dev). Domain cũ [reactjs.org](https://reactjs.org) bây giờ sẽ chuyển hướng đến đây.

Tài liệu React cũ bây giờ được lưu trữ tại [legacy.reactjs.org](https://legacy.reactjs.org). Tất cả các liên kết hiện có đến nội dung cũ sẽ tự động chuyển hướng đến đó để tránh "phá vỡ web", nhưng trang legacy sẽ không nhận được nhiều cập nhật hơn.

Tin hay không, React sẽ sớm được mười tuổi. Trong thế giới JavaScript, đó như là cả một thế kỷ! Chúng tôi đã [làm mới trang chủ React](https://react.dev) để phản ánh lý do tại sao chúng tôi nghĩ React là một cách tuyệt vời để tạo giao diện người dùng ngày nay, và cập nhật các hướng dẫn bắt đầu để đề cập nổi bật hơn đến các framework hiện đại dựa trên React.

Nếu bạn chưa thấy trang chủ mới, hãy xem nó!

## Đặt cược tất cả vào React hiện đại với Hooks {/*going-all-in-on-modern-react-with-hooks*/}

Khi chúng tôi phát hành React Hooks năm 2018, tài liệu Hooks giả định rằng người đọc quen thuộc với class components. Điều này đã giúp cộng đồng áp dụng Hooks rất nhanh chóng, nhưng sau một thời gian tài liệu cũ không phục vụ được người đọc mới. Người đọc mới phải học React hai lần: một lần với class components và sau đó một lần nữa với Hooks.

**Tài liệu mới dạy React với Hooks từ đầu.** Tài liệu được chia thành hai phần chính:

* **[Học React](/learn)** là một khóa học tự học dạy React từ đầu.
* **[Tham chiếu API](/reference)** cung cấp chi tiết và ví dụ sử dụng cho mọi API React.

Hãy xem kỹ hơn những gì bạn có thể tìm thấy trong mỗi phần.

<Note>

Vẫn còn một vài trường hợp sử dụng class component hiếm gặp chưa có tương đương dựa trên Hook. Class components vẫn được hỗ trợ, và được ghi lại trong phần [Legacy API](/reference/react/legacy) của trang mới.

</Note>

## Bắt đầu nhanh {/*quick-start*/}

Phần Learn bắt đầu với trang [Bắt đầu nhanh](/learn). Đây là một tour giới thiệu ngắn về React. Nó giới thiệu cú pháp cho các khái niệm như components, props và state, nhưng không đi vào nhiều chi tiết về cách sử dụng chúng.

Nếu bạn thích học bằng cách thực hành, chúng tôi khuyên bạn nên xem [Hướng dẫn Tic-Tac-Toe](/learn/tutorial-tic-tac-toe) tiếp theo. Nó hướng dẫn bạn xây dựng một trò chơi nhỏ với React, trong khi dạy các kỹ năng bạn sẽ sử dụng hàng ngày. Đây là những gì bạn sẽ xây dựng:

<Sandpack>

```js src/App.js
import { useState } from 'react';

function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    const nextSquares = squares.slice();
    if (xIsNext) {
      nextSquares[i] = 'X';
    } else {
      nextSquares[i] = 'O';
    }
    onPlay(nextSquares);
  }

  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else {
    status = 'Next player: ' + (xIsNext ? 'X' : 'O');
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}

export default function Game() {
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const xIsNext = currentMove % 2 === 0;
  const currentSquares = history[currentMove];

  function handlePlay(nextSquares) {
    const nextHistory = [...history.slice(0, currentMove + 1), nextSquares];
    setHistory(nextHistory);
    setCurrentMove(nextHistory.length - 1);
  }

  function jumpTo(nextMove) {
    setCurrentMove(nextMove);
  }

  const moves = history.map((squares, move) => {
    let description;
    if (move > 0) {
      description = 'Go to move #' + move;
    } else {
      description = 'Go to game start';
    }
    return (
      <li key={move}>
        <button onClick={() => jumpTo(move)}>{description}</button>
      </li>
    );
  });

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>
      <div className="game-info">
        <ol>{moves}</ol>
      </div>
    </div>
  );
}

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

```css src/styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

.square {
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 24px;
  font-weight: bold;
  line-height: 34px;
  height: 34px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 34px;
}

.board-row:after {
  clear: both;
  content: '';
  display: table;
}

.status {
  margin-bottom: 10px;
}
.game {
  display: flex;
  flex-direction: row;
}

.game-info {
  margin-left: 20px;
}
```

</Sandpack>

Chúng tôi cũng muốn nhấn mạnh [Thinking in React](/learn/thinking-in-react)—đó là hướng dẫn đã làm React "click" đối với nhiều người trong chúng tôi. **Chúng tôi đã cập nhật cả hai hướng dẫn cổ điển này để sử dụng function components và Hooks,** vì vậy chúng tốt như mới.

<Note>

Ví dụ trên là một *sandbox*. Chúng tôi đã thêm rất nhiều sandboxes—hơn 600!—ở khắp nơi trên trang web. Bạn có thể chỉnh sửa bất kỳ sandbox nào, hoặc nhấn "Fork" ở góc trên bên phải để mở nó trong một tab riêng. Sandboxes cho phép bạn nhanh chóng chơi với các API React, khám phá ý tưởng của bạn và kiểm tra sự hiểu biết của bạn.

</Note>

## Học React từng bước {/*learn-react-step-by-step*/}

Chúng tôi muốn mọi người trên thế giới có cơ hội bình đẳng để học React miễn phí theo cách riêng của họ.

Đây là lý do tại sao phần Learn được tổ chức như một khóa học tự học được chia thành các chương. Hai chương đầu tiên mô tả các nguyên tắc cơ bản của React. Nếu bạn mới học React, hoặc muốn ôn lại trong bộ nhớ của bạn, hãy bắt đầu từ đây:

- **[Mô tả UI](/learn/describing-the-ui)** dạy cách hiển thị thông tin với components.
- **[Thêm tính tương tác](/learn/adding-interactivity)** dạy cách cập nhật màn hình để phản hồi với đầu vào của người dùng.

Hai chương tiếp theo nâng cao hơn, và sẽ giúp bạn có cái nhìn sâu hơn về các phần khó hơn:

- **[Quản lý State](/learn/managing-state)** dạy cách tổ chức logic của bạn khi ứng dụng của bạn phát triển về mức độ phức tạp.
- **[Lối thoát](/learn/escape-hatches)** dạy cách bạn có thể "bước ra ngoài" React, và khi nào thì hợp lý nhất để làm như vậy.

Mỗi chương bao gồm một số trang liên quan. Hầu hết các trang này dạy một kỹ năng hoặc kỹ thuật cụ thể—ví dụ: [Viết Markup với JSX](/learn/writing-markup-with-jsx), [Cập nhật Objects trong State](/learn/updating-objects-in-state), hoặc [Chia sẻ State giữa các Components](/learn/sharing-state-between-components). Một số trang tập trung vào việc giải thích một ý tưởng—như [Render và Commit](/learn/render-and-commit), hoặc [State như một Snapshot](/learn/state-as-a-snapshot). Và có một vài trang, như [Bạn có thể không cần một Effect](/learn/you-might-not-need-an-effect), chia sẻ các gợi ý của chúng tôi dựa trên những gì chúng tôi đã học trong những năm qua.

Bạn không phải đọc các chương này theo thứ tự. Ai có thời gian cho điều đó?! Nhưng bạn có thể. Các trang trong phần Learn chỉ dựa trên các khái niệm được giới thiệu bởi các trang trước đó. Nếu bạn muốn đọc nó như một cuốn sách, hãy làm!

### Kiểm tra sự hiểu biết của bạn với các thử thách {/*check-your-understanding-with-challenges*/}

Hầu hết các trang trong phần Learn kết thúc bằng một vài thử thách để kiểm tra sự hiểu biết của bạn. Ví dụ: đây là một vài thử thách từ trang về [Conditional Rendering](/learn/conditional-rendering#challenges).

Bạn không phải giải chúng ngay bây giờ! Trừ khi bạn *thực sự* muốn.

<Challenges noTitle={true}>

#### Hiển thị biểu tượng cho các mục chưa hoàn chỉnh với `? :` {/*show-an-icon-for-incomplete-items-with--*/}

Sử dụng toán tử điều kiện (`cond ? a : b`) để render ❌ nếu `isPacked` không phải là `true`.

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

#### Hiển thị tầm quan trọng của mục với `&&` {/*show-the-item-importance-with-*/}

Trong ví dụ này, mỗi `Item` nhận một prop `importance` dạng số. Sử dụng toán tử `&&` để render "_(Độ quan trọng: X)_" in nghiêng, nhưng chỉ cho các mục có độ quan trọng khác không. Danh sách mục của bạn nên trông như thế này:

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

Đây sẽ là cách giải:

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

Lưu ý rằng bạn phải viết `importance > 0 && ...` thay vì `importance && ...` để nếu `importance` là `0`, `0` không được render là kết quả!

Trong giải pháp này, hai điều kiện riêng biệt được sử dụng để chèn khoảng trắng giữa tên và nhãn độ quan trọng. Ngoài ra, bạn có thể sử dụng Fragment với khoảng trắng ở đầu: `importance > 0 && <> <i>...</i></>` hoặc thêm khoảng trắng ngay bên trong `<i>`: `importance > 0 && <i> ...</i>`.

</Solution>

</Challenges>

Chú ý nút "Hiển thị giải pháp" ở góc dưới bên trái. Nó tiện dụng nếu bạn muốn kiểm tra bản thân!

### Xây dựng trực giác với sơ đồ và hình minh họa {/*build-an-intuition-with-diagrams-and-illustrations*/}

Khi chúng tôi không thể tìm ra cách giải thích điều gì đó chỉ bằng code và chữ, chúng tôi đã thêm các sơ đồ giúp cung cấp một số trực giác. Ví dụ: đây là một trong các sơ đồ từ [Bảo tồn và Đặt lại State](/learn/preserving-and-resetting-state):

<Diagram name="preserving_state_diff_same_pt1" height={350} width={794} alt="Diagram with three sections, with an arrow transitioning each section in between. The first section contains a React component labeled 'div' with a single child labeled 'section', which has a single child labeled 'Counter' containing a state bubble labeled 'count' with value 3. The middle section has the same 'div' parent, but the child components have now been deleted, indicated by a yellow 'proof' image. The third section has the same 'div' parent again, now with a new child labeled 'div', highlighted in yellow, also with a new child labeled 'Counter' containing a state bubble labeled 'count' with value 0, all highlighted in yellow.">

Khi `section` thay đổi thành `div`, `section` bị xóa và `div` mới được thêm vào

</Diagram>

Bạn cũng sẽ thấy một số hình minh họa trong toàn bộ tài liệu--đây là một hình về [trình duyệt vẽ màn hình](/learn/render-and-commit#epilogue-browser-paint):

<Illustration alt="A browser painting 'still life with card element'." src="/images/docs/illustrations/i_browser-paint.png" />

Chúng tôi đã xác nhận với các nhà cung cấp trình duyệt rằng hình ảnh mô tả này chính xác 100% về mặt khoa học.

## Tham chiếu API mới, chi tiết {/*a-new-detailed-api-reference*/}

Trong [Tham chiếu API](/reference/react), mỗi API React bây giờ có một trang riêng. Điều này bao gồm tất cả các loại API:

- Các Hook tích hợp như [`useState`](/reference/react/useState).
- Các component tích hợp như [`<Suspense>`](/reference/react/Suspense).
- Các component trình duyệt tích hợp như [`<input>`](/reference/react-dom/components/input).
- Các API hướng framework như [`renderToPipeableStream`](/reference/react-dom/server/renderToReadableStream).
- Các API React khác như [`memo`](/reference/react/memo).

Bạn sẽ nhận thấy rằng mỗi trang API được chia thành ít nhất hai phần: *Tham chiếu* và *Cách sử dụng*.

[Tham chiếu](/reference/react/useState#reference) mô tả chữ ký API chính thức bằng cách liệt kê các đối số và giá trị trả về của nó. Nó súc tích, nhưng có thể cảm thấy hơi trừu tượng nếu bạn không quen thuộc với API đó. Nó mô tả những gì một API làm, nhưng không phải cách sử dụng nó.

[Cách sử dụng](/reference/react/useState#usage) hiển thị lý do tại sao và cách bạn sẽ sử dụng API này trong thực tế, như một đồng nghiệp hoặc một người bạn có thể giải thích. Nó hiển thị **các tình huống chuẩn về cách mỗi API được thiết kế để sử dụng bởi nhóm React.** Chúng tôi đã thêm các đoạn code có màu sắc, ví dụ về việc sử dụng các API khác nhau cùng nhau, và các công thức mà bạn có thể sao chép và dán:

<Recipes titleText="Basic useState examples" titleId="examples-basic">

#### Bộ đếm (number) {/*counter-number*/}

Trong ví dụ này, biến state `count` giữ một số. Nhấp vào nút tăng nó lên.

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

#### Trường văn bản (string) {/*text-field-string*/}

Trong ví dụ này, biến state `text` giữ một chuỗi. Khi bạn gõ, `handleChange` đọc giá trị đầu vào mới nhất từ phần tử DOM input của trình duyệt, và gọi `setText` để cập nhật state. Điều này cho phép bạn hiển thị `text` hiện tại bên dưới.

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

#### Checkbox (boolean) {/*checkbox-boolean*/}

Trong ví dụ này, biến state `liked` giữ một boolean. Khi bạn nhấp vào input, `setLiked` cập nhật biến state `liked` với việc checkbox của trình duyệt có được check hay không. Biến `liked` được sử dụng để render văn bản bên dưới checkbox.

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

Some API pages also include [Troubleshooting](/reference/react/useEffect#troubleshooting) (for common problems) and [Alternatives](/reference/react-dom/findDOMNode#alternatives) (for deprecated APIs).

We hope that this approach will make the API reference useful not only as a way to look up an argument, but as a way to see all the different things you can do with any given API—and how it connects to the other ones.

## What's next? {/*whats-next*/}

That's a wrap for our little tour! Have a look around the new website, see what you like or don't like, and keep the feedback coming in our [issue tracker](https://github.com/reactjs/react.dev/issues).

We acknowledge this project has taken a long time to ship. We wanted to maintain a high quality bar that the React community deserves. While writing these docs and creating all of the examples, we found mistakes in some of our own explanations, bugs in React, and even gaps in the React design that we are now working to address. We hope that the new documentation will help us hold React itself to a higher bar in the future.

We've heard many of your requests to expand the content and functionality of the website, for example:

- Providing a TypeScript version for all examples;
- Creating the updated performance, testing, and accessibility guides;
- Documenting React Server Components independently from the frameworks that support them;
- Working with our international community to get the new docs translated;
- Adding missing features to the new website (for example, RSS for this blog).

Now that [react.dev](https://react.dev/) is out, we will be able to shift our focus from "catching up" with the third-party React educational resources to adding new information and further improving our new website.

We think there's never been a better time to learn React.

## Who worked on this? {/*who-worked-on-this*/}

On the React team, [Rachel Nabors](https://twitter.com/rachelnabors/) led the project (and provided the illustrations), and [Dan Abramov](https://bsky.app/profile/danabra.mov) designed the curriculum. They co-authored most of the content together as well.

Of course, no project this large happens in isolation. We have a lot of people to thank!

[Sylwia Vargas](https://twitter.com/SylwiaVargas) overhauled our examples to go beyond "foo/bar/baz" and kittens, and feature scientists, artists and cities from around the world. [Maggie Appleton](https://twitter.com/Mappletons) turned our doodles into a clear diagram system.

Thanks to [David McCabe](https://twitter.com/mcc_abe), [Sophie Alpert](https://twitter.com/sophiebits), [Rick Hanlon](https://twitter.com/rickhanlonii), [Andrew Clark](https://twitter.com/acdlite), and [Matt Carroll](https://twitter.com/mattcarrollcode) for additional writing contributions. We'd also like to thank [Natalia Tepluhina](https://twitter.com/n_tepluhina) and [Sebastian Markbåge](https://twitter.com/sebmarkbage) for their ideas and feedback.

Thanks to [Dan Lebowitz](https://twitter.com/lebo) for the site design and [Razvan Gradinar](https://dribbble.com/GradinarRazvan) for the sandbox design.

On the development front, thanks to [Jared Palmer](https://twitter.com/jaredpalmer) for prototype development. Thanks to [Dane Grant](https://twitter.com/danecando) and [Dustin Goodman](https://twitter.com/dustinsgoodman) from [ThisDotLabs](https://www.thisdot.co/) for their support on UI development. Thanks to [Ives van Hoorne](https://twitter.com/CompuIves), [Alex Moldovan](https://twitter.com/alexnmoldovan), [Jasper De Moor](https://twitter.com/JasperDeMoor), and [Danilo Woznica](https://twitter.com/danilowoz) from [CodeSandbox](https://codesandbox.io/) for their work with sandbox integration. Thanks to [Rick Hanlon](https://twitter.com/rickhanlonii) for spot development and design work, finessing our colors and finer details. Thanks to [Harish Kumar](https://www.strek.in/) and [Luna Ruan](https://twitter.com/lunaruan) for adding new features to the site and helping maintain it.

Huge thanks to the folks who volunteered their time to participate in the alpha and beta testing program. Your enthusiasm and invaluable feedback helped us shape these docs. A special shout out to our beta tester, [Debbie O'Brien](https://twitter.com/debs_obrien), who gave a talk about her experience using the React docs at React Conf 2021.

Finally, thanks to the React community for being the inspiration behind this effort. You are the reason we do this, and we hope that the new docs will help you use React to build any user interface that you want.
