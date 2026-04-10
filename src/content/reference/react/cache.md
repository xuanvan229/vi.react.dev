---
title: cache
---

<RSC>

`cache` chỉ dùng với [React Server Components](/reference/rsc/server-components).

</RSC>

<Intro>

`cache` cho phép bạn lưu trữ kết quả của một lần tải dữ liệu hoặc tính toán vào bộ nhớ cache.

```js
const cachedFn = cache(fn);
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `cache(fn)` {/*cache*/}

Gọi `cache` bên ngoài bất kỳ component nào để tạo ra một phiên bản của hàm có tính năng cache.

```js {4,7}
import {cache} from 'react';
import calculateMetrics from 'lib/metrics';

const getMetrics = cache(calculateMetrics);

function Chart({data}) {
  const report = getMetrics(data);
  // ...
}
```

Khi `getMetrics` được gọi lần đầu tiên với `data`, `getMetrics` sẽ gọi `calculateMetrics(data)` và lưu kết quả vào cache. Nếu `getMetrics` được gọi lại với cùng `data`, nó sẽ trả về kết quả đã được cache thay vì gọi `calculateMetrics(data)` một lần nữa.

[Xem thêm ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

- `fn`: Hàm mà bạn muốn cache kết quả. `fn` có thể nhận bất kỳ tham số nào và trả về bất kỳ giá trị nào.

#### Giá trị trả về {/*returns*/}

`cache` trả về một phiên bản đã được cache của `fn` với cùng kiểu chữ ký. Nó không gọi `fn` trong quá trình này.

Khi gọi `cachedFn` với các tham số đã cho, trước tiên nó kiểm tra xem có kết quả đã được cache trong bộ nhớ không. Nếu có, nó trả về kết quả đó. Nếu không, nó gọi `fn` với các tham số, lưu kết quả vào cache và trả về kết quả. Hàm `fn` chỉ được gọi khi không có cache hit.

<Note>

Tối ưu hóa việc lưu trữ giá trị trả về dựa trên đầu vào được gọi là [_memoization_](https://en.wikipedia.org/wiki/Memoization). Chúng ta gọi hàm được trả về từ `cache` là hàm memoized.

</Note>

#### Lưu ý {/*caveats*/}

- React sẽ vô hiệu hóa cache cho tất cả các hàm memoized cho mỗi yêu cầu từ server.
- Mỗi lần gọi `cache` tạo ra một hàm mới. Điều này có nghĩa là gọi `cache` với cùng một hàm nhiều lần sẽ trả về các hàm memoized khác nhau và không chia sẻ cùng một cache.
- `cachedFn` cũng sẽ cache các lỗi. Nếu `fn` ném ra lỗi với một số tham số nhất định, lỗi đó sẽ được cache, và cùng một lỗi sẽ được ném lại khi `cachedFn` được gọi với các tham số đó.
- `cache` chỉ dùng trong [Server Components](/reference/rsc/server-components).

---

## Cách sử dụng {/*usage*/}

### Cache một phép tính tốn nhiều tài nguyên {/*cache-expensive-computation*/}

Sử dụng `cache` để bỏ qua công việc trùng lặp.

```js [[1, 7, "getUserMetrics(user)"],[2, 13, "getUserMetrics(user)"]]
import {cache} from 'react';
import calculateUserMetrics from 'lib/user';

const getUserMetrics = cache(calculateUserMetrics);

function Profile({user}) {
  const metrics = getUserMetrics(user);
  // ...
}

function TeamReport({users}) {
  for (let user in users) {
    const metrics = getUserMetrics(user);
    // ...
  }
  // ...
}
```

Nếu cùng một đối tượng `user` được render trong cả `Profile` và `TeamReport`, hai component có thể chia sẻ công việc và chỉ gọi `calculateUserMetrics` một lần cho `user` đó.

Giả sử `Profile` được render trước. Nó sẽ gọi <CodeStep step={1}>`getUserMetrics`</CodeStep>, và kiểm tra xem có kết quả đã được cache không. Vì đây là lần đầu tiên `getUserMetrics` được gọi với `user` đó, sẽ không có cache hit. `getUserMetrics` sau đó sẽ gọi `calculateUserMetrics` với `user` đó và ghi kết quả vào cache.

Khi `TeamReport` render danh sách `users` và đến cùng đối tượng `user`, nó sẽ gọi <CodeStep step={2}>`getUserMetrics`</CodeStep> và đọc kết quả từ cache.

Nếu `calculateUserMetrics` có thể bị hủy bằng cách truyền [`AbortSignal`](https://developer.mozilla.org/en-US/docs/Web/API/AbortSignal), bạn có thể dùng [`cacheSignal()`](/reference/react/cacheSignal) để hủy phép tính tốn nhiều tài nguyên nếu React đã hoàn tất việc render. `calculateUserMetrics` có thể đã xử lý việc hủy nội bộ bằng cách dùng `cacheSignal` trực tiếp.

<Pitfall>

##### Gọi các hàm memoized khác nhau sẽ đọc từ các cache khác nhau. {/*pitfall-different-memoized-functions*/}

Để truy cập cùng một cache, các component phải gọi cùng một hàm memoized.

```js [[1, 7, "getWeekReport"], [1, 7, "cache(calculateWeekReport)"], [1, 8, "getWeekReport"]]
// Temperature.js
import {cache} from 'react';
import {calculateWeekReport} from './report';

