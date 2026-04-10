---
title: Cấu hình
---

<Intro>

Trang này liệt kê tất cả các tùy chọn cấu hình có sẵn trong React Compiler.

</Intro>

<Note>

Đối với hầu hết ứng dụng, các tùy chọn mặc định sẽ hoạt động ngay lập tức. Nếu bạn có nhu cầu đặc biệt, bạn có thể sử dụng các tùy chọn nâng cao này.

</Note>

```js
// babel.config.js
module.exports = {
  plugins: [
    [
      'babel-plugin-react-compiler', {
        // tùy chọn compiler
      }
    ]
  ]
};
```

---

## Kiểm soát compile {/*compilation-control*/}

Các tùy chọn này kiểm soát *những gì* compiler tối ưu hóa và *cách* nó chọn các component và hook để compile.

* [`compilationMode`](/reference/react-compiler/compilationMode) kiểm soát chiến lược chọn các hàm để compile (ví dụ: tất cả các hàm, chỉ những hàm được chú thích, hoặc phát hiện thông minh).

```js
{
  compilationMode: 'annotation' // Chỉ compile các hàm "use memo"
}
```

---

## Tương thích phiên bản {/*version-compatibility*/}

Cấu hình phiên bản React đảm bảo compiler tạo mã tương thích với phiên bản React của bạn.

[`target`](/reference/react-compiler/target) chỉ định phiên bản React bạn đang sử dụng (17, 18, hoặc 19).

```js
// Cho các dự án React 18
{
  target: '18' // Cũng yêu cầu gói react-compiler-runtime
}
```

---

## Xử lý lỗi {/*error-handling*/}

Các tùy chọn này kiểm soát cách compiler phản hồi với mã không tuân theo [Quy tắc của React](/reference/rules).

[`panicThreshold`](/reference/react-compiler/panicThreshold) xác định xem nên làm thất bại bản build hay bỏ qua các component có vấn đề.

```js
// Khuyến nghị cho production
{
  panicThreshold: 'none' // Bỏ qua các component có lỗi thay vì làm thất bại bản build
}
```

---

## Gỡ lỗi {/*debugging*/}

Các tùy chọn ghi log và phân tích giúp bạn hiểu compiler đang làm gì.

[`logger`](/reference/react-compiler/logger) cung cấp ghi log tùy chỉnh cho các sự kiện compile.

```js
{
  logger: {
    logEvent(filename, event) {
      if (event.kind === 'CompileSuccess') {
        console.log('Compiled:', filename);
      }
    }
  }
}
```

---

## Feature Flag {/*feature-flags*/}

Compile có điều kiện cho phép bạn kiểm soát khi nào mã đã tối ưu hóa được sử dụng.

[`gating`](/reference/react-compiler/gating) cho phép sử dụng feature flag runtime cho A/B testing hoặc triển khai dần dần.

```js
{
  gating: {
    source: 'my-feature-flags',
    importSpecifierName: 'isCompilerEnabled'
  }
}
```

---

## Các mẫu cấu hình phổ biến {/*common-patterns*/}

### Cấu hình mặc định {/*default-configuration*/}

Đối với hầu hết ứng dụng React 19, compiler hoạt động mà không cần cấu hình:

```js
// babel.config.js
module.exports = {
  plugins: [
    'babel-plugin-react-compiler'
  ]
};
```

### Dự án React 17/18 {/*react-17-18*/}

Các phiên bản React cũ hơn cần gói runtime và cấu hình target:

```bash
npm install react-compiler-runtime@latest
```

```js
{
  target: '18' // hoặc '17'
}
```

### Áp dụng dần dần {/*incremental-adoption*/}

Bắt đầu với các thư mục cụ thể và mở rộng dần:

```js
{
  compilationMode: 'annotation' // Chỉ compile các hàm "use memo"
}
```
