---
title: target
---

<Intro>

Tùy chọn `target` chỉ định phiên bản React mà compiler nên tạo mã tương thích.

</Intro>

```js
{
  target: '19' // hoặc '18', '17'
}
```

<InlineToc />

---

## Tham khảo {/*reference*/}

### `target` {/*target*/}

Cấu hình tương thích phiên bản React cho đầu ra đã compile.

#### Kiểu {/*type*/}

```
'17' | '18' | '19'
```

#### Giá trị mặc định {/*default-value*/}

`'19'`

#### Các giá trị hợp lệ {/*valid-values*/}

- **`'19'`**: Nhắm đến React 19 (mặc định). Không cần runtime bổ sung.
- **`'18'`**: Nhắm đến React 18. Yêu cầu gói `react-compiler-runtime`.
- **`'17'`**: Nhắm đến React 17. Yêu cầu gói `react-compiler-runtime`.

#### Lưu ý {/*caveats*/}

- Luôn sử dụng giá trị chuỗi, không phải số (ví dụ: `'17'` không phải `17`)
- Không bao gồm phiên bản patch (ví dụ: sử dụng `'18'` không phải `'18.2.0'`)
- React 19 bao gồm các API runtime compiler tích hợp sẵn
- React 17 và 18 yêu cầu cài đặt `react-compiler-runtime@latest`

---

## Cách sử dụng {/*usage*/}

### Nhắm đến React 19 (mặc định) {/*targeting-react-19*/}

Cho React 19, không cần cấu hình đặc biệt:

```js
{
  // mặc định là target: '19'
}
```

Compiler sẽ sử dụng các API runtime tích hợp sẵn của React 19:

```js
// Đầu ra đã compile sử dụng API native của React 19
import { c as _c } from 'react/compiler-runtime';
```

### Nhắm đến React 17 hoặc 18 {/*targeting-react-17-or-18*/}

Cho các dự án React 17 và React 18, bạn cần hai bước:

1. Cài đặt gói runtime:

```bash
npm install react-compiler-runtime@latest
```

2. Cấu hình target:

```js
// Cho React 18
{
  target: '18'
}

// Cho React 17
{
  target: '17'
}
```

Compiler sẽ sử dụng polyfill runtime cho cả hai phiên bản:

```js
// Đầu ra đã compile sử dụng polyfill
import { c as _c } from 'react-compiler-runtime';
```

---

## Xử lý sự cố {/*troubleshooting*/}

### Lỗi runtime về thiếu compiler runtime {/*missing-runtime*/}

Nếu bạn thấy lỗi như "Cannot find module 'react/compiler-runtime'":

1. Kiểm tra phiên bản React của bạn:
   ```bash
   npm why react
   ```

2. Nếu sử dụng React 17 hoặc 18, cài đặt runtime:
   ```bash
   npm install react-compiler-runtime@latest
   ```

3. Đảm bảo target khớp với phiên bản React của bạn:
   ```js
   {
     target: '18' // Phải khớp với phiên bản React chính của bạn
   }
   ```

### Gói runtime không hoạt động {/*runtime-not-working*/}

Đảm bảo gói runtime:

1. Được cài đặt trong dự án (không phải toàn cục)
2. Được liệt kê trong `package.json` dependencies
3. Đúng phiên bản (tag `@latest`)
4. Không nằm trong `devDependencies` (nó cần thiết tại runtime)

### Kiểm tra đầu ra đã compile {/*checking-output*/}

Để xác nhận runtime đúng đang được sử dụng, lưu ý import khác nhau (`react/compiler-runtime` cho tích hợp sẵn, gói độc lập `react-compiler-runtime` cho 17/18):

```js
// Cho React 19 (runtime tích hợp sẵn)
import { c } from 'react/compiler-runtime'
//                      ^

// Cho React 17/18 (polyfill runtime)
import { c } from 'react-compiler-runtime'
//                      ^
```