export function Temperature({cityData}) {
  // 🚩 Sai: Gọi `cache` trong component tạo ra `getWeekReport` mới cho mỗi lần render
  const getWeekReport = cache(calculateWeekReport);
  const report = getWeekReport(cityData);
  // ...
}
```

```js [[2, 6, "getWeekReport"], [2, 6, "cache(calculateWeekReport)"], [2, 9, "getWeekReport"]]
// Precipitation.js
import {cache} from 'react';
import {calculateWeekReport} from './report';

// 🚩 Sai: `getWeekReport` chỉ có thể truy cập từ component `Precipitation`.
const getWeekReport = cache(calculateWeekReport);

export function Precipitation({cityData}) {
  const report = getWeekReport(cityData);
  // ...
}
```

Trong ví dụ trên, <CodeStep step={2}>`Precipitation`</CodeStep> và <CodeStep step={1}>`Temperature`</CodeStep> đều gọi `cache` để tạo ra hàm memoized mới với cache look-up riêng. Nếu cả hai component render cùng `cityData`, chúng sẽ làm công việc trùng lặp để gọi `calculateWeekReport`.

Ngoài ra, `Temperature` tạo ra một <CodeStep step={1}>hàm memoized mới</CodeStep> mỗi khi component được render, điều này không cho phép chia sẻ cache.

Để tối đa hóa cache hit và giảm công việc, hai component nên gọi cùng một hàm memoized để truy cập cùng một cache. Thay vào đó, hãy định nghĩa hàm memoized trong một module riêng có thể được [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) trên các component.

```js [[3, 5, "export default cache(calculateWeekReport)"]]
// getWeekReport.js
import {cache} from 'react';
import {calculateWeekReport} from './report';

export default cache(calculateWeekReport);
```

```js [[3, 2, "getWeekReport", 0], [3, 5, "getWeekReport"]]
// Temperature.js
import getWeekReport from './getWeekReport';

export default function Temperature({cityData}) {
	const report = getWeekReport(cityData);
  // ...
}
```

```js [[3, 2, "getWeekReport", 0], [3, 5, "getWeekReport"]]
// Precipitation.js
import getWeekReport from './getWeekReport';

export default function Precipitation({cityData}) {
  const report = getWeekReport(cityData);
  // ...
}
```
Ở đây, cả hai component đều gọi <CodeStep step={3}>cùng một hàm memoized</CodeStep> được export từ `./getWeekReport.js` để đọc và ghi vào cùng một cache.
</Pitfall>

### Chia sẻ một bản snapshot của dữ liệu {/*take-and-share-snapshot-of-data*/}

Để chia sẻ một bản snapshot của dữ liệu giữa các component, hãy gọi `cache` với một hàm tải dữ liệu như `fetch`. Khi nhiều component thực hiện cùng một yêu cầu tải dữ liệu, chỉ có một yêu cầu được thực hiện và dữ liệu trả về được cache và chia sẻ trên các component. Tất cả các component đều tham chiếu đến cùng một bản snapshot của dữ liệu trong suốt quá trình render trên server.

```js [[1, 4, "city"], [1, 5, "fetchTemperature(city)"], [2, 4, "getTemperature"], [2, 9, "getTemperature"], [1, 9, "city"], [2, 14, "getTemperature"], [1, 14, "city"]]
import {cache} from 'react';
import {fetchTemperature} from './api.js';

const getTemperature = cache(async (city) => {
	return await fetchTemperature(city);
});

async function AnimatedWeatherCard({city}) {
	const temperature = await getTemperature(city);
	// ...
}

