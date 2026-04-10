---
title: Các quy tắc của Hooks
---

<Intro>
Hooks được định nghĩa bằng các hàm JavaScript, nhưng chúng đại diện cho một loại logic UI có thể tái sử dụng đặc biệt với các hạn chế về nơi chúng có thể được gọi.
</Intro>

<InlineToc />

---

## Chỉ gọi Hooks ở cấp cao nhất {/*only-call-hooks-at-the-top-level*/}

Các hàm có tên bắt đầu bằng `use` được gọi là [*Hooks*](/reference/react) trong React.

**Đừng gọi Hooks bên trong vòng lặp, điều kiện, hàm lồng nhau, hoặc các khối `try`/`catch`/`finally`.** Thay vào đó, luôn sử dụng Hooks ở cấp cao nhất của hàm React của bạn, trước bất kỳ lệnh return sớm nào. Bạn chỉ có thể gọi Hooks trong khi React đang render một function component:

* ✅ Gọi chúng ở cấp cao nhất trong thân của một [function component](/learn/your-first-component).
* ✅ Gọi chúng ở cấp cao nhất trong thân của một [custom Hook](/learn/reusing-logic-with-custom-hooks).

```js{2-3,8-9}
function Counter() {
  // ✅ Good: top-level in a function component
  const [count, setCount] = useState(0);
  // ...
}

function useWindowWidth() {
  // ✅ Good: top-level in a custom Hook
  const [width, setWidth] = useState(window.innerWidth);
  // ...
}
```

**Không** được hỗ trợ gọi Hooks (các hàm bắt đầu bằng `use`) trong bất kỳ trường hợp nào khác, ví dụ:

* 🔴 Không gọi Hooks bên trong điều kiện hoặc vòng lặp.
* 🔴 Không gọi Hooks sau một câu lệnh `return` có điều kiện.
* 🔴 Không gọi Hooks trong event handlers.
* 🔴 Không gọi Hooks trong class components.
* 🔴 Không gọi Hooks bên trong các hàm được truyền cho `useMemo`, `useReducer`, hoặc `useEffect`.
* 🔴 Không gọi Hooks bên trong các khối `try`/`catch`/`finally`.

Nếu bạn vi phạm các quy tắc này, bạn có thể thấy lỗi này.

```js{3-4,11-12,20-21}
function Bad({ cond }) {
  if (cond) {
    // 🔴 Bad: inside a condition (to fix, move it outside!)
    const theme = useContext(ThemeContext);
  }
  // ...
}

function Bad() {
  for (let i = 0; i < 10; i++) {
    // 🔴 Bad: inside a loop (to fix, move it outside!)
    const theme = useContext(ThemeContext);
  }
  // ...
}

function Bad({ cond }) {
  if (cond) {
    return;
  }
  // 🔴 Bad: after a conditional return (to fix, move it before the return!)
  const theme = useContext(ThemeContext);
  // ...
}

function Bad() {
  function handleClick() {
    // 🔴 Bad: inside an event handler (to fix, move it outside!)
    const theme = useContext(ThemeContext);
  }
  // ...
}

function Bad() {
  const style = useMemo(() => {
    // 🔴 Bad: inside useMemo (to fix, move it outside!)
    const theme = useContext(ThemeContext);
    return createStyle(theme);
  });
  // ...
}

class Bad extends React.Component {
  render() {
    // 🔴 Bad: inside a class component (to fix, write a function component instead of a class!)
    useEffect(() => {})
    // ...
  }
}

function Bad() {
  try {
    // 🔴 Bad: inside try/catch/finally block (to fix, move it outside!)
    const [x, setX] = useState(0);
  } catch {
    const [x, setX] = useState(1);
  }
}
```

Bạn có thể sử dụng [plugin `eslint-plugin-react-hooks`](https://www.npmjs.com/package/eslint-plugin-react-hooks) để phát hiện các lỗi này.

<Note>

[Custom Hooks](/learn/reusing-logic-with-custom-hooks) *có thể* gọi các Hooks khác (đó là toàn bộ mục đích của chúng). Điều này hoạt động vì custom Hooks cũng chỉ được gọi trong khi một function component đang render.

</Note>

---

## Chỉ gọi Hooks từ các hàm React {/*only-call-hooks-from-react-functions*/}

Đừng gọi Hooks từ các hàm JavaScript thông thường. Thay vào đó, bạn có thể:

✅ Gọi Hooks từ React function components.
✅ Gọi Hooks từ [custom Hooks](/learn/reusing-logic-with-custom-hooks#extracting-your-own-custom-hook-from-a-component).

Bằng cách tuân theo quy tắc này, bạn đảm bảo rằng tất cả logic stateful trong một component đều hiển thị rõ ràng từ source code của nó.

```js {2,5}
function FriendList() {
  const [onlineStatus, setOnlineStatus] = useOnlineStatus(); // ✅
}

function setOnlineStatus() { // ❌ Not a component or custom Hook!
  const [onlineStatus, setOnlineStatus] = useOnlineStatus();
}
```
