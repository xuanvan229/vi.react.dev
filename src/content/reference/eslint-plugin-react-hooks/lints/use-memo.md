---
title: use-memo
---

<Intro>

Kiểm tra rằng hook `useMemo` được sử dụng với giá trị trả về. Xem [tài liệu `useMemo`](/reference/react/useMemo) để biết thêm.

</Intro>

## Chi tiết quy tắc {/*rule-details*/}

`useMemo` dùng để tính toán và cache các giá trị tốn kém, không phải cho side effect. Không có giá trị trả về, `useMemo` trả về `undefined`, điều này đánh bại mục đích của nó và có thể cho thấy bạn đang sử dụng sai hook.

### Không hợp lệ {/*invalid*/}

Ví dụ về code không đúng cho quy tắc này:

```js {expectedErrors: {'react-compiler': [3]}}
// ❌ Không có giá trị trả về
function Component({ data }) {
  const processed = useMemo(() => {
    data.forEach(item => console.log(item));
    // Thiếu return!
  }, [data]);

  return <div>{processed}</div>; // Luôn là undefined
}
```

### Hợp lệ {/*valid*/}

Ví dụ về code đúng cho quy tắc này:

```js
// ✅ Trả về giá trị đã tính toán
function Component({ data }) {
  const processed = useMemo(() => {
    return data.map(item => item * 2);
  }, [data]);

  return <div>{processed}</div>;
}
```

## Xử lý sự cố {/*troubleshooting*/}

### Tôi cần chạy side effect khi dependency thay đổi {/*side-effects*/}

Bạn có thể cố sử dụng `useMemo` cho side effect:

{/* TODO(@poteto) fix compiler validation to check for unassigned useMemos */}
```js {expectedErrors: {'react-compiler': [4]}}
// ❌ Sai: Side effect trong useMemo
function Component({user}) {
  // Không có giá trị trả về, chỉ side effect
  useMemo(() => {
    analytics.track('UserViewed', {userId: user.id});
  }, [user.id]);

  // Không được gán cho biến
  useMemo(() => {
    return analytics.track('UserViewed', {userId: user.id});
  }, [user.id]);
}
```

Nếu side effect cần xảy ra để phản hồi tương tác người dùng, tốt nhất là đặt side effect cùng với event:

```js
// ✅ Tốt: Side effect trong event handler
function Component({user}) {
  const handleClick = () => {
    analytics.track('ButtonClicked', {userId: user.id});
    // Logic click khác...
  };

  return <button onClick={handleClick}>Nhấn vào đây</button>;
}
```

Nếu side effect đồng bộ React state với state bên ngoài (hoặc ngược lại), sử dụng `useEffect`:

```js
// ✅ Tốt: Đồng bộ hóa trong useEffect
function Component({theme}) {
  useEffect(() => {
    localStorage.setItem('preferredTheme', theme);
    document.body.className = theme;
  }, [theme]);

  return <div>Theme hiện tại: {theme}</div>;
}
```
