---
title: static-components
---

<Intro>

Kiểm tra rằng component là tĩnh, không được tạo lại mỗi lần render. Component được tạo lại động có thể reset state và kích hoạt re-render quá mức.

</Intro>

## Chi tiết quy tắc {/*rule-details*/}

Component được định nghĩa bên trong component khác sẽ được tạo lại mỗi lần render. React coi mỗi cái là một kiểu component hoàn toàn mới, unmount cái cũ và mount cái mới, hủy tất cả state và các node DOM trong quá trình đó.

### Không hợp lệ {/*invalid*/}

Ví dụ về code không đúng cho quy tắc này:

```js
// ❌ Component được định nghĩa bên trong component
function Parent() {
  const ChildComponent = () => { // Component mới mỗi lần render!
    const [count, setCount] = useState(0);
    return <button onClick={() => setCount(count + 1)}>{count}</button>;
  };

  return <ChildComponent />; // State reset mỗi lần render
}

// ❌ Tạo component động
function Parent({type}) {
  const Component = type === 'button'
    ? () => <button>Nhấn</button>
    : () => <div>Văn bản</div>;

  return <Component />;
}
```

### Hợp lệ {/*valid*/}

Ví dụ về code đúng cho quy tắc này:

```js
// ✅ Component ở cấp module
const ButtonComponent = () => <button>Nhấn</button>;
const TextComponent = () => <div>Văn bản</div>;

function Parent({type}) {
  const Component = type === 'button'
    ? ButtonComponent  // Tham chiếu component có sẵn
    : TextComponent;

  return <Component />;
}
```

## Xử lý sự cố {/*troubleshooting*/}

### Tôi cần render component khác nhau có điều kiện {/*conditional-components*/}

Bạn có thể định nghĩa component bên trong để truy cập state cục bộ:

```js {expectedErrors: {'react-compiler': [13]}}
// ❌ Sai: Component bên trong để truy cập state cha
function Parent() {
  const [theme, setTheme] = useState('light');

  function ThemedButton() { // Được tạo lại mỗi lần render!
    return (
      <button className={theme}>
        Nhấn vào đây
      </button>
    );
  }

  return <ThemedButton />;
}
```

Truyền dữ liệu qua props thay vì:

```js
// ✅ Tốt hơn: Truyền props cho component tĩnh
function ThemedButton({theme}) {
  return (
    <button className={theme}>
      Nhấn vào đây
    </button>
  );
}

function Parent() {
  const [theme, setTheme] = useState('light');
  return <ThemedButton theme={theme} />;
}
```

<Note>

Nếu bạn thấy mình muốn định nghĩa component bên trong component khác để truy cập biến cục bộ, đó là dấu hiệu bạn nên truyền props thay vì. Điều này làm component dễ tái sử dụng và dễ kiểm thử hơn.

</Note>