async function MinimalWeatherCard({city}) {
	const temperature = await getTemperature(city);
	// ...
}
```

Nếu `AnimatedWeatherCard` và `MinimalWeatherCard` đều render cho cùng một <CodeStep step={1}>city</CodeStep>, chúng sẽ nhận cùng một bản snapshot dữ liệu từ <CodeStep step={2}>hàm memoized</CodeStep>.

Nếu `AnimatedWeatherCard` và `MinimalWeatherCard` cung cấp các tham số <CodeStep step={1}>city</CodeStep> khác nhau cho <CodeStep step={2}>`getTemperature`</CodeStep>, thì `fetchTemperature` sẽ được gọi hai lần và mỗi call site sẽ nhận dữ liệu khác nhau.

<CodeStep step={1}>city</CodeStep> đóng vai trò là cache key.

<Note>

<CodeStep step={3}>Rendering bất đồng bộ</CodeStep> chỉ được hỗ trợ cho Server Components.

```js [[3, 1, "async"], [3, 2, "await"]]
async function AnimatedWeatherCard({city}) {
	const temperature = await getTemperature(city);
	// ...
}
```

Để render các component sử dụng dữ liệu bất đồng bộ trong Client Components, xem [tài liệu `use()`](/reference/react/use).

</Note>

### Tải trước dữ liệu {/*preload-data*/}

Bằng cách cache một lần tải dữ liệu chạy lâu, bạn có thể bắt đầu công việc bất đồng bộ trước khi render component.

```jsx [[2, 6, "await getUser(id)"], [1, 17, "getUser(id)"]]
const getUser = cache(async (id) => {
  return await db.user.query(id);
});

async function Profile({id}) {
  const user = await getUser(id);
  return (
    <section>
      <img src={user.profilePic} />
      <h2>{user.name}</h2>
    </section>
  );
}

function Page({id}) {
  // ✅ Tốt: bắt đầu tải dữ liệu user
  getUser(id);
  // ... một số công việc tính toán
  return (
    <>
      <Profile id={id} />
    </>
  );
}
```

Khi render `Page`, component gọi <CodeStep step={1}>`getUser`</CodeStep> nhưng lưu ý rằng nó không sử dụng dữ liệu trả về. Lần gọi <CodeStep step={1}>`getUser`</CodeStep> sớm này khởi động truy vấn cơ sở dữ liệu bất đồng bộ xảy ra trong khi `Page` đang làm công việc tính toán khác và render các component con.

Khi render `Profile`, chúng ta gọi <CodeStep step={2}>`getUser`</CodeStep> lại. Nếu lần gọi <CodeStep step={1}>`getUser`</CodeStep> ban đầu đã trả về và cache dữ liệu user, khi `Profile` <CodeStep step={2}>yêu cầu và chờ dữ liệu này</CodeStep>, nó có thể chỉ đọc từ cache mà không cần thêm một lần gọi procedure từ xa. Nếu <CodeStep step={1}> yêu cầu dữ liệu ban đầu</CodeStep> chưa hoàn thành, việc tải trước dữ liệu theo mẫu này giúp giảm độ trễ khi tải dữ liệu.

<DeepDive>

#### Cache công việc bất đồng bộ {/*caching-asynchronous-work*/}

Khi đánh giá một [hàm bất đồng bộ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function), bạn sẽ nhận được một [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) cho công việc đó. Promise nắm giữ trạng thái của công việc đó (_pending_, _fulfilled_, _failed_) và kết quả cuối cùng của nó.

Trong ví dụ này, hàm bất đồng bộ <CodeStep step={1}>`fetchData`</CodeStep> trả về một promise đang chờ `fetch`.

```js [[1, 1, "fetchData()"], [2, 8, "getData()"], [3, 10, "getData()"]]
async function fetchData() {
  return await fetch(`https://...`);
}

const getData = cache(fetchData);

