---
title: Gỡ lỗi và xử lý sự cố
---

<Intro>
Hướng dẫn này giúp bạn xác định và sửa lỗi khi sử dụng React Compiler. Tìm hiểu cách gỡ lỗi các vấn đề biên dịch và giải quyết các lỗi phổ biến.
</Intro>

<YouWillLearn>

* Sự khác biệt giữa lỗi compiler và lỗi runtime
* Các pattern phổ biến gây lỗi biên dịch
* Quy trình gỡ lỗi từng bước

</YouWillLearn>

## Hiểu hành vi của Compiler {/*understanding-compiler-behavior*/}

React Compiler được thiết kế để xử lý code tuân theo [Quy tắc của React](/reference/rules). Khi gặp code có thể vi phạm các quy tắc này, nó sẽ an toàn bỏ qua tối ưu hóa thay vì mạo hiểm thay đổi hành vi ứng dụng của bạn.

### Lỗi Compiler vs Lỗi Runtime {/*compiler-errors-vs-runtime-issues*/}

**Lỗi compiler** xảy ra tại thời điểm build và ngăn code của bạn biên dịch. Những lỗi này hiếm khi xảy ra vì compiler được thiết kế để bỏ qua code có vấn đề thay vì thất bại.

**Lỗi runtime** xảy ra khi code đã biên dịch hoạt động khác so với mong đợi. Hầu hết thời gian, nếu bạn gặp vấn đề với React Compiler, đó là lỗi runtime. Điều này thường xảy ra khi code của bạn vi phạm Quy tắc của React theo cách tinh tế mà compiler không thể phát hiện, và compiler đã biên dịch nhầm một component mà lẽ ra nó nên bỏ qua.

Khi gỡ lỗi runtime, hãy tập trung vào việc tìm các vi phạm Quy tắc của React trong các component bị ảnh hưởng mà ESLint rule không phát hiện được. Compiler dựa vào việc code của bạn tuân theo các quy tắc này, và khi chúng bị vi phạm theo cách mà nó không thể phát hiện, đó là khi các vấn đề runtime xảy ra.


## Các Pattern phổ biến gây lỗi {/*common-breaking-patterns*/}

Một trong những cách chính mà React Compiler có thể làm hỏng ứng dụng của bạn là nếu code của bạn được viết để dựa vào memoization cho tính đúng đắn. Điều này có nghĩa là ứng dụng của bạn phụ thuộc vào các giá trị cụ thể được memoize để hoạt động đúng. Vì compiler có thể memoize khác với cách thủ công của bạn, điều này có thể dẫn đến hành vi không mong đợi như effect chạy quá nhiều lần, vòng lặp vô hạn, hoặc thiếu cập nhật.

Các tình huống phổ biến khi điều này xảy ra:

- **Effect dựa vào referential equality** - Khi effect phụ thuộc vào object hoặc array duy trì cùng tham chiếu qua các lần render
- **Mảng dependency cần tham chiếu ổn định** - Khi dependency không ổn định khiến effect chạy quá thường xuyên hoặc tạo vòng lặp vô hạn
- **Logic điều kiện dựa trên kiểm tra tham chiếu** - Khi code sử dụng kiểm tra referential equality cho caching hoặc tối ưu hóa

## Quy trình gỡ lỗi {/*debugging-workflow*/}

Làm theo các bước sau khi bạn gặp vấn đề:

### Lỗi Build của Compiler {/*compiler-build-errors*/}

Nếu bạn gặp lỗi compiler bất ngờ làm hỏng build, đây có thể là bug trong compiler. Báo cáo đến repository [facebook/react](https://github.com/facebook/react/issues) với:
- Thông báo lỗi
- Code gây ra lỗi
- Phiên bản React và compiler của bạn

### Lỗi Runtime {/*runtime-issues*/}

Đối với các vấn đề hành vi runtime:

### 1. Tạm thời vô hiệu hóa biên dịch {/*temporarily-disable-compilation*/}

Sử dụng `"use no memo"` để cô lập xem vấn đề có liên quan đến compiler không:

```js
function ProblematicComponent() {
  "use no memo"; // Bỏ qua biên dịch cho component này
  // ... phần còn lại của component
}
```

Nếu vấn đề biến mất, nó có thể liên quan đến vi phạm Quy tắc của React.

Bạn cũng có thể thử xóa memoization thủ công (useMemo, useCallback, memo) khỏi component có vấn đề để xác minh rằng ứng dụng hoạt động đúng mà không cần memoization nào. Nếu bug vẫn xảy ra khi tất cả memoization được xóa, bạn có vi phạm Quy tắc của React cần được sửa.

### 2. Sửa lỗi từng bước {/*fix-issues-step-by-step*/}

1. Xác định nguyên nhân gốc (thường là memoization-for-correctness)
2. Kiểm tra sau mỗi lần sửa
3. Xóa `"use no memo"` sau khi đã sửa xong
4. Xác minh component hiển thị badge ✨ trong React DevTools

## Báo cáo bug Compiler {/*reporting-compiler-bugs*/}

Nếu bạn tin rằng mình đã tìm thấy bug compiler:

1. **Xác minh không phải vi phạm Quy tắc của React** - Kiểm tra với ESLint
2. **Tạo bản tái hiện tối thiểu** - Cô lập vấn đề trong ví dụ nhỏ
3. **Kiểm tra mà không có compiler** - Xác nhận vấn đề chỉ xảy ra khi biên dịch
4. **Tạo [issue](https://github.com/facebook/react/issues/new?template=compiler_bug_report.yml)**:
   - Phiên bản React và compiler
   - Code tái hiện tối thiểu
   - Hành vi mong đợi vs thực tế
   - Bất kỳ thông báo lỗi nào

## Bước tiếp theo {/*next-steps*/}

- Xem lại [Quy tắc của React](/reference/rules) để ngăn ngừa vấn đề
- Xem [hướng dẫn áp dụng dần dần](/learn/react-compiler/incremental-adoption) cho chiến lược triển khai từng bước
