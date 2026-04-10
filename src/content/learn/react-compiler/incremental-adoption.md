---
title: Áp dụng dần dần
---

<Intro>
React Compiler có thể được áp dụng dần dần, cho phép bạn thử nghiệm nó trên các phần cụ thể của codebase trước. Hướng dẫn này cho bạn thấy cách triển khai compiler từng bước trong các dự án hiện có.
</Intro>

<YouWillLearn>

* Tại sao nên áp dụng dần dần
* Sử dụng Babel overrides để áp dụng theo thư mục
* Sử dụng directive "use memo" để chọn tham gia biên dịch
* Sử dụng directive "use no memo" để loại trừ component
* Cờ tính năng runtime với gating
* Theo dõi tiến độ áp dụng

</YouWillLearn>

## Tại sao nên áp dụng dần dần? {/*why-incremental-adoption*/}

React Compiler được thiết kế để tự động tối ưu hóa toàn bộ codebase của bạn, nhưng bạn không cần phải áp dụng tất cả cùng một lúc. Áp dụng dần dần cho bạn quyền kiểm soát quá trình triển khai, cho phép bạn kiểm tra compiler trên các phần nhỏ của ứng dụng trước khi mở rộng ra phần còn lại.

Bắt đầu từ nhỏ giúp bạn xây dựng sự tự tin vào các tối ưu hóa của compiler. Bạn có thể xác minh rằng ứng dụng hoạt động đúng với code đã biên dịch, đo lường cải thiện hiệu suất, và xác định bất kỳ trường hợp đặc biệt nào trong codebase của bạn. Cách tiếp cận này đặc biệt có giá trị cho các ứng dụng production nơi tính ổn định là quan trọng.

Áp dụng dần dần cũng giúp dễ dàng hơn trong việc xử lý bất kỳ vi phạm Quy tắc của React nào mà compiler có thể tìm thấy. Thay vì sửa vi phạm trên toàn bộ codebase cùng một lúc, bạn có thể xử lý chúng một cách có hệ thống khi mở rộng phạm vi compiler. Điều này giữ cho việc di chuyển có thể quản lý được và giảm rủi ro tạo ra bug.

Bằng cách kiểm soát phần nào của code được biên dịch, bạn cũng có thể chạy A/B test để đo lường tác động thực tế của các tối ưu hóa compiler. Dữ liệu này giúp bạn đưa ra quyết định sáng suốt về việc áp dụng đầy đủ và chứng minh giá trị cho team của bạn.

## Các cách tiếp cận áp dụng dần dần {/*approaches-to-incremental-adoption*/}

Có ba cách tiếp cận chính để áp dụng React Compiler dần dần:

1. **Babel overrides** - Áp dụng compiler cho các thư mục cụ thể
2. **Chọn tham gia với "use memo"** - Chỉ biên dịch các component chọn tham gia rõ ràng
3. **Runtime gating** - Kiểm soát biên dịch với cờ tính năng

Tất cả các cách tiếp cận cho phép bạn kiểm tra compiler trên các phần cụ thể của ứng dụng trước khi triển khai đầy đủ.

## Áp dụng theo thư mục với Babel Overrides {/*directory-based-adoption*/}

Tùy chọn `overrides` của Babel cho phép bạn áp dụng các plugin khác nhau cho các phần khác nhau của codebase. Đây là cách lý tưởng để áp dụng dần dần React Compiler theo từng thư mục.

### Cấu hình cơ bản {/*basic-configuration*/}

Bắt đầu bằng cách áp dụng compiler cho một thư mục cụ thể:

```js
// babel.config.js
module.exports = {
  plugins: [
    // Plugin toàn cục áp dụng cho tất cả file
  ],
  overrides: [
    {
      test: './src/modern/**/*.{js,jsx,ts,tsx}',
      plugins: [
        'babel-plugin-react-compiler'
      ]
    }
  ]
};
```

### Mở rộng phạm vi {/*expanding-coverage*/}

Khi bạn có thêm sự tự tin, thêm nhiều thư mục hơn:

```js
// babel.config.js
module.exports = {
  plugins: [
    // Plugin toàn cục
  ],
  overrides: [
    {
      test: ['./src/modern/**/*.{js,jsx,ts,tsx}', './src/features/**/*.{js,jsx,ts,tsx}'],
      plugins: [
        'babel-plugin-react-compiler'
      ]
    },
    {
      test: './src/legacy/**/*.{js,jsx,ts,tsx}',
      plugins: [
        // Plugin khác cho code cũ
      ]
    }
  ]
};
```

### Với tùy chọn Compiler {/*with-compiler-options*/}

