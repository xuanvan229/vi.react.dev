---
title: rules-of-hooks
---

<Intro>

Kiểm tra rằng component và hook tuân theo [Quy tắc của Hook](/reference/rules/rules-of-hooks).

</Intro>

## Chi tiết quy tắc {/*rule-details*/}

React dựa vào thứ tự gọi hook để bảo toàn state đúng cách giữa các lần render. Mỗi lần component render, React mong đợi chính xác các hook giống nhau được gọi theo đúng thứ tự. Khi hook được gọi có điều kiện hoặc trong vòng lặp, React mất dấu state nào tương ứng với lệnh gọi hook nào, dẫn đến lỗi như state không khớp và lỗi "Rendered fewer/more hooks than expected".

## Các vi phạm phổ biến {/*common-violations*/}

Các pattern này vi phạm Quy tắc của Hook:

- **Hook trong điều kiện** (`if`/`else`, toán tử ba ngôi, `&&`/`||`)
- **Hook trong vòng lặp** (`for`, `while`, `do-while`)
- **Hook sau return sớm**
- **Hook trong callback/event handler**
- **Hook trong hàm async**
- **Hook trong phương thức class**
- **Hook ở cấp module**

<Note>

### Hook `use` {/*use-hook*/}

Hook `use` khác với các React hook khác. Bạn có thể gọi nó có điều kiện và trong vòng lặp:

```js
// ✅ `use` có thể có điều kiện
if (shouldFetch) {
  const data = use(fetchPromise);
}

// ✅ `use` có thể trong vòng lặp
for (const promise of promises) {
  results.push(use(promise));
}
```

Tuy nhiên, `use` vẫn có hạn chế:
- Không thể bọc trong try/catch
- Phải được gọi bên trong component hoặc hook

Tìm hiểu thêm: [Tham chiếu API `use`](/reference/react/use)

</Note>

### Không hợp lệ {/*invalid*/}

Ví dụ về code không đúng cho quy tắc này:

```js
// ❌ Hook trong điều kiện
if (isLoggedIn) {
  const [user, setUser] = useState(null);
}

// ❌ Hook sau return sớm
if (!data) return <Loading />;
const [processed, setProcessed] = useState(data);

// ❌ Hook trong callback
<button onClick={() => {
  const [clicked, setClicked] = useState(false);
}}/>

// ❌ `use` trong try/catch
try {
  const data = use(promise);
} catch (e) {
  // xử lý lỗi
}

// ❌ Hook ở cấp module
const globalState = useState(0); // Ngoài component
```

### Hợp lệ {/*valid*/}

Ví dụ về code đúng cho quy tắc này:

```js
function Component({ isSpecial, shouldFetch, fetchPromise }) {
  // ✅ Hook ở cấp cao nhất
  const [count, setCount] = useState(0);
  const [name, setName] = useState('');

  if (!isSpecial) {
    return null;
  }

  if (shouldFetch) {
    // ✅ `use` có thể có điều kiện
    const data = use(fetchPromise);
    return <div>{data}</div>;
  }

  return <div>{name}: {count}</div>;
}
```

## Xử lý sự cố {/*troubleshooting*/}

### Tôi muốn fetch dữ liệu dựa trên điều kiện {/*conditional-data-fetching*/}

Bạn đang cố gọi useEffect có điều kiện:

```js
// ❌ Hook có điều kiện
if (isLoggedIn) {
  useEffect(() => {
    fetchUserData();
  }, []);
}
```

Gọi hook không điều kiện, kiểm tra điều kiện bên trong:

```js
// ✅ Điều kiện bên trong hook
useEffect(() => {
  if (isLoggedIn) {
    fetchUserData();
  }
}, [isLoggedIn]);
```

<Note>

Có những cách tốt hơn để fetch dữ liệu thay vì trong useEffect. Hãy xem xét sử dụng TanStack Query, useSWR, hoặc React Router 6.4+ để fetch dữ liệu. Các giải pháp này xử lý việc loại bỏ request trùng lặp, cache response, và tránh waterfall mạng.

Tìm hiểu thêm: [Fetch dữ liệu](/learn/synchronizing-with-effects#fetching-data)

</Note>

### Tôi cần state khác nhau cho các tình huống khác nhau {/*conditional-state-initialization*/}

Bạn đang cố khởi tạo state có điều kiện:

```js
// ❌ State có điều kiện
if (userType === 'admin') {
  const [permissions, setPermissions] = useState(adminPerms);
} else {
  const [permissions, setPermissions] = useState(userPerms);
}
```

Luôn gọi useState, đặt giá trị khởi tạo có điều kiện:

```js
// ✅ Giá trị khởi tạo có điều kiện
const [permissions, setPermissions] = useState(
  userType === 'admin' ? adminPerms : userPerms
);
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

- `additionalEffectHooks`: Pattern regex khớp với các hook tùy chỉnh cần được coi là effect. Điều này cho phép `useEffectEvent` và các hàm event tương tự được gọi từ các effect hook tùy chỉnh của bạn.

Cấu hình chung này được sử dụng bởi cả quy tắc `rules-of-hooks` và `exhaustive-deps`, đảm bảo hành vi nhất quán cho tất cả lint liên quan đến hook.
