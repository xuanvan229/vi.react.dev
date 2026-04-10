---
title: experimental_taintObjectReference
version: experimental
---

<Experimental>

**API này là thử nghiệm và chưa có sẵn trong phiên bản ổn định của React.**

Bạn có thể thử nó bằng cách nâng cấp các gói React lên phiên bản thử nghiệm gần nhất:

- `react@experimental`
- `react-dom@experimental`
- `eslint-plugin-react-hooks@experimental`

Các phiên bản thử nghiệm của React có thể chứa bug. Đừng sử dụng chúng trong production.

API này chỉ có sẵn bên trong React Server Components.

</Experimental>


<Intro>

`taintObjectReference` cho phép bạn ngăn một instance object cụ thể không bị truyền đến một Client Component như một object `user`.

```js
experimental_taintObjectReference(message, object);
```

Để ngăn truyền một key, hash hoặc token, xem [`taintUniqueValue`](/reference/react/experimental_taintUniqueValue).

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `taintObjectReference(message, object)` {/*taintobjectreference*/}

Gọi `taintObjectReference` với một object để đăng ký nó với React như một thứ không được phép truyền đến Client như nguyên trạng:

```js
import {experimental_taintObjectReference} from 'react';

experimental_taintObjectReference(
  'Do not pass ALL environment variables to the client.',
  process.env
);
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `message`: Thông báo bạn muốn hiển thị nếu object được truyền đến một Client Component. Thông báo này sẽ được hiển thị như một phần của Error sẽ được ném ra nếu object được truyền đến một Client Component.

* `object`: Object cần được đánh dấu. Hàm và các instance class có thể được truyền vào `taintObjectReference` như `object`. Hàm và class đã bị chặn khỏi việc truyền đến Client Components nhưng thông báo lỗi mặc định của React sẽ được thay thế bằng những gì bạn đã định nghĩa trong `message`. Khi một instance cụ thể của Typed Array được truyền vào `taintObjectReference` như `object`, bất kỳ bản sao nào khác của Typed Array sẽ không bị đánh dấu.

#### Giá trị trả về {/*returns*/}

`experimental_taintObjectReference` trả về `undefined`.

#### Lưu ý {/*caveats*/}

- Tạo lại hoặc clone một object đã đánh dấu tạo ra một object mới không bị đánh dấu có thể chứa dữ liệu nhạy cảm. Ví dụ, nếu bạn có một object `user` đã đánh dấu, `const userInfo = {name: user.name, ssn: user.ssn}` hoặc `{...user}` sẽ tạo ra các object mới không bị đánh dấu. `taintObjectReference` chỉ bảo vệ chống lại các sai lầm đơn giản khi object được truyền qua đến Client Component mà không thay đổi.

<Pitfall>

**Đừng chỉ dựa vào việc đánh dấu cho bảo mật.** Đánh dấu một object không ngăn rò rỉ mọi giá trị có thể được phát sinh. Ví dụ, bản clone của một object đã đánh dấu sẽ tạo ra một object mới không bị đánh dấu. Sử dụng dữ liệu từ một object đã đánh dấu (ví dụ: `{secret: taintedObj.secret}`) sẽ tạo ra một giá trị hoặc object mới không bị đánh dấu. Đánh dấu là một lớp bảo vệ; một ứng dụng bảo mật sẽ có nhiều lớp bảo vệ, các API được thiết kế tốt và các mẫu cách ly.

</Pitfall>

---

## Cách sử dụng {/*usage*/}

### Ngăn dữ liệu người dùng vô tình đến client {/*prevent-user-data-from-unintentionally-reaching-the-client*/}

Một Client Component không nên bao giờ chấp nhận các object mang dữ liệu nhạy cảm. Lý tưởng nhất là các hàm tải dữ liệu không nên tiết lộ dữ liệu mà người dùng hiện tại không được phép truy cập. Đôi khi sai lầm xảy ra trong quá trình refactoring. Để bảo vệ chống lại những sai lầm này xảy ra về sau, chúng ta có thể "đánh dấu" object user trong API dữ liệu của chúng ta.

```js
import {experimental_taintObjectReference} from 'react';

export async function getUser(id) {
  const user = await db`SELECT * FROM users WHERE id = ${id}`;
  experimental_taintObjectReference(
    'Do not pass the entire user object to the client. ' +
      'Instead, pick off the specific properties you need for this use case.',
    user,
  );
  return user;
}
```

Bây giờ bất cứ khi nào ai đó cố gắng truyền object này đến một Client Component, một lỗi sẽ được ném ra với thông báo lỗi đã được truyền vào.

<DeepDive>

#### Bảo vệ chống rò rỉ trong tải dữ liệu {/*protecting-against-leaks-in-data-fetching*/}

Nếu bạn đang chạy môi trường Server Components có quyền truy cập vào dữ liệu nhạy cảm, bạn phải cẩn thận không truyền trực tiếp các object:

```js
// api.js
export async function getUser(id) {
  const user = await db`SELECT * FROM users WHERE id = ${id}`;
  return user;
}
```

```js
import { getUser } from 'api.js';
import { InfoCard } from 'components.js';

export async function Profile(props) {
  const user = await getUser(props.userId);
  // DO NOT DO THIS
  return <InfoCard user={user} />;
}
```

```js
// components.js
"use client";

export async function InfoCard({ user }) {
  return <div>{user.name}</div>;
}
```

Lý tưởng nhất, `getUser` không nên tiết lộ dữ liệu mà người dùng hiện tại không được phép truy cập. Để ngăn truyền object `user` đến một Client Component về sau, chúng ta có thể "đánh dấu" object user:


```js
// api.js
import {experimental_taintObjectReference} from 'react';

export async function getUser(id) {
  const user = await db`SELECT * FROM users WHERE id = ${id}`;
  experimental_taintObjectReference(
    'Do not pass the entire user object to the client. ' +
      'Instead, pick off the specific properties you need for this use case.',
    user,
  );
  return user;
}
```

Bây giờ nếu ai đó cố gắng truyền object `user` đến một Client Component, một lỗi sẽ được ném ra với thông báo lỗi đã được truyền vào.

</DeepDive>
