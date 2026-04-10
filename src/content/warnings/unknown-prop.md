---
title: Cảnh báo Prop không xác định
---

Cảnh báo unknown-prop sẽ xuất hiện nếu bạn cố render một phần tử DOM với một prop mà React không nhận dạng là thuộc tính DOM hợp lệ. Bạn nên đảm bảo rằng các phần tử DOM của bạn không có các props thừa.

Có một vài lý do có thể khiến cảnh báo này xuất hiện:

1. Bạn có đang sử dụng `{...props}` hoặc `cloneElement(element, props)` không? Khi sao chép props sang component con, bạn nên đảm bảo rằng bạn không vô tình chuyển tiếp các props chỉ dành cho component cha. Xem các cách sửa phổ biến cho vấn đề này bên dưới.

2. Bạn đang sử dụng một thuộc tính DOM không chuẩn trên một node DOM gốc, có thể để đại diện cho dữ liệu tùy chỉnh. Nếu bạn đang cố gắng gắn dữ liệu tùy chỉnh vào một phần tử DOM chuẩn, hãy cân nhắc sử dụng thuộc tính data tùy chỉnh như được mô tả [trên MDN](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Using_data_attributes).

3. React chưa nhận dạng thuộc tính mà bạn chỉ định. Điều này có thể sẽ được sửa trong phiên bản tương lai của React. React sẽ cho phép bạn truyền nó mà không có cảnh báo nếu bạn viết tên thuộc tính bằng chữ thường.

4. Bạn đang sử dụng một React component mà không viết hoa, ví dụ `<myButton />`. React diễn giải nó như một thẻ DOM vì React JSX transform sử dụng quy ước chữ hoa và chữ thường để phân biệt giữa component do người dùng định nghĩa và thẻ DOM. Đối với các React component của bạn, hãy sử dụng PascalCase. Ví dụ, viết `<MyButton />` thay vì `<myButton />`.

---

Nếu bạn nhận được cảnh báo này vì bạn truyền props như `{...props}`, component cha của bạn cần "tiêu thụ" bất kỳ prop nào dành cho component cha và không dành cho component con. Ví dụ:

**Sai:** Prop `layout` không mong muốn được chuyển tiếp đến thẻ `div`.

```js
function MyDiv(props) {
  if (props.layout === 'horizontal') {
    // SAI! Vì bạn biết chắc "layout" không phải là prop mà <div> hiểu được.
    return <div {...props} style={getHorizontalStyle()} />
  } else {
    // SAI! Vì bạn biết chắc "layout" không phải là prop mà <div> hiểu được.
    return <div {...props} style={getVerticalStyle()} />
  }
}
```

**Đúng:** Cú pháp spread có thể được sử dụng để tách các biến ra khỏi props, và đặt các props còn lại vào một biến.

```js
function MyDiv(props) {
  const { layout, ...rest } = props
  if (layout === 'horizontal') {
    return <div {...rest} style={getHorizontalStyle()} />
  } else {
    return <div {...rest} style={getVerticalStyle()} />
  }
}
```

**Đúng:** Bạn cũng có thể gán props vào một object mới và xóa các key mà bạn đang sử dụng khỏi object mới. Hãy chắc chắn không xóa props khỏi object `this.props` gốc, vì object đó nên được coi là immutable.

```js
function MyDiv(props) {
  const divProps = Object.assign({}, props);
  delete divProps.layout;

  if (props.layout === 'horizontal') {
    return <div {...divProps} style={getHorizontalStyle()} />
  } else {
    return <div {...divProps} style={getVerticalStyle()} />
  }
}
```
