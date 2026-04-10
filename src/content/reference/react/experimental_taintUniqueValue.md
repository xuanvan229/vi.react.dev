---
title: experimental_taintUniqueValue
version: experimental
---

<Experimental>

**API này là thử nghiệm và chưa có sẵn trong phiên bản ổn định của React.**

Bạn có thể thử nó bằng cách nâng cấp các gói React lên phiên bản thử nghiệm gần nhất:

- `react@experimental`
- `react-dom@experimental`
- `eslint-plugin-react-hooks@experimental`

Các phiên bản thử nghiệm của React có thể chứa bug. Đừng sử dụng chúng trong production.

API này chỉ có sẵn bên trong [React Server Components](/reference/rsc/use-client).

</Experimental>


<Intro>

`taintUniqueValue` cho phép bạn ngăn các giá trị duy nhất không bị truyền đến Client Components như mật khẩu, key hoặc token.

```js
taintUniqueValue(errMessage, lifetime, value)
```

Để ngăn truyền một object chứa dữ liệu nhạy cảm, xem [`taintObjectReference`](/reference/react/experimental_taintObjectReference).

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `taintUniqueValue(message, lifetime, value)` {/*taintuniquevalue*/}

Gọi `taintUniqueValue` với một mật khẩu, token, key hoặc hash để đăng ký nó với React như một thứ không được phép truyền đến Client như nguyên trạng:

```js
import {experimental_taintUniqueValue} from 'react';

experimental_taintUniqueValue(
  'Do not pass secret keys to the client.',
  process,
  process.env.SECRET_KEY
);
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `message`: Thông báo bạn muốn hiển thị nếu `value` được truyền đến một Client Component. Thông báo này sẽ được hiển thị như một phần của Error sẽ được ném ra nếu `value` được truyền đến một Client Component.

* `lifetime`: Bất kỳ object nào chỉ định thời gian `value` nên bị đánh dấu. `value` sẽ bị chặn khỏi việc được gửi đến bất kỳ Client Component nào trong khi object này vẫn tồn tại. Ví dụ, truyền `globalThis` chặn giá trị trong suốt vòng đời của ứng dụng. `lifetime` thường là một object có các thuộc tính chứa `value`.

* `value`: Một chuỗi, bigint hoặc TypedArray. `value` phải là một chuỗi ký tự hoặc byte duy nhất với entropy cao như một token mật mã, private key, hash, hoặc một mật khẩu dài. `value` sẽ bị chặn khỏi việc được gửi đến bất kỳ Client Component nào.

#### Giá trị trả về {/*returns*/}

`experimental_taintUniqueValue` trả về `undefined`.

#### Lưu ý {/*caveats*/}

* Phát sinh các giá trị mới từ các giá trị đã đánh dấu có thể làm tổn hại đến bảo vệ đánh dấu. Các giá trị mới được tạo bằng cách viết hoa các giá trị đã đánh dấu, nối các giá trị chuỗi đã đánh dấu thành một chuỗi lớn hơn, chuyển đổi các giá trị đã đánh dấu sang base64, cắt substring các giá trị đã đánh dấu, và các phép biến đổi tương tự khác không bị đánh dấu trừ khi bạn gọi rõ ràng `taintUniqueValue` trên các giá trị mới được tạo này.
* Đừng sử dụng `taintUniqueValue` để bảo vệ các giá trị entropy thấp như mã PIN hoặc số điện thoại. Nếu bất kỳ giá trị nào trong một yêu cầu được kiểm soát bởi kẻ tấn công, họ có thể suy ra giá trị nào bị đánh dấu bằng cách liệt kê tất cả các giá trị có thể của bí mật.

---

## Cách sử dụng {/*usage*/}

### Ngăn một token không bị truyền đến Client Components {/*prevent-a-token-from-being-passed-to-client-components*/}

Để đảm bảo rằng thông tin nhạy cảm như mật khẩu, session token hoặc các giá trị duy nhất khác không vô tình bị truyền đến Client Components, hàm `taintUniqueValue` cung cấp một lớp bảo vệ. Khi một giá trị bị đánh dấu, bất kỳ nỗ lực nào để truyền nó đến một Client Component sẽ dẫn đến lỗi.

Đối số `lifetime` xác định khoảng thời gian giá trị vẫn bị đánh dấu. Đối với các giá trị nên vẫn bị đánh dấu vô thời hạn, các object như [`globalThis`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/globalThis) hoặc `process` có thể phục vụ như đối số `lifetime`. Các object này có vòng đời kéo dài suốt toàn bộ thời gian thực thi ứng dụng của bạn.

```js
import {experimental_taintUniqueValue} from 'react';

experimental_taintUniqueValue(
  'Do not pass a user password to the client.',
  globalThis,
  process.env.SECRET_KEY
);
```

Nếu vòng đời của giá trị đã đánh dấu gắn liền với một object, `lifetime` nên là object bao gọi giá trị đó. Điều này đảm bảo giá trị đã đánh dấu vẫn được bảo vệ trong suốt vòng đời của object bao gọi.

```js
import {experimental_taintUniqueValue} from 'react';

