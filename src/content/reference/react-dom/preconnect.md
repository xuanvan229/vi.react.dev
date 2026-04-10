---
title: preconnect
---

<Intro>

`preconnect` cho phép bạn kết nối trước đến một server mà bạn dự kiến sẽ tải tài nguyên từ đó.

```js
preconnect("https://example.com");
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `preconnect(href)` {/*preconnect*/}

Để kết nối trước đến một host, gọi hàm `preconnect` từ `react-dom`.

```js
import { preconnect } from 'react-dom';

function AppRoot() {
  preconnect("https://example.com");
  // ...
}

```

[Xem thêm các ví dụ bên dưới.](#usage)

Hàm `preconnect` cung cấp cho trình duyệt một gợi ý rằng nó nên mở kết nối đến server đã cho. Nếu trình duyệt chọn làm vậy, điều này có thể tăng tốc việc tải tài nguyên từ server đó.

#### Tham số {/*parameters*/}

* `href`: một chuỗi. URL của server bạn muốn kết nối đến.


#### Giá trị trả về {/*returns*/}

`preconnect` không trả về gì.

#### Lưu ý {/*caveats*/}

* Nhiều lần gọi `preconnect` đến cùng một server có tác dụng giống như một lần gọi duy nhất.
* Trong trình duyệt, bạn có thể gọi `preconnect` trong bất kỳ tình huống nào: khi render một component, trong Effect, trong event handler, v.v.
* Trong server-side rendering hoặc khi render Server Components, `preconnect` chỉ có tác dụng nếu bạn gọi nó khi render một component hoặc trong một ngữ cảnh bất đồng bộ bắt nguồn từ việc render một component. Bất kỳ lệnh gọi nào khác sẽ bị bỏ qua.
* Nếu bạn biết chính xác tài nguyên nào mình cần, bạn có thể gọi [các hàm khác](/reference/react-dom/#resource-preloading-apis) để bắt đầu tải tài nguyên ngay lập tức.
* Không có lợi ích gì khi kết nối trước đến cùng server mà trang web đang được host vì nó đã được kết nối trước khi gợi ý được đưa ra.

---

## Cách sử dụng {/*usage*/}

### Kết nối trước khi render {/*preconnecting-when-rendering*/}

Gọi `preconnect` khi render một component nếu bạn biết rằng các phần tử con của nó sẽ tải tài nguyên bên ngoài từ host đó.

```js
import { preconnect } from 'react-dom';

function AppRoot() {
  preconnect("https://example.com");
  return ...;
}
```

### Kết nối trước trong event handler {/*preconnecting-in-an-event-handler*/}

Gọi `preconnect` trong event handler trước khi chuyển đổi đến trang hoặc state nơi tài nguyên bên ngoài sẽ được cần. Điều này bắt đầu quá trình sớm hơn so với việc gọi nó trong quá trình render trang hoặc state mới.

```js
import { preconnect } from 'react-dom';

function CallToAction() {
  const onClick = () => {
    preconnect('http://example.com');
    startWizard();
  }
  return (
    <button onClick={onClick}>Start Wizard</button>
  );
}
```