Bạn cũng có thể cấu hình tùy chọn compiler cho mỗi override:

```js
// babel.config.js
module.exports = {
  plugins: [],
  overrides: [
    {
      test: './src/experimental/**/*.{js,jsx,ts,tsx}',
      plugins: [
        ['babel-plugin-react-compiler', {
          // tùy chọn ...
        }]
      ]
    },
    {
      test: './src/production/**/*.{js,jsx,ts,tsx}',
      plugins: [
        ['babel-plugin-react-compiler', {
          // tùy chọn ...
        }]
      ]
    }
  ]
};
```


## Chế độ chọn tham gia với "use memo" {/*opt-in-mode-with-use-memo*/}

Để kiểm soát tối đa, bạn có thể sử dụng `compilationMode: 'annotation'` để chỉ biên dịch các component và hook chọn tham gia rõ ràng với directive `"use memo"`.

<Note>
Cách tiếp cận này cho bạn quyền kiểm soát chi tiết đối với từng component và hook. Nó hữu ích khi bạn muốn kiểm tra compiler trên các component cụ thể mà không ảnh hưởng đến toàn bộ thư mục.
</Note>

### Cấu hình Annotation Mode {/*annotation-mode-configuration*/}

```js
// babel.config.js
module.exports = {
  plugins: [
    ['babel-plugin-react-compiler', {
      compilationMode: 'annotation',
    }],
  ],
};
```

### Sử dụng Directive {/*using-the-directive*/}

Thêm `"use memo"` vào đầu hàm bạn muốn biên dịch:

```js
function TodoList({ todos }) {
  "use memo"; // Chọn tham gia biên dịch cho component này

  const sortedTodos = todos.slice().sort();

  return (
    <ul>
      {sortedTodos.map(todo => (
        <TodoItem key={todo.id} todo={todo} />
      ))}
    </ul>
  );
}

function useSortedData(data) {
  "use memo"; // Chọn tham gia biên dịch cho hook này

  return data.slice().sort();
}
```

Với `compilationMode: 'annotation'`, bạn phải:
- Thêm `"use memo"` vào mỗi component bạn muốn tối ưu hóa
- Thêm `"use memo"` vào mỗi custom hook
- Nhớ thêm nó vào các component mới

Điều này cho bạn quyền kiểm soát chính xác component nào được biên dịch trong khi bạn đánh giá tác động của compiler.

## Cờ tính năng Runtime với Gating {/*runtime-feature-flags-with-gating*/}

Tùy chọn `gating` cho phép bạn kiểm soát biên dịch tại runtime bằng cờ tính năng. Điều này hữu ích cho việc chạy A/B test hoặc triển khai dần dần compiler dựa trên phân khúc người dùng.

### Cách Gating hoạt động {/*how-gating-works*/}

Compiler bọc code đã tối ưu hóa trong một kiểm tra runtime. Nếu gate trả về `true`, phiên bản đã tối ưu hóa chạy. Ngược lại, code gốc chạy.

### Cấu hình Gating {/*gating-configuration*/}

```js
// babel.config.js
module.exports = {
  plugins: [
    ['babel-plugin-react-compiler', {
      gating: {
        source: 'ReactCompilerFeatureFlags',
        importSpecifierName: 'isCompilerEnabled',
      },
    }],
  ],
};
```

### Triển khai cờ tính năng {/*implementing-the-feature-flag*/}

Tạo một module xuất hàm gating của bạn:

```js
// ReactCompilerFeatureFlags.js
export function isCompilerEnabled() {
  // Sử dụng hệ thống cờ tính năng của bạn
  return getFeatureFlag('react-compiler-enabled');
}
```

## Xử lý sự cố khi áp dụng {/*troubleshooting-adoption*/}

Nếu bạn gặp vấn đề trong quá trình áp dụng:

1. Sử dụng `"use no memo"` để tạm thời loại trừ component có vấn đề
2. Xem [hướng dẫn gỡ lỗi](/learn/react-compiler/debugging) cho các vấn đề phổ biến
3. Sửa các vi phạm Quy tắc của React được xác định bởi ESLint plugin
4. Cân nhắc sử dụng `compilationMode: 'annotation'` để áp dụng dần dần hơn

## Bước tiếp theo {/*next-steps*/}

- Đọc [hướng dẫn cấu hình](/reference/react-compiler/configuration) để biết thêm tùy chọn
- Tìm hiểu về [kỹ thuật gỡ lỗi](/learn/react-compiler/debugging)
- Xem [tham chiếu API](/reference/react-compiler/configuration) cho tất cả tùy chọn compiler
