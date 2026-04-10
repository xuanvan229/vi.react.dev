---
title: Tổng quan Tham chiếu React
---

<Intro>

Phần này cung cấp tài liệu tham chiếu chi tiết cho việc làm việc với React. Để giới thiệu về React, vui lòng truy cập phần [Học](/learn).

</Intro>

Tài liệu tham chiếu React được chia thành các phần chức năng:

## React {/*react*/}

Các tính năng lập trình của React:

* [Hooks](/reference/react/hooks) - Sử dụng các tính năng khác nhau của React từ component của bạn.
* [Components](/reference/react/components) - Các component tích hợp sẵn mà bạn có thể sử dụng trong JSX.
* [APIs](/reference/react/apis) - Các API hữu ích cho việc định nghĩa component.
* [Directives](/reference/rsc/directives) - Cung cấp hướng dẫn cho các bundler tương thích với React Server Components.

## React DOM {/*react-dom*/}

React DOM chứa các tính năng chỉ được hỗ trợ cho các ứng dụng web (chạy trong môi trường DOM của trình duyệt). Phần này được chia thành các mục sau:

* [Hooks](/reference/react-dom/hooks) - Hooks cho các ứng dụng web chạy trong môi trường DOM của trình duyệt.
* [Components](/reference/react-dom/components) - React hỗ trợ tất cả các component HTML và SVG tích hợp sẵn của trình duyệt.
* [APIs](/reference/react-dom) - Package `react-dom` chứa các phương thức chỉ được hỗ trợ trong ứng dụng web.
* [Client APIs](/reference/react-dom/client) - Các API `react-dom/client` cho phép bạn render các component React phía client (trong trình duyệt).
* [Server APIs](/reference/react-dom/server) - Các API `react-dom/server` cho phép bạn render các component React thành HTML trên server.
* [Static APIs](/reference/react-dom/static) - Các API `react-dom/static` cho phép bạn tạo HTML tĩnh cho các component React.

## React Compiler {/*react-compiler*/}

React Compiler là một công cụ tối ưu hóa tại thời điểm build, tự động memo hóa các component và giá trị React của bạn:

* [Cấu hình](/reference/react-compiler/configuration) - Các tùy chọn cấu hình cho React Compiler.
* [Directives](/reference/react-compiler/directives) - Các directive cấp hàm để kiểm soát quá trình biên dịch.
* [Biên dịch thư viện](/reference/react-compiler/compiling-libraries) - Hướng dẫn phát hành mã thư viện đã được biên dịch sẵn.

## ESLint Plugin React Hooks {/*eslint-plugin-react-hooks*/}

[Plugin ESLint cho React Hooks](/reference/eslint-plugin-react-hooks) giúp thực thi các Quy tắc của React:

* [Lints](/reference/eslint-plugin-react-hooks) - Tài liệu chi tiết cho từng lint với các ví dụ.

## Quy tắc của React {/*rules-of-react*/}

React có các idiom — hay quy tắc — về cách biểu diễn các pattern theo cách dễ hiểu và tạo ra các ứng dụng chất lượng cao:

* [Components và Hooks phải pure](/reference/rules/components-and-hooks-must-be-pure) – Tính pure giúp code của bạn dễ hiểu hơn, dễ debug hơn, và cho phép React tự động tối ưu hóa các component và hooks của bạn một cách chính xác.
* [React gọi Components và Hooks](/reference/rules/react-calls-components-and-hooks) – React chịu trách nhiệm render các component và hooks khi cần thiết để tối ưu hóa trải nghiệm người dùng.
* [Quy tắc của Hooks](/reference/rules/rules-of-hooks) – Hooks được định nghĩa bằng các hàm JavaScript, nhưng chúng đại diện cho một loại logic UI có thể tái sử dụng đặc biệt với các hạn chế về nơi chúng có thể được gọi.

## API Kế thừa {/*legacy-apis*/}

* [API Kế thừa](/reference/react/legacy) - Được export từ package `react`, nhưng không được khuyến nghị sử dụng trong code viết mới.
