---
title: "'use client'"
titleForTitleTag: "Directive 'use client'"
---

<RSC>

`'use client'` được dùng với [React Server Components](/reference/rsc/server-components).

</RSC>


<Intro>

`'use client'` cho phép bạn đánh dấu code nào chạy trên client.

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `'use client'` {/*use-client*/}

Thêm `'use client'` vào đầu file để đánh dấu module đó và các dependency bắc cầu của nó là code client.

```js {1}
'use client';

import { useState } from 'react';
import { formatDate } from './formatters';
import Button from './button';

export default function RichTextEditor({ timestamp, text }) {
  const date = formatDate(timestamp);
  // ...
  const editButton = <Button />;
  // ...
}
```

Khi một file được đánh dấu bằng `'use client'` được import từ Server Component, [các bundler tương thích](/learn/creating-a-react-app#full-stack-frameworks) sẽ coi việc import module đó như một ranh giới giữa code chạy trên server và code chạy trên client.

Là các dependency của `RichTextEditor`, `formatDate` và `Button` cũng sẽ được đánh giá trên client bất kể module của chúng có chứa directive `'use client'` hay không. Lưu ý rằng một module duy nhất có thể được đánh giá trên server khi được import từ code server và trên client khi được import từ code client.

#### Lưu ý {/*caveats*/}

* `'use client'` phải nằm ở đầu file, trước bất kỳ import hoặc code nào khác (comment thì được). Chúng phải được viết bằng dấu nháy đơn hoặc đôi, nhưng không phải dấu backtick.
* Khi một module `'use client'` được import từ một module được render trên client khác, directive đó không có tác dụng.
* Khi một module component chứa directive `'use client'`, mọi cách sử dụng component đó đều được đảm bảo là Client Component. Tuy nhiên, một component vẫn có thể được đánh giá trên client ngay cả khi không có directive `'use client'`.
	* Một cách sử dụng component được coi là Client Component nếu nó được định nghĩa trong module có directive `'use client'` hoặc khi nó là một dependency bắc cầu của một module chứa directive `'use client'`. Nếu không, nó là một Server Component.
* Code được đánh dấu để đánh giá trên client không chỉ giới hạn ở các component. Tất cả code là một phần của cây con module Client đều được gửi đến và chạy bởi client.
* Khi một module được đánh giá trên server import các giá trị từ một module `'use client'`, các giá trị đó phải là một React component hoặc [các giá trị prop có thể serialize được hỗ trợ](#passing-props-from-server-to-client-components) để truyền cho Client Component. Bất kỳ trường hợp sử dụng nào khác sẽ throw một exception.

### Cách `'use client'` đánh dấu code client {/*how-use-client-marks-client-code*/}

Trong ứng dụng React, các component thường được tách thành các file riêng biệt, hay còn gọi là [module](/learn/importing-and-exporting-components#exporting-and-importing-a-component).

Đối với các ứng dụng sử dụng React Server Components, ứng dụng được server-render theo mặc định. `'use client'` giới thiệu một ranh giới server-client trong [cây dependency module](/learn/understanding-your-ui-as-a-tree#the-module-dependency-tree), hiệu quả tạo ra một cây con của các module Client.

Để minh họa rõ hơn, hãy xem xét ứng dụng React Server Components sau đây.

<Sandpack>

```js src/App.js
import FancyText from './FancyText';
import InspirationGenerator from './InspirationGenerator';
import Copyright from './Copyright';

export default function App() {
  return (
    <>
      <FancyText title text="Get Inspired App" />
      <InspirationGenerator>
        <Copyright year={2004} />
      </InspirationGenerator>
    </>
  );
}

```

```js src/FancyText.js
export default function FancyText({title, text}) {
  return title
    ? <h1 className='fancy title'>{text}</h1>
    : <h3 className='fancy cursive'>{text}</h3>
}
```

```js src/InspirationGenerator.js
'use client';

import { useState } from 'react';
import inspirations from './inspirations';
import FancyText from './FancyText';

export default function InspirationGenerator({children}) {
  const [index, setIndex] = useState(0);
  const quote = inspirations[index];
  const next = () => setIndex((index + 1) % inspirations.length);

  return (
    <>
      <p>Your inspirational quote is:</p>
      <FancyText text={quote} />
      <button onClick={next}>Inspire me again</button>
      {children}
    </>
  );
}
```

```js src/Copyright.js
export default function Copyright({year}) {
  return <p className='small'>©️ {year}</p>;
}
```

```js src/inspirations.js
export default [
  "Don't let yesterday take up too much of today." — Will Rogers",
  "Ambition is putting a ladder against the sky.",
  "A joy that's shared is a joy made double.",
];
```

```css
.fancy {
  font-family: 'Georgia';
}
.title {
  color: #007AA3;
  text-decoration: underline;
}
.cursive {
  font-style: italic;
}
.small {
  font-size: 10px;
}
```

</Sandpack>

Trong cây dependency module của ứng dụng ví dụ này, directive `'use client'` trong `InspirationGenerator.js` đánh dấu module đó và tất cả các dependency bắc cầu của nó là các module Client. Cây con bắt đầu tại `InspirationGenerator.js` hiện được đánh dấu là các module Client.

<Diagram name="use_client_module_dependency" height={250} width={545} alt="Đồ thị cây với nút trên cùng đại diện cho module 'App.js'. 'App.js' có ba nút con: 'Copyright.js', 'FancyText.js', và 'InspirationGenerator.js'. 'InspirationGenerator.js' có hai nút con: 'FancyText.js' và 'inspirations.js'. Các nút bên dưới và bao gồm 'InspirationGenerator.js' có màu nền vàng để biểu thị rằng đồ thị con này được render trên client do directive 'use client' trong 'InspirationGenerator.js'.">
`'use client'` phân đoạn cây dependency module của ứng dụng React Server Components, đánh dấu `InspirationGenerator.js` và tất cả các dependency của nó là được render trên client.
</Diagram>

Trong quá trình render, framework sẽ server-render component gốc và tiếp tục qua [cây render](/learn/understanding-your-ui-as-a-tree#the-render-tree), không đánh giá bất kỳ code nào được import từ code được đánh dấu client.

Phần được server-render của cây render sau đó được gửi đến client. Client, với code client đã được tải xuống, sau đó hoàn thành việc render phần còn lại của cây.

<Diagram name="use_client_render_tree" height={250} width={500} alt="Đồ thị cây trong đó mỗi nút đại diện cho một component và các nút con của nó là các component con. Nút cấp cao nhất được gán nhãn 'App' và có hai component con 'InspirationGenerator' và 'FancyText'. 'InspirationGenerator' có hai component con, 'FancyText' và 'Copyright'. Cả 'InspirationGenerator' và component con 'FancyText' của nó đều được đánh dấu để render trên client.">
Cây render cho ứng dụng React Server Components. `InspirationGenerator` và component con `FancyText` của nó là các component được xuất từ code được đánh dấu client và được coi là Client Components.
</Diagram>

Chúng tôi giới thiệu các định nghĩa sau:

* **Client Components** là các component trong cây render được render trên client.
* **Server Components** là các component trong cây render được render trên server.

Làm việc qua ứng dụng ví dụ, `App`, `FancyText` và `Copyright` đều được server-render và được coi là Server Components. Vì `InspirationGenerator.js` và các dependency bắc cầu của nó được đánh dấu là code client, component `InspirationGenerator` và component con `FancyText` của nó là Client Components.

<DeepDive>
#### Làm thế nào `FancyText` vừa là Server Component vừa là Client Component? {/*how-is-fancytext-both-a-server-and-a-client-component*/}

Theo các định nghĩa trên, component `FancyText` vừa là Server vừa là Client Component, điều này có thể xảy ra như thế nào?

Trước tiên, hãy làm rõ rằng thuật ngữ "component" không chính xác lắm. Đây chỉ là hai cách hiểu về "component":

1. Một "component" có thể chỉ đến một **định nghĩa component**. Trong hầu hết các trường hợp đây sẽ là một hàm.

```js
// Đây là định nghĩa của một component
function MyComponent() {
  return <p>My Component</p>
}
```

2. Một "component" cũng có thể chỉ đến **cách sử dụng component** của định nghĩa đó.
```js
import MyComponent from './MyComponent';

function App() {
  // Đây là cách sử dụng một component
  return <MyComponent />;
}
```

Thường thì sự không chính xác không quan trọng khi giải thích các khái niệm, nhưng trong trường hợp này thì có.

Khi chúng ta nói về Server hoặc Client Components, chúng ta đang đề cập đến cách sử dụng component.

* Nếu component được định nghĩa trong một module có directive `'use client'`, hoặc component được import và gọi trong một Client Component, thì cách sử dụng component đó là Client Component.
* Nếu không, cách sử dụng component đó là Server Component.


<Diagram name="use_client_render_tree" height={150} width={450} alt="Đồ thị cây trong đó mỗi nút đại diện cho một component và các nút con của nó là các component con. Nút cấp cao nhất được gán nhãn 'App' và có hai component con 'InspirationGenerator' và 'FancyText'. 'InspirationGenerator' có hai component con, 'FancyText' và 'Copyright'. Cả 'InspirationGenerator' và component con 'FancyText' của nó đều được đánh dấu để render trên client.">Cây render minh họa các cách sử dụng component.</Diagram>

Trở lại câu hỏi về `FancyText`, chúng ta thấy rằng định nghĩa component _không_ có directive `'use client'` và nó có hai cách sử dụng.

Cách sử dụng `FancyText` như một con của `App`, đánh dấu cách sử dụng đó là Server Component. Khi `FancyText` được import và gọi dưới `InspirationGenerator`, cách sử dụng `FancyText` đó là Client Component vì `InspirationGenerator` chứa directive `'use client'`.

Điều này có nghĩa là định nghĩa component cho `FancyText` sẽ vừa được đánh giá trên server vừa được tải xuống bởi client để render cách sử dụng Client Component của nó.

</DeepDive>

<DeepDive>

#### Tại sao `Copyright` là Server Component? {/*why-is-copyright-a-server-component*/}

Vì `Copyright` được render như một con của Client Component `InspirationGenerator`, bạn có thể ngạc nhiên rằng nó là Server Component.

Nhớ lại rằng `'use client'` định nghĩa ranh giới giữa code server và client trên _cây dependency module_, không phải cây render.

<Diagram name="use_client_module_dependency" height={200} width={500} alt="Đồ thị cây với nút trên cùng đại diện cho module 'App.js'. 'App.js' có ba nút con: 'Copyright.js', 'FancyText.js', và 'InspirationGenerator.js'. 'InspirationGenerator.js' có hai nút con: 'FancyText.js' và 'inspirations.js'. Các nút bên dưới và bao gồm 'InspirationGenerator.js' có màu nền vàng để biểu thị rằng đồ thị con này được render trên client do directive 'use client' trong 'InspirationGenerator.js'.">
`'use client'` định nghĩa ranh giới giữa code server và client trên cây dependency module.
</Diagram>

Trong cây dependency module, chúng ta thấy rằng `App.js` import và gọi `Copyright` từ module `Copyright.js`. Vì `Copyright.js` không chứa directive `'use client'`, cách sử dụng component được render trên server. `App` được render trên server vì nó là component gốc.

Client Components có thể render Server Components vì bạn có thể truyền JSX như props. Trong trường hợp này, `InspirationGenerator` nhận `Copyright` như [children](/learn/passing-props-to-a-component#passing-jsx-as-children). Tuy nhiên, module `InspirationGenerator` không bao giờ trực tiếp import module `Copyright` hay gọi component đó, tất cả điều đó được thực hiện bởi `App`. Trên thực tế, component `Copyright` được thực thi hoàn toàn trước khi `InspirationGenerator` bắt đầu render.

Bài học là mối quan hệ render cha-con giữa các component không đảm bảo cùng môi trường render.

</DeepDive>

### Khi nào sử dụng `'use client'` {/*when-to-use-use-client*/}

Với `'use client'`, bạn có thể xác định khi nào các component là Client Components. Vì Server Components là mặc định, đây là tổng quan ngắn gọn về các ưu điểm và hạn chế của Server Components để xác định khi nào bạn cần đánh dấu thứ gì đó là được render trên client.

Để đơn giản, chúng tôi nói về Server Components, nhưng các nguyên tắc tương tự áp dụng cho tất cả code trong ứng dụng của bạn được chạy trên server.

#### Ưu điểm của Server Components {/*advantages*/}
* Server Components có thể giảm lượng code được gửi và chạy bởi client. Chỉ các module Client được đóng gói và đánh giá bởi client.
* Server Components được hưởng lợi từ việc chạy trên server. Chúng có thể truy cập hệ thống tệp cục bộ và có thể trải nghiệm độ trễ thấp cho việc fetch dữ liệu và các network request.

#### Hạn chế của Server Components {/*limitations*/}
* Server Components không thể hỗ trợ tương tác vì event handlers phải được đăng ký và kích hoạt bởi client.
	* Ví dụ, event handlers như `onClick` chỉ có thể được định nghĩa trong Client Components.
* Server Components không thể sử dụng hầu hết các Hooks.
	* Khi Server Components được render, đầu ra của chúng về cơ bản là một danh sách các component để client render. Server Components không tồn tại trong bộ nhớ sau khi render và không thể có state riêng.

### Các kiểu có thể serialize được trả về bởi Server Components {/*serializable-types*/}

Như trong bất kỳ ứng dụng React nào, các component cha truyền dữ liệu cho các component con. Vì chúng được render trong các môi trường khác nhau, việc truyền dữ liệu từ Server Component sang Client Component đòi hỏi sự cân nhắc thêm.

Các giá trị prop được truyền từ Server Component sang Client Component phải có thể serialize được.

Các prop có thể serialize bao gồm:
* Kiểu nguyên thủy
	* [string](https://developer.mozilla.org/en-US/docs/Glossary/String)
	* [number](https://developer.mozilla.org/en-US/docs/Glossary/Number)
	* [bigint](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt)
	* [boolean](https://developer.mozilla.org/en-US/docs/Glossary/Boolean)
	* [undefined](https://developer.mozilla.org/en-US/docs/Glossary/Undefined)
	* [null](https://developer.mozilla.org/en-US/docs/Glossary/Null)
	* [symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol), chỉ các symbol được đăng ký trong Symbol registry toàn cục thông qua [`Symbol.for`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/for)
* Các iterable chứa các giá trị có thể serialize
	* [String](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)
	* [Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
	* [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)
	* [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)
	* [TypedArray](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray) và [ArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)
* [Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)
* [Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object) thuần túy: những object được tạo bằng [object initializers](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer), với các thuộc tính có thể serialize
* Các hàm là [Server Functions](/reference/rsc/server-functions)
* Các phần tử Client hoặc Server Component (JSX)
* [Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Đặc biệt, những thứ này không được hỗ trợ:
* [Functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function) không được xuất từ các module được đánh dấu client hoặc được đánh dấu bằng [`'use server'`](/reference/rsc/use-server)
* [Classes](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Classes_in_JavaScript)
* Object là các instance của bất kỳ class nào (ngoài các built-in được đề cập) hoặc object có [prototype null](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object#null-prototype_objects)
* Các symbol không được đăng ký toàn cục, ví dụ `Symbol('my new symbol')`


## Cách sử dụng {/*usage*/}

### Xây dựng với tính tương tác và state {/*building-with-interactivity-and-state*/}

<Sandpack>

```js src/App.js
'use client';

import { useState } from 'react';

export default function Counter({initialValue = 0}) {
  const [countValue, setCountValue] = useState(initialValue);
  const increment = () => setCountValue(countValue + 1);
  const decrement = () => setCountValue(countValue - 1);
  return (
    <>
      <h2>Count Value: {countValue}</h2>
      <button onClick={increment}>+1</button>
      <button onClick={decrement}>-1</button>
    </>
  );
}
```

</Sandpack>

Vì `Counter` cần cả Hook `useState` lẫn event handlers để tăng hoặc giảm giá trị, component này phải là Client Component và sẽ yêu cầu directive `'use client'` ở đầu.

Ngược lại, một component render UI mà không có tương tác sẽ không cần là Client Component.

```js
import { readFile } from 'node:fs/promises';
import Counter from './Counter';

export default async function CounterContainer() {
  const initialValue = await readFile('/path/to/counter_value');
  return <Counter initialValue={initialValue} />
}
```

Ví dụ, component cha của `Counter`, `CounterContainer`, không yêu cầu `'use client'` vì nó không có tương tác và không sử dụng state. Ngoài ra, `CounterContainer` phải là Server Component vì nó đọc từ hệ thống tệp cục bộ trên server, điều này chỉ có thể thực hiện trong Server Component.

Ngoài ra còn có các component không sử dụng bất kỳ tính năng chỉ dành cho server hoặc client nào và có thể không phân biệt nơi chúng render. Trong ví dụ trước của chúng tôi, `FancyText` là một component như vậy.

```js
export default function FancyText({title, text}) {
  return title
    ? <h1 className='fancy title'>{text}</h1>
    : <h3 className='fancy cursive'>{text}</h3>
}
```

Trong trường hợp này, chúng tôi không thêm directive `'use client'`, dẫn đến _đầu ra_ của `FancyText` (thay vì source code của nó) được gửi đến trình duyệt khi được tham chiếu từ Server Component. Như được minh họa trong ví dụ ứng dụng Inspirations trước đó, `FancyText` được sử dụng như cả Server Component lẫn Client Component, tùy thuộc vào nơi nó được import và sử dụng.

Nhưng nếu đầu ra HTML của `FancyText` lớn so với source code của nó (bao gồm các dependency), có thể hiệu quả hơn khi buộc nó luôn là Client Component. Các component trả về một chuỗi SVG path dài là một trường hợp có thể hiệu quả hơn khi buộc component là Client Component.

### Sử dụng client APIs {/*using-client-apis*/}

Ứng dụng React của bạn có thể sử dụng các API dành riêng cho client, chẳng hạn như các API của trình duyệt cho web storage, thao tác audio và video, và phần cứng thiết bị, trong số [những thứ khác](https://developer.mozilla.org/en-US/docs/Web/API).

Trong ví dụ này, component sử dụng [DOM APIs](https://developer.mozilla.org/en-US/docs/Glossary/DOM) để thao tác phần tử [`canvas`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/canvas). Vì những API đó chỉ có sẵn trong trình duyệt, nó phải được đánh dấu là Client Component.

```js
'use client';

import {useRef, useEffect} from 'react';

export default function Circle() {
  const ref = useRef(null);
  useLayoutEffect(() => {
    const canvas = ref.current;
    const context = canvas.getContext('2d');
    context.reset();
    context.beginPath();
    context.arc(100, 75, 50, 0, 2 * Math.PI);
    context.stroke();
  });
  return <canvas ref={ref} />;
}
```

### Sử dụng thư viện bên thứ ba {/*using-third-party-libraries*/}

Thông thường trong ứng dụng React, bạn sẽ tận dụng các thư viện bên thứ ba để xử lý các pattern UI hoặc logic phổ biến.

Những thư viện này có thể dựa vào component Hooks hoặc client APIs. Các component bên thứ ba sử dụng bất kỳ API React nào sau đây phải chạy trên client:
* [createContext](/reference/react/createContext)
* [`react`](/reference/react/hooks) và [`react-dom`](/reference/react-dom/hooks) Hooks, ngoại trừ [`use`](/reference/react/use) và [`useId`](/reference/react/useId)
* [forwardRef](/reference/react/forwardRef)
* [memo](/reference/react/memo)
* [startTransition](/reference/react/startTransition)
* Nếu chúng sử dụng client APIs, ví dụ chèn DOM hoặc native platform views

Nếu những thư viện này đã được cập nhật để tương thích với React Server Components, thì chúng sẽ đã bao gồm các marker `'use client'` của riêng chúng, cho phép bạn sử dụng chúng trực tiếp từ Server Components của bạn. Nếu một thư viện chưa được cập nhật, hoặc nếu một component cần props như event handlers chỉ có thể được chỉ định trên client, bạn có thể cần thêm file Client Component của riêng mình giữa Client Component bên thứ ba và Server Component nơi bạn muốn sử dụng nó.

[TODO]: <> (Troubleshooting - need use-cases)
