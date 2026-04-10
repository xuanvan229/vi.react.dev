---
title: Các API React DOM
---

<Intro>

Package `react-dom` chứa các phương thức chỉ được hỗ trợ cho các ứng dụng web (chạy trong môi trường DOM của trình duyệt). Chúng không được hỗ trợ cho React Native.

</Intro>

---

## Các API {/*apis*/}

Các API này có thể được import từ các component của bạn. Chúng hiếm khi được sử dụng:

* [`createPortal`](/reference/react-dom/createPortal) cho phép bạn render các component con vào một phần khác của cây DOM.
* [`flushSync`](/reference/react-dom/flushSync) cho phép bạn buộc React flush một cập nhật state và cập nhật DOM một cách đồng bộ.

## Các API Tải trước Tài nguyên {/*resource-preloading-apis*/}

Các API này có thể được sử dụng để làm ứng dụng nhanh hơn bằng cách tải trước các tài nguyên như script, stylesheet và font ngay khi bạn biết mình cần chúng, ví dụ trước khi điều hướng đến trang khác nơi các tài nguyên đó sẽ được sử dụng.

[Các framework dựa trên React](/learn/creating-a-react-app) thường xử lý việc tải tài nguyên cho bạn, vì vậy bạn có thể không cần tự gọi các API này. Hãy tham khảo tài liệu của framework của bạn để biết chi tiết.

* [`prefetchDNS`](/reference/react-dom/prefetchDNS) cho phép bạn tải trước địa chỉ IP của một tên miền DNS mà bạn dự kiến sẽ kết nối đến.
* [`preconnect`](/reference/react-dom/preconnect) cho phép bạn kết nối đến một server mà bạn dự kiến sẽ yêu cầu tài nguyên từ đó, ngay cả khi bạn chưa biết cần những tài nguyên nào.
* [`preload`](/reference/react-dom/preload) cho phép bạn tải một stylesheet, font, hình ảnh hoặc script bên ngoài mà bạn dự kiến sẽ sử dụng.
* [`preloadModule`](/reference/react-dom/preloadModule) cho phép bạn tải một ESM module mà bạn dự kiến sẽ sử dụng.
* [`preinit`](/reference/react-dom/preinit) cho phép bạn tải và thực thi một script bên ngoài hoặc tải và chèn một stylesheet.
* [`preinitModule`](/reference/react-dom/preinitModule) cho phép bạn tải và thực thi một ESM module.

---

## Các điểm vào {/*entry-points*/}

Package `react-dom` cung cấp hai điểm vào bổ sung:

* [`react-dom/client`](/reference/react-dom/client) chứa các API để render các component React trên client (trong trình duyệt).
* [`react-dom/server`](/reference/react-dom/server) chứa các API để render các component React trên server.

---

## Các API đã bị loại bỏ {/*removed-apis*/}

Các API này đã bị loại bỏ trong React 19:

* [`findDOMNode`](https://18.react.dev/reference/react-dom/findDOMNode): xem [các giải pháp thay thế](https://18.react.dev/reference/react-dom/findDOMNode#alternatives).
* [`hydrate`](https://18.react.dev/reference/react-dom/hydrate): sử dụng [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) thay thế.
* [`render`](https://18.react.dev/reference/react-dom/render): sử dụng [`createRoot`](/reference/react-dom/client/createRoot) thay thế.
* [`unmountComponentAtNode`](/reference/react-dom/unmountComponentAtNode): sử dụng [`root.unmount()`](/reference/react-dom/client/createRoot#root-unmount) thay thế.
* [`renderToNodeStream`](https://18.react.dev/reference/react-dom/server/renderToNodeStream): sử dụng các API [`react-dom/server`](/reference/react-dom/server) thay thế.
* [`renderToStaticNodeStream`](https://18.react.dev/reference/react-dom/server/renderToStaticNodeStream): sử dụng các API [`react-dom/server`](/reference/react-dom/server) thay thế.
