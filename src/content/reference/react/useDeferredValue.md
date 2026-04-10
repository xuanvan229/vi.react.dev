---
title: useDeferredValue
---

<Intro>

`useDeferredValue` là một React Hook cho phép bạn trì hoãn việc cập nhật một phần của giao diện người dùng.

```js
const deferredValue = useDeferredValue(value)
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `useDeferredValue(value, initialValue?)` {/*usedeferredvalue*/}

Gọi `useDeferredValue` ở cấp cao nhất của component để lấy phiên bản trì hoãn của giá trị đó.

```js
import { useState, useDeferredValue } from 'react';

function SearchPage() {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query);
  // ...
}
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `value`: Giá trị bạn muốn trì hoãn. Nó có thể có bất kỳ kiểu nào.
* **tùy chọn** `initialValue`: Giá trị sử dụng trong lần render đầu tiên của component. Nếu tùy chọn này bị bỏ qua, `useDeferredValue` sẽ không trì hoãn trong lần render đầu tiên, vì không có phiên bản trước của `value` để render thay thế.


#### Giá trị trả về {/*returns*/}

- `currentValue`: Trong lần render đầu tiên, giá trị trì hoãn trả về sẽ là `initialValue`, hoặc giống với giá trị bạn đã cung cấp. Trong các lần cập nhật, React sẽ trước tiên thử render lại với giá trị cũ (vì vậy sẽ trả về giá trị cũ), sau đó thử render lại ở nền với giá trị mới (vì vậy sẽ trả về giá trị đã cập nhật).

#### Lưu ý {/*caveats*/}

- Khi một cập nhật nằm trong một Transition, `useDeferredValue` luôn trả về `value` mới và không tạo ra render trì hoãn, vì cập nhật đã được trì hoãn rồi.

- Các giá trị bạn truyền vào `useDeferredValue` phải là các giá trị nguyên thủy (như chuỗi và số) hoặc các object được tạo bên ngoài quá trình render. Nếu bạn tạo một object mới trong khi render và ngay lập tức truyền nó vào `useDeferredValue`, nó sẽ khác nhau ở mỗi lần render, gây ra các lần render lại ở nền không cần thiết.

- Khi `useDeferredValue` nhận được một giá trị khác (so sánh bằng [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is)), ngoài lần render hiện tại (khi nó vẫn sử dụng giá trị trước), nó lên lịch một lần render lại ở nền với giá trị mới. Render lại ở nền có thể bị gián đoạn: nếu có một cập nhật khác cho `value`, React sẽ khởi động lại render lại ở nền từ đầu. Ví dụ, nếu người dùng gõ vào một input nhanh hơn một biểu đồ nhận giá trị trì hoãn có thể render lại, biểu đồ sẽ chỉ render lại sau khi người dùng ngừng gõ.

- `useDeferredValue` được tích hợp với [`<Suspense>`.](/reference/react/Suspense) Nếu cập nhật ở nền do một giá trị mới gây ra làm tạm dừng giao diện, người dùng sẽ không thấy fallback. Họ sẽ thấy giá trị trì hoãn cũ cho đến khi dữ liệu tải xong.

- `useDeferredValue` tự nó không ngăn chặn các network request thêm.

- Không có độ trễ cố định do `useDeferredValue` gây ra. Ngay khi React hoàn thành lần render lại ban đầu, React sẽ ngay lập tức bắt đầu làm việc với render lại ở nền với giá trị trì hoãn mới. Bất kỳ cập nhật nào được gây ra bởi các sự kiện (như gõ phím) sẽ làm gián đoạn render lại ở nền và được ưu tiên hơn.

- Render lại ở nền do `useDeferredValue` không kích hoạt các Effect cho đến khi nó được cam kết vào màn hình. Nếu render lại ở nền tạm dừng, các Effect của nó sẽ chạy sau khi dữ liệu tải và giao diện cập nhật.

---

## Cách sử dụng {/*usage*/}

