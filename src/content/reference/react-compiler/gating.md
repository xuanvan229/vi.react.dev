---
title: gating
---

<Intro>

Tùy chọn `gating` cho phép compile có điều kiện, giúp bạn kiểm soát khi nào mã đã tối ưu hóa được sử dụng tại runtime.

</Intro>

```js
{
  gating: {
    source: 'my-feature-flags',
    importSpecifierName: 'shouldUseCompiler'
  }
}
```

<InlineToc />

---

## Tham khảo {/*reference*/}

### `gating` {/*gating*/}

Cấu hình feature flag runtime cho các hàm đã compile.

#### Kiểu {/*type*/}

```
{
  source: string;
  importSpecifierName: string;
} | null
```

#### Giá trị mặc định {/*default-value*/}

`null`

#### Thuộc tính {/*properties*/}

- **`source`**: Đường dẫn module để import feature flag
- **`importSpecifierName`**: Tên của hàm được export để import

#### Lưu ý {/*caveats*/}

- Hàm gating phải trả về giá trị boolean
- Cả phiên bản đã compile và phiên bản gốc đều làm tăng kích thước bundle
- Import được thêm vào mỗi file có các hàm đã compile

---

## Cách sử dụng {/*usage*/}

### Thiết lập feature flag cơ bản {/*basic-setup*/}

1. Tạo một module feature flag:

```js
// src/utils/feature-flags.js
export function shouldUseCompiler() {
  // logic của bạn ở đây
  return getFeatureFlag('react-compiler-enabled');
}
```

2. Cấu hình compiler:

```js
{
  gating: {
    source: './src/utils/feature-flags',
    importSpecifierName: 'shouldUseCompiler'
  }
}
```

3. Compiler tạo mã có điều kiện:

```js
// Đầu vào
function Button(props) {
  return <button>{props.label}</button>;
}

// Đầu ra (đã đơn giản hóa)
import { shouldUseCompiler } from './src/utils/feature-flags';

const Button = shouldUseCompiler()
  ? function Button_optimized(props) { /* phiên bản đã compile */ }
  : function Button_original(props) { /* phiên bản gốc */ };
```

Lưu ý rằng hàm gating được đánh giá một lần tại thời điểm module, vì vậy sau khi bundle JS được phân tích và thực thi, lựa chọn component sẽ giữ nguyên cho phần còn lại của phiên trình duyệt.

---

## Xử lý sự cố {/*troubleshooting*/}

### Feature flag không hoạt động {/*flag-not-working*/}

Xác nhận module flag của bạn export đúng hàm:

```js
// ❌ Sai: Export mặc định
export default function shouldUseCompiler() {
  return true;
}

// ✅ Đúng: Export có tên khớp với importSpecifierName
export function shouldUseCompiler() {
  return true;
}
```

### Lỗi import {/*import-errors*/}

Đảm bảo đường dẫn source chính xác:

```js
// ❌ Sai: Tương đối so với babel.config.js
{
  source: './src/flags',
  importSpecifierName: 'flag'
}

// ✅ Đúng: Đường dẫn phân giải module
{
  source: '@myapp/feature-flags',
  importSpecifierName: 'flag'
}

// ✅ Cũng đúng: Đường dẫn tuyệt đối từ thư mục gốc dự án
{
  source: './src/utils/flags',
  importSpecifierName: 'flag'
}
```
