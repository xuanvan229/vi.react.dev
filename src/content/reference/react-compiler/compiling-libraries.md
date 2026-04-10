---
title: Compile thư viện
---

<Intro>
Hướng dẫn này giúp các tác giả thư viện hiểu cách sử dụng React Compiler để phân phối mã thư viện đã được tối ưu hóa đến người dùng.
</Intro>

<InlineToc />

## Tại sao nên phân phối mã đã compile? {/*why-ship-compiled-code*/}

Là một tác giả thư viện, bạn có thể compile mã thư viện trước khi phát hành lên npm. Điều này mang lại nhiều lợi ích:

- **Cải thiện hiệu suất cho tất cả người dùng** - Người dùng thư viện của bạn nhận được mã đã tối ưu hóa ngay cả khi họ chưa sử dụng React Compiler
- **Người dùng không cần cấu hình** - Các tối ưu hóa hoạt động ngay lập tức
- **Hành vi nhất quán** - Tất cả người dùng nhận được cùng phiên bản đã tối ưu hóa bất kể cấu hình build của họ

## Thiết lập compile {/*setting-up-compilation*/}

Thêm React Compiler vào quy trình build của thư viện:

<TerminalBlock>
npm install -D babel-plugin-react-compiler@latest
</TerminalBlock>

Cấu hình công cụ build của bạn để compile thư viện. Ví dụ, với Babel:

```js
// babel.config.js
module.exports = {
  plugins: [
    'babel-plugin-react-compiler',
  ],
  // ... cấu hình khác
};
```

## Tương thích ngược {/*backwards-compatibility*/}

Nếu thư viện của bạn hỗ trợ các phiên bản React dưới 19, bạn sẽ cần cấu hình bổ sung:

### 1. Cài đặt gói runtime {/*install-runtime-package*/}

Chúng tôi khuyến nghị cài đặt react-compiler-runtime như một dependency trực tiếp:

<TerminalBlock>
npm install react-compiler-runtime@latest
</TerminalBlock>

```json
{
  "dependencies": {
    "react-compiler-runtime": "^1.0.0"
  },
  "peerDependencies": {
    "react": "^17.0.0 || ^18.0.0 || ^19.0.0"
  }
}
```

### 2. Cấu hình phiên bản target {/*configure-target-version*/}

Đặt phiên bản React tối thiểu mà thư viện của bạn hỗ trợ:

```js
{
  target: '17', // Phiên bản React tối thiểu được hỗ trợ
}
```

## Chiến lược kiểm thử {/*testing-strategy*/}

Kiểm thử thư viện của bạn cả có và không có compile để đảm bảo tính tương thích. Chạy bộ kiểm thử hiện có với mã đã compile, và cũng tạo một cấu hình kiểm thử riêng bỏ qua compiler. Điều này giúp phát hiện bất kỳ vấn đề nào có thể phát sinh từ quá trình compile và đảm bảo thư viện hoạt động chính xác trong mọi tình huống.

## Xử lý sự cố {/*troubleshooting*/}

### Thư viện không hoạt động với các phiên bản React cũ hơn {/*library-doesnt-work-with-older-react-versions*/}

Nếu thư viện đã compile của bạn gặp lỗi trên React 17 hoặc 18:

1. Xác nhận bạn đã cài đặt `react-compiler-runtime` như một dependency
2. Kiểm tra cấu hình `target` khớp với phiên bản React tối thiểu được hỗ trợ
3. Đảm bảo gói runtime được bao gồm trong bundle đã phát hành

### Xung đột compile với các plugin Babel khác {/*compilation-conflicts-with-other-babel-plugins*/}

Một số plugin Babel có thể xung đột với React Compiler:

1. Đặt `babel-plugin-react-compiler` ở đầu danh sách plugin
2. Tắt các tối ưu hóa xung đột trong các plugin khác
3. Kiểm thử kỹ đầu ra build

### Không tìm thấy module runtime {/*runtime-module-not-found*/}

Nếu người dùng thấy lỗi "Cannot find module 'react-compiler-runtime'":

1. Đảm bảo runtime được liệt kê trong `dependencies`, không phải `devDependencies`
2. Kiểm tra bundler của bạn bao gồm runtime trong đầu ra
3. Xác nhận gói được phát hành lên npm cùng với thư viện

## Bước tiếp theo {/*next-steps*/}

- Tìm hiểu về [kỹ thuật gỡ lỗi](/learn/react-compiler/debugging) cho mã đã compile
- Xem [các tùy chọn cấu hình](/reference/react-compiler/configuration) cho tất cả tùy chọn compiler
- Khám phá [chế độ compile](/reference/react-compiler/compilationMode) cho tối ưu hóa có chọn lọc
