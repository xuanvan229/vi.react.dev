---
title: unsupported-syntax
---

<Intro>

Kiểm tra cú pháp mà React Compiler không hỗ trợ. Nếu cần, bạn vẫn có thể sử dụng cú pháp này bên ngoài React, chẳng hạn trong một hàm tiện ích độc lập.

</Intro>

## Chi tiết quy tắc {/*rule-details*/}

React Compiler cần phân tích tĩnh code của bạn để áp dụng các tối ưu hóa. Các tính năng như `eval` và `with` khiến việc hiểu tĩnh code làm gì tại thời điểm compile trở nên bất khả thi, vì vậy compiler không thể tối ưu hóa các component sử dụng chúng.

### Không hợp lệ {/*invalid*/}

Ví dụ về code không đúng cho quy tắc này:

```js
// ❌ Sử dụng eval trong component
function Component({ code }) {
  const result = eval(code); // Không thể phân tích
  return <div>{result}</div>;
}

// ❌ Sử dụng câu lệnh with
function Component() {
  with (Math) { // Thay đổi scope động
    return <div>{sin(PI / 2)}</div>;
  }
}

// ❌ Truy cập thuộc tính động bằng eval
function Component({propName}) {
  const value = eval(`props.${propName}`);
  return <div>{value}</div>;
}
```

### Hợp lệ {/*valid*/}

Ví dụ về code đúng cho quy tắc này:

```js
// ✅ Sử dụng truy cập thuộc tính thông thường
function Component({propName, props}) {
  const value = props[propName]; // Có thể phân tích
  return <div>{value}</div>;
}

// ✅ Sử dụng các phương thức Math tiêu chuẩn
function Component() {
  return <div>{Math.sin(Math.PI / 2)}</div>;
}
```

## Xử lý sự cố {/*troubleshooting*/}

### Tôi cần đánh giá code động {/*evaluate-dynamic-code*/}

Bạn có thể cần đánh giá code do người dùng cung cấp:

```js {expectedErrors: {'react-compiler': [3]}}
// ❌ Sai: eval trong component
function Calculator({expression}) {
  const result = eval(expression); // Không an toàn và không thể tối ưu hóa
  return <div>Kết quả: {result}</div>;
}
```

Thay vào đó, sử dụng một trình phân tích biểu thức an toàn:

```js
// ✅ Tốt hơn: Sử dụng trình phân tích an toàn
import {evaluate} from 'mathjs'; // hoặc thư viện tương tự

function Calculator({expression}) {
  const [result, setResult] = useState(null);

  const calculate = () => {
    try {
      // Đánh giá biểu thức toán học an toàn
      setResult(evaluate(expression));
    } catch (error) {
      setResult('Biểu thức không hợp lệ');
    }
  };

  return (
    <div>
      <button onClick={calculate}>Tính toán</button>
      {result && <div>Kết quả: {result}</div>}
    </div>
  );
}
```

<Note>

Không bao giờ sử dụng `eval` với đầu vào người dùng - đó là rủi ro bảo mật. Sử dụng các thư viện phân tích chuyên dụng cho các trường hợp sử dụng cụ thể như biểu thức toán học, phân tích JSON, hoặc đánh giá template.

</Note>