export async function getUser(id) {
  const user = await db`SELECT * FROM users WHERE id = ${id}`;
  experimental_taintUniqueValue(
    'Do not pass a user session token to the client.',
    user,
    user.session.token
  );
  return user;
}
```

Trong ví dụ này, object `user` phục vụ như đối số `lifetime`. Nếu object này được lưu trong một cache toàn cục hoặc được truy cập bởi một yêu cầu khác, session token vẫn bị đánh dấu.

<Pitfall>

**Đừng chỉ dựa vào đánh dấu cho bảo mật.** Đánh dấu một giá trị không chặn mọi giá trị có thể được phát sinh. Ví dụ, tạo một giá trị mới bằng cách viết hoa một chuỗi đã đánh dấu sẽ không đánh dấu giá trị mới.


```js
import {experimental_taintUniqueValue} from 'react';

const password = 'correct horse battery staple';

experimental_taintUniqueValue(
  'Do not pass the password to the client.',
  globalThis,
  password
);

const uppercasePassword = password.toUpperCase() // `uppercasePassword` is not tainted
```

Trong ví dụ này, hằng số `password` bị đánh dấu. Sau đó `password` được sử dụng để tạo một giá trị mới `uppercasePassword` bằng cách gọi phương thức `toUpperCase` trên `password`. `uppercasePassword` mới được tạo ra không bị đánh dấu.

Các cách tương tự khác để phát sinh các giá trị mới từ các giá trị đã đánh dấu như nối nó vào một chuỗi lớn hơn, chuyển đổi sang base64, hoặc trả về một substring đều tạo ra các giá trị không bị đánh dấu.

Đánh dấu chỉ bảo vệ chống lại các sai lầm đơn giản như truyền rõ ràng các giá trị bí mật đến client. Sai lầm trong việc gọi `taintUniqueValue` như sử dụng một global store bên ngoài React, không có object lifetime tương ứng, có thể khiến giá trị đã đánh dấu trở nên không bị đánh dấu. Đánh dấu là một lớp bảo vệ; một ứng dụng bảo mật sẽ có nhiều lớp bảo vệ, các API được thiết kế tốt và các mẫu cách ly.

</Pitfall>

<DeepDive>

#### Sử dụng `server-only` và `taintUniqueValue` để ngăn rò rỉ bí mật {/*using-server-only-and-taintuniquevalue-to-prevent-leaking-secrets*/}

Nếu bạn đang chạy môi trường Server Components có quyền truy cập vào các private key hoặc mật khẩu như mật khẩu cơ sở dữ liệu, bạn phải cẩn thận không truyền chúng đến một Client Component.

```js
export async function Dashboard(props) {
  // DO NOT DO THIS
  return <Overview password={process.env.API_PASSWORD} />;
}
```

```js
"use client";

import {useEffect} from '...'

export async function Overview({ password }) {
  useEffect(() => {
    const headers = { Authorization: password };
    fetch(url, { headers }).then(...);
  }, [password]);
  ...
}
```

Ví dụ này sẽ rò rỉ API token bí mật đến client. Nếu API token này có thể được sử dụng để truy cập dữ liệu mà người dùng cụ thể này không được phép truy cập, nó có thể dẫn đến vi phạm dữ liệu.

[comment]: <> (TODO: Link to `server-only` docs once they are written)

Lý tưởng nhất, các bí mật như thế này được trừu tượng hóa vào một tệp helper duy nhất chỉ có thể được import bởi các tiện ích dữ liệu đáng tin cậy trên server. Helper thậm chí có thể được gắn thẻ với [`server-only`](https://www.npmjs.com/package/server-only) để đảm bảo tệp này không được import trên client.

```js
import "server-only";

export function fetchAPI(url) {
  const headers = { Authorization: process.env.API_PASSWORD };
  return fetch(url, { headers });
}
```

Đôi khi sai lầm xảy ra trong quá trình refactoring và không phải tất cả đồng nghiệp của bạn đều biết về điều này.
Để bảo vệ chống lại những sai lầm này xảy ra về sau, chúng ta có thể "đánh dấu" mật khẩu thực tế:

```js
import "server-only";
import {experimental_taintUniqueValue} from 'react';

experimental_taintUniqueValue(
  'Do not pass the API token password to the client. ' +
    'Instead do all fetches on the server.'
  process,
  process.env.API_PASSWORD
);
```

Bây giờ bất cứ khi nào ai đó cố gắng truyền mật khẩu này đến một Client Component, hoặc gửi mật khẩu đến một Client Component với một Server Function, một lỗi sẽ được ném ra với thông báo bạn đã định nghĩa khi gọi `taintUniqueValue`.

</DeepDive>

---
