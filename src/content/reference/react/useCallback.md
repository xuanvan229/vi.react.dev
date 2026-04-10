---
title: useCallback
---

<Intro>

`useCallback` là một React Hook cho phép bạn cache một định nghĩa hàm giữa các lần re-render.

```js
const cachedFn = useCallback(fn, dependencies)
```

</Intro>

<Note>

[React Compiler](/learn/react-compiler) tự động memoize giá trị và hàm, giảm nhu cầu gọi `useCallback` thủ công. Bạn có thể sử dụng compiler để xử lý memoization tự động.

</Note>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `useCallback(fn, dependencies)` {/*usecallback*/}

Gọi `useCallback` ở cấp cao nhất của component để cache một định nghĩa hàm giữa các lần re-render:

```js {4,9}
import { useCallback } from 'react';

export default function ProductPage({ productId, referrer, theme }) {
  const handleSubmit = useCallback((orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }, [productId, referrer]);
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `fn`: Giá trị hàm bạn muốn cache. Nó có thể nhận bất kỳ đối số nào và trả về bất kỳ giá trị nào. React sẽ trả về (không gọi!) hàm của bạn trong lần render đầu tiên. Trong các lần render tiếp theo, React sẽ cho bạn cùng hàm đó nếu các `dependencies` chưa thay đổi kể từ lần render trước. Ngược lại, nó sẽ cho bạn hàm bạn đã truyền trong lần render hiện tại, và lưu trữ nó phòng trường hợp có thể tái sử dụng sau. React sẽ không gọi hàm của bạn. Hàm được trả về cho bạn để bạn quyết định khi nào và có nên gọi nó hay không.

* `dependencies`: Danh sách tất cả các giá trị reactive được tham chiếu bên trong code `fn`. Các giá trị reactive bao gồm props, state, và tất cả các biến và hàm được khai báo trực tiếp bên trong thân component. Nếu linter của bạn được [cấu hình cho React](/learn/editor-setup#linting), nó sẽ kiểm tra rằng mọi giá trị reactive được chỉ định đúng cách làm dependency. Danh sách dependencies phải có số lượng mục không đổi và được viết inline như `[dep1, dep2, dep3]`. React sẽ so sánh mỗi dependency với giá trị trước đó của nó sử dụng thuật toán so sánh [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is).

#### Giá trị trả về {/*returns*/}

Trong lần render đầu tiên, `useCallback` trả về hàm `fn` bạn đã truyền.

Trong các lần render tiếp theo, nó sẽ trả về hàm `fn` đã được lưu trữ từ lần render trước (nếu các dependencies chưa thay đổi), hoặc trả về hàm `fn` bạn đã truyền trong lần render này.

#### Lưu ý {/*caveats*/}

* `useCallback` là một Hook, vì vậy bạn chỉ có thể gọi nó **ở cấp cao nhất của component** hoặc các Hook của riêng bạn. Bạn không thể gọi nó bên trong vòng lặp hoặc điều kiện. Nếu bạn cần điều đó, hãy tách một component mới và chuyển state vào đó.
* React **sẽ không loại bỏ hàm đã cache trừ khi có lý do cụ thể để làm điều đó.** Ví dụ, trong development, React loại bỏ cache khi bạn chỉnh sửa file component. Cả trong development và production, React sẽ loại bỏ cache nếu component của bạn suspend trong lần mount đầu tiên. Trong tương lai, React có thể thêm nhiều tính năng hơn tận dụng việc loại bỏ cache -- ví dụ, nếu React thêm hỗ trợ tích hợp cho virtualized lists trong tương lai, sẽ hợp lý khi loại bỏ cache cho các mục cuộn ra khỏi viewport của bảng ảo hóa. Điều này sẽ phù hợp với kỳ vọng của bạn nếu bạn dựa vào `useCallback` chỉ như là tối ưu hóa hiệu suất. Ngược lại, [biến state](/reference/react/useState#im-trying-to-set-state-to-a-function-but-it-gets-called-instead) hoặc [ref](/reference/react/useRef#avoiding-recreating-the-ref-contents) có thể phù hợp hơn.

---

## Cách sử dụng {/*usage*/}

### Bỏ qua re-rendering của các component {/*skipping-re-rendering-of-components*/}

Khi bạn tối ưu hóa hiệu suất render, đôi khi bạn cần cache các hàm mà bạn truyền cho các component con. Hãy xem cú pháp để làm điều này trước, sau đó xem trong trường hợp nào nó hữu ích.

Để cache một hàm giữa các lần re-render của component, hãy bọc định nghĩa của nó vào Hook `useCallback`:

```js [[3, 4, "handleSubmit"], [2, 9, "[productId, referrer]"]]
import { useCallback } from 'react';

