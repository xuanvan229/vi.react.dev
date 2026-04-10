---
title: Cài đặt
---

<Intro>

React đã được thiết kế từ đầu để áp dụng dần dần. Bạn có thể sử dụng ít hoặc nhiều React tùy ý. Cho dù bạn muốn trải nghiệm React, thêm một số tương tác vào trang HTML, hay bắt đầu một ứng dụng phức tạp chạy bằng React, phần này sẽ giúp bạn bắt đầu.

</Intro>

## Thử React {/*try-react*/}

Bạn không cần cài đặt bất cứ thứ gì để thử React. Hãy thử chỉnh sửa sandbox này!

<Sandpack>

```js
function Greeting({ name }) {
  return <h1>Hello, {name}</h1>;
}

export default function App() {
  return <Greeting name="world" />
}
```

</Sandpack>

Bạn có thể chỉnh sửa trực tiếp hoặc mở nó trong tab mới bằng cách nhấn nút "Fork" ở góc trên bên phải.

Hầu hết các trang trong tài liệu React đều chứa các sandbox như thế này. Ngoài tài liệu React, có nhiều sandbox trực tuyến hỗ trợ React: ví dụ, [CodeSandbox](https://codesandbox.io/s/new), [StackBlitz](https://stackblitz.com/fork/react), hoặc [CodePen.](https://codepen.io/pen?template=QWYVwWN)

Để thử React trên máy tính cục bộ, [tải trang HTML này.](https://gist.githubusercontent.com/gaearon/0275b1e1518599bbeafcde4722e79ed1/raw/db72dcbf3384ee1708c4a07d3be79860db04bff0/example.html) Mở nó trong trình soạn thảo và trình duyệt của bạn!

## Tạo ứng dụng React {/*creating-a-react-app*/}

Nếu bạn muốn bắt đầu một ứng dụng React mới, bạn có thể [tạo ứng dụng React](/learn/creating-a-react-app) sử dụng một framework được khuyến nghị.

## Xây dựng ứng dụng React từ đầu {/*build-a-react-app-from-scratch*/}

Nếu framework không phù hợp với dự án của bạn, bạn thích xây dựng framework riêng, hoặc bạn chỉ muốn tìm hiểu những kiến thức cơ bản của ứng dụng React, bạn có thể [xây dựng ứng dụng React từ đầu](/learn/build-a-react-app-from-scratch).

## Thêm React vào dự án hiện có {/*add-react-to-an-existing-project*/}

Nếu bạn muốn thử sử dụng React trong ứng dụng hoặc website hiện có, bạn có thể [thêm React vào dự án hiện có.](/learn/add-react-to-an-existing-project)


<Note>

#### Tôi có nên sử dụng Create React App không? {/*should-i-use-create-react-app*/}

Không. Create React App đã ngừng phát triển. Để biết thêm thông tin, xem [Ngừng hỗ trợ Create React App](/blog/2025/02/14/sunsetting-create-react-app).

</Note>

## Bước tiếp theo {/*next-steps*/}

Hãy đến trang hướng dẫn [Bắt đầu nhanh](/learn) để tìm hiểu các khái niệm React quan trọng nhất mà bạn sẽ gặp hàng ngày.

