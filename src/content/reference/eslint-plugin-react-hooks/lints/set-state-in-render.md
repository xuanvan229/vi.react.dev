---
title: set-state-in-render
---

<Intro>

Kiểm tra việc set state không điều kiện trong quá trình render, điều có thể kích hoạt thêm render và có thể gây vòng lặp render vô hạn.

</Intro>

## Chi tiết quy tắc {/*rule-details*/}

Gọi `setState` trong quá trình render không điều kiện sẽ kích hoạt một lần render khác trước khi lần hiện tại hoàn thành. Điều này tạo ra vòng lặp vô hạn làm crash ứng dụng.

## Các vi phạm phổ biến {/*common-violations*/}

### Không hợp lệ {/*invalid*/}

```js {expectedErrors: {'react-compiler': [4]}}
// ❌ setState không điều kiện trực tiếp trong render
function Component({value}) {
  const [count, setCount] = useState(0);
  setCount(value); // Vòng lặp vô hạn!
  return <div>{count}</div>;
}
```

### Hợp lệ {/*valid*/}

```js
// ✅ Tính toán trong render
function Component({items}) {
  const sorted = [...items].sort(); // Chỉ cần tính trong render
  return <ul>{sorted.map(/*...*/)}</ul>;
}

// ✅ Set state trong event handler
function Component() {
  const [count, setCount] = useState(0);
  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}

// ✅ Tính từ props thay vì set state
function Component({user}) {
  const name = user?.name || '';
  const email = user?.email || '';
  return <div>{name}</div>;
}

// ✅ Suy ra state có điều kiện từ props và state từ các lần render trước
function Component({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selection, setSelection] = useState(null);

  const [prevItems, setPrevItems] = useState(items);
  if (items !== prevItems) { // Điều kiện này làm nó hợp lệ
    setPrevItems(items);
    setSelection(null);
  }
  // ...
}
```

## Xử lý sự cố {/*troubleshooting*/}

### Tôi muốn đồng bộ state với prop {/*clamp-state-to-prop*/}

Một vấn đề phổ biến là cố "sửa" state sau khi render. Giả sử bạn muốn giữ bộ đếm không vượt quá prop `max`:

```js
// ❌ Sai: giới hạn trong render
function Counter({max}) {
  const [count, setCount] = useState(0);

  if (count > max) {
    setCount(max);
  }

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```

Ngay khi `count` vượt quá `max`, vòng lặp vô hạn được kích hoạt.

Thay vào đó, thường tốt hơn là chuyển logic này vào event (nơi state được set lần đầu). Ví dụ, bạn có thể áp dụng giá trị tối đa tại thời điểm cập nhật state:

```js
// ✅ Giới hạn khi cập nhật
function Counter({max}) {
  const [count, setCount] = useState(0);

  const increment = () => {
    setCount(current => Math.min(current + 1, max));
  };

  return <button onClick={increment}>{count}</button>;
}
```

Bây giờ setter chỉ chạy để phản hồi click, React hoàn thành render bình thường, và `count` không bao giờ vượt quá `max`.

Trong những trường hợp hiếm, bạn có thể cần điều chỉnh state dựa trên thông tin từ các lần render trước. Đối với những trường hợp đó, hãy theo [pattern này](https://react.dev/reference/react/useState#storing-information-from-previous-renders) để set state có điều kiện.
