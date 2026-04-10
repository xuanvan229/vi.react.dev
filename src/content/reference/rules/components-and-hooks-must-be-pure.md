---
title: Components và Hooks phải thuần khiết
---

<Intro>
Các hàm thuần khiết chỉ thực hiện tính toán và không làm gì khác. Điều này giúp code của bạn dễ hiểu hơn, dễ debug hơn, và cho phép React tự động tối ưu hóa các component và Hooks của bạn một cách chính xác.
</Intro>

<Note>
Trang tham chiếu này bao gồm các chủ đề nâng cao và yêu cầu quen thuộc với các khái niệm được đề cập trong trang [Giữ cho Components thuần khiết](/learn/keeping-components-pure).
</Note>

<InlineToc />

### Tại sao tính thuần khiết quan trọng? {/*why-does-purity-matter*/}

Một trong những khái niệm chính tạo nên React chính là _tính thuần khiết_. Một component hoặc hook thuần khiết là một component hoặc hook:

* **Idempotent** – Bạn [luôn nhận được cùng kết quả mỗi lần](/learn/keeping-components-pure#purity-components-as-formulas) chạy nó với cùng đầu vào – props, state, context cho đầu vào component; và đối số cho đầu vào hook.
* **Không có side effect trong render** – Code có side effect nên chạy [**tách biệt với rendering**](#how-does-react-run-your-code). Ví dụ như một [event handler](/learn/responding-to-events) – nơi người dùng tương tác với UI và khiến nó cập nhật; hoặc một [Effect](/reference/react/useEffect) – chạy sau khi render.
* **Không thay đổi các giá trị không cục bộ**: Components và Hooks [không bao giờ được sửa đổi các giá trị không được tạo cục bộ](#mutation) trong render.

Khi render được giữ thuần khiết, React có thể hiểu cách ưu tiên cập nhật nào quan trọng nhất để người dùng thấy trước. Điều này được thực hiện nhờ tính thuần khiết của render: vì các component không có side effect [trong render](#how-does-react-run-your-code), React có thể tạm dừng render các component không quan trọng để cập nhật, và chỉ quay lại chúng sau khi cần.

Cụ thể, điều này có nghĩa là logic rendering có thể được chạy nhiều lần theo cách cho phép React mang lại trải nghiệm người dùng tốt. Tuy nhiên, nếu component của bạn có side effect không được theo dõi – như sửa đổi giá trị của biến toàn cục [trong render](#how-does-react-run-your-code) – khi React chạy lại code rendering của bạn, các side effect sẽ được kích hoạt theo cách không khớp với những gì bạn muốn. Điều này thường dẫn đến các lỗi bất ngờ có thể làm suy giảm trải nghiệm người dùng của bạn. Bạn có thể xem [ví dụ về điều này trong trang Giữ cho Components thuần khiết](/learn/keeping-components-pure#side-effects-unintended-consequences).

#### React chạy code của bạn như thế nào? {/*how-does-react-run-your-code*/}

React mang tính khai báo: bạn nói cho React biết _cái gì_ cần render, và React sẽ tìm ra _cách_ tốt nhất để hiển thị nó cho người dùng. Để làm điều này, React có một vài giai đoạn nơi nó chạy code của bạn. Bạn không cần biết về tất cả các giai đoạn này để sử dụng React tốt. Nhưng ở cấp độ cao, bạn nên biết code nào chạy trong _render_, và code nào chạy bên ngoài nó.

_Rendering_ đề cập đến việc tính toán phiên bản tiếp theo của UI của bạn trông như thế nào. Sau khi rendering, React lấy tính toán mới này và so sánh nó với tính toán được sử dụng để tạo phiên bản trước đó của UI của bạn. Sau đó React commit chỉ những thay đổi tối thiểu cần thiết vào [DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model) (những gì người dùng thực sự thấy) để áp dụng các thay đổi. Cuối cùng, [Effects](/learn/synchronizing-with-effects) được xả (nghĩa là chúng được chạy cho đến khi không còn gì nữa). Để biết thêm thông tin chi tiết, xem tài liệu về [Render](/learn/render-and-commit) và [Commit và Effect Hooks](/reference/react/hooks#effect-hooks).

<DeepDive>

#### Cách nhận biết code có chạy trong render hay không {/*how-to-tell-if-code-runs-in-render*/}

Một cách heuristic nhanh để biết code có chạy trong render hay không là kiểm tra nơi nó được viết: nếu nó được viết ở cấp cao nhất như trong ví dụ bên dưới, rất có thể nó chạy trong render.

```js {2}
function Dropdown() {
  const selectedItems = new Set(); // created during render
  // ...
}
```

Event handlers và Effects không chạy trong render:

```js {4}
function Dropdown() {
  const selectedItems = new Set();
  const onSelect = (item) => {
    // this code is in an event handler, so it's only run when the user triggers this
    selectedItems.add(item);
  }
}
```

```js {4}
function Dropdown() {
  const selectedItems = new Set();
  useEffect(() => {
    // this code is inside of an Effect, so it only runs after rendering
    logForAnalytics(selectedItems);
  }, [selectedItems]);
}
```
</DeepDive>

---

## Components và Hooks phải có tính idempotent {/*components-and-hooks-must-be-idempotent*/}

Các component phải luôn trả về cùng một đầu ra với các đầu vào của chúng – props, state, và context. Đây được gọi là _idempotency_. [Idempotency](https://en.wikipedia.org/wiki/Idempotence) là một thuật ngữ phổ biến trong lập trình hàm. Nó chỉ ý tưởng rằng bạn [luôn nhận được cùng kết quả mỗi lần](learn/keeping-components-pure) chạy đoạn code đó với cùng đầu vào.

Điều này có nghĩa là _tất cả_ code chạy [trong render](#how-does-react-run-your-code) cũng phải có tính idempotent để quy tắc này được giữ. Ví dụ, dòng code này không có tính idempotent (và do đó, component cũng vậy):

```js {2}
function Clock() {
  const time = new Date(); // 🔴 Bad: always returns a different result!
  return <span>{time.toLocaleString()}</span>
}
```

`new Date()` không có tính idempotent vì nó luôn trả về ngày hiện tại và thay đổi kết quả mỗi lần được gọi. Khi bạn render component trên, thời gian hiển thị trên màn hình sẽ bị kẹt ở thời điểm component được render. Tương tự, các hàm như `Math.random()` cũng không có tính idempotent, vì chúng trả về các kết quả khác nhau mỗi lần được gọi, ngay cả khi đầu vào giống nhau.

Điều này không có nghĩa là bạn không nên sử dụng các hàm không idempotent như `new Date()` _hoàn toàn_ – bạn chỉ nên tránh sử dụng chúng [trong render](#how-does-react-run-your-code). Trong trường hợp này, chúng ta có thể _đồng bộ hóa_ ngày mới nhất với component này bằng một [Effect](/reference/react/useEffect):

<Sandpack>

```js
import { useState, useEffect } from 'react';

function useTime() {
  // 1. Keep track of the current date's state. `useState` receives an initializer function as its
  //    initial state. It only runs once when the hook is called, so only the current date at the
  //    time the hook is called is set first.
  const [time, setTime] = useState(() => new Date());

  useEffect(() => {
    // 2. Update the current date every second using `setInterval`.
    const id = setInterval(() => {
      setTime(new Date()); // ✅ Good: non-idempotent code no longer runs in render
    }, 1000);
    // 3. Return a cleanup function so we don't leak the `setInterval` timer.
    return () => clearInterval(id);
  }, []);

  return time;
}

export default function Clock() {
  const time = useTime();
  return <span>{time.toLocaleString()}</span>;
}
```

</Sandpack>

Bằng cách bọc lời gọi `new Date()` không idempotent trong một Effect, nó chuyển tính toán đó [ra ngoài rendering](#how-does-react-run-your-code).

Nếu bạn không cần đồng bộ một số state bên ngoài với React, bạn cũng có thể cân nhắc sử dụng [event handler](/learn/responding-to-events) nếu nó chỉ cần được cập nhật để phản hồi tương tác người dùng.

---

## Side effects phải chạy bên ngoài render {/*side-effects-must-run-outside-of-render*/}

[Side effects](/learn/keeping-components-pure#side-effects-unintended-consequences) không nên chạy [trong render](#how-does-react-run-your-code), vì React có thể render các component nhiều lần để tạo trải nghiệm người dùng tốt nhất có thể.

<Note>
Side effects là một thuật ngữ rộng hơn Effects. Effects đặc biệt chỉ code được bọc trong `useEffect`, trong khi side effect là thuật ngữ chung cho code có bất kỳ hiệu ứng có thể quan sát nào khác ngoài kết quả chính là trả về giá trị cho caller.

Side effects thường được viết bên trong [event handlers](/learn/responding-to-events) hoặc Effects. Nhưng không bao giờ trong render.
</Note>

Mặc dù render phải được giữ thuần khiết, side effects là cần thiết tại một thời điểm nào đó để ứng dụng của bạn làm điều gì đó thú vị, như hiển thị thứ gì đó trên màn hình! Điểm mấu chốt của quy tắc này là side effects không nên chạy [trong render](#how-does-react-run-your-code), vì React có thể render các component nhiều lần. Trong hầu hết các trường hợp, bạn sẽ sử dụng [event handlers](learn/responding-to-events) để xử lý side effects. Sử dụng event handler một cách rõ ràng cho React biết rằng code này không cần chạy trong render, giữ cho render thuần khiết. Nếu bạn đã hết tất cả các tùy chọn – và chỉ là phương án cuối cùng – bạn cũng có thể xử lý side effects bằng `useEffect`.

### Khi nào thì mutation được phép? {/*mutation*/}

#### Local mutation {/*local-mutation*/}
Một ví dụ phổ biến của side effect là mutation, trong JavaScript chỉ việc thay đổi giá trị của một giá trị không phải [primitive](https://developer.mozilla.org/en-US/docs/Glossary/Primitive). Nói chung, mặc dù mutation không phải là idiom trong React, _local_ mutation hoàn toàn ổn:

```js {2,7}
function FriendList({ friends }) {
  const items = []; // ✅ Good: locally created
  for (let i = 0; i < friends.length; i++) {
    const friend = friends[i];
    items.push(
      <Friend key={friend.id} friend={friend} />
    ); // ✅ Good: local mutation is okay
  }
  return <section>{items}</section>;
}
```

Không cần phải uốn cong code của bạn để tránh local mutation. Có thể sử dụng [`Array.map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) ở đây để ngắn gọn hơn, nhưng không có gì sai khi tạo một mảng cục bộ và sau đó push các item vào nó [trong render](#how-does-react-run-your-code).

Mặc dù có vẻ như chúng ta đang mutate `items`, điều quan trọng cần lưu ý là code này chỉ làm vậy _cục bộ_ – mutation không được "ghi nhớ" khi component được render lại. Nói cách khác, `items` chỉ tồn tại trong khoảng thời gian component tồn tại. Vì `items` luôn được _tạo lại_ mỗi khi `<FriendList />` được render, component sẽ luôn trả về cùng kết quả.

Mặt khác, nếu `items` được tạo bên ngoài component, nó giữ lại các giá trị trước đó và ghi nhớ các thay đổi:

```js {1,7}
const items = []; // 🔴 Bad: created outside of the component
function FriendList({ friends }) {
  for (let i = 0; i < friends.length; i++) {
    const friend = friends[i];
    items.push(
      <Friend key={friend.id} friend={friend} />
    ); // 🔴 Bad: mutates a value created outside of render
  }
  return <section>{items}</section>;
}
```

Khi `<FriendList />` chạy lại, chúng ta sẽ tiếp tục thêm `friends` vào `items` mỗi lần component đó chạy, dẫn đến nhiều kết quả trùng lặp. Phiên bản `<FriendList />` này có observable side effects [trong render](#how-does-react-run-your-code) và **vi phạm quy tắc**.

#### Lazy initialization {/*lazy-initialization*/}

Lazy initialization cũng ổn mặc dù không hoàn toàn "thuần khiết":

```js {2}
function ExpenseForm() {
  SuperCalculator.initializeIfNotReady(); // ✅ Good: if it doesn't affect other components
  // Continue rendering...
}
```

#### Thay đổi DOM {/*changing-the-dom*/}

Side effects trực tiếp hiển thị với người dùng không được phép trong logic render của các React component. Nói cách khác, chỉ việc gọi hàm component không nên tự nó tạo ra thay đổi trên màn hình.

```js {2}
function ProductDetailPage({ product }) {
  document.title = product.title; // 🔴 Bad: Changes the DOM
}
```

Một cách để đạt được kết quả mong muốn là cập nhật `document.title` bên ngoài render là [đồng bộ component với `document`](/learn/synchronizing-with-effects).

Miễn là việc gọi component nhiều lần là an toàn và không ảnh hưởng đến việc render của các component khác, React không quan tâm nếu nó không 100% thuần khiết theo nghĩa lập trình hàm nghiêm ngặt. Quan trọng hơn là [các component phải có tính idempotent](/reference/rules/components-and-hooks-must-be-pure).

---

## Props và state là bất biến {/*props-and-state-are-immutable*/}

Props và state của một component là [snapshot](learn/state-as-a-snapshot) bất biến. Không bao giờ mutate trực tiếp chúng. Thay vào đó, hãy truyền các props mới xuống, và sử dụng hàm setter từ `useState`.

Bạn có thể nghĩ về các giá trị props và state như các snapshot được cập nhật sau khi render. Vì lý do này, bạn không sửa đổi các biến props hoặc state trực tiếp: thay vào đó bạn truyền props mới, hoặc sử dụng hàm setter được cung cấp cho bạn để nói cho React biết rằng state cần cập nhật lần render tiếp theo của component.

### Không mutate Props {/*props*/}
Props là bất biến vì nếu bạn mutate chúng, ứng dụng sẽ tạo ra đầu ra không nhất quán, có thể khó debug vì nó có thể hoạt động hoặc không tùy thuộc vào hoàn cảnh.

```js {expectedErrors: {'react-compiler': [2]}} {2}
function Post({ item }) {
  item.url = new Url(item.url, base); // 🔴 Bad: never mutate props directly
  return <Link url={item.url}>{item.title}</Link>;
}
```

```js {2}
function Post({ item }) {
  const url = new Url(item.url, base); // ✅ Good: make a copy instead
  return <Link url={url}>{item.title}</Link>;
}
```

### Không mutate State {/*state*/}
`useState` trả về biến state và một setter để cập nhật state đó.

```js
const [stateVariable, setter] = useState(0);
```

Thay vì cập nhật biến state trực tiếp, chúng ta cần cập nhật nó bằng hàm setter được trả về bởi `useState`. Thay đổi giá trị trên biến state không khiến component cập nhật, để lại UI lỗi thời cho người dùng. Sử dụng hàm setter thông báo cho React rằng state đã thay đổi, và chúng ta cần đưa vào hàng đợi một re-render để cập nhật UI.

```js {expectedErrors: {'react-compiler': [2, 5]}} {5}
function Counter() {
  const [count, setCount] = useState(0);

  function handleClick() {
    count = count + 1; // 🔴 Bad: never mutate state directly
  }

  return (
    <button onClick={handleClick}>
      You pressed me {count} times
    </button>
  );
}
```

```js {5}
function Counter() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1); // ✅ Good: use the setter function returned by useState
  }

  return (
    <button onClick={handleClick}>
      You pressed me {count} times
    </button>
  );
}
```

---

## Giá trị trả về và đối số của Hooks là bất biến {/*return-values-and-arguments-to-hooks-are-immutable*/}

Một khi các giá trị được truyền cho hook, bạn không nên sửa đổi chúng. Giống như props trong JSX, các giá trị trở nên bất biến khi được truyền cho hook.

```js {expectedErrors: {'react-compiler': [4]}} {4}
function useIconStyle(icon) {
  const theme = useContext(ThemeContext);
  if (icon.enabled) {
    icon.className = computeStyle(icon, theme); // 🔴 Bad: never mutate hook arguments directly
  }
  return icon;
}
```

```js {3}
function useIconStyle(icon) {
  const theme = useContext(ThemeContext);
  const newIcon = { ...icon }; // ✅ Good: make a copy instead
  if (icon.enabled) {
    newIcon.className = computeStyle(icon, theme);
  }
  return newIcon;
}
```

Một nguyên tắc quan trọng trong React là _local reasoning_: khả năng hiểu một component hoặc hook làm gì bằng cách nhìn vào code của nó một cách độc lập. Hooks nên được coi như "hộp đen" khi chúng được gọi. Ví dụ, một custom hook có thể đã sử dụng các đối số của nó như dependency để memoize các giá trị bên trong nó:

```js {4}
function useIconStyle(icon) {
  const theme = useContext(ThemeContext);

  return useMemo(() => {
    const newIcon = { ...icon };
    if (icon.enabled) {
      newIcon.className = computeStyle(icon, theme);
    }
    return newIcon;
  }, [icon, theme]);
}
```

Nếu bạn mutate các đối số của Hook, memoization của custom hook sẽ trở nên không chính xác, vì vậy quan trọng là tránh làm điều đó.

```js {4}
style = useIconStyle(icon);         // `style` is memoized based on `icon`
icon.enabled = false;               // Bad: 🔴 never mutate hook arguments directly
style = useIconStyle(icon);         // previously memoized result is returned
```

```js {4}
style = useIconStyle(icon);         // `style` is memoized based on `icon`
icon = { ...icon, enabled: false }; // Good: ✅ make a copy instead
style = useIconStyle(icon);         // new value of `style` is calculated
```

Tương tự, quan trọng là không sửa đổi giá trị trả về của Hooks, vì chúng có thể đã được memoize.

---

## Giá trị là bất biến sau khi được truyền vào JSX {/*values-are-immutable-after-being-passed-to-jsx*/}

Đừng mutate các giá trị sau khi chúng đã được sử dụng trong JSX. Di chuyển mutation trước khi JSX được tạo.

Khi bạn sử dụng JSX trong một expression, React có thể đánh giá sẵn JSX trước khi component hoàn thành render. Điều này có nghĩa là mutate các giá trị sau khi chúng đã được truyền cho JSX có thể dẫn đến UI lỗi thời, vì React sẽ không biết cập nhật đầu ra của component.

```js {expectedErrors: {'react-compiler': [4]}} {4}
function Page({ colour }) {
  const styles = { colour, size: "large" };
  const header = <Header styles={styles} />;
  styles.size = "small"; // 🔴 Bad: styles was already used in the JSX above
  const footer = <Footer styles={styles} />;
  return (
    <>
      {header}
      <Content />
      {footer}
    </>
  );
}
```

```js {4}
function Page({ colour }) {
  const headerStyles = { colour, size: "large" };
  const header = <Header styles={headerStyles} />;
  const footerStyles = { colour, size: "small" }; // ✅ Good: we created a new value
  const footer = <Footer styles={footerStyles} />;
  return (
    <>
      {header}
      <Content />
      {footer}
    </>
  );
}
```
