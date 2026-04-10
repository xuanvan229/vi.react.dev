---
title: "use memo"
titleForTitleTag: "Directive 'use memo'"
---

<Intro>

`"use memo"` đánh dấu một hàm để React Compiler tối ưu hóa.

</Intro>

<Note>

Trong hầu hết trường hợp, bạn không cần `"use memo"`. Nó chủ yếu cần thiết trong chế độ `annotation` nơi bạn phải đánh dấu rõ ràng các hàm để tối ưu hóa. Trong chế độ `infer`, compiler tự động phát hiện các component và hook theo mẫu đặt tên (PascalCase cho component, tiền tố `use` cho hook). Nếu một component hoặc hook không được compile trong chế độ `infer`, bạn nên sửa quy ước đặt tên thay vì ép buộc compile bằng `"use memo"`.

</Note>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `"use memo"` {/*use-memo*/}

Thêm `"use memo"` ở đầu hàm để đánh dấu nó cho việc tối ưu hóa của React Compiler.

```js {1}
function MyComponent() {
  "use memo";
  // ...
}
```

Khi một hàm chứa `"use memo"`, React Compiler sẽ phân tích và tối ưu hóa nó trong quá trình build. Compiler sẽ tự động memo hóa các giá trị và component để ngăn chặn việc tính toán lại và render lại không cần thiết.

#### Lưu ý {/*caveats*/}

* `"use memo"` phải ở ngay đầu thân hàm, trước bất kỳ import hoặc mã nào khác (comment thì được).
* Directive phải được viết bằng dấu ngoặc kép đơn hoặc kép, không phải backtick.
* Directive phải khớp chính xác `"use memo"`.
* Chỉ directive đầu tiên trong hàm được xử lý; các directive bổ sung bị bỏ qua.
* Hiệu lực của directive phụ thuộc vào cài đặt [`compilationMode`](/reference/react-compiler/compilationMode) của bạn.

### Cách `"use memo"` đánh dấu các hàm để tối ưu hóa {/*how-use-memo-marks*/}

Trong một ứng dụng React sử dụng React Compiler, các hàm được phân tích tại thời điểm build để xác định xem chúng có thể được tối ưu hóa hay không. Theo mặc định, compiler tự động suy luận component nào cần memo hóa, nhưng điều này có thể phụ thuộc vào cài đặt [`compilationMode`](/reference/react-compiler/compilationMode) nếu bạn đã thiết lập.

`"use memo"` đánh dấu rõ ràng một hàm để tối ưu hóa, ghi đè hành vi mặc định:

* Trong chế độ `annotation`: Chỉ các hàm có `"use memo"` được tối ưu hóa
* Trong chế độ `infer`: Compiler sử dụng phương pháp suy luận, nhưng `"use memo"` buộc tối ưu hóa
* Trong chế độ `all`: Mọi thứ được tối ưu hóa theo mặc định, khiến `"use memo"` trở nên thừa

Directive tạo ra ranh giới rõ ràng trong codebase giữa mã được tối ưu hóa và không được tối ưu hóa, cho bạn khả năng kiểm soát chi tiết quá trình compile.

### Khi nào sử dụng `"use memo"` {/*when-to-use*/}

Bạn nên cân nhắc sử dụng `"use memo"` khi:

#### Bạn đang sử dụng chế độ annotation {/*annotation-mode-use*/}
Trong `compilationMode: 'annotation'`, directive là bắt buộc cho bất kỳ hàm nào bạn muốn tối ưu hóa:

```js
// ✅ Component này sẽ được tối ưu hóa
function OptimizedList() {
  "use memo";
  // ...
}

// ❌ Component này sẽ không được tối ưu hóa
function SimpleWrapper() {
  // ...
}
```

#### Bạn đang áp dụng React Compiler dần dần {/*gradual-adoption*/}
Bắt đầu với chế độ `annotation` và tối ưu hóa có chọn lọc các component ổn định:

```js
// Bắt đầu bằng cách tối ưu hóa các component lá
function Button({ onClick, children }) {
  "use memo";
  // ...
}

// Dần dần di chuyển lên cây component khi bạn xác nhận hành vi
function ButtonGroup({ buttons }) {
  "use memo";
  // ...
}
```

---

## Cách sử dụng {/*usage*/}

### Làm việc với các chế độ compile khác nhau {/*compilation-modes*/}

Hành vi của `"use memo"` thay đổi dựa trên cấu hình compiler:

```js
// babel.config.js
module.exports = {
  plugins: [
    ['babel-plugin-react-compiler', {
      compilationMode: 'annotation' // hoặc 'infer' hoặc 'all'
    }]
  ]
};
```

#### Chế độ annotation {/*annotation-mode-example*/}
```js
// ✅ Được tối ưu hóa với "use memo"
function ProductCard({ product }) {
  "use memo";
  // ...
}

// ❌ Không được tối ưu hóa (không có directive)
function ProductList({ products }) {
  // ...
}
```

#### Chế độ infer (mặc định) {/*infer-mode-example*/}
```js
// Tự động được memo hóa vì đặt tên giống Component
function ComplexDashboard({ data }) {
  // ...
}

// Bị bỏ qua: Không đặt tên giống Component
function simpleDisplay({ text }) {
  // ...
}
```

Trong chế độ `infer`, compiler tự động phát hiện các component và hook theo mẫu đặt tên (PascalCase cho component, tiền tố `use` cho hook). Nếu một component hoặc hook không được compile trong chế độ `infer`, bạn nên sửa quy ước đặt tên thay vì ép buộc compile bằng `"use memo"`.

---

## Xử lý sự cố {/*troubleshooting*/}

### Xác minh tối ưu hóa {/*verifying-optimization*/}

Để xác nhận component của bạn đang được tối ưu hóa:

1. Kiểm tra đầu ra đã compile trong bản build
2. Sử dụng React DevTools để kiểm tra huy hiệu Memo ✨

### Xem thêm {/*see-also*/}

* [`"use no memo"`](/reference/react-compiler/directives/use-no-memo) - Loại khỏi danh sách compile
* [`compilationMode`](/reference/react-compiler/compilationMode) - Cấu hình hành vi compile
* [React Compiler](/learn/react-compiler) - Hướng dẫn bắt đầu