### Hiển thị nội dung cũ trong khi nội dung mới đang tải {/*showing-stale-content-while-fresh-content-is-loading*/}

Gọi `useDeferredValue` ở cấp cao nhất của component để trì hoãn việc cập nhật một phần của giao diện.

```js [[1, 5, "query"], [2, 5, "deferredQuery"]]
import { useState, useDeferredValue } from 'react';

function SearchPage() {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query);
  // ...
}
```

Trong lần render đầu tiên, <CodeStep step={2}>giá trị trì hoãn</CodeStep> sẽ giống với <CodeStep step={1}>giá trị</CodeStep> bạn đã cung cấp.

Trong các lần cập nhật, <CodeStep step={2}>giá trị trì hoãn</CodeStep> sẽ "tụt lại" so với <CodeStep step={1}>giá trị</CodeStep> mới nhất. Cụ thể, React trước tiên sẽ render lại *mà không* cập nhật giá trị trì hoãn, sau đó cố gắng render lại với giá trị mới nhận được ở nền.

**Hãy xem một ví dụ để hiểu khi nào điều này hữu ích.**

<Note>

Ví dụ này giả định bạn sử dụng một nguồn dữ liệu hỗ trợ Suspense:

- Tải dữ liệu với các framework hỗ trợ Suspense như [Relay](https://relay.dev/docs/guided-tour/rendering/loading-states/) và [Next.js](https://nextjs.org/docs/app/getting-started/fetching-data#with-suspense)
- Lazy-loading code component với [`lazy`](/reference/react/lazy)
- Đọc giá trị của một Promise với [`use`](/reference/react/use)

[Tìm hiểu thêm về Suspense và các giới hạn của nó.](/reference/react/Suspense)

</Note>


Trong ví dụ này, component `SearchResults` [tạm dừng](/reference/react/Suspense#displaying-a-fallback-while-content-is-loading) trong khi tải kết quả tìm kiếm. Thử gõ `"a"`, đợi kết quả, rồi chỉnh sửa thành `"ab"`. Kết quả cho `"a"` bị thay thế bởi loading fallback.

<Sandpack>

```js src/App.js
import { Suspense, useState } from 'react';
import SearchResults from './SearchResults.js';

export default function App() {
  const [query, setQuery] = useState('');
  return (
    <>
      <label>
        Search albums:
        <input value={query} onChange={e => setQuery(e.target.value)} />
      </label>
      <Suspense fallback={<h2>Loading...</h2>}>
        <SearchResults query={query} />
      </Suspense>
    </>
  );
}
```

```js src/SearchResults.js
import {use} from 'react';
import { fetchData } from './data.js';

export default function SearchResults({ query }) {
  if (query === '') {
    return null;
  }
  const albums = use(fetchData(`/search?q=${query}`));
  if (albums.length === 0) {
    return <p>No matches for <i>"{query}"</i></p>;
  }
  return (
    <ul>
      {albums.map(album => (
        <li key={album.id}>
          {album.title} ({album.year})
        </li>
      ))}
    </ul>
  );
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url.startsWith('/search?q=')) {
    return await getSearchResults(url.slice('/search?q='.length));
  } else {
    throw Error('Not implemented');
  }
}

async function getSearchResults(query) {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 1000);
  });

  const allAlbums = [{
    id: 13,
    title: 'Let It Be',
    year: 1970
  }, {
    id: 12,
    title: 'Abbey Road',
    year: 1969
  }, {
    id: 11,
    title: 'Yellow Submarine',
    year: 1969
  }, {
    id: 10,
    title: 'The Beatles',
    year: 1968
  }, {
    id: 9,
    title: 'Magical Mystery Tour',
    year: 1967
  }, {
    id: 8,
    title: 'Sgt. Pepper\'s Lonely Hearts Club Band',
    year: 1967
  }, {
    id: 7,
    title: 'Revolver',
    year: 1966
  }, {
    id: 6,
    title: 'Rubber Soul',
    year: 1965
  }, {
    id: 5,
    title: 'Help!',
    year: 1965
  }, {
    id: 4,
    title: 'Beatles For Sale',
    year: 1964
  }, {
    id: 3,
    title: 'A Hard Day\'s Night',
    year: 1964
  }, {
    id: 2,
    title: 'With The Beatles',
    year: 1963
  }, {
    id: 1,
    title: 'Please Please Me',
    year: 1963
  }];

  const lowerQuery = query.trim().toLowerCase();
  return allAlbums.filter(album => {
    const lowerTitle = album.title.toLowerCase();
    return (
      lowerTitle.startsWith(lowerQuery) ||
      lowerTitle.indexOf(' ' + lowerQuery) !== -1
    )
  });
}
```

```css
input { margin: 10px; }
```

</Sandpack>

Một pattern UI thay thế phổ biến là *trì hoãn* việc cập nhật danh sách kết quả và tiếp tục hiển thị kết quả trước đó cho đến khi kết quả mới sẵn sàng. Gọi `useDeferredValue` để truyền một phiên bản trì hoãn của query xuống:

```js {3,11}
export default function App() {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query);
  return (
    <>
      <label>
        Search albums:
        <input value={query} onChange={e => setQuery(e.target.value)} />
      </label>
      <Suspense fallback={<h2>Loading...</h2>}>
        <SearchResults query={deferredQuery} />
      </Suspense>
    </>
  );
}
```

`query` sẽ cập nhật ngay lập tức, vì vậy input sẽ hiển thị giá trị mới. Tuy nhiên, `deferredQuery` sẽ giữ giá trị trước của nó cho đến khi dữ liệu đã tải, vì vậy `SearchResults` sẽ hiển thị kết quả cũ trong một khoảng thời gian.

Nhập `"a"` vào ví dụ dưới đây, đợi kết quả tải, rồi chỉnh sửa input thành `"ab"`. Lưu ý rằng thay vì Suspense fallback, bạn bây giờ thấy danh sách kết quả cũ cho đến khi kết quả mới đã tải:

<Sandpack>

```js src/App.js
import { Suspense, useState, useDeferredValue } from 'react';
import SearchResults from './SearchResults.js';

export default function App() {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query);
  return (
    <>
      <label>
        Search albums:
        <input value={query} onChange={e => setQuery(e.target.value)} />
      </label>
      <Suspense fallback={<h2>Loading...</h2>}>
        <SearchResults query={deferredQuery} />
      </Suspense>
    </>
  );
}
```

```js src/SearchResults.js
import {use} from 'react';
import { fetchData } from './data.js';

export default function SearchResults({ query }) {
  if (query === '') {
    return null;
  }
  const albums = use(fetchData(`/search?q=${query}`));
  if (albums.length === 0) {
    return <p>No matches for <i>"{query}"</i></p>;
  }
  return (
    <ul>
      {albums.map(album => (
        <li key={album.id}>
          {album.title} ({album.year})
        </li>
      ))}
    </ul>
  );
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url.startsWith('/search?q=')) {
    return await getSearchResults(url.slice('/search?q='.length));
  } else {
    throw Error('Not implemented');
  }
}

async function getSearchResults(query) {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 1000);
  });

  const allAlbums = [{
    id: 13,
    title: 'Let It Be',
    year: 1970
  }, {
    id: 12,
    title: 'Abbey Road',
    year: 1969
  }, {
    id: 11,
    title: 'Yellow Submarine',
    year: 1969
  }, {
    id: 10,
    title: 'The Beatles',
    year: 1968
  }, {
    id: 9,
    title: 'Magical Mystery Tour',
    year: 1967
  }, {
    id: 8,
    title: 'Sgt. Pepper\'s Lonely Hearts Club Band',
    year: 1967
  }, {
    id: 7,
    title: 'Revolver',
    year: 1966
  }, {
    id: 6,
    title: 'Rubber Soul',
    year: 1965
  }, {
    id: 5,
    title: 'Help!',
    year: 1965
  }, {
    id: 4,
    title: 'Beatles For Sale',
    year: 1964
  }, {
    id: 3,
    title: 'A Hard Day\'s Night',
    year: 1964
  }, {
    id: 2,
    title: 'With The Beatles',
    year: 1963
  }, {
    id: 1,
    title: 'Please Please Me',
    year: 1963
  }];

  const lowerQuery = query.trim().toLowerCase();
  return allAlbums.filter(album => {
    const lowerTitle = album.title.toLowerCase();
    return (
      lowerTitle.startsWith(lowerQuery) ||
      lowerTitle.indexOf(' ' + lowerQuery) !== -1
    )
  });
}
```

```css
input { margin: 10px; }
```

</Sandpack>

<DeepDive>

#### Cơ chế trì hoãn giá trị hoạt động như thế nào? {/*how-does-deferring-a-value-work-under-the-hood*/}

Bạn có thể nghĩ về nó như xảy ra theo hai bước:

1. **Đầu tiên, React render lại với `query` mới (`"ab"`) nhưng với `deferredQuery` cũ (vẫn là `"a"`).** Giá trị `deferredQuery`, mà bạn truyền cho danh sách kết quả, bị *trì hoãn:* nó "tụt lại" so với giá trị `query`.

2. **Ở nền, React cố gắng render lại với cả `query` và `deferredQuery` được cập nhật thành `"ab"`.** Nếu lần render lại này hoàn thành, React sẽ hiển thị nó trên màn hình. Tuy nhiên, nếu nó tạm dừng (kết quả cho `"ab"` chưa tải xong), React sẽ từ bỏ lần thử render này và thử lại lần render lại này sau khi dữ liệu đã tải. Người dùng sẽ tiếp tục thấy giá trị trì hoãn cũ cho đến khi dữ liệu sẵn sàng.

Render "nền" trì hoãn có thể bị gián đoạn. Ví dụ, nếu bạn gõ vào input một lần nữa, React sẽ từ bỏ nó và khởi động lại với giá trị mới. React sẽ luôn sử dụng giá trị được cung cấp mới nhất.

Lưu ý rằng vẫn có một network request cho mỗi lần nhấn phím. Điều đang được trì hoãn ở đây là hiển thị kết quả (cho đến khi chúng sẵn sàng), không phải bản thân các network request. Ngay cả khi người dùng tiếp tục gõ, phản hồi cho mỗi lần nhấn phím được lưu vào cache, vì vậy nhấn Backspace là tức thời và không tải lại.

</DeepDive>

---

### Chỉ thị rằng nội dung đã cũ {/*indicating-that-the-content-is-stale*/}

Trong ví dụ trên, không có dấu hiệu nào cho thấy danh sách kết quả cho query mới nhất vẫn đang tải. Điều này có thể gây nhầm lẫn cho người dùng nếu kết quả mới mất một lúc để tải. Để làm rõ hơn cho người dùng rằng danh sách kết quả không khớp với query mới nhất, bạn có thể thêm chỉ thị trực quan khi danh sách kết quả cũ được hiển thị:

```js {2}
<div style={{
  opacity: query !== deferredQuery ? 0.5 : 1,
}}>
  <SearchResults query={deferredQuery} />
</div>
```

Với thay đổi này, ngay khi bạn bắt đầu gõ, danh sách kết quả cũ sẽ hơi mờ cho đến khi danh sách kết quả mới tải xong. Bạn cũng có thể thêm CSS transition để trì hoãn việc mờ dần để cảm giác dần dần hơn, như trong ví dụ dưới đây:

<Sandpack>

```js src/App.js
import { Suspense, useState, useDeferredValue } from 'react';
import SearchResults from './SearchResults.js';

export default function App() {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query);
  const isStale = query !== deferredQuery;
  return (
    <>
      <label>
        Search albums:
        <input value={query} onChange={e => setQuery(e.target.value)} />
      </label>
      <Suspense fallback={<h2>Loading...</h2>}>
        <div style={{
          opacity: isStale ? 0.5 : 1,
          transition: isStale ? 'opacity 0.2s 0.2s linear' : 'opacity 0s 0s linear'
        }}>
          <SearchResults query={deferredQuery} />
        </div>
      </Suspense>
    </>
  );
}
```

```js src/SearchResults.js
import {use} from 'react';
import { fetchData } from './data.js';

export default function SearchResults({ query }) {
  if (query === '') {
    return null;
  }
  const albums = use(fetchData(`/search?q=${query}`));
  if (albums.length === 0) {
    return <p>No matches for <i>"{query}"</i></p>;
  }
  return (
    <ul>
      {albums.map(album => (
        <li key={album.id}>
          {album.title} ({album.year})
        </li>
      ))}
    </ul>
  );
}
```

```js src/data.js hidden
// Note: the way you would do data fetching depends on
// the framework that you use together with Suspense.
// Normally, the caching logic would be inside a framework.

let cache = new Map();

export function fetchData(url) {
  if (!cache.has(url)) {
    cache.set(url, getData(url));
  }
  return cache.get(url);
}

async function getData(url) {
  if (url.startsWith('/search?q=')) {
    return await getSearchResults(url.slice('/search?q='.length));
  } else {
    throw Error('Not implemented');
  }
}

async function getSearchResults(query) {
  // Add a fake delay to make waiting noticeable.
  await new Promise(resolve => {
    setTimeout(resolve, 1000);
  });

  const allAlbums = [{
    id: 13,
    title: 'Let It Be',
    year: 1970
  }, {
    id: 12,
    title: 'Abbey Road',
    year: 1969
  }, {
    id: 11,
    title: 'Yellow Submarine',
    year: 1969
  }, {
    id: 10,
    title: 'The Beatles',
    year: 1968
  }, {
    id: 9,
    title: 'Magical Mystery Tour',
    year: 1967
  }, {
    id: 8,
    title: 'Sgt. Pepper\'s Lonely Hearts Club Band',
    year: 1967
  }, {
    id: 7,
    title: 'Revolver',
    year: 1966
  }, {
    id: 6,
    title: 'Rubber Soul',
    year: 1965
  }, {
    id: 5,
    title: 'Help!',
    year: 1965
  }, {
    id: 4,
    title: 'Beatles For Sale',
    year: 1964
  }, {
    id: 3,
    title: 'A Hard Day\'s Night',
    year: 1964
  }, {
    id: 2,
    title: 'With The Beatles',
    year: 1963
  }, {
    id: 1,
    title: 'Please Please Me',
    year: 1963
  }];

  const lowerQuery = query.trim().toLowerCase();
  return allAlbums.filter(album => {
    const lowerTitle = album.title.toLowerCase();
    return (
      lowerTitle.startsWith(lowerQuery) ||
      lowerTitle.indexOf(' ' + lowerQuery) !== -1
    )
  });
}
```

```css
input { margin: 10px; }
```

</Sandpack>

---

### Trì hoãn render lại cho một phần của giao diện {/*deferring-re-rendering-for-a-part-of-the-ui*/}

Bạn cũng có thể áp dụng `useDeferredValue` như một tối ưu hóa hiệu suất. Nó hữu ích khi một phần của giao diện chậm để render lại, không có cách dễ dàng để tối ưu hóa nó, và bạn muốn ngăn nó chặn phần còn lại của giao diện.

Hãy tưởng tượng bạn có một text field và một component (như biểu đồ hoặc danh sách dài) render lại ở mỗi lần nhấn phím:

```js
function App() {
  const [text, setText] = useState('');
  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <SlowList text={text} />
    </>
  );
}
```

Đầu tiên, tối ưu hóa `SlowList` để bỏ qua render lại khi props của nó giống nhau. Để làm điều này, [bọc nó trong `memo`:](/reference/react/memo#skipping-re-rendering-when-props-are-unchanged)

```js {1,3}
const SlowList = memo(function SlowList({ text }) {
  // ...
});
```

Tuy nhiên, điều này chỉ giúp ích nếu props `SlowList` *giống nhau* như trong lần render trước. Vấn đề bạn đang gặp phải bây giờ là khi chúng *khác nhau*, và khi bạn thực sự cần hiển thị đầu ra trực quan khác nhau.

Cụ thể, vấn đề hiệu suất chính là mỗi khi bạn gõ vào input, `SlowList` nhận props mới và render lại toàn bộ cây của nó khiến việc gõ phím cảm thấy giật cục. Trong trường hợp này, `useDeferredValue` cho phép bạn ưu tiên cập nhật input (phải nhanh) hơn cập nhật danh sách kết quả (được phép chậm hơn):

```js {3,7}
function App() {
  const [text, setText] = useState('');
  const deferredText = useDeferredValue(text);
  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <SlowList text={deferredText} />
    </>
  );
}
```

Điều này không làm cho việc render lại `SlowList` nhanh hơn. Tuy nhiên, nó cho React biết rằng việc render lại danh sách có thể được giảm ưu tiên để nó không chặn các lần nhấn phím. Danh sách sẽ "tụt lại" so với input và sau đó "bắt kịp". Như trước, React sẽ cố gắng cập nhật danh sách càng sớm càng tốt, nhưng sẽ không chặn người dùng gõ phím.

<Recipes titleText="Sự khác biệt giữa useDeferredValue và render lại không được tối ưu hóa" titleId="examples">

#### Render lại trì hoãn của danh sách {/*deferred-re-rendering-of-the-list*/}

Trong ví dụ này, mỗi mục trong component `SlowList` **bị làm chậm nhân tạo** để bạn có thể thấy cách `useDeferredValue` giúp bạn giữ input phản hồi. Gõ vào input và lưu ý rằng việc gõ phím cảm thấy nhanh nhẹn trong khi danh sách "tụt lại" phía sau.

<Sandpack>

```js
import { useState, useDeferredValue } from 'react';
import SlowList from './SlowList.js';

export default function App() {
  const [text, setText] = useState('');
  const deferredText = useDeferredValue(text);
  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <SlowList text={deferredText} />
    </>
  );
}
```

```js {expectedErrors: {'react-compiler': [19, 20]}} src/SlowList.js
import { memo } from 'react';

const SlowList = memo(function SlowList({ text }) {
  // Log once. The actual slowdown is inside SlowItem.
  console.log('[ARTIFICIALLY SLOW] Rendering 250 <SlowItem />');

  let items = [];
  for (let i = 0; i < 250; i++) {
    items.push(<SlowItem key={i} text={text} />);
  }
  return (
    <ul className="items">
      {items}
    </ul>
  );
});

function SlowItem({ text }) {
  let startTime = performance.now();
  while (performance.now() - startTime < 1) {
    // Do nothing for 1 ms per item to emulate extremely slow code
  }

  return (
    <li className="item">
      Text: {text}
    </li>
  )
}

export default SlowList;
```

```css
.items {
  padding: 0;
}

.item {
  list-style: none;
  display: block;
  height: 40px;
  padding: 5px;
  margin-top: 10px;
  border-radius: 4px;
  border: 1px solid #aaa;
}
```

</Sandpack>

<Solution />

#### Render lại không được tối ưu hóa của danh sách {/*unoptimized-re-rendering-of-the-list*/}

Trong ví dụ này, mỗi mục trong component `SlowList` **bị làm chậm nhân tạo**, nhưng không có `useDeferredValue`.

Lưu ý cách gõ vào input cảm thấy rất giật cục. Điều này là vì không có `useDeferredValue`, mỗi lần nhấn phím buộc toàn bộ danh sách render lại ngay lập tức theo cách không thể gián đoạn.

<Sandpack>

```js
import { useState } from 'react';
import SlowList from './SlowList.js';

export default function App() {
  const [text, setText] = useState('');
  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <SlowList text={text} />
    </>
  );
}
```

```js {expectedErrors: {'react-compiler': [19, 20]}} src/SlowList.js
import { memo } from 'react';

const SlowList = memo(function SlowList({ text }) {
  // Log once. The actual slowdown is inside SlowItem.
  console.log('[ARTIFICIALLY SLOW] Rendering 250 <SlowItem />');

  let items = [];
  for (let i = 0; i < 250; i++) {
    items.push(<SlowItem key={i} text={text} />);
  }
  return (
    <ul className="items">
      {items}
    </ul>
  );
});

function SlowItem({ text }) {
  let startTime = performance.now();
  while (performance.now() - startTime < 1) {
    // Do nothing for 1 ms per item to emulate extremely slow code
  }

  return (
    <li className="item">
      Text: {text}
    </li>
  )
}

export default SlowList;
```

```css
.items {
  padding: 0;
}

.item {
  list-style: none;
  display: block;
  height: 40px;
  padding: 5px;
  margin-top: 10px;
  border-radius: 4px;
  border: 1px solid #aaa;
}
```

</Sandpack>

<Solution />

</Recipes>

<Pitfall>

Tối ưu hóa này yêu cầu `SlowList` được bọc trong [`memo`.](/reference/react/memo) Điều này là vì mỗi khi `text` thay đổi, React cần có khả năng render lại component cha một cách nhanh chóng. Trong lần render lại đó, `deferredText` vẫn có giá trị trước của nó, vì vậy `SlowList` có thể bỏ qua việc render lại (props của nó chưa thay đổi). Không có [`memo`,](/reference/react/memo) nó sẽ phải render lại dù sao, làm mất đi mục đích của tối ưu hóa.

</Pitfall>

<DeepDive>

#### Sự khác biệt giữa trì hoãn một giá trị và debouncing, throttling? {/*how-is-deferring-a-value-different-from-debouncing-and-throttling*/}

Có hai kỹ thuật tối ưu hóa phổ biến bạn có thể đã sử dụng trước đây trong tình huống này:

- *Debouncing* có nghĩa là bạn sẽ đợi người dùng ngừng gõ (ví dụ: trong một giây) trước khi cập nhật danh sách.
- *Throttling* có nghĩa là bạn sẽ cập nhật danh sách thỉnh thoảng (ví dụ: tối đa một lần mỗi giây).

Mặc dù các kỹ thuật này hữu ích trong một số trường hợp, `useDeferredValue` phù hợp hơn để tối ưu hóa việc render vì nó được tích hợp sâu với React và thích nghi với thiết bị của người dùng.

Không giống như debouncing hay throttling, nó không yêu cầu chọn bất kỳ độ trễ cố định nào. Nếu thiết bị của người dùng nhanh (ví dụ: laptop mạnh mẽ), việc render lại trì hoãn sẽ xảy ra gần như ngay lập tức và không đáng chú ý. Nếu thiết bị của người dùng chậm, danh sách sẽ "tụt lại" phía sau input tỷ lệ với độ chậm của thiết bị.

Ngoài ra, không giống như debouncing hay throttling, các lần render lại trì hoãn được thực hiện bởi `useDeferredValue` có thể bị gián đoạn theo mặc định. Điều này có nghĩa là nếu React đang ở giữa việc render lại một danh sách lớn, nhưng người dùng thực hiện thêm một lần nhấn phím, React sẽ từ bỏ lần render lại đó, xử lý lần nhấn phím, và sau đó bắt đầu render lại ở nền một lần nữa. Ngược lại, debouncing và throttling vẫn tạo ra trải nghiệm giật cục vì chúng *chặn:* chúng chỉ trì hoãn thời điểm khi việc render chặn lần nhấn phím.

Nếu công việc bạn đang tối ưu hóa không xảy ra trong quá trình render, debouncing và throttling vẫn hữu ích. Ví dụ, chúng có thể cho phép bạn gửi ít network request hơn. Bạn cũng có thể sử dụng các kỹ thuật này cùng nhau.

</DeepDive>
