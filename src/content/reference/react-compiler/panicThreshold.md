---
title: panicThreshold
---

<Intro>

Tùy chọn `panicThreshold` kiểm soát cách React Compiler xử lý lỗi trong quá trình compile.

</Intro>

```js
{
  panicThreshold: 'none' // Khuyến nghị
}
```

<InlineToc />

---

## Tham khảo {/*reference*/}

### `panicThreshold` {/*panicthreshold*/}

Xác định xem lỗi compile nên làm thất bại bản build hay bỏ qua việc tối ưu hóa.

#### Kiểu {/*type*/}

```
'none' | 'critical_errors' | 'all_errors'
```

#### Giá trị mặc định {/*default-value*/}

`'none'`

#### Các tùy chọn {/*options*/}

- **`'none'`** (mặc định, khuyến nghị): Bỏ qua các component không thể compile và tiếp tục build
- **`'critical_errors'`**: Làm thất bại bản build chỉ khi có lỗi compiler nghiêm trọng
- **`'all_errors'`**: Làm thất bại bản build khi có bất kỳ chẩn đoán compiler nào

#### Lưu ý {/*caveats*/}

- Bản build production luôn nên sử dụng `'none'`
- Lỗi build ngăn ứng dụng của bạn được build
- Compiler tự động phát hiện và bỏ qua mã có vấn đề với `'none'`
- Ngưỡng cao hơn chỉ hữu ích trong quá trình phát triển để gỡ lỗi

---

## Cách sử dụng {/*usage*/}

### Cấu hình production (khuyến nghị) {/*production-configuration*/}

Cho bản build production, luôn sử dụng `'none'`. Đây là giá trị mặc định:

```js
{
  panicThreshold: 'none'
}
```

Điều này đảm bảo:
- Bản build của bạn không bao giờ thất bại do vấn đề compiler
- Các component không thể tối ưu hóa vẫn chạy bình thường
- Tối đa các component được tối ưu hóa
- Triển khai production ổn định

### Gỡ lỗi trong quá trình phát triển {/*development-debugging*/}

Tạm thời sử dụng ngưỡng nghiêm ngặt hơn để tìm vấn đề:

```js
const isDevelopment = process.env.NODE_ENV === 'development';

{
  panicThreshold: isDevelopment ? 'critical_errors' : 'none',
  logger: {
    logEvent(filename, event) {
      if (isDevelopment && event.kind === 'CompileError') {
        // ...
      }
    }
  }
}
```
