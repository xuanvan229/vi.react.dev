---
title: gating
---

<Intro>

Kiểm tra cấu hình của [chế độ gating](/reference/react-compiler/gating).

</Intro>

## Chi tiết quy tắc {/*rule-details*/}

Chế độ gating cho phép bạn dần dần áp dụng React Compiler bằng cách đánh dấu các component cụ thể để tối ưu hóa. Quy tắc này đảm bảo cấu hình gating của bạn là hợp lệ để compiler biết component nào cần xử lý.

### Không hợp lệ {/*invalid*/}

Ví dụ về code không đúng cho quy tắc này:

```js
// ❌ Thiếu các trường bắt buộc
module.exports = {
  plugins: [
    ['babel-plugin-react-compiler', {
      gating: {
        importSpecifierName: '__experimental_useCompiler'
        // Thiếu trường 'source'
      }
    }]
  ]
};

// ❌ Kiểu gating không hợp lệ
module.exports = {
  plugins: [
    ['babel-plugin-react-compiler', {
      gating: '__experimental_useCompiler' // Nên là object
    }]
  ]
};
```

### Hợp lệ {/*valid*/}

Ví dụ về code đúng cho quy tắc này:

```js
// ✅ Cấu hình gating đầy đủ
module.exports = {
  plugins: [
    ['babel-plugin-react-compiler', {
      gating: {
        importSpecifierName: 'isCompilerEnabled', // tên hàm được export
        source: 'featureFlags' // tên module
      }
    }]
  ]
};

// featureFlags.js
export function isCompilerEnabled() {
  // ...
}

// ✅ Không có gating (biên dịch tất cả)
module.exports = {
  plugins: [
    ['babel-plugin-react-compiler', {
      // Không có trường gating - biên dịch tất cả component
    }]
  ]
};
```
