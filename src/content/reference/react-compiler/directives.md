---
title: Directive
---

<Intro>
Các directive của React Compiler là các chuỗi ký tự đặc biệt kiểm soát việc các hàm cụ thể có được compile hay không.
</Intro>

```js
function MyComponent() {
  "use memo"; // Đưa component này vào danh sách compile
  return <div>{/* ... */}</div>;
}
```

<InlineToc />

---

## Tổng quan {/*overview*/}

Các directive của React Compiler cung cấp khả năng kiểm soát chi tiết về những hàm nào được compiler tối ưu hóa. Chúng là các chuỗi ký tự được đặt ở đầu thân hàm hoặc ở đầu module.

### Các directive có sẵn {/*available-directives*/}

* **[`"use memo"`](/reference/react-compiler/directives/use-memo)** - Đưa một hàm vào danh sách compile
* **[`"use no memo"`](/reference/react-compiler/directives/use-no-memo)** - Loại một hàm khỏi danh sách compile

### So sánh nhanh {/*quick-comparison*/}

| Directive | Mục đích | Khi nào sử dụng |
|-----------|---------|-------------|
| [`"use memo"`](/reference/react-compiler/directives/use-memo) | Buộc compile | Khi sử dụng chế độ `annotation` hoặc để ghi đè phương pháp suy luận của chế độ `infer` |
| [`"use no memo"`](/reference/react-compiler/directives/use-no-memo) | Ngăn chặn compile | Gỡ lỗi hoặc làm việc với mã không tương thích |

---

## Cách sử dụng {/*usage*/}

### Directive cấp hàm {/*function-level*/}

Đặt directive ở đầu hàm để kiểm soát việc compile:

```js
// Đưa vào danh sách compile
function OptimizedComponent() {
  "use memo";
  return <div>Đây sẽ được tối ưu hóa</div>;
}

// Loại khỏi danh sách compile
function UnoptimizedComponent() {
  "use no memo";
  return <div>Đây sẽ không được tối ưu hóa</div>;
}
```

### Directive cấp module {/*module-level*/}

Đặt directive ở đầu file để ảnh hưởng đến tất cả các hàm trong module đó:

```js
// Ở đầu file
"use memo";

// Tất cả các hàm trong file này sẽ được compile
function Component1() {
  return <div>Đã compile</div>;
}

function Component2() {
  return <div>Cũng được compile</div>;
}

// Có thể ghi đè ở cấp hàm
function Component3() {
  "use no memo"; // Ghi đè directive của module
  return <div>Không được compile</div>;
}
```

### Tương tác với các chế độ compile {/*compilation-modes*/}

Các directive hoạt động khác nhau tùy thuộc vào [`compilationMode`](/reference/react-compiler/compilationMode) của bạn:

* **Chế độ `annotation`**: Chỉ các hàm có `"use memo"` được compile
* **Chế độ `infer`**: Compiler quyết định compile gì, directive ghi đè các quyết định
* **Chế độ `all`**: Mọi thứ đều được compile, `"use no memo"` có thể loại trừ các hàm cụ thể

---

## Thực hành tốt nhất {/*best-practices*/}

### Sử dụng directive một cách tiết kiệm {/*use-sparingly*/}

Directive là cơ chế thoát hiểm. Ưu tiên cấu hình compiler ở cấp dự án:

```js
// ✅ Tốt - cấu hình toàn dự án
{
  plugins: [
    ['babel-plugin-react-compiler', {
      compilationMode: 'infer'
    }]
  ]
}

// ⚠️ Chỉ sử dụng directive khi cần thiết
function SpecialCase() {
  "use no memo"; // Ghi chú tại sao cần thiết
  // ...
}
```

### Ghi chú việc sử dụng directive {/*document-usage*/}

Luôn giải thích tại sao sử dụng directive:

```js
// ✅ Tốt - giải thích rõ ràng
function DataGrid() {
  "use no memo"; // TODO: Xóa sau khi sửa vấn đề chiều cao hàng động (JIRA-123)
  // Triển khai grid phức tạp
}

// ❌ Không tốt - không có giải thích
function Mystery() {
  "use no memo";
  // ...
}
```

### Lên kế hoạch xóa bỏ {/*plan-removal*/}

Các directive loại trừ nên là tạm thời:

1. Thêm directive với comment TODO
2. Tạo một issue theo dõi
3. Sửa vấn đề gốc
4. Xóa directive

```js
function TemporaryWorkaround() {
  "use no memo"; // TODO: Xóa sau khi nâng cấp ThirdPartyLib lên v2.0
  return <ThirdPartyComponent />;
}
```

---

## Các mẫu phổ biến {/*common-patterns*/}

### Áp dụng dần dần {/*gradual-adoption*/}

Khi áp dụng React Compiler trong một codebase lớn:

```js
// Bắt đầu với chế độ annotation
{
  compilationMode: 'annotation'
}

// Đưa các component ổn định vào
function StableComponent() {
  "use memo";
  // Component đã được kiểm thử kỹ
}

// Sau đó, chuyển sang chế độ infer và loại trừ các component có vấn đề
function ProblematicComponent() {
  "use no memo"; // Sửa các vấn đề trước khi xóa
  // ...
}
```


---

## Xử lý sự cố {/*troubleshooting*/}

Đối với các vấn đề cụ thể với directive, xem phần xử lý sự cố trong:

* [Xử lý sự cố `"use memo"`](/reference/react-compiler/directives/use-memo#troubleshooting)
* [Xử lý sự cố `"use no memo"`](/reference/react-compiler/directives/use-no-memo#troubleshooting)

### Các vấn đề phổ biến {/*common-issues*/}

1. **Directive bị bỏ qua**: Kiểm tra vị trí đặt (phải ở đầu tiên) và chính tả
2. **Vẫn bị compile**: Kiểm tra cài đặt `ignoreUseNoForget`
3. **Directive cấp module không hoạt động**: Đảm bảo đặt trước tất cả các import

---

## Xem thêm {/*see-also*/}

* [`compilationMode`](/reference/react-compiler/compilationMode) - Cấu hình cách compiler chọn những gì để tối ưu hóa
* [`Configuration`](/reference/react-compiler/configuration) - Tất cả các tùy chọn cấu hình compiler
* [Tài liệu React Compiler](https://react.dev/learn/react-compiler) - Hướng dẫn bắt đầu
