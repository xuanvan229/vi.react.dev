---
title: exhaustive-deps
---

<Intro>

Kiểm tra mảng dependency cho React hook chứa tất cả các dependency cần thiết.

</Intro>

## Chi tiết quy tắc {/*rule-details*/}

Các React hook như `useEffect`, `useMemo`, và `useCallback` nhận mảng dependency. Khi một giá trị được tham chiếu bên trong các hook này không được bao gồm trong mảng dependency, React sẽ không chạy lại effect hoặc tính toán lại giá trị khi dependency đó thay đổi. Điều này gây ra closure cũ nơi hook sử dụng các giá trị đã lỗi thời.

## Các vi phạm phổ biến {/*common-violations*/}

Lỗi này thường xảy ra khi bạn cố "đánh lừa" React về các dependency để kiểm soát thời điểm effect chạy. Effect nên đồng bộ hóa component của bạn với các hệ thống bên ngoài. Mảng dependency cho React biết effect sử dụng những giá trị nào, để React biết khi nào cần đồng bộ lại.

Nếu bạn thấy mình đang chiến đấu với linter, có thể bạn cần tái cấu trúc code. Xem [Loại bỏ dependency của Effect](/learn/removing-effect-dependencies) để tìm hiểu cách làm.

### Không hợp lệ {/*invalid*/}

Ví dụ về code không đúng cho quy tắc này:

```js
// ❌ Thiếu dependency
useEffect(() => {
  console.log(count);
}, []); // Thiếu 'count'

// ❌ Thiếu prop
useEffect(() => {
  fetchUser(userId);
}, []); // Thiếu 'userId'

// ❌ Dependency không đầy đủ
useMemo(() => {
  return items.sort(sortOrder);
}, [items]); // Thiếu 'sortOrder'
```

### Hợp lệ {/*valid*/}

Ví dụ về code đúng cho quy tắc này:

```js
// ✅ Tất cả dependency được bao gồm
useEffect(() => {
  console.log(count);
}, [count]);

// ✅ Tất cả dependency được bao gồm
useEffect(() => {
  fetchUser(userId);
}, [userId]);
```

## Xử lý sự cố {/*troubleshooting*/}

### Thêm dependency là hàm gây vòng lặp vô hạn {/*function-dependency-loops*/}

Bạn có một effect, nhưng bạn đang tạo một hàm mới mỗi lần render:

```js
// ❌ Gây vòng lặp vô hạn
const logItems = () => {
  console.log(items);
};

useEffect(() => {
  logItems();
}, [logItems]); // Vòng lặp vô hạn!
```

Trong hầu hết trường hợp, bạn không cần effect. Gọi hàm ở nơi hành động xảy ra thay vì dùng effect:

```js
// ✅ Gọi từ event handler
const logItems = () => {
  console.log(items);
};

return <button onClick={logItems}>Log</button>;

// ✅ Hoặc tính toán trong quá trình render nếu không có side effect
items.forEach(item => {
  console.log(item);
});
```

Nếu bạn thực sự cần effect (ví dụ, để subscribe một thứ bên ngoài), hãy làm cho dependency ổn định:

```js
// ✅ useCallback giữ tham chiếu hàm ổn định
const logItems = useCallback(() => {
  console.log(items);
}, [items]);

useEffect(() => {
  logItems();
}, [logItems]);

// ✅ Hoặc đưa logic trực tiếp vào effect
useEffect(() => {
  console.log(items);
}, [items]);
```

### Chỉ chạy effect một lần {/*effect-on-mount*/}

Bạn muốn chạy effect một lần khi mount, nhưng linter phàn nàn về dependency thiếu:

```js
// ❌ Thiếu dependency
useEffect(() => {
  sendAnalytics(userId);
}, []); // Thiếu 'userId'
```

Hoặc bao gồm dependency (khuyến nghị) hoặc sử dụng ref nếu bạn thực sự cần chạy một lần:

```js
// ✅ Bao gồm dependency
useEffect(() => {
  sendAnalytics(userId);
}, [userId]);

// ✅ Hoặc sử dụng ref guard bên trong effect
const sent = useRef(false);

useEffect(() => {
  if (sent.current) {
    return;
  }

  sent.current = true;
  sendAnalytics(userId);
}, [userId]);
```

## Tùy chọn {/*options*/}

Bạn có thể cấu hình các effect hook tùy chỉnh sử dụng cài đặt ESLint chung (có sẵn trong `eslint-plugin-react-hooks` 6.1.1 trở lên):

```js
{
  "settings": {
    "react-hooks": {
      "additionalEffectHooks": "(useMyEffect|useCustomEffect)"
    }
  }
}
```

- `additionalEffectHooks`: Pattern regex khớp với các hook tùy chỉnh cần được kiểm tra dependency đầy đủ. Cấu hình này được chia sẻ giữa tất cả các quy tắc `react-hooks`.

Để tương thích ngược, quy tắc này cũng chấp nhận tùy chọn cấp quy tắc:

```js
{
  "rules": {
    "react-hooks/exhaustive-deps": ["warn", {
      "additionalHooks": "(useMyCustomHook|useAnotherHook)"
    }]
  }
}
```

- `additionalHooks`: Regex cho các hook cần được kiểm tra dependency đầy đủ. **Lưu ý:** Nếu tùy chọn cấp quy tắc này được chỉ định, nó sẽ ưu tiên hơn cấu hình `settings` chung.
