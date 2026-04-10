---
title: Các API React DOM phía Client
---

<Intro>

Các API `react-dom/client` cho phép bạn render các component React trên client (trong trình duyệt). Các API này thường được sử dụng ở cấp cao nhất của ứng dụng để khởi tạo cây React. Một [framework](/learn/creating-a-react-app#full-stack-frameworks) có thể gọi chúng cho bạn. Hầu hết các component của bạn không cần import hoặc sử dụng chúng.

</Intro>

---

## Các API phía Client {/*client-apis*/}

* [`createRoot`](/reference/react-dom/client/createRoot) cho phép bạn tạo một root để hiển thị các component React bên trong một DOM node của trình duyệt.
* [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) cho phép bạn hiển thị các component React bên trong một DOM node của trình duyệt mà nội dung HTML đã được tạo trước đó bởi [`react-dom/server`.](/reference/react-dom/server)

---

## Hỗ trợ trình duyệt {/*browser-support*/}

React hỗ trợ tất cả các trình duyệt phổ biến, bao gồm Internet Explorer 9 trở lên. Một số polyfill được yêu cầu cho các trình duyệt cũ hơn như IE 9 và IE 10.
