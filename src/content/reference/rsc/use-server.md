---
title: "'use server'"
titleForTitleTag: "Directive 'use server'"
---

<RSC>

`'use server'` được dùng với [React Server Components](/reference/rsc/server-components).

</RSC>


<Intro>

`'use server'` đánh dấu các hàm phía server có thể được gọi từ code phía client.

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `'use server'` {/*use-server*/}

Thêm `'use server'` vào đầu thân hàm async để đánh dấu hàm đó có thể được gọi bởi client. Chúng tôi gọi các hàm này là [_Server Functions_](/reference/rsc/server-functions).

```js {2}
async function addToCart(data) {
  'use server';
  // ...
}
```

Khi gọi một Server Function trên client, nó sẽ thực hiện một network request đến server bao gồm một bản sao đã serialize của bất kỳ đối số nào được truyền vào. Nếu Server Function trả về một giá trị, giá trị đó sẽ được serialize và trả về cho client.

Thay vì đánh dấu từng hàm riêng lẻ bằng `'use server'`, bạn có thể thêm directive vào đầu file để đánh dấu tất cả các export trong file đó là Server Functions có thể được sử dụng ở bất kỳ đâu, kể cả được import trong code client.

#### Lưu ý {/*caveats*/}
* `'use server'` phải nằm ở đầu thân hàm hoặc module; phía trên bất kỳ code nào khác kể cả các import (comment phía trên directive thì được). Chúng phải được viết bằng dấu nháy đơn hoặc đôi, không phải backtick.
* `'use server'` chỉ có thể được sử dụng trong các file phía server. Các Server Function kết quả có thể được truyền cho Client Components thông qua props. Xem các [kiểu được hỗ trợ để serialize](#serializable-parameters-and-return-values).
* Để import một Server Function từ [code client](/reference/rsc/use-client), directive phải được sử dụng ở cấp module.
* Vì các network call cơ bản luôn bất đồng bộ, `'use server'` chỉ có thể được sử dụng trên các hàm async.
* Luôn coi các đối số cho Server Functions là đầu vào không tin cậy và ủy quyền bất kỳ thay đổi nào. Xem [các cân nhắc về bảo mật](#security).
* Server Functions nên được gọi trong một [Transition](/reference/react/useTransition). Các Server Functions được truyền cho [`<form action>`](/reference/react-dom/components/form#props) hoặc [`formAction`](/reference/react-dom/components/input#props) sẽ tự động được gọi trong một transition.
* Server Functions được thiết kế cho các thao tác thay đổi trạng thái phía server; chúng không được khuyến nghị cho việc fetch dữ liệu. Theo đó, các framework triển khai Server Functions thường xử lý một action tại một thời điểm và không có cách cache giá trị trả về.

### Các cân nhắc về bảo mật {/*security*/}

Các đối số cho Server Functions hoàn toàn được kiểm soát bởi client. Vì lý do bảo mật, luôn coi chúng là đầu vào không tin cậy, và đảm bảo validate và escape các đối số khi cần thiết.

Trong bất kỳ Server Function nào, hãy đảm bảo validate rằng người dùng đã đăng nhập được phép thực hiện action đó.

<Wip>

Để ngăn việc gửi dữ liệu nhạy cảm từ một Server Function, có các taint APIs thực nghiệm để ngăn các giá trị và object duy nhất được truyền cho code client.

Xem [experimental_taintUniqueValue](/reference/react/experimental_taintUniqueValue) và [experimental_taintObjectReference](/reference/react/experimental_taintObjectReference).

</Wip>

### Các đối số và giá trị trả về có thể serialize {/*serializable-parameters-and-return-values*/}

Vì code client gọi Server Function qua network, bất kỳ đối số nào được truyền vào sẽ cần có thể serialize được.

Đây là các kiểu được hỗ trợ cho đối số Server Function:

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
* Các instance [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData)
* [Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object) thuần túy: những object được tạo bằng [object initializers](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer), với các thuộc tính có thể serialize
* Các hàm là Server Functions
* [Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Đặc biệt, những thứ này không được hỗ trợ:
* Các phần tử React, hay [JSX](/learn/writing-markup-with-jsx)
* Các hàm, bao gồm các hàm component hoặc bất kỳ hàm nào khác không phải là Server Function
* [Classes](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Classes_in_JavaScript)
* Object là các instance của bất kỳ class nào (ngoài các built-in được đề cập) hoặc object có [prototype null](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object#null-prototype_objects)
* Các symbol không được đăng ký toàn cục, ví dụ `Symbol('my new symbol')`
* Các event từ event handlers


Các giá trị trả về có thể serialize được hỗ trợ cũng giống với [serializable props](/reference/rsc/use-client#serializable-types) cho một boundary Client Component.


## Cách sử dụng {/*usage*/}

### Server Functions trong forms {/*server-functions-in-forms*/}

Trường hợp sử dụng phổ biến nhất của Server Functions là gọi các hàm thay đổi dữ liệu. Trên trình duyệt, [phần tử HTML form](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form) là cách tiếp cận truyền thống để người dùng gửi một thay đổi. Với React Server Components, React giới thiệu hỗ trợ hạng nhất cho Server Functions như Actions trong [forms](/reference/react-dom/components/form).

Đây là một form cho phép người dùng yêu cầu tên người dùng.

```js [[1, 3, "formData"]]
// App.js

async function requestUsername(formData) {
  'use server';
  const username = formData.get('username');
  // ...
}

export default function App() {
  return (
    <form action={requestUsername}>
      <input type="text" name="username" />
      <button type="submit">Request</button>
    </form>
  );
}
```

Trong ví dụ này `requestUsername` là một Server Function được truyền cho một `<form>`. Khi người dùng gửi form này, có một network request đến server function `requestUsername`. Khi gọi một Server Function trong form, React sẽ cung cấp <CodeStep step={1}>[FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData)</CodeStep> của form như đối số đầu tiên cho Server Function.

Bằng cách truyền một Server Function cho form `action`, React có thể [progressive enhance](https://developer.mozilla.org/en-US/docs/Glossary/Progressive_Enhancement) form. Điều này có nghĩa là các form có thể được gửi trước khi JavaScript bundle được tải.

#### Xử lý giá trị trả về trong forms {/*handling-return-values*/}

Trong form yêu cầu tên người dùng, có thể có trường hợp tên người dùng không khả dụng. `requestUsername` nên cho chúng ta biết nếu nó thất bại hay không.

Để cập nhật UI dựa trên kết quả của Server Function trong khi hỗ trợ progressive enhancement, hãy sử dụng [`useActionState`](/reference/react/useActionState).

```js
// requestUsername.js
'use server';

export default async function requestUsername(formData) {
  const username = formData.get('username');
  if (canRequest(username)) {
    // ...
    return 'successful';
  }
  return 'failed';
}
```

```js {4,8}, [[2, 2, "'use client'"]]
// UsernameForm.js
'use client';

import { useActionState } from 'react';
import requestUsername from './requestUsername';

function UsernameForm() {
  const [state, action] = useActionState(requestUsername, null, 'n/a');

  return (
    <>
      <form action={action}>
        <input type="text" name="username" />
        <button type="submit">Request</button>
      </form>
      <p>Last submission request returned: {state}</p>
    </>
  );
}
```

Lưu ý rằng giống như hầu hết các Hooks, `useActionState` chỉ có thể được gọi trong <CodeStep step={1}>[code client](/reference/rsc/use-client)</CodeStep>.

### Gọi Server Function bên ngoài `<form>` {/*calling-a-server-function-outside-of-form*/}

Server Functions là các endpoint server được mở ra và có thể được gọi ở bất kỳ đâu trong code client.

Khi sử dụng Server Function bên ngoài [form](/reference/react-dom/components/form), hãy gọi Server Function trong một [Transition](/reference/react/useTransition), cho phép bạn hiển thị loading indicator, hiển thị [optimistic state updates](/reference/react/useOptimistic), và xử lý các lỗi không mong muốn. Các form sẽ tự động bọc Server Functions trong các transition.

```js {9-12}
import incrementLike from './actions';
import { useState, useTransition } from 'react';

function LikeButton() {
  const [isPending, startTransition] = useTransition();
  const [likeCount, setLikeCount] = useState(0);

  const onClick = () => {
    startTransition(async () => {
      const currentCount = await incrementLike();
      setLikeCount(currentCount);
    });
  };

  return (
    <>
      <p>Total Likes: {likeCount}</p>
      <button onClick={onClick} disabled={isPending}>Like</button>;
    </>
  );
}
```

```js
// actions.js
'use server';

let likeCount = 0;
export default async function incrementLike() {
  likeCount++;
  return likeCount;
}
```

Để đọc giá trị trả về của Server Function, bạn sẽ cần `await` promise được trả về.
