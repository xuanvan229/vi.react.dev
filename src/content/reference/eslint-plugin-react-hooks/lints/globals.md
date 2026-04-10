---
title: globals
---

<Intro>

Kiểm tra việc gán/thay đổi biến toàn cục trong quá trình render, một phần của việc đảm bảo rằng [side effect phải chạy bên ngoài render](/reference/rules/components-and-hooks-must-be-pure#side-effects-must-run-outside-of-render).

</Intro>

## Chi tiết quy tắc {/*rule-details*/}

Biến toàn cục tồn tại ngoài tầm kiểm soát của React. Khi bạn thay đổi chúng trong quá trình render, bạn phá vỡ giả định của React rằng render là thuần túy. Điều này có thể khiến component hoạt động khác nhau trong development so với production, phá vỡ Fast Refresh, và làm cho ứng dụng của bạn không thể tối ưu hóa với các tính năng như React Compiler.

### Không hợp lệ {/*invalid*/}

Ví dụ về code không đúng cho quy tắc này:

```js
// ❌ Bộ đếm toàn cục
let renderCount = 0;
function Component() {
  renderCount++; // Thay đổi biến toàn cục
  return <div>Count: {renderCount}</div>;
}

// ❌ Thay đổi thuộc tính window
function Component({userId}) {
  window.currentUser = userId; // Thay đổi biến toàn cục
  return <div>User: {userId}</div>;
}

// ❌ Push vào mảng toàn cục
const events = [];
function Component({event}) {
  events.push(event); // Thay đổi mảng toàn cục
  return <div>Events: {events.length}</div>;
}

// ❌ Thao tác cache
const cache = {};
function Component({id}) {
  if (!cache[id]) {
    cache[id] = fetchData(id); // Thay đổi cache trong quá trình render
  }
  return <div>{cache[id]}</div>;
}
```

### Hợp lệ {/*valid*/}

Ví dụ về code đúng cho quy tắc này:

```js
// ✅ Dùng state cho bộ đếm
function Component() {
  const [clickCount, setClickCount] = useState(0);

  const handleClick = () => {
    setClickCount(c => c + 1);
  };

  return (
    <button onClick={handleClick}>
      Đã nhấn: {clickCount} lần
    </button>
  );
}

// ✅ Dùng context cho giá trị toàn cục
function Component() {
  const user = useContext(UserContext);
  return <div>User: {user.id}</div>;
}

// ✅ Đồng bộ state bên ngoài với React
function Component({title}) {
  useEffect(() => {
    document.title = title; // OK trong effect
  }, [title]);

  return <div>Trang: {title}</div>;
}
```