function ProductPage({ productId, referrer, theme }) {
  const handleSubmit = useCallback((orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }, [productId, referrer]);
  // ...
```

Bạn cần truyền hai thứ cho `useCallback`:

1. Một định nghĩa hàm bạn muốn cache giữa các lần re-render.
2. Một <CodeStep step={2}>danh sách dependencies</CodeStep> bao gồm mọi giá trị trong component được dùng trong hàm.

Trong lần render đầu tiên, <CodeStep step={3}>hàm được trả về</CodeStep> bạn sẽ nhận từ `useCallback` sẽ là hàm bạn đã truyền.

Trong các lần render tiếp theo, React sẽ so sánh các <CodeStep step={2}>dependencies</CodeStep> với dependencies bạn đã truyền trong lần render trước. Nếu không có dependency nào thay đổi (được so sánh với [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is)), `useCallback` sẽ trả về cùng hàm như trước. Ngược lại, `useCallback` sẽ trả về hàm bạn đã truyền trong lần render *này*.

Nói cách khác, `useCallback` cache một hàm giữa các lần re-render cho đến khi các dependencies của nó thay đổi.

**Hãy xem qua một ví dụ để thấy khi nào điều này hữu ích.**

Giả sử bạn đang truyền một hàm `handleSubmit` xuống từ `ProductPage` cho component `ShippingForm`:

```js {5}
function ProductPage({ productId, referrer, theme }) {
  // ...
  return (
    <div className={theme}>
      <ShippingForm onSubmit={handleSubmit} />
    </div>
  );
```

Bạn nhận thấy rằng việc toggle prop `theme` làm đóng băng ứng dụng một lúc, nhưng nếu bạn xóa `<ShippingForm />` khỏi JSX, nó cảm thấy nhanh. Điều này cho bạn biết rằng đáng thử tối ưu hóa component `ShippingForm`.

**Mặc định, khi một component re-render, React re-render tất cả các con của nó một cách đệ quy.** Đây là lý do tại sao, khi `ProductPage` re-render với `theme` khác, component `ShippingForm` *cũng* re-render. Điều này ổn với các component không cần nhiều tính toán để re-render. Nhưng nếu bạn xác nhận re-render chậm, bạn có thể nói cho `ShippingForm` bỏ qua re-render khi các props của nó giống như ở lần render trước bằng cách bọc nó trong [`memo`:](/reference/react/memo)

```js {3,5}
import { memo } from 'react';

const ShippingForm = memo(function ShippingForm({ onSubmit }) {
  // ...
});
```

**Với thay đổi này, `ShippingForm` sẽ bỏ qua re-render nếu tất cả các props của nó *giống* như ở lần render trước.** Đây là lúc caching một hàm trở nên quan trọng! Giả sử bạn đã định nghĩa `handleSubmit` không có `useCallback`:

```js {2,3,8,12-13}
function ProductPage({ productId, referrer, theme }) {
  // Mỗi lần theme thay đổi, đây sẽ là hàm khác...
  function handleSubmit(orderDetails) {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }

  return (
    <div className={theme}>
      {/* ... vì vậy props của ShippingForm sẽ không bao giờ giống nhau, và nó sẽ re-render mỗi lần */}
      <ShippingForm onSubmit={handleSubmit} />
    </div>
  );
}
```

**Trong JavaScript, một `function () {}` hoặc `() => {}` luôn tạo ra một hàm _khác_,** tương tự như cách literal object `{}` luôn tạo một object mới. Thông thường, điều này không phải là vấn đề, nhưng nó có nghĩa là props của `ShippingForm` sẽ không bao giờ giống nhau, và tối ưu hóa [`memo`](/reference/react/memo) của bạn sẽ không hoạt động. Đây là nơi `useCallback` trở nên hữu ích:

```js {2,3,8,12-13}
function ProductPage({ productId, referrer, theme }) {
  // Nói với React để cache hàm của bạn giữa các lần re-render...
  const handleSubmit = useCallback((orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }, [productId, referrer]); // ...miễn là các dependencies này không thay đổi...

  return (
    <div className={theme}>
      {/* ...ShippingForm sẽ nhận cùng props và có thể bỏ qua re-rendering */}
      <ShippingForm onSubmit={handleSubmit} />
    </div>
  );
}
```

**Bằng cách bọc `handleSubmit` trong `useCallback`, bạn đảm bảo rằng đó là *cùng* hàm giữa các lần re-render** (cho đến khi dependencies thay đổi). Bạn không *phải* bọc một hàm trong `useCallback` trừ khi bạn làm điều đó vì một lý do cụ thể. Trong ví dụ này, lý do là bạn truyền nó cho một component được bọc trong [`memo`,](/reference/react/memo) và điều này cho phép nó bỏ qua re-rendering. Có những lý do khác bạn có thể cần `useCallback` được mô tả thêm ở phần sau của trang này.

<Note>

**Bạn chỉ nên dựa vào `useCallback` như là tối ưu hóa hiệu suất.** Nếu code của bạn không hoạt động mà không có nó, hãy tìm vấn đề cơ bản và sửa nó trước. Sau đó bạn có thể thêm `useCallback` trở lại.

</Note>

<DeepDive>

#### useCallback liên quan đến useMemo như thế nào? {/*how-is-usecallback-related-to-usememo*/}

Bạn sẽ thường thấy [`useMemo`](/reference/react/useMemo) bên cạnh `useCallback`. Cả hai đều hữu ích khi bạn đang cố gắng tối ưu hóa một component con. Chúng cho phép bạn [memoize](https://en.wikipedia.org/wiki/Memoization) (hay, nói cách khác, cache) thứ bạn đang truyền xuống:

```js {6-8,10-15,19}
import { useMemo, useCallback } from 'react';

function ProductPage({ productId, referrer }) {
  const product = useData('/product/' + productId);

  const requirements = useMemo(() => { // Gọi hàm của bạn và cache kết quả
    return computeRequirements(product);
  }, [product]);

  const handleSubmit = useCallback((orderDetails) => { // Cache bản thân hàm của bạn
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }, [productId, referrer]);

  return (
    <div className={theme}>
      <ShippingForm requirements={requirements} onSubmit={handleSubmit} />
    </div>
  );
}
```

Sự khác biệt là ở *những gì* chúng cho phép bạn cache:

* **[`useMemo`](/reference/react/useMemo) cache *kết quả* của việc gọi hàm.** Trong ví dụ này, nó cache kết quả của việc gọi `computeRequirements(product)` để nó không thay đổi trừ khi `product` đã thay đổi. Điều này cho phép bạn truyền object `requirements` xuống mà không cần re-render `ShippingForm` một cách không cần thiết. Khi cần thiết, React sẽ gọi hàm bạn đã truyền trong quá trình render để tính toán kết quả.
* **`useCallback` cache *bản thân hàm.*** Không giống như `useMemo`, nó không gọi hàm bạn cung cấp. Thay vào đó, nó cache hàm bạn đã cung cấp để `handleSubmit` *bản thân nó* không thay đổi trừ khi `productId` hoặc `referrer` đã thay đổi. Điều này cho phép bạn truyền hàm `handleSubmit` xuống mà không cần re-render `ShippingForm` một cách không cần thiết. Code của bạn sẽ không chạy cho đến khi người dùng submit form.

Nếu bạn đã quen thuộc với [`useMemo`,](/reference/react/useMemo) bạn có thể thấy hữu ích khi nghĩ về `useCallback` như thế này:

```js {expectedErrors: {'react-compiler': [3]}}
// Triển khai đơn giản hóa (bên trong React)
function useCallback(fn, dependencies) {
  return useMemo(() => fn, dependencies);
}
```

[Đọc thêm về sự khác biệt giữa `useMemo` và `useCallback`.](/reference/react/useMemo#memoizing-a-function)

</DeepDive>

<DeepDive>

#### Bạn có nên thêm useCallback ở khắp nơi không? {/*should-you-add-usecallback-everywhere*/}

Nếu ứng dụng của bạn như trang web này, và hầu hết các tương tác là thô (như thay thế một trang hoặc toàn bộ section), memoization thường không cần thiết. Mặt khác, nếu ứng dụng của bạn giống như một trình chỉnh sửa bản vẽ, và hầu hết các tương tác là chi tiết (như di chuyển shapes), thì bạn có thể thấy memoization rất hữu ích.

Việc cache một hàm với `useCallback` chỉ có giá trị trong một số trường hợp:

- Bạn truyền nó như prop cho một component được bọc trong [`memo`.](/reference/react/memo) Bạn muốn bỏ qua re-rendering nếu giá trị không thay đổi. Memoization cho phép component của bạn re-render chỉ khi dependencies thay đổi.
- Hàm bạn đang truyền sau đó được dùng như dependency của một Hook nào đó. Ví dụ, một hàm khác được bọc trong `useCallback` phụ thuộc vào nó, hoặc bạn phụ thuộc vào hàm này từ [`useEffect.`](/reference/react/useEffect)

Không có lợi ích gì khi bọc một hàm trong `useCallback` trong các trường hợp khác. Cũng không có hại đáng kể khi làm điều đó, vì vậy một số nhóm chọn không nghĩ về từng trường hợp riêng lẻ, và memoize càng nhiều càng tốt. Nhược điểm là code trở nên ít dễ đọc hơn. Ngoài ra, không phải tất cả memoization đều hiệu quả: một giá trị duy nhất "luôn mới" là đủ để phá vỡ memoization cho toàn bộ component.

Lưu ý rằng `useCallback` không ngăn *việc tạo* hàm. Bạn luôn tạo một hàm (và điều đó ổn!), nhưng React bỏ qua nó và cho bạn lại hàm đã cache nếu không có gì thay đổi.

**Trong thực tế, bạn có thể làm cho rất nhiều memoization trở nên không cần thiết bằng cách tuân theo một vài nguyên tắc:**

1. Khi một component bọc các component khác về mặt hình ảnh, hãy để nó [chấp nhận JSX như children.](/learn/passing-props-to-a-component#passing-jsx-as-children) Sau đó, nếu component wrapper cập nhật state của chính nó, React biết rằng các con của nó không cần re-render.
2. Ưu tiên state cục bộ và đừng [đẩy state lên](/learn/sharing-state-between-components) xa hơn mức cần thiết. Đừng giữ state tạm thời như forms và liệu một mục có đang được hover ở đầu cây hay trong thư viện state global.
3. Giữ [logic render của bạn pure.](/learn/keeping-components-pure) Nếu re-render một component gây ra vấn đề hoặc tạo ra một số artifact hình ảnh đáng chú ý, đó là bug trong component của bạn! Hãy sửa bug thay vì thêm memoization.
4. Tránh [các Effects không cần thiết cập nhật state.](/learn/you-might-not-need-an-effect) Hầu hết các vấn đề hiệu suất trong ứng dụng React được gây ra bởi chuỗi cập nhật bắt nguồn từ Effects làm cho các component render đi render lại.
5. Thử [loại bỏ các dependencies không cần thiết khỏi Effects của bạn.](/learn/removing-effect-dependencies) Ví dụ, thay vì memoization, thường đơn giản hơn khi di chuyển một số object hoặc hàm vào trong một Effect hoặc ra bên ngoài component.

Nếu một tương tác cụ thể vẫn cảm thấy lag, [sử dụng profiler React Developer Tools](https://legacy.reactjs.org/blog/2018/09/10/introducing-the-react-profiler.html) để xem component nào hưởng lợi nhiều nhất từ memoization, và thêm memoization khi cần. Những nguyên tắc này làm cho component của bạn dễ debug và hiểu hơn, vì vậy tốt khi tuân theo chúng trong mọi trường hợp. Về lâu dài, chúng tôi đang nghiên cứu [thực hiện memoization tự động](https://www.youtube.com/watch?v=lGEMwh32soc) để giải quyết vấn đề này một lần và mãi mãi.

</DeepDive>

<Recipes titleText="Sự khác biệt giữa useCallback và khai báo hàm trực tiếp" titleId="examples-rerendering">

#### Bỏ qua re-rendering với `useCallback` và `memo` {/*skipping-re-rendering-with-usecallback-and-memo*/}

Trong ví dụ này, component `ShippingForm` được **làm chậm nhân tạo** để bạn có thể thấy điều gì xảy ra khi một React component bạn đang render thực sự chậm. Thử tăng bộ đếm và toggle theme.

Việc tăng bộ đếm cảm thấy chậm vì nó buộc `ShippingForm` đã bị làm chậm re-render. Điều đó là dự kiến vì bộ đếm đã thay đổi, và vì vậy bạn cần phản ánh lựa chọn mới của người dùng trên màn hình.

Tiếp theo, thử toggle theme. **Nhờ có `useCallback` cùng với [`memo`](/reference/react/memo), nó nhanh mặc dù bị làm chậm nhân tạo!** `ShippingForm` đã bỏ qua re-rendering vì hàm `handleSubmit` chưa thay đổi. Hàm `handleSubmit` chưa thay đổi vì cả `productId` và `referrer` (các dependency `useCallback` của bạn) chưa thay đổi kể từ lần render trước.

<Sandpack>

```js src/App.js
import { useState } from 'react';
import ProductPage from './ProductPage.js';

export default function App() {
  const [isDark, setIsDark] = useState(false);
  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Dark mode
      </label>
      <hr />
      <ProductPage
        referrerId="wizard_of_oz"
        productId={123}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}
```

```js src/ProductPage.js active
import { useCallback } from 'react';
import ShippingForm from './ShippingForm.js';

export default function ProductPage({ productId, referrer, theme }) {
  const handleSubmit = useCallback((orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }, [productId, referrer]);

  return (
    <div className={theme}>
      <ShippingForm onSubmit={handleSubmit} />
    </div>
  );
}

function post(url, data) {
  // Imagine this sends a request...
  console.log('POST /' + url);
  console.log(data);
}
```

```js {expectedErrors: {'react-compiler': [7, 8]}} src/ShippingForm.js
import { memo, useState } from 'react';

const ShippingForm = memo(function ShippingForm({ onSubmit }) {
  const [count, setCount] = useState(1);

  console.log('[ARTIFICIALLY SLOW] Rendering <ShippingForm />');
  let startTime = performance.now();
  while (performance.now() - startTime < 500) {
    // Do nothing for 500 ms to emulate extremely slow code
  }

  function handleSubmit(e) {
    e.preventDefault();
    const formData = new FormData(e.target);
    const orderDetails = {
      ...Object.fromEntries(formData),
      count
    };
    onSubmit(orderDetails);
  }

  return (
    <form onSubmit={handleSubmit}>
      <p><b>Note: <code>ShippingForm</code> is artificially slowed down!</b></p>
      <label>
        Number of items:
        <button type="button" onClick={() => setCount(count - 1)}>–</button>
        {count}
        <button type="button" onClick={() => setCount(count + 1)}>+</button>
      </label>
      <label>
        Street:
        <input name="street" />
      </label>
      <label>
        City:
        <input name="city" />
      </label>
      <label>
        Postal code:
        <input name="zipCode" />
      </label>
      <button type="submit">Submit</button>
    </form>
  );
});

export default ShippingForm;
```

```css
label {
  display: block; margin-top: 10px;
}

input {
  margin-left: 5px;
}

button[type="button"] {
  margin: 5px;
}

.dark {
  background-color: black;
  color: white;
}

.light {
  background-color: white;
  color: black;
}
```

</Sandpack>

<Solution />

#### Luôn re-render một component {/*always-re-rendering-a-component*/}

Trong ví dụ này, triển khai `ShippingForm` cũng **bị làm chậm nhân tạo** để bạn có thể thấy điều gì xảy ra khi một React component bạn đang render thực sự chậm. Thử tăng bộ đếm và toggle theme.

Không giống như ví dụ trước, việc toggle theme bây giờ cũng chậm! Điều này vì **không có lời gọi `useCallback` trong phiên bản này,** vì vậy `handleSubmit` luôn là hàm mới, và component `ShippingForm` đã bị làm chậm không thể bỏ qua re-rendering.

<Sandpack>

```js src/App.js
import { useState } from 'react';
import ProductPage from './ProductPage.js';

export default function App() {
  const [isDark, setIsDark] = useState(false);
  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Dark mode
      </label>
      <hr />
      <ProductPage
        referrerId="wizard_of_oz"
        productId={123}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}
```

```js src/ProductPage.js active
import ShippingForm from './ShippingForm.js';

export default function ProductPage({ productId, referrer, theme }) {
  function handleSubmit(orderDetails) {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }

  return (
    <div className={theme}>
      <ShippingForm onSubmit={handleSubmit} />
    </div>
  );
}

function post(url, data) {
  // Imagine this sends a request...
  console.log('POST /' + url);
  console.log(data);
}
```

```js {expectedErrors: {'react-compiler': [7, 8]}} src/ShippingForm.js
import { memo, useState } from 'react';

const ShippingForm = memo(function ShippingForm({ onSubmit }) {
  const [count, setCount] = useState(1);

  console.log('[ARTIFICIALLY SLOW] Rendering <ShippingForm />');
  let startTime = performance.now();
  while (performance.now() - startTime < 500) {
    // Do nothing for 500 ms to emulate extremely slow code
  }

  function handleSubmit(e) {
    e.preventDefault();
    const formData = new FormData(e.target);
    const orderDetails = {
      ...Object.fromEntries(formData),
      count
    };
    onSubmit(orderDetails);
  }

  return (
    <form onSubmit={handleSubmit}>
      <p><b>Note: <code>ShippingForm</code> is artificially slowed down!</b></p>
      <label>
        Number of items:
        <button type="button" onClick={() => setCount(count - 1)}>–</button>
        {count}
        <button type="button" onClick={() => setCount(count + 1)}>+</button>
      </label>
      <label>
        Street:
        <input name="street" />
      </label>
      <label>
        City:
        <input name="city" />
      </label>
      <label>
        Postal code:
        <input name="zipCode" />
      </label>
      <button type="submit">Submit</button>
    </form>
  );
});

export default ShippingForm;
```

```css
label {
  display: block; margin-top: 10px;
}

input {
  margin-left: 5px;
}

button[type="button"] {
  margin: 5px;
}

.dark {
  background-color: black;
  color: white;
}

.light {
  background-color: white;
  color: black;
}
```

</Sandpack>


Tuy nhiên, đây là cùng code **với việc làm chậm nhân tạo đã bị xóa.** Việc thiếu `useCallback` có cảm thấy đáng chú ý không?

<Sandpack>

```js src/App.js
import { useState } from 'react';
import ProductPage from './ProductPage.js';

export default function App() {
  const [isDark, setIsDark] = useState(false);
  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Dark mode
      </label>
      <hr />
      <ProductPage
        referrerId="wizard_of_oz"
        productId={123}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}
```

```js src/ProductPage.js active
import ShippingForm from './ShippingForm.js';

export default function ProductPage({ productId, referrer, theme }) {
  function handleSubmit(orderDetails) {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }

  return (
    <div className={theme}>
      <ShippingForm onSubmit={handleSubmit} />
    </div>
  );
}

function post(url, data) {
  // Imagine this sends a request...
  console.log('POST /' + url);
  console.log(data);
}
```

```js src/ShippingForm.js
import { memo, useState } from 'react';

const ShippingForm = memo(function ShippingForm({ onSubmit }) {
  const [count, setCount] = useState(1);

  console.log('Rendering <ShippingForm />');

  function handleSubmit(e) {
    e.preventDefault();
    const formData = new FormData(e.target);
    const orderDetails = {
      ...Object.fromEntries(formData),
      count
    };
    onSubmit(orderDetails);
  }

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Number of items:
        <button type="button" onClick={() => setCount(count - 1)}>–</button>
        {count}
        <button type="button" onClick={() => setCount(count + 1)}>+</button>
      </label>
      <label>
        Street:
        <input name="street" />
      </label>
      <label>
        City:
        <input name="city" />
      </label>
      <label>
        Postal code:
        <input name="zipCode" />
      </label>
      <button type="submit">Submit</button>
    </form>
  );
});

export default ShippingForm;
```

```css
label {
  display: block; margin-top: 10px;
}

input {
  margin-left: 5px;
}

button[type="button"] {
  margin: 5px;
}

.dark {
  background-color: black;
  color: white;
}

.light {
  background-color: white;
  color: black;
}
```

</Sandpack>


Thường thì code không có memoization hoạt động tốt. Nếu các tương tác của bạn đủ nhanh, bạn không cần memoization.

Hãy nhớ rằng bạn cần chạy React ở production mode, tắt [React Developer Tools](/learn/react-developer-tools), và sử dụng các thiết bị tương tự như những gì người dùng ứng dụng của bạn có để cảm nhận thực tế những gì thực sự làm chậm ứng dụng của bạn.

<Solution />

</Recipes>

---

### Cập nhật state từ callback đã được memoize {/*updating-state-from-a-memoized-callback*/}

Đôi khi, bạn có thể cần cập nhật state dựa trên state trước đó từ callback đã được memoize.

Hàm `handleAddTodo` này chỉ định `todos` là dependency vì nó tính toán todos tiếp theo từ đó:

```js {6,7}
function TodoList() {
  const [todos, setTodos] = useState([]);

  const handleAddTodo = useCallback((text) => {
    const newTodo = { id: nextId++, text };
    setTodos([...todos, newTodo]);
  }, [todos]);
  // ...
```

Bạn thường muốn các hàm đã được memoize có ít dependencies nhất có thể. Khi bạn chỉ đọc một số state để tính toán state tiếp theo, bạn có thể loại bỏ dependency đó bằng cách truyền [hàm updater](/reference/react/useState#updating-state-based-on-the-previous-state) thay thế:

```js {6,7}
function TodoList() {
  const [todos, setTodos] = useState([]);

  const handleAddTodo = useCallback((text) => {
    const newTodo = { id: nextId++, text };
    setTodos(todos => [...todos, newTodo]);
  }, []); // ✅ Không cần dependency todos
  // ...
```

Ở đây, thay vì làm `todos` thành dependency và đọc nó bên trong, bạn truyền một hướng dẫn về *cách* cập nhật state (`todos => [...todos, newTodo]`) cho React. [Đọc thêm về hàm updater.](/reference/react/useState#updating-state-based-on-the-previous-state)

---

### Ngăn Effect khởi chạy quá thường xuyên {/*preventing-an-effect-from-firing-too-often*/}

Đôi khi, bạn có thể muốn gọi một hàm từ bên trong một [Effect:](/learn/synchronizing-with-effects)

```js {4-9,12}
function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  function createOptions() {
    return {
      serverUrl: 'https://localhost:1234',
      roomId: roomId
    };
  }

  useEffect(() => {
    const options = createOptions();
    const connection = createConnection(options);
    connection.connect();
    // ...
```

Điều này tạo ra một vấn đề. [Mọi giá trị reactive phải được khai báo là dependency của Effect.](/learn/lifecycle-of-reactive-effects#react-verifies-that-you-specified-every-reactive-value-as-a-dependency) Tuy nhiên, nếu bạn khai báo `createOptions` là dependency, nó sẽ khiến Effect liên tục kết nối lại với chat room:


```js {6}
  useEffect(() => {
    const options = createOptions();
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [createOptions]); // 🔴 Vấn đề: Dependency này thay đổi mỗi lần render
  // ...
```

Để giải quyết vấn đề này, bạn có thể bọc hàm bạn cần gọi từ Effect vào `useCallback`:

```js {4-9,16}
function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  const createOptions = useCallback(() => {
    return {
      serverUrl: 'https://localhost:1234',
      roomId: roomId
    };
  }, [roomId]); // ✅ Chỉ thay đổi khi roomId thay đổi

  useEffect(() => {
    const options = createOptions();
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [createOptions]); // ✅ Chỉ thay đổi khi createOptions thay đổi
  // ...
```

Điều này đảm bảo rằng hàm `createOptions` là giống nhau giữa các lần re-render nếu `roomId` giống nhau. **Tuy nhiên, tốt hơn nữa là loại bỏ nhu cầu về dependency hàm.** Di chuyển hàm *vào bên trong* Effect:

```js {5-10,16}
function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  useEffect(() => {
    function createOptions() { // ✅ Không cần useCallback hay function dependencies!
      return {
        serverUrl: 'https://localhost:1234',
        roomId: roomId
      };
    }

    const options = createOptions();
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ Chỉ thay đổi khi roomId thay đổi
  // ...
```

Bây giờ code của bạn đơn giản hơn và không cần `useCallback`. [Tìm hiểu thêm về việc loại bỏ Effect dependencies.](/learn/removing-effect-dependencies#move-dynamic-objects-and-functions-inside-your-effect)

---

### Tối ưu hóa custom Hook {/*optimizing-a-custom-hook*/}

Nếu bạn đang viết [custom Hook,](/learn/reusing-logic-with-custom-hooks) được khuyến nghị bọc bất kỳ hàm nào nó trả về vào `useCallback`:

```js {4-6,8-10}
function useRouter() {
  const { dispatch } = useContext(RouterStateContext);

  const navigate = useCallback((url) => {
    dispatch({ type: 'navigate', url });
  }, [dispatch]);

  const goBack = useCallback(() => {
    dispatch({ type: 'back' });
  }, [dispatch]);

  return {
    navigate,
    goBack,
  };
}
```

Điều này đảm bảo rằng người dùng Hook của bạn có thể tối ưu hóa code của riêng họ khi cần.

---

## Xử lý sự cố {/*troubleshooting*/}

### Mỗi lần component render, `useCallback` trả về hàm khác {/*every-time-my-component-renders-usecallback-returns-a-different-function*/}

Hãy đảm bảo bạn đã chỉ định dependency array làm đối số thứ hai!

Nếu bạn quên dependency array, `useCallback` sẽ trả về hàm mới mỗi lần:

```js {7}
function ProductPage({ productId, referrer }) {
  const handleSubmit = useCallback((orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }); // 🔴 Trả về hàm mới mỗi lần: không có dependency array
  // ...
```

Đây là phiên bản đã sửa truyền dependency array làm đối số thứ hai:

```js {7}
function ProductPage({ productId, referrer }) {
  const handleSubmit = useCallback((orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }, [productId, referrer]); // ✅ Không trả về hàm mới một cách không cần thiết
  // ...
```

Nếu điều này không giúp được, thì vấn đề là ít nhất một trong các dependency của bạn khác với lần render trước. Bạn có thể debug vấn đề này bằng cách log thủ công các dependency vào console:

```js {5}
  const handleSubmit = useCallback((orderDetails) => {
    // ..
  }, [productId, referrer]);

  console.log([productId, referrer]);
```

Bạn có thể nhấp chuột phải vào các mảng từ các lần re-render khác nhau trong console và chọn "Store as a global variable" cho cả hai. Giả sử cái đầu tiên được lưu là `temp1` và cái thứ hai được lưu là `temp2`, bạn có thể sử dụng browser console để kiểm tra xem mỗi dependency trong cả hai mảng có giống nhau không:

```js
Object.is(temp1[0], temp2[0]); // Dependency đầu tiên có giống nhau giữa các mảng không?
Object.is(temp1[1], temp2[1]); // Dependency thứ hai có giống nhau giữa các mảng không?
Object.is(temp1[2], temp2[2]); // ... và cứ thế cho mỗi dependency ...
```

Khi bạn tìm thấy dependency nào đang phá vỡ memoization, hãy tìm cách loại bỏ nó, hoặc [memoize nó cũng vậy.](/reference/react/useMemo#memoizing-a-dependency-of-another-hook)

---

### Tôi cần gọi `useCallback` cho mỗi mục danh sách trong vòng lặp, nhưng không được phép {/*i-need-to-call-usememo-for-each-list-item-in-a-loop-but-its-not-allowed*/}

Giả sử component `Chart` được bọc trong [`memo`](/reference/react/memo). Bạn muốn bỏ qua re-rendering mỗi `Chart` trong danh sách khi component `ReportList` re-render. Tuy nhiên, bạn không thể gọi `useCallback` trong vòng lặp:

```js {expectedErrors: {'react-compiler': [6]}} {5-14}
function ReportList({ items }) {
  return (
    <article>
      {items.map(item => {
        // 🔴 Bạn không thể gọi useCallback trong vòng lặp như thế này:
        const handleClick = useCallback(() => {
          sendReport(item)
        }, [item]);

        return (
          <figure key={item.id}>
            <Chart onClick={handleClick} />
          </figure>
        );
      })}
    </article>
  );
}
```

Thay vào đó, hãy tách một component cho một mục riêng lẻ, và đặt `useCallback` ở đó:

```js {5,12-21}
function ReportList({ items }) {
  return (
    <article>
      {items.map(item =>
        <Report key={item.id} item={item} />
      )}
    </article>
  );
}

function Report({ item }) {
  // ✅ Gọi useCallback ở cấp cao nhất:
  const handleClick = useCallback(() => {
    sendReport(item)
  }, [item]);

  return (
    <figure>
      <Chart onClick={handleClick} />
    </figure>
  );
}
```

Ngoài ra, bạn có thể loại bỏ `useCallback` trong đoạn code cuối và thay vào đó bọc chính `Report` trong [`memo`.](/reference/react/memo) Nếu prop `item` không thay đổi, `Report` sẽ bỏ qua re-rendering, vì vậy `Chart` cũng sẽ bỏ qua re-rendering:

```js {5,6-8,15}
function ReportList({ items }) {
  // ...
}

const Report = memo(function Report({ item }) {
  function handleClick() {
    sendReport(item);
  }

  return (
    <figure>
      <Chart onClick={handleClick} />
    </figure>
  );
});
```
