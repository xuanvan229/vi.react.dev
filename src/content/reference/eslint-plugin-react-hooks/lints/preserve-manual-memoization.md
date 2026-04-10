---
title: preserve-manual-memoization
---

<Intro>

Kiểm tra rằng memoization thủ công hiện có được compiler bảo toàn. React Compiler sẽ chỉ biên dịch component và hook nếu suy luận của nó [khớp hoặc vượt qua memoization thủ công hiện có](/learn/react-compiler/introduction#what-should-i-do-about-usememo-usecallback-and-reactmemo).

</Intro>

## Chi tiết quy tắc {/*rule-details*/}

React Compiler bảo toàn các lệnh gọi `useMemo`, `useCallback`, và `React.memo` hiện có của bạn. Nếu bạn đã memoize thủ công một thứ gì đó, compiler giả định bạn có lý do chính đáng và sẽ không loại bỏ nó. Tuy nhiên, dependency không đầy đủ ngăn compiler hiểu luồng dữ liệu code của bạn và áp dụng các tối ưu hóa tiếp theo.

### Không hợp lệ {/*invalid*/}

Ví dụ về code không đúng cho quy tắc này:

```js
// ❌ Thiếu dependency trong useMemo
function Component({ data, filter }) {
  const filtered = useMemo(
    () => data.filter(filter),
    [data] // Thiếu dependency 'filter'
  );

  return <List items={filtered} />;
}

// ❌ Thiếu dependency trong useCallback
function Component({ onUpdate, value }) {
  const handleClick = useCallback(() => {
    onUpdate(value);
  }, [onUpdate]); // Thiếu 'value'

  return <button onClick={handleClick}>Cập nhật</button>;
}
```

### Hợp lệ {/*valid*/}

Ví dụ về code đúng cho quy tắc này:

```js
// ✅ Đầy đủ dependency
function Component({ data, filter }) {
  const filtered = useMemo(
    () => data.filter(filter),
    [data, filter] // Tất cả dependency được bao gồm
  );

  return <List items={filtered} />;
}

// ✅ Hoặc để compiler xử lý
function Component({ data, filter }) {
  // Không cần memoization thủ công
  const filtered = data.filter(filter);
  return <List items={filtered} />;
}
```

## Xử lý sự cố {/*troubleshooting*/}

### Tôi có nên loại bỏ memoization thủ công không? {/*remove-manual-memoization*/}

Bạn có thể tự hỏi liệu React Compiler có làm memoization thủ công trở nên không cần thiết:

```js
// Tôi còn cần cái này không?
function Component({items, sortBy}) {
  const sorted = useMemo(() => {
    return [...items].sort((a, b) => {
      return a[sortBy] - b[sortBy];
    });
  }, [items, sortBy]);

  return <List items={sorted} />;
}
```

Bạn có thể loại bỏ nó an toàn nếu sử dụng React Compiler:

```js
// ✅ Tốt hơn: Để compiler tối ưu hóa
function Component({items, sortBy}) {
  const sorted = [...items].sort((a, b) => {
    return a[sortBy] - b[sortBy];
  });

  return <List items={sorted} />;
}
```
