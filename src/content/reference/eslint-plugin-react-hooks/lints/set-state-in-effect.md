---
title: set-state-in-effect
---

<Intro>

Kiểm tra việc gọi setState đồng bộ trong effect, điều có thể dẫn đến re-render làm giảm hiệu suất.

</Intro>

## Chi tiết quy tắc {/*rule-details*/}

Đặt state ngay bên trong effect buộc React phải khởi động lại toàn bộ chu trình render. Khi bạn cập nhật state trong effect, React phải re-render component, áp dụng thay đổi vào DOM, rồi chạy effect lần nữa. Điều này tạo ra một lượt render thêm mà có thể tránh được bằng cách biến đổi dữ liệu trực tiếp trong quá trình render hoặc suy ra state từ props. Hãy biến đổi dữ liệu ở cấp cao nhất của component. Code này sẽ tự nhiên chạy lại khi props hoặc state thay đổi mà không kích hoạt thêm chu trình render.

Lệnh gọi `setState` đồng bộ trong effect kích hoạt re-render ngay lập tức trước khi trình duyệt có thể vẽ, gây ra vấn đề hiệu suất và hiện tượng giật hình. React phải render hai lần: một lần để áp dụng cập nhật state, rồi lần nữa sau khi effect chạy. Việc render đôi này lãng phí khi có thể đạt được cùng kết quả với một lần render duy nhất.

Trong nhiều trường hợp, bạn cũng có thể không cần effect. Vui lòng xem [Bạn có thể không cần Effect](/learn/you-might-not-need-an-effect) để biết thêm.

## Các vi phạm phổ biến {/*common-violations*/}

Quy tắc này bắt nhiều pattern nơi setState đồng bộ được sử dụng không cần thiết:

- Đặt state loading đồng bộ
- Suy ra state từ props trong effect
- Biến đổi dữ liệu trong effect thay vì render

### Không hợp lệ {/*invalid*/}

Ví dụ về code không đúng cho quy tắc này:

```js
// ❌ setState đồng bộ trong effect
function Component({data}) {
  const [items, setItems] = useState([]);

  useEffect(() => {
    setItems(data); // Render thêm, dùng state ban đầu thay vì
  }, [data]);
}

// ❌ Đặt state loading đồng bộ
function Component() {
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    setLoading(true); // Đồng bộ, gây render thêm
    fetchData().then(() => setLoading(false));
  }, []);
}

// ❌ Biến đổi dữ liệu trong effect
function Component({rawData}) {
  const [processed, setProcessed] = useState([]);

  useEffect(() => {
    setProcessed(rawData.map(transform)); // Nên tính trong render
  }, [rawData]);
}

// ❌ Suy ra state từ props
function Component({selectedId, items}) {
  const [selected, setSelected] = useState(null);

  useEffect(() => {
    setSelected(items.find(i => i.id === selectedId));
  }, [selectedId, items]);
}
```

### Hợp lệ {/*valid*/}

Ví dụ về code đúng cho quy tắc này:

```js
// ✅ setState trong effect ổn nếu giá trị đến từ ref
function Tooltip() {
  const ref = useRef(null);
  const [tooltipHeight, setTooltipHeight] = useState(0);

  useLayoutEffect(() => {
    const { height } = ref.current.getBoundingClientRect();
    setTooltipHeight(height);
  }, []);
}

// ✅ Tính toán trong quá trình render
function Component({selectedId, items}) {
  const selected = items.find(i => i.id === selectedId);
  return <div>{selected?.name}</div>;
}
```

**Khi một thứ có thể tính toán từ props hoặc state hiện có, đừng đặt nó vào state.** Thay vào đó, tính toán nó trong quá trình render. Điều này làm code nhanh hơn, đơn giản hơn, và ít lỗi hơn. Tìm hiểu thêm trong [Bạn có thể không cần Effect](/learn/you-might-not-need-an-effect).