async function MyComponent() {
  getData();
  // ... một số công việc tính toán
  await getData();
  // ...
}
```

Khi gọi <CodeStep step={2}>`getData`</CodeStep> lần đầu tiên, promise được trả về từ <CodeStep step={1}>`fetchData`</CodeStep> sẽ được cache. Các lần tra cứu tiếp theo sẽ trả về cùng một promise.

Lưu ý rằng lần gọi <CodeStep step={2}>`getData`</CodeStep> đầu tiên không `await` trong khi lần <CodeStep step={3}>thứ hai</CodeStep> thì có. [`await`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await) là một toán tử JavaScript sẽ chờ và trả về kết quả đã hoàn thành của promise. Lần gọi <CodeStep step={2}>`getData`</CodeStep> đầu tiên chỉ đơn giản khởi động `fetch` để cache promise cho lần gọi <CodeStep step={3}>`getData`</CodeStep> thứ hai tra cứu.

Nếu ở lần <CodeStep step={3}>gọi thứ hai</CodeStep> promise vẫn đang _pending_, thì `await` sẽ tạm dừng để chờ kết quả. Tối ưu hóa ở đây là trong khi chúng ta chờ `fetch`, React có thể tiếp tục công việc tính toán, do đó giảm thời gian chờ cho lần <CodeStep step={3}>gọi thứ hai</CodeStep>.

Nếu promise đã hoàn thành, dù là lỗi hay kết quả _fulfilled_, `await` sẽ trả về giá trị đó ngay lập tức. Trong cả hai kết quả, đều có lợi ích về hiệu suất.
</DeepDive>

<Pitfall>

##### Gọi một hàm memoized bên ngoài component sẽ không sử dụng cache. {/*pitfall-memoized-call-outside-component*/}

```jsx [[1, 3, "getUser"]]
import {cache} from 'react';

const getUser = cache(async (userId) => {
  return await db.user.query(userId);
});

// 🚩 Sai: Gọi hàm memoized bên ngoài component sẽ không memoize.
getUser('demo-id');

async function DemoProfile() {
  // ✅ Tốt: `getUser` sẽ memoize.
  const user = await getUser('demo-id');
  return <Profile user={user} />;
}
```

React chỉ cung cấp quyền truy cập cache cho hàm memoized trong một component. Khi gọi <CodeStep step={1}>`getUser`</CodeStep> bên ngoài một component, nó vẫn sẽ đánh giá hàm nhưng không đọc hoặc cập nhật cache.

Điều này là vì quyền truy cập cache được cung cấp thông qua một [context](/learn/passing-data-deeply-with-context) chỉ có thể truy cập từ một component.

</Pitfall>

<DeepDive>

#### Khi nào nên dùng `cache`, [`memo`](/reference/react/memo), hay [`useMemo`](/reference/react/useMemo)? {/*cache-memo-usememo*/}

Tất cả các API được đề cập đều cung cấp tính năng memoization nhưng sự khác biệt là chúng được dùng để memoize gì, ai có thể truy cập cache và khi nào cache bị vô hiệu hóa.

#### `useMemo` {/*deep-dive-use-memo*/}

Nói chung, bạn nên sử dụng [`useMemo`](/reference/react/useMemo) để cache một phép tính tốn nhiều tài nguyên trong một Client Component qua các lần render. Ví dụ, để memoize một phép biến đổi dữ liệu trong một component.

```jsx {expectedErrors: {'react-compiler': [4]}} {4}
'use client';

function WeatherReport({record}) {
  const avgTemp = useMemo(() => calculateAvg(record), record);
  // ...
}

function App() {
  const record = getRecord();
  return (
    <>
      <WeatherReport record={record} />
      <WeatherReport record={record} />
    </>
  );
}
```
Trong ví dụ này, `App` render hai `WeatherReport` với cùng bản ghi. Mặc dù cả hai component đều làm cùng một công việc, chúng không thể chia sẻ công việc. Cache của `useMemo` chỉ là cục bộ trong component.

Tuy nhiên, `useMemo` đảm bảo rằng nếu `App` re-render và đối tượng `record` không thay đổi, mỗi instance component sẽ bỏ qua công việc và sử dụng giá trị đã memoize của `avgTemp`. `useMemo` chỉ cache phép tính cuối cùng của `avgTemp` với các dependencies đã cho.

#### `cache` {/*deep-dive-cache*/}

Nói chung, bạn nên sử dụng `cache` trong Server Components để memoize công việc có thể được chia sẻ trên các component.

```js [[1, 12, "<WeatherReport city={city} />"], [3, 13, "<WeatherReport city={city} />"], [2, 1, "cache(fetchReport)"]]
const cachedFetchReport = cache(fetchReport);

function WeatherReport({city}) {
  const report = cachedFetchReport(city);
  // ...
}

function App() {
  const city = "Los Angeles";
  return (
    <>
      <WeatherReport city={city} />
      <WeatherReport city={city} />
    </>
  );
}
```
Viết lại ví dụ trước để sử dụng `cache`, trong trường hợp này <CodeStep step={3}>instance thứ hai của `WeatherReport`</CodeStep> sẽ có thể bỏ qua công việc trùng lặp và đọc từ cùng cache với <CodeStep step={1}>`WeatherReport` đầu tiên</CodeStep>. Một điểm khác biệt so với ví dụ trước là `cache` cũng được khuyến nghị để <CodeStep step={2}>memoize các lần tải dữ liệu</CodeStep>, không giống `useMemo` chỉ nên được sử dụng cho các phép tính.

Hiện tại, `cache` chỉ nên được sử dụng trong Server Components và cache sẽ bị vô hiệu hóa qua các yêu cầu từ server.

#### `memo` {/*deep-dive-memo*/}

Bạn nên sử dụng [`memo`](reference/react/memo) để ngăn một component re-render nếu props của nó không thay đổi.

```js
'use client';

