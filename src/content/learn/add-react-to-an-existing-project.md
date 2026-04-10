---
title: Thêm React vào Dự Án Hiện Có
---

<Intro>

Nếu bạn muốn thêm một số tương tác vào dự án hiện có, bạn không cần phải viết lại nó bằng React. Thêm React vào stack hiện có của bạn, và render các React component tương tác ở bất kỳ đâu.

</Intro>

<Note>

**Bạn cần cài đặt [Node.js](https://nodejs.org/en/) để phát triển cục bộ.** Mặc dù bạn có thể [thử React](/learn/installation#try-react) trực tuyến hoặc với một trang HTML đơn giản, thực tế hầu hết các công cụ JavaScript mà bạn muốn sử dụng để phát triển đều yêu cầu Node.js.

</Note>

## Sử dụng React cho toàn bộ một subroute của website hiện có {/*using-react-for-an-entire-subroute-of-your-existing-website*/}

Giả sử bạn có một ứng dụng web hiện có tại `example.com` được xây dựng bằng công nghệ server khác (như Rails), và bạn muốn triển khai tất cả các route bắt đầu bằng `example.com/some-app/` hoàn toàn bằng React.

Đây là cách chúng tôi khuyến nghị thiết lập:

1. **Xây dựng phần React của ứng dụng** sử dụng một trong các [framework dựa trên React](/learn/creating-a-react-app).
2. **Chỉ định `/some-app` là *base path*** trong cấu hình framework của bạn (cách làm: [Next.js](https://nextjs.org/docs/app/api-reference/config/next-config-js/basePath), [Gatsby](https://www.gatsbyjs.com/docs/how-to/previews-deploys-hosting/path-prefix/)).
3. **Cấu hình server hoặc proxy** để tất cả các yêu cầu dưới `/some-app/` được xử lý bởi ứng dụng React của bạn.

Điều này đảm bảo phần React của ứng dụng có thể [hưởng lợi từ các best practice](/learn/build-a-react-app-from-scratch#consider-using-a-framework) được tích hợp trong các framework đó.

Nhiều framework dựa trên React là full-stack và cho phép ứng dụng React của bạn tận dụng server. Tuy nhiên, bạn có thể sử dụng cách tiếp cận tương tự ngay cả khi bạn không thể hoặc không muốn chạy JavaScript trên server. Trong trường hợp đó, hãy phục vụ HTML/CSS/JS export ([output của `next export`](https://nextjs.org/docs/advanced-features/static-html-export) cho Next.js, mặc định cho Gatsby) tại `/some-app/` thay thế.

## Sử dụng React cho một phần của trang hiện có {/*using-react-for-a-part-of-your-existing-page*/}

Giả sử bạn có một trang hiện có được xây dựng bằng công nghệ khác (có thể là server như Rails, hoặc client như Backbone), và bạn muốn render các React component tương tác ở đâu đó trên trang đó. Đây là cách phổ biến để tích hợp React -- thực ra, đó là cách hầu hết việc sử dụng React trông như thế nào tại Meta trong nhiều năm!

Bạn có thể làm điều này trong hai bước:

1. **Thiết lập môi trường JavaScript** cho phép bạn sử dụng [cú pháp JSX](/learn/writing-markup-with-jsx), chia code thành các module với cú pháp [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) / [`export`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export), và sử dụng các package (ví dụ, React) từ registry [npm](https://www.npmjs.com/).
2. **Render các React component** ở nơi bạn muốn xem chúng trên trang.

Cách tiếp cận chính xác phụ thuộc vào thiết lập trang hiện có của bạn, vì vậy hãy cùng xem xét một số chi tiết.

### Bước 1: Thiết lập môi trường JavaScript dạng module {/*step-1-set-up-a-modular-javascript-environment*/}

Môi trường JavaScript dạng module cho phép bạn viết các React component trong từng file riêng lẻ, thay vì viết tất cả code trong một file duy nhất. Nó cũng cho phép bạn sử dụng tất cả các package tuyệt vời được phát hành bởi các nhà phát triển khác trên registry [npm](https://www.npmjs.com/) -- bao gồm cả React! Cách thực hiện phụ thuộc vào thiết lập hiện có của bạn:

* **Nếu ứng dụng của bạn đã được chia thành các file sử dụng câu lệnh `import`,** hãy thử sử dụng thiết lập bạn đã có. Kiểm tra xem việc viết `<div />` trong code JS có gây ra lỗi cú pháp không. Nếu gây ra lỗi cú pháp, bạn có thể cần [chuyển đổi code JavaScript với Babel](https://babeljs.io/setup), và bật [Babel React preset](https://babeljs.io/docs/babel-preset-react) để sử dụng JSX.

* **Nếu ứng dụng của bạn không có thiết lập sẵn để biên dịch các module JavaScript,** hãy thiết lập với [Vite](https://vite.dev/). Cộng đồng Vite duy trì [nhiều tích hợp với các backend framework](https://github.com/vitejs/awesome-vite#integrations-with-backends), bao gồm Rails, Django, và Laravel. Nếu backend framework của bạn không có trong danh sách, [hãy theo hướng dẫn này](https://vite.dev/guide/backend-integration.html) để tích hợp thủ công Vite builds với backend của bạn.

Để kiểm tra xem thiết lập của bạn có hoạt động không, chạy lệnh này trong thư mục dự án:

<TerminalBlock>
npm install react react-dom
</TerminalBlock>

Sau đó thêm các dòng code này vào đầu file JavaScript chính của bạn (nó có thể được gọi là `index.js` hoặc `main.js`):

<Sandpack>

```html public/index.html hidden
<!DOCTYPE html>
<html>
  <head><title>My app</title></head>
  <body>
    <!-- Nội dung trang hiện có của bạn (trong ví dụ này, nó bị thay thế) -->
    <div id="root"></div>
  </body>
</html>
```

```js src/index.js active
import { createRoot } from 'react-dom/client';

// Xóa nội dung HTML hiện có
document.body.innerHTML = '<div id="app"></div>';

// Render React component của bạn thay thế
const root = createRoot(document.getElementById('app'));
root.render(<h1>Hello, world</h1>);
```

</Sandpack>

Nếu toàn bộ nội dung của trang bị thay thế bởi "Hello, world!", mọi thứ đã hoạt động! Tiếp tục đọc.

<Note>

Tích hợp môi trường JavaScript dạng module vào một dự án hiện có lần đầu tiên có thể cảm thấy đáng sợ, nhưng nó rất đáng! Nếu bạn bị kẹt, hãy thử [tài nguyên cộng đồng](/community) của chúng tôi hoặc [Vite Chat](https://chat.vite.dev/).

</Note>

### Bước 2: Render các React component ở bất kỳ đâu trên trang {/*step-2-render-react-components-anywhere-on-the-page*/}

Trong bước trước, bạn đặt code này ở đầu file chính:

```js
import { createRoot } from 'react-dom/client';

// Xóa nội dung HTML hiện có
document.body.innerHTML = '<div id="app"></div>';

// Render React component của bạn thay thế
const root = createRoot(document.getElementById('app'));
root.render(<h1>Hello, world</h1>);
```

Tất nhiên, bạn thực sự không muốn xóa nội dung HTML hiện có!

Hãy xóa code này đi.

Thay vào đó, bạn có thể muốn render các React component ở những vị trí cụ thể trong HTML. Mở trang HTML của bạn (hoặc các server template tạo ra nó) và thêm thuộc tính [`id`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/id) duy nhất vào bất kỳ thẻ nào, ví dụ:

```html
<!-- ... ở đâu đó trong html của bạn ... -->
<nav id="navigation"></nav>
<!-- ... thêm html ... -->
```

Điều này cho phép bạn tìm phần tử HTML đó bằng [`document.getElementById`](https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementById) và truyền nó cho [`createRoot`](/reference/react-dom/client/createRoot) để bạn có thể render React component của riêng mình bên trong:

<Sandpack>

```html public/index.html
<!DOCTYPE html>
<html>
  <head><title>My app</title></head>
  <body>
    <p>This paragraph is a part of HTML.</p>
    <nav id="navigation"></nav>
    <p>This paragraph is also a part of HTML.</p>
  </body>
</html>
```

```js src/index.js active
import { createRoot } from 'react-dom/client';

function NavigationBar() {
  // TODO: Actually implement a navigation bar
  return <h1>Hello from React!</h1>;
}

const domNode = document.getElementById('navigation');
const root = createRoot(domNode);
root.render(<NavigationBar />);
```

</Sandpack>

Lưu ý cách nội dung HTML gốc từ `index.html` được giữ nguyên, nhưng React component `NavigationBar` của bạn hiện xuất hiện bên trong `<nav id="navigation">` từ HTML của bạn. Đọc [tài liệu sử dụng `createRoot`](/reference/react-dom/client/createRoot#rendering-a-page-partially-built-with-react) để tìm hiểu thêm về việc render các React component bên trong trang HTML hiện có.

Khi bạn áp dụng React trong một dự án hiện có, thông thường bắt đầu với các component tương tác nhỏ (như nút bấm), và sau đó dần dần "di chuyển lên trên" cho đến khi toàn bộ trang của bạn được xây dựng bằng React. Nếu bạn đạt đến điểm đó, chúng tôi khuyến nghị chuyển sang [một React framework](/learn/creating-a-react-app) ngay sau đó để tận dụng tối đa React.

## Sử dụng React Native trong ứng dụng native mobile hiện có {/*using-react-native-in-an-existing-native-mobile-app*/}

[React Native](https://reactnative.dev/) cũng có thể được tích hợp vào các ứng dụng native hiện có một cách dần dần. Nếu bạn có ứng dụng native hiện có cho Android (Java hoặc Kotlin) hoặc iOS (Objective-C hoặc Swift), [hãy theo hướng dẫn này](https://reactnative.dev/docs/integration-with-existing-apps) để thêm một màn hình React Native vào nó.
