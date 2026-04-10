---
title: "use no memo"
titleForTitleTag: "Directive 'use no memo'"
---

<Intro>

`"use no memo"` ngăn một hàm không bị React Compiler tối ưu hóa.

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `"use no memo"` {/*use-no-memo*/}

Thêm `"use no memo"` ở đầu hàm để ngăn React Compiler tối ưu hóa.

```js {1}
function MyComponent() {
  "use no memo";
  // ...
}
```

Khi một hàm chứa `"use no memo"`, React Compiler sẽ bỏ qua hoàn toàn hàm đó trong quá trình tối ưu hóa. Điều này hữu ích như một cơ chế thoát hiểm tạm thời khi gỡ lỗi hoặc khi làm việc với mã không hoạt động chính xác với compiler.

#### Lưu ý {/*caveats*/}

* `"use no memo"` phải ở ngay đầu thân hàm, trước bất kỳ import hoặc mã nào khác (comment thì được).
* Directive phải được viết bằng dấu ngoặc kép đơn hoặc kép, không phải backtick.
* Directive phải khớp chính xác `"use no memo"` hoặc bí danh `"use no forget"`.
* Directive này được ưu tiên hơn tất cả các chế độ compile và directive khác.
* Nó được thiết kế như một công cụ gỡ lỗi tạm thời, không phải giải pháp lâu dài.

### Cách `"use no memo"` loại trừ khỏi tối ưu hóa {/*how-use-no-memo-opts-out*/}

React Compiler phân tích mã của bạn tại thời điểm build để áp dụng các tối ưu hóa. `"use no memo"` tạo ra một ranh giới rõ ràng nói với compiler bỏ qua hoàn toàn một hàm.

Directive này được ưu tiên hơn tất cả các cài đặt khác:
* Trong chế độ `all`: Hàm bị bỏ qua bất chấp cài đặt toàn cục
* Trong chế độ `infer`: Hàm bị bỏ qua ngay cả khi phương pháp suy luận sẽ tối ưu hóa nó

Compiler xử lý các hàm này như thể React Compiler không được bật, giữ nguyên chúng đúng như đã viết.

### Khi nào sử dụng `"use no memo"` {/*when-to-use*/}

`"use no memo"` nên được sử dụng một cách tiết kiệm và tạm thời. Các tình huống phổ biến bao gồm:

#### Gỡ lỗi các vấn đề compiler {/*debugging-compiler*/}
Khi bạn nghi ngờ compiler gây ra vấn đề, tạm thời tắt tối ưu hóa để cô lập vấn đề:

```js
function ProblematicComponent({ data }) {
  "use no memo"; // TODO: Xóa sau khi sửa issue #123

  // Vi phạm Quy tắc của React không được phát hiện tĩnh
  // ...
}
```

#### Tích hợp thư viện bên thứ ba {/*third-party*/}
Khi tích hợp với các thư viện có thể không tương thích với compiler:

```js
function ThirdPartyWrapper() {
  "use no memo";

  useThirdPartyHook(); // Có side effect mà compiler có thể tối ưu hóa sai
  // ...
}
```

---

## Cách sử dụng {/*usage*/}

Directive `"use no memo"` được đặt ở đầu thân hàm để ngăn React Compiler tối ưu hóa hàm đó:

```js
function MyComponent() {
  "use no memo";
  // Thân hàm
}
```

Directive cũng có thể được đặt ở đầu file để ảnh hưởng đến tất cả các hàm trong module đó:

```js
"use no memo";

// Tất cả các hàm trong file này sẽ bị compiler bỏ qua
```

`"use no memo"` ở cấp hàm ghi đè directive cấp module.

---

## Xử lý sự cố {/*troubleshooting*/}

### Directive không ngăn chặn compile {/*not-preventing*/}

Nếu `"use no memo"` không hoạt động:

```js
// ❌ Sai - directive sau mã
function Component() {
  const data = getData();
  "use no memo"; // Quá muộn!
}

// ✅ Đúng - directive đặt đầu tiên
function Component() {
  "use no memo";
  const data = getData();
}
```

Cũng kiểm tra:
* Chính tả - phải chính xác là `"use no memo"`
* Dấu ngoặc kép - phải sử dụng dấu ngoặc đơn hoặc kép, không phải backtick

### Thực hành tốt nhất {/*best-practices*/}

**Luôn ghi chú lý do** bạn tắt tối ưu hóa:

```js
// ✅ Tốt - giải thích rõ ràng và có theo dõi
function DataProcessor() {
  "use no memo"; // TODO: Xóa sau khi sửa vi phạm quy tắc của React
  // ...
}

// ❌ Không tốt - không có giải thích
function Mystery() {
  "use no memo";
  // ...
}
```

### Xem thêm {/*see-also*/}

* [`"use memo"`](/reference/react-compiler/directives/use-memo) - Đưa vào danh sách compile
* [React Compiler](/learn/react-compiler) - Hướng dẫn bắt đầu
