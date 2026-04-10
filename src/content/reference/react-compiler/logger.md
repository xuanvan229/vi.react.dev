---
title: logger
---

<Intro>

Tùy chọn `logger` cung cấp ghi log tùy chỉnh cho các sự kiện React Compiler trong quá trình compile.

</Intro>

```js
{
  logger: {
    logEvent(filename, event) {
      console.log(`[Compiler] ${event.kind}: ${filename}`);
    }
  }
}
```

<InlineToc />

---

## Tham khảo {/*reference*/}

### `logger` {/*logger*/}

Cấu hình ghi log tùy chỉnh để theo dõi hành vi compiler và gỡ lỗi.

#### Kiểu {/*type*/}

```
{
  logEvent: (filename: string | null, event: LoggerEvent) => void;
} | null
```

#### Giá trị mặc định {/*default-value*/}

`null`

#### Phương thức {/*methods*/}

- **`logEvent`**: Được gọi cho mỗi sự kiện compiler với tên file và chi tiết sự kiện

#### Các loại sự kiện {/*event-types*/}

- **`CompileSuccess`**: Hàm được compile thành công
- **`CompileError`**: Hàm bị bỏ qua do lỗi
- **`CompileDiagnostic`**: Thông tin chẩn đoán không nghiêm trọng
- **`CompileSkip`**: Hàm bị bỏ qua vì lý do khác
- **`PipelineError`**: Lỗi compile không mong đợi
- **`Timing`**: Thông tin thời gian hiệu suất

#### Lưu ý {/*caveats*/}

- Cấu trúc sự kiện có thể thay đổi giữa các phiên bản
- Các codebase lớn tạo ra nhiều bản ghi log

---

## Cách sử dụng {/*usage*/}

### Ghi log cơ bản {/*basic-logging*/}

Theo dõi compile thành công và thất bại:

```js
{
  logger: {
    logEvent(filename, event) {
      switch (event.kind) {
        case 'CompileSuccess': {
          console.log(`✅ Đã compile: ${filename}`);
          break;
        }
        case 'CompileError': {
          console.log(`❌ Đã bỏ qua: ${filename}`);
          break;
        }
        default: {}
      }
    }
  }
}
```

### Ghi log lỗi chi tiết {/*detailed-error-logging*/}

Lấy thông tin cụ thể về các lỗi compile:

```js
{
  logger: {
    logEvent(filename, event) {
      if (event.kind === 'CompileError') {
        console.error(`\nCompile thất bại: ${filename}`);
        console.error(`Lý do: ${event.detail.reason}`);

        if (event.detail.description) {
          console.error(`Chi tiết: ${event.detail.description}`);
        }

        if (event.detail.loc) {
          const { line, column } = event.detail.loc.start;
          console.error(`Vị trí: Dòng ${line}, Cột ${column}`);
        }

        if (event.detail.suggestions) {
          console.error('Gợi ý:', event.detail.suggestions);
        }
      }
    }
  }
}
```
