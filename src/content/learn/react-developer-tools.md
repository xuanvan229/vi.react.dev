---
title: React Developer Tools
---

<Intro>

Sử dụng React Developer Tools để kiểm tra các React [component](/learn/your-first-component), chỉnh sửa [props](/learn/passing-props-to-a-component) và [state](/learn/state-a-components-memory), và xác định các vấn đề về hiệu suất.

</Intro>

<YouWillLearn>

* Cách cài đặt React Developer Tools

</YouWillLearn>

## Extension trình duyệt {/*browser-extension*/}

Cách dễ nhất để debug các website được xây dựng bằng React là cài đặt extension trình duyệt React Developer Tools. Nó có sẵn cho một số trình duyệt phổ biến:

* [Cài đặt cho **Chrome**](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en)
* [Cài đặt cho **Firefox**](https://addons.mozilla.org/en-US/firefox/addon/react-devtools/)
* [Cài đặt cho **Edge**](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil)

Bây giờ, nếu bạn truy cập một website **được xây dựng bằng React,** bạn sẽ thấy các panel _Components_ và _Profiler_.

![Extension React Developer Tools](/images/docs/react-devtools-extension.png)

### Safari và các trình duyệt khác {/*safari-and-other-browsers*/}
Đối với các trình duyệt khác (ví dụ, Safari), cài đặt package npm [`react-devtools`](https://www.npmjs.com/package/react-devtools):
```bash
# Yarn
yarn global add react-devtools

# Npm
npm install -g react-devtools
```

Tiếp theo mở developer tools từ terminal:
```bash
react-devtools
```

Sau đó kết nối website của bạn bằng cách thêm thẻ `<script>` sau vào đầu `<head>` của website:
```html {3}
<html>
  <head>
    <script src="http://localhost:8097"></script>
```

Tải lại website trong trình duyệt để xem nó trong developer tools.

![React Developer Tools standalone](/images/docs/react-devtools-standalone.png)

## Mobile (React Native) {/*mobile-react-native*/}

Để kiểm tra các ứng dụng được xây dựng bằng [React Native](https://reactnative.dev/), bạn có thể sử dụng [React Native DevTools](https://reactnative.dev/docs/react-native-devtools), debugger tích hợp sẵn tích hợp sâu React Developer Tools. Tất cả các tính năng hoạt động giống hệt extension trình duyệt, bao gồm tô sáng và chọn phần tử native.

[Tìm hiểu thêm về debug trong React Native.](https://reactnative.dev/docs/debugging)

> Đối với các phiên bản React Native trước 0.76, vui lòng sử dụng bản standalone của React DevTools bằng cách làm theo hướng dẫn [Safari và các trình duyệt khác](#safari-and-other-browsers) ở trên.
