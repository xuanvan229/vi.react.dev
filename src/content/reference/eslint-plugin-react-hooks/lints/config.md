---
title: config
---

<Intro>

Kiểm tra các [tùy chọn cấu hình](/reference/react-compiler/configuration) của compiler.

</Intro>

## Chi tiết quy tắc {/*rule-details*/}

React Compiler chấp nhận nhiều [tùy chọn cấu hình](/reference/react-compiler/configuration) khác nhau để điều khiển hành vi của nó. Quy tắc này kiểm tra rằng cấu hình của bạn sử dụng đúng tên tùy chọn và kiểu giá trị, ngăn chặn các lỗi im lặng từ lỗi đánh máy hoặc cài đặt không chính xác.

### Không hợp lệ {/*invalid*/}

Ví dụ về code không đúng cho quy tắc này:

```js
// ❌ Tên tùy chọn không xác định
module.exports = {
  plugins: [
    ['babel-plugin-react-compiler', {
      compileMode: 'all' // Lỗi đánh máy: nên là compilationMode
    }]
  ]
};

// ❌ Giá trị tùy chọn không hợp lệ
module.exports = {
  plugins: [
    ['babel-plugin-react-compiler', {
      compilationMode: 'everything' // Không hợp lệ: dùng 'all' hoặc 'infer'
    }]
  ]
};
```

### Hợp lệ {/*valid*/}

Ví dụ về code đúng cho quy tắc này:

```js
// ✅ Cấu hình compiler hợp lệ
module.exports = {
  plugins: [
    ['babel-plugin-react-compiler', {
      compilationMode: 'infer',
      panicThreshold: 'critical_errors'
    }]
  ]
};
```

## Xử lý sự cố {/*troubleshooting*/}

### Cấu hình không hoạt động như mong đợi {/*config-not-working*/}

Cấu hình compiler của bạn có thể có lỗi đánh máy hoặc giá trị không chính xác:

```js
// ❌ Sai: Các lỗi cấu hình phổ biến
module.exports = {
  plugins: [
    ['babel-plugin-react-compiler', {
      // Lỗi đánh máy trong tên tùy chọn
      compilationMod: 'all',
      // Sai kiểu giá trị
      panicThreshold: true,
      // Tùy chọn không xác định
      optimizationLevel: 'max'
    }]
  ]
};
```

Kiểm tra [tài liệu cấu hình](/reference/react-compiler/configuration) để biết các tùy chọn hợp lệ:

```js
// ✅ Tốt hơn: Cấu hình hợp lệ
module.exports = {
  plugins: [
    ['babel-plugin-react-compiler', {
      compilationMode: 'all', // hoặc 'infer'
      panicThreshold: 'none', // hoặc 'critical_errors', 'all_errors'
      // Chỉ sử dụng các tùy chọn được ghi trong tài liệu
    }]
  ]
};
```
