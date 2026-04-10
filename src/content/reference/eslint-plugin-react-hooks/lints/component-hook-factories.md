---
title: component-hook-factories
---

<Intro>

Kiểm tra việc sử dụng các hàm bậc cao định nghĩa component hoặc hook lồng nhau. Component và hook nên được định nghĩa ở cấp module.

</Intro>

## Chi tiết quy tắc {/*rule-details*/}

Việc định nghĩa component hoặc hook bên trong các hàm khác tạo ra các instance mới mỗi lần gọi. React coi mỗi cái là một component hoàn toàn khác, hủy và tạo lại toàn bộ cây component, mất tất cả state, và gây ra vấn đề hiệu suất.

### Không hợp lệ {/*invalid*/}

Ví dụ về code không đúng cho quy tắc này:

```js {expectedErrors: {'react-compiler': [14]}}
// ❌ Hàm factory tạo component
function createComponent(defaultValue) {
  return function Component() {
    // ...
  };
}

// ❌ Component được định nghĩa bên trong component
function Parent() {
  function Child() {
    // ...
  }

  return <Child />;
}

// ❌ Hàm factory cho hook
function createCustomHook(endpoint) {
  return function useData() {
    // ...
  };
}
```

### Hợp lệ {/*valid*/}

Ví dụ về code đúng cho quy tắc này:

```js
// ✅ Component được định nghĩa ở cấp module
function Component({ defaultValue }) {
  // ...
}

// ✅ Custom hook ở cấp module
function useData(endpoint) {
  // ...
}
```

## Xử lý sự cố {/*troubleshooting*/}

### Tôi cần hành vi component động {/*dynamic-behavior*/}

Bạn có thể nghĩ rằng bạn cần factory để tạo các component tùy chỉnh:

```js
// ❌ Sai: Pattern factory
function makeButton(color) {
  return function Button({children}) {
    return (
      <button style={{backgroundColor: color}}>
        {children}
      </button>
    );
  };
}

const RedButton = makeButton('red');
const BlueButton = makeButton('blue');
```

Thay vào đó hãy truyền [JSX như children](/learn/passing-props-to-a-component#passing-jsx-as-children):

```js
// ✅ Tốt hơn: Truyền JSX như children
function Button({color, children}) {
  return (
    <button style={{backgroundColor: color}}>
      {children}
    </button>
  );
}

function App() {
  return (
    <>
      <Button color="red">Red</Button>
      <Button color="blue">Blue</Button>
    </>
  );
}
```