function WeatherReport({record}) {
  const avgTemp = calculateAvg(record);
  // ...
}

const MemoWeatherReport = memo(WeatherReport);

function App() {
  const record = getRecord();
  return (
    <>
      <MemoWeatherReport record={record} />
      <MemoWeatherReport record={record} />
    </>
  );
}
```

Trong ví dụ này, cả hai component `MemoWeatherReport` đều sẽ gọi `calculateAvg` khi được render lần đầu tiên. Tuy nhiên, nếu `App` re-render mà không có thay đổi nào đối với `record`, không có props nào thay đổi và `MemoWeatherReport` sẽ không re-render.

So với `useMemo`, `memo` memoize việc render component dựa trên props so với các phép tính cụ thể. Tương tự như `useMemo`, component đã memoize chỉ cache lần render cuối cùng với các giá trị prop cuối cùng. Khi props thay đổi, cache bị vô hiệu hóa và component re-render.

</DeepDive>

---

## Xử lý sự cố {/*troubleshooting*/}

### Hàm memoized của tôi vẫn chạy mặc dù tôi đã gọi nó với cùng các tham số {/*memoized-function-still-runs*/}

Xem các pitfall đã đề cập trước đó
* [Gọi các hàm memoized khác nhau sẽ đọc từ các cache khác nhau.](#pitfall-different-memoized-functions)
* [Gọi một hàm memoized bên ngoài component sẽ không sử dụng cache.](#pitfall-memoized-call-outside-component)

Nếu không có điều nào ở trên áp dụng, có thể là vấn đề với cách React kiểm tra xem có gì đó tồn tại trong cache không.

Nếu các tham số của bạn không phải là [primitives](https://developer.mozilla.org/en-US/docs/Glossary/Primitive) (ví dụ: objects, functions, arrays), hãy đảm bảo bạn đang truyền cùng một object reference.

Khi gọi một hàm memoized, React sẽ tra cứu các tham số đầu vào để xem liệu kết quả đã được cache chưa. React sẽ sử dụng shallow equality của các tham số để xác định có cache hit không.

```js
import {cache} from 'react';

const calculateNorm = cache((vector) => {
  // ...
});

function MapMarker(props) {
  // 🚩 Sai: props là một object thay đổi mỗi lần render.
  const length = calculateNorm(props);
  // ...
}

function App() {
  return (
    <>
      <MapMarker x={10} y={10} z={10} />
      <MapMarker x={10} y={10} z={10} />
    </>
  );
}
```

Trong trường hợp này hai `MapMarker` có vẻ như đang làm cùng một công việc và gọi `calculateNorm` với cùng giá trị `{x: 10, y: 10, z:10}`. Mặc dù các object chứa cùng giá trị, chúng không phải là cùng một object reference vì mỗi component tạo ra object `props` riêng của mình.

React sẽ gọi [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) trên đầu vào để xác minh có cache hit không.

```js {3,9}
import {cache} from 'react';

const calculateNorm = cache((x, y, z) => {
  // ...
});

function MapMarker(props) {
  // ✅ Tốt: Truyền primitives cho hàm memoized
  const length = calculateNorm(props.x, props.y, props.z);
  // ...
}

function App() {
  return (
    <>
      <MapMarker x={10} y={10} z={10} />
      <MapMarker x={10} y={10} z={10} />
    </>
  );
}
```

Một cách để giải quyết vấn đề này là truyền các chiều của vector cho `calculateNorm`. Điều này hoạt động vì bản thân các chiều là primitives.

Một giải pháp khác có thể là truyền chính object vector như một prop cho component. Chúng ta cần truyền cùng một object cho cả hai instance component.

```js {3,9,14}
import {cache} from 'react';

const calculateNorm = cache((vector) => {
  // ...
});

function MapMarker(props) {
  // ✅ Tốt: Truyền cùng một object `vector`
  const length = calculateNorm(props.vector);
  // ...
}

function App() {
  const vector = [10, 10, 10];
  return (
    <>
      <MapMarker vector={vector} />
      <MapMarker vector={vector} />
    </>
  );
}
```
