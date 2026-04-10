---
title: prefetchDNS
---

<Intro>

`prefetchDNS` cho phép bạn tra cứu trước địa chỉ IP của một server mà bạn dự kiến sẽ tải tài nguyên từ đó.

```js
prefetchDNS("https://example.com");
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `prefetchDNS(href)` {/*prefetchdns*/}

Để tra cứu một host, gọi hàm `prefetchDNS` từ `react-dom`.

```js
import { prefetchDNS } from 'react-dom';

function AppRoot() {
  prefetchDNS("https://example.com");
  // ...
}

```

[Xem thêm các ví dụ bên dưới.](#usage)

Hàm prefetchDNS cung cấp cho trình duyệt một gợi ý rằng nó nên tra cứu địa chỉ IP của server đã cho. Nếu trình duyệt chọn làm vậy, điều này có thể tăng tốc việc tải tài nguyên từ server đó.

#### Tham số {/*parameters*/}

* `href`: một chuỗi. URL của server bạn muốn kết nối đến.

#### Giá trị trả về {/*returns*/}

`prefetchDNS` không trả về gì.

#### Lưu ý {/*caveats*/}

* Nhiều lần gọi `prefetchDNS` đến cùng một server có tác dụng giống như một lần gọi duy nhất.
* Trong trình duyệt, bạn có thể gọi `prefetchDNS` trong bất kỳ tình huống nào: khi render một component, trong Effect, trong event handler, v.v.
* Trong server-side rendering hoặc khi render Server Components, `prefetchDNS` chỉ có tác dụng nếu bạn gọi nó khi render một component hoặc trong một ngữ cảnh bất đồng bộ bắt nguồn từ việc render một component. Bất kỳ lệnh gọi nào khác sẽ bị bỏ qua.
* Nếu bạn biết chính xác tài nguyên nào mình cần, bạn có thể gọi [các hàm khác](/reference/react-dom/#resource-preloading-apis) để bắt đầu tải tài nguyên ngay lập tức.
* Không có lợi ích gì khi tra cứu trước cùng server mà trang web đang được host vì nó đã được tra cứu trước khi gợi ý được đưa ra.
* So với [`preconnect`](/reference/react-dom/preconnect), `prefetchDNS` có thể tốt hơn nếu bạn đang kết nối dự đoán đến một số lượng lớn domain, trong trường hợp đó chi phí của các kết nối trước có thể lớn hơn lợi ích.

---

## Cách sử dụng {/*usage*/}

### Tra cứu trước DNS khi render {/*prefetching-dns-when-rendering*/}

Gọi `prefetchDNS` khi render một component nếu bạn biết rằng các phần tử con của nó sẽ tải tài nguyên bên ngoài từ host đó.

```js
import { prefetchDNS } from 'react-dom';

function AppRoot() {
  prefetchDNS("https://example.com");
  return ...;
}
```

### Tra cứu trước DNS trong event handler {/*prefetching-dns-in-an-event-handler*/}

Gọi `prefetchDNS` trong event handler trước khi chuyển đổi đến trang hoặc state nơi tài nguyên bên ngoài sẽ được cần. Điều này bắt đầu quá trình sớm hơn so với việc gọi nó trong quá trình render trang hoặc state mới.

```js
import { prefetchDNS } from 'react-dom';

function CallToAction() {
  const onClick = () => {
    prefetchDNS('http://example.com');
    startWizard();
  }
  return (
    <button onClick={onClick}>Start Wizard</button>
  );
}
```
