---
title: cacheSignal
---

<RSC>

`cacheSignal` hiện chỉ được sử dụng với [React Server Components](/blog/2023/03/22/react-labs-what-we-have-been-working-on-march-2023#react-server-components).

</RSC>

<Intro>

`cacheSignal` cho phép bạn biết khi nào thời gian tồn tại của `cache()` kết thúc.

```js
const signal = cacheSignal();
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `cacheSignal` {/*cachesignal*/}

Gọi `cacheSignal` để nhận một `AbortSignal`.

```js {3,7}
import {cacheSignal} from 'react';
async function Component() {
  await fetch(url, { signal: cacheSignal() });
}
```

Khi React hoàn tất việc render, `AbortSignal` sẽ bị hủy. Điều này cho phép bạn hủy bất kỳ công việc đang thực hiện nào mà không còn cần thiết nữa.
Rendering được coi là hoàn thành khi:
- React đã hoàn tất rendering thành công
- quá trình render bị hủy
- quá trình render thất bại

#### Tham số {/*parameters*/}

Hàm này không nhận bất kỳ tham số nào.

#### Giá trị trả về {/*returns*/}

`cacheSignal` trả về một `AbortSignal` nếu được gọi trong quá trình rendering. Ngược lại `cacheSignal()` trả về `null`.

#### Lưu ý {/*caveats*/}

- `cacheSignal` hiện chỉ dùng trong [React Server Components](/reference/rsc/server-components). Trong Client Components, nó sẽ luôn trả về `null`. Trong tương lai nó cũng sẽ được sử dụng cho Client Component khi một client cache được làm mới hoặc bị vô hiệu hóa. Bạn không nên giả định rằng nó sẽ luôn là null trên client.
- Nếu được gọi bên ngoài quá trình rendering, `cacheSignal` sẽ trả về `null` để làm rõ rằng phạm vi hiện tại không được cache mãi mãi.

---

## Cách sử dụng {/*usage*/}

### Hủy các yêu cầu đang thực hiện {/*cancel-in-flight-requests*/}

Gọi <CodeStep step={1}>`cacheSignal`</CodeStep> để hủy các yêu cầu đang thực hiện.

```js [[1, 4, "cacheSignal()"]]
import {cache, cacheSignal} from 'react';
const dedupedFetch = cache(fetch);
async function Component() {
  await dedupedFetch(url, { signal: cacheSignal() });
}
```

<Pitfall>
Bạn không thể dùng `cacheSignal` để hủy công việc bất đồng bộ được bắt đầu bên ngoài quá trình rendering, ví dụ:

```js
import {cacheSignal} from 'react';
// 🚩 Pitfall: Yêu cầu sẽ không thực sự bị hủy nếu rendering của `Component` kết thúc.
const response = fetch(url, { signal: cacheSignal() });
async function Component() {
  await response;
}
```
</Pitfall>

### Bỏ qua lỗi sau khi React hoàn tất rendering {/*ignore-errors-after-react-has-finished-rendering*/}

Nếu một hàm ném ra lỗi, có thể là do việc hủy (ví dụ: <CodeStep step={1}>kết nối Database</CodeStep> đã bị đóng). Bạn có thể dùng thuộc tính <CodeStep step={2}>`aborted`</CodeStep> để kiểm tra xem lỗi có phải do hủy hay là lỗi thực sự không. Bạn có thể muốn <CodeStep step={3}>bỏ qua các lỗi</CodeStep> do hủy.

```js [[1, 2, "./database"], [2, 8, "cacheSignal()?.aborted"], [3, 12, "return null"]]
import {cacheSignal} from "react";
import {queryDatabase, logError} from "./database";

async function getData(id) {
  try {
     return await queryDatabase(id);
  } catch (x) {
     if (!cacheSignal()?.aborted) {
        // chỉ log nếu đây là lỗi thực sự và không phải do hủy
       logError(x);
     }
     return null;
  }
}

async function Component({id}) {
  const data = await getData(id);
  if (data === null) {
    return <div>Không có dữ liệu</div>;
  }
  return <div>{data.name}</div>;
}
```
