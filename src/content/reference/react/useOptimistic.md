---
title: useOptimistic
---

<Intro>

`useOptimistic` là một React Hook cho phép bạn cập nhật giao diện một cách lạc quan (optimistically).

```js
const [optimisticState, setOptimistic] = useOptimistic(value, reducer?);
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `useOptimistic(value, reducer?)` {/*useoptimistic*/}

Gọi `useOptimistic` ở cấp cao nhất của component để tạo trạng thái lạc quan cho một giá trị.

```js
import { useOptimistic } from 'react';

function MyComponent({name, todos}) {
  const [optimisticAge, setOptimisticAge] = useOptimistic(28);
  const [optimisticName, setOptimisticName] = useOptimistic(name);
  const [optimisticTodos, setOptimisticTodos] = useOptimistic(todos, todoReducer);
  // ...
}
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `value`: Giá trị được trả về khi không có Action nào đang chờ xử lý.
* **tùy chọn** `reducer(currentState, action)`: Hàm reducer chỉ định cách trạng thái lạc quan được cập nhật. Nó phải là pure, nhận tham số là trạng thái hiện tại và action của reducer, và phải trả về trạng thái lạc quan tiếp theo.

#### Giá trị trả về {/*returns*/}

`useOptimistic` trả về một mảng chính xác hai giá trị:

1. `optimisticState`: Trạng thái lạc quan hiện tại. Nó bằng với `value` trừ khi có một Action đang chờ xử lý, trong trường hợp đó nó bằng với trạng thái được trả về bởi `reducer` (hoặc giá trị được truyền vào hàm set nếu không có `reducer` nào được cung cấp).
2. [Hàm `set`](#setoptimistic) cho phép bạn cập nhật trạng thái lạc quan thành một giá trị khác bên trong một Action.

---

### Các hàm `set`, như `setOptimistic(optimisticState)` {/*setoptimistic*/}

Hàm `set` được trả về bởi `useOptimistic` cho phép bạn cập nhật trạng thái trong thời gian một [Action](reference/react/useTransition#functions-called-in-starttransition-are-called-actions) đang diễn ra. Bạn có thể truyền trạng thái tiếp theo trực tiếp, hoặc một hàm tính toán nó từ trạng thái trước:

```js
const [optimisticLike, setOptimisticLike] = useOptimistic(false);
const [optimisticSubs, setOptimisticSubs] = useOptimistic(subs);

function handleClick() {
  startTransition(async () => {
    setOptimisticLike(true);
    setOptimisticSubs(a => a + 1);
    await saveChanges();
  });
}
```

#### Tham số {/*setoptimistic-parameters*/}

* `optimisticState`: Giá trị bạn muốn trạng thái lạc quan trở thành trong thời gian một [Action](reference/react/useTransition#functions-called-in-starttransition-are-called-actions). Nếu bạn cung cấp `reducer` cho `useOptimistic`, giá trị này sẽ được truyền như là tham số thứ hai cho reducer của bạn. Nó có thể là một giá trị của bất kỳ kiểu nào.
    * Nếu bạn truyền một hàm như `optimisticState`, nó sẽ được coi là một _hàm updater_. Nó phải là pure, nhận trạng thái đang chờ là tham số duy nhất, và phải trả về trạng thái lạc quan tiếp theo. React sẽ đặt hàm updater của bạn vào hàng đợi và render lại component của bạn. Trong lần render tiếp theo, React sẽ tính toán trạng thái tiếp theo bằng cách áp dụng các updater được xếp hàng đối với trạng thái trước tương tự như [các updater `useState`](/reference/react/useState#setstate-parameters).

#### Giá trị trả về {/*setoptimistic-returns*/}

Các hàm `set` không có giá trị trả về.

#### Lưu ý {/*setoptimistic-caveats*/}

* Hàm `set` phải được gọi bên trong một [Action](reference/react/useTransition#functions-called-in-starttransition-are-called-actions). Nếu bạn gọi setter bên ngoài một Action, [React sẽ hiển thị cảnh báo](#an-optimistic-state-update-occurred-outside-a-transition-or-action) và trạng thái lạc quan sẽ render ngắn gọn.

<DeepDive>

#### Cơ chế hoạt động của trạng thái lạc quan {/*how-optimistic-state-works*/}

`useOptimistic` cho phép bạn hiển thị một giá trị tạm thời trong khi một Action đang diễn ra:

```js
const [value, setValue] = useState('a');
const [optimistic, setOptimistic] = useOptimistic(value);

startTransition(async () => {
  setOptimistic('b');
  const newValue = await saveChanges('b');
  setValue(newValue);
});
```

Khi setter được gọi bên trong một Action, `useOptimistic` sẽ kích hoạt render lại để hiển thị trạng thái đó trong khi Action đang diễn ra. Ngược lại, `value` được truyền vào `useOptimistic` được trả về.

Trạng thái này được gọi là "lạc quan" vì nó được sử dụng để ngay lập tức trình bày cho người dùng kết quả của việc thực hiện một Action, mặc dù Action thực sự cần thời gian để hoàn thành.

**Luồng cập nhật diễn ra như thế nào**

1. **Cập nhật ngay lập tức**: Khi `setOptimistic('b')` được gọi, React ngay lập tức render với `'b'`.

2. **(Tùy chọn) await trong Action**: Nếu bạn await trong Action, React tiếp tục hiển thị `'b'`.

3. **Transition được lên lịch**: `setValue(newValue)` lên lịch một cập nhật cho trạng thái thực.

4. **(Tùy chọn) đợi Suspense**: Nếu `newValue` tạm dừng, React tiếp tục hiển thị `'b'`.

5. **Cam kết render đơn lẻ**: Cuối cùng, `newValue` được cam kết cho cả `value` và `optimistic`.

Không có render thêm nào để "xóa" trạng thái lạc quan. Trạng thái lạc quan và trạng thái thực hội tụ trong cùng một lần render khi Transition hoàn thành.

<Note>

#### Trạng thái lạc quan là tạm thời {/*optimistic-state-is-temporary*/}

Trạng thái lạc quan chỉ render trong khi một Action đang diễn ra, nếu không thì `value` được render.

Nếu `saveChanges` trả về `'c'`, thì cả `value` và `optimistic` sẽ là `'c'`, không phải `'b'`.

</Note>

**Cách trạng thái cuối cùng được xác định**

Tham số `value` cho `useOptimistic` xác định những gì hiển thị sau khi Action kết thúc. Cách hoạt động này phụ thuộc vào pattern bạn sử dụng:

- **Các giá trị hardcoded** như `useOptimistic(false)`: Sau Action, `state` vẫn là `false`, vì vậy giao diện hiển thị `false`. Điều này hữu ích cho các trạng thái chờ mà bạn luôn bắt đầu từ `false`.

- **Props hoặc state được truyền vào** như `useOptimistic(isLiked)`: Nếu cha cập nhật `isLiked` trong khi Action đang diễn ra, giá trị mới được sử dụng sau khi Action hoàn thành. Đây là cách giao diện phản ánh kết quả của Action.

- **Pattern reducer** như `useOptimistic(items, fn)`: Nếu `items` thay đổi trong khi Action đang chờ, React chạy lại `reducer` của bạn với `items` mới để tính toán lại trạng thái. Điều này giữ các mục bổ sung lạc quan của bạn trên dữ liệu mới nhất.

**Điều gì xảy ra khi Action thất bại**

Nếu Action ném lỗi, Transition vẫn kết thúc, và React render với bất kỳ giá trị `value` nào hiện tại. Vì cha thường chỉ cập nhật `value` khi thành công, một thất bại có nghĩa là `value` chưa thay đổi, vì vậy giao diện hiển thị những gì nó hiển thị trước khi cập nhật lạc quan. Bạn có thể bắt lỗi để hiển thị thông báo cho người dùng.

</DeepDive>

---

## Cách sử dụng {/*usage*/}

### Thêm trạng thái lạc quan vào một component {/*adding-optimistic-state-to-a-component*/}

Gọi `useOptimistic` ở cấp cao nhất của component để khai báo một hoặc nhiều trạng thái lạc quan.

```js [[1, 4, "age"], [1, 5, "name"], [1, 6, "todos"], [2, 4, "optimisticAge"], [2, 5, "optimisticName"], [2, 6, "optimisticTodos"], [3, 4, "setOptimisticAge"], [3, 5, "setOptimisticName"], [3, 6, "setOptimisticTodos"], [4, 6, "reducer"]]
import { useOptimistic } from 'react';

function MyComponent({age, name, todos}) {
  const [optimisticAge, setOptimisticAge] = useOptimistic(age);
  const [optimisticName, setOptimisticName] = useOptimistic(name);
  const [optimisticTodos, setOptimisticTodos] = useOptimistic(todos, reducer);
  // ...
```

`useOptimistic` trả về một mảng chính xác hai mục:

1. <CodeStep step={2}>Trạng thái lạc quan</CodeStep>, ban đầu được đặt thành <CodeStep step={1}>giá trị</CodeStep> được cung cấp.
2. <CodeStep step={3}>Hàm set</CodeStep> cho phép bạn tạm thời thay đổi trạng thái trong thời gian một [Action](reference/react/useTransition#functions-called-in-starttransition-are-called-actions).
   * Nếu một <CodeStep step={4}>reducer</CodeStep> được cung cấp, nó sẽ chạy trước khi trả về trạng thái lạc quan.

Để sử dụng <CodeStep step={2}>trạng thái lạc quan</CodeStep>, hãy gọi hàm `set` bên trong một Action.

Các Action là các hàm được gọi bên trong `startTransition`:

```js {3}
function onAgeChange(e) {
  startTransition(async () => {
    setOptimisticAge(42);
    const newAge = await postAge(42);
    setAge(newAge);
  });
}
```

React sẽ render trạng thái lạc quan `42` trước trong khi `age` vẫn là giá trị hiện tại. Action chờ POST, sau đó render `newAge` cho cả `age` và `optimisticAge`.

Xem [Cơ chế hoạt động của trạng thái lạc quan](#how-optimistic-state-works) để tìm hiểu sâu hơn.

<Note>

Khi sử dụng [Action props](/reference/react/useTransition#exposing-action-props-from-components), bạn có thể gọi hàm set mà không cần `startTransition`:

```js [[3, 2, "setOptimisticName"]]
async function submitAction() {
  setOptimisticName('Taylor');
  await updateName('Taylor');
}
```

Điều này hoạt động vì Action props đã được gọi bên trong `startTransition`.

Để xem ví dụ, hãy xem: [Sử dụng trạng thái lạc quan trong Action props](#using-optimistic-state-in-action-props).

</Note>

---

### Sử dụng trạng thái lạc quan trong Action props {/*using-optimistic-state-in-action-props*/}

Trong một [Action prop](/reference/react/useTransition#exposing-action-props-from-components), bạn có thể gọi setter lạc quan trực tiếp mà không cần `startTransition`.

Ví dụ này đặt trạng thái lạc quan bên trong prop `submitAction` của `<form>`:

<Sandpack>

```js src/App.js
import { useState, startTransition } from 'react';
import EditName from './EditName';

export default function App() {
  const [name, setName] = useState('Alice');

  return <EditName name={name} action={setName} />;
}
```

```js src/EditName.js active
import { useOptimistic, startTransition } from 'react';
import { updateName } from './actions.js';

export default function EditName({ name, action }) {
  const [optimisticName, setOptimisticName] = useOptimistic(name);

  async function submitAction(formData) {
    const newName = formData.get('name');
    setOptimisticName(newName);

    const updatedName = await updateName(newName);
    startTransition(() => {
      action(updatedName);
    })
  }

  return (
    <form action={submitAction}>
      <p>Your name is: {optimisticName}</p>
      <p>
        <label>Change it: </label>
        <input
          type="text"
          name="name"
          disabled={name !== optimisticName}
        />
      </p>
    </form>
  );
}
```

```js src/actions.js hidden
export async function updateName(name) {
  await new Promise((res) => setTimeout(res, 1000));
  return name;
}
```

</Sandpack>

Trong ví dụ này, khi người dùng gửi form, `optimisticName` cập nhật ngay lập tức để hiển thị `newName` một cách lạc quan trong khi yêu cầu server đang diễn ra. Khi yêu cầu hoàn thành, `name` và `optimisticName` được render với `updatedName` thực tế từ phản hồi.

<DeepDive>

#### Tại sao điều này không cần `startTransition`? {/*why-doesnt-this-need-starttransition*/}

Theo quy ước, các props được gọi bên trong `startTransition` được đặt tên với "Action".

Vì `submitAction` được đặt tên với "Action", bạn biết nó đã được gọi bên trong `startTransition`.

Xem [Tiếp xúc prop `action` từ các component](/reference/react/useTransition#exposing-action-props-from-components) để biết pattern Action prop.

</DeepDive>

---

### Thêm trạng thái lạc quan vào Action props {/*adding-optimistic-state-to-action-props*/}

Khi tạo một [Action prop](/reference/react/useTransition#exposing-action-props-from-components), bạn có thể thêm `useOptimistic` để hiển thị phản hồi ngay lập tức.

Đây là một nút hiển thị "Submitting..." trong khi `action` đang chờ:

<Sandpack>

```js src/App.js
import { useState, startTransition } from 'react';
import Button from './Button';
import { submitForm } from './actions.js';

export default function App() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <Button action={async () => {
        await submitForm();
        startTransition(() => {
          setCount(c => c + 1);
        });
      }}>Increment</Button>
      {count > 0 && <p>Submitted {count}!</p>}
    </div>
  );
}
```

```js src/Button.js active
import { useOptimistic, startTransition } from 'react';

export default function Button({ action, children }) {
  const [isPending, setIsPending] = useOptimistic(false);

  return (
    <button
      disabled={isPending}
      onClick={() => {
        startTransition(async () => {
          setIsPending(true);
          await action();
        });
      }}
    >
      {isPending ? 'Submitting...' : children}
    </button>
  );
}
```

```js src/actions.js hidden
export async function submitForm() {
  await new Promise((res) => setTimeout(res, 1000));
}
```

</Sandpack>

Khi nút được nhấp, `setIsPending(true)` sử dụng trạng thái lạc quan để ngay lập tức hiển thị "Submitting..." và vô hiệu hóa nút. Khi Action hoàn thành, `isPending` được render là `false` tự động.

Pattern này tự động hiển thị trạng thái chờ dù prop `action` được sử dụng với `Button` như thế nào:

```js
// Hiển thị trạng thái chờ cho cập nhật state
<Button action={() => { setState(c => c + 1) }} />

// Hiển thị trạng thái chờ cho điều hướng
<Button action={() => { navigate('/done') }} />

// Hiển thị trạng thái chờ cho một POST
<Button action={async () => { await fetch(/* ... */) }} />

// Hiển thị trạng thái chờ cho bất kỳ sự kết hợp nào
<Button action={async () => {
  setState(c => c + 1);
  await fetch(/* ... */);
  navigate('/done');
}} />
```

Trạng thái chờ sẽ được hiển thị cho đến khi mọi thứ trong prop `action` hoàn thành.

<Note>

Bạn cũng có thể sử dụng [`useTransition`](/reference/react/useTransition) để lấy trạng thái chờ qua `isPending`.

Sự khác biệt là `useTransition` cho bạn hàm `startTransition`, trong khi `useOptimistic` hoạt động với bất kỳ Transition nào. Sử dụng cái nào phù hợp với nhu cầu của component của bạn.

</Note>

---

### Cập nhật props hoặc state một cách lạc quan {/*updating-props-or-state-optimistically*/}

Bạn có thể bọc props hoặc state trong `useOptimistic` để cập nhật nó ngay lập tức trong khi một Action đang diễn ra.

Trong ví dụ này, `LikeButton` nhận `isLiked` như một prop và ngay lập tức bật tắt nó khi được nhấp:

<Sandpack>

```js src/App.js
import { useState, useOptimistic, startTransition } from 'react';
import { toggleLike } from './actions.js';

export default function App() {
  const [isLiked, setIsLiked] = useState(false);
  const [optimisticIsLiked, setOptimisticIsLiked] = useOptimistic(isLiked);

  function handleClick() {
    startTransition(async () => {
      const newValue = !optimisticIsLiked
      console.log('⏳ setting optimistic state: ' + newValue);

      setOptimisticIsLiked(newValue);
      const updatedValue = await toggleLike(newValue);

      startTransition(() => {
        console.log('⏳ setting real state: ' + updatedValue );
        setIsLiked(updatedValue);
      });
    });
  }

  if (optimisticIsLiked !== isLiked) {
    console.log('✅ rendering optimistic state: ' + optimisticIsLiked);
  } else {
    console.log('✅ rendering real value: ' + optimisticIsLiked);
  }


  return (
    <button onClick={handleClick}>
      {optimisticIsLiked ? '❤️ Unlike' : '🤍 Like'}
    </button>
  );
}
```

```js src/actions.js hidden
export async function toggleLike(value) {
  return await new Promise((res) => setTimeout(() => res(value), 1000));
  // In a real app, this would update the server
}
```

```js src/index.js hidden
import React from 'react';
import {createRoot} from 'react-dom/client';
import './styles.css';

import App from './App';

const root = createRoot(document.getElementById('root'));
// Not using StrictMode so double render logs are not shown.
root.render(<App />);
```

</Sandpack>

Khi nút được nhấp, `setOptimisticIsLiked` ngay lập tức cập nhật trạng thái hiển thị để hiển thị trái tim là đã thích. Trong khi đó, `await toggleLike` chạy ở nền. Khi `await` hoàn thành, `setIsLiked` cha cập nhật trạng thái `isLiked` "thực", và trạng thái lạc quan được render khớp với giá trị mới này.

<Note>

Ví dụ này đọc từ `optimisticIsLiked` để tính toán giá trị tiếp theo. Điều này hoạt động khi trạng thái cơ sở sẽ không thay đổi, nhưng nếu trạng thái cơ sở có thể thay đổi trong khi Action của bạn đang chờ, bạn có thể muốn sử dụng hàm updater state hoặc reducer.

Xem [Cập nhật state dựa trên trạng thái hiện tại](#updating-state-based-on-current-state) để có ví dụ.

</Note>

---

### Cập nhật nhiều giá trị cùng nhau {/*updating-multiple-values-together*/}

Khi một cập nhật lạc quan ảnh hưởng đến nhiều giá trị liên quan, hãy sử dụng reducer để cập nhật chúng cùng nhau. Điều này đảm bảo giao diện luôn nhất quán.

Đây là nút theo dõi cập nhật cả trạng thái theo dõi và số lượng người theo dõi:

<Sandpack>

```js src/App.js
import { useState, startTransition } from 'react';
import { followUser, unfollowUser } from './actions.js';
import FollowButton from './FollowButton';

export default function App() {
  const [user, setUser] = useState({
    name: 'React',
    isFollowing: false,
    followerCount: 10500
  });

  async function followAction(shouldFollow) {
    if (shouldFollow) {
      await followUser(user.name);
    } else {
      await unfollowUser(user.name);
    }
    startTransition(() => {
      setUser(current => ({
        ...current,
        isFollowing: shouldFollow,
        followerCount: current.followerCount + (shouldFollow ? 1 : -1)
      }));
    });
  }

  return <FollowButton user={user} followAction={followAction} />;
}
```

```js src/FollowButton.js active
import { useOptimistic, startTransition } from 'react';

export default function FollowButton({ user, followAction }) {
  const [optimisticState, updateOptimistic] = useOptimistic(
    { isFollowing: user.isFollowing, followerCount: user.followerCount },
    (current, isFollowing) => ({
      isFollowing,
      followerCount: current.followerCount + (isFollowing ? 1 : -1)
    })
  );

  function handleClick() {
    const newFollowState = !optimisticState.isFollowing;
    startTransition(async () => {
      updateOptimistic(newFollowState);
      await followAction(newFollowState);
    });
  }

  return (
    <div>
      <p><strong>{user.name}</strong></p>
      <p>{optimisticState.followerCount} followers</p>
      <button onClick={handleClick}>
        {optimisticState.isFollowing ? 'Unfollow' : 'Follow'}
      </button>
    </div>
  );
}
```

```js src/actions.js hidden
export async function followUser(name) {
  await new Promise((res) => setTimeout(res, 1000));
}

export async function unfollowUser(name) {
  await new Promise((res) => setTimeout(res, 1000));
}
```

</Sandpack>

Reducer nhận giá trị `isFollowing` mới và tính toán cả trạng thái theo dõi mới và số lượng người theo dõi đã cập nhật trong một lần cập nhật duy nhất. Điều này đảm bảo văn bản nút và số lượng luôn đồng bộ.


<DeepDive>

#### Chọn giữa updater và reducer {/*choosing-between-updaters-and-reducers*/}

`useOptimistic` hỗ trợ hai pattern để tính toán state dựa trên trạng thái hiện tại:

**Hàm updater** hoạt động giống như [updater của useState](/reference/react/useState#updating-state-based-on-the-previous-state). Truyền một hàm vào setter:

```js
const [optimistic, setOptimistic] = useOptimistic(value);
setOptimistic(current => !current);
```

**Reducer** tách logic cập nhật ra khỏi lời gọi setter:

```js
const [optimistic, dispatch] = useOptimistic(value, (current, action) => {
  // Tính toán trạng thái tiếp theo dựa trên current và action
});
dispatch(action);
```

**Sử dụng updater** cho các tính toán mà lời gọi setter tự nhiên mô tả cập nhật. Điều này tương tự như sử dụng `setState(prev => ...)` với `useState`.

**Sử dụng reducer** khi bạn cần truyền dữ liệu cho cập nhật (như mục nào cần thêm) hoặc khi xử lý nhiều loại cập nhật với một hook duy nhất.

**Tại sao sử dụng reducer?**

Reducer là thiết yếu khi trạng thái cơ sở có thể thay đổi trong khi Transition của bạn đang chờ. Nếu `todos` thay đổi trong khi việc thêm của bạn đang chờ (ví dụ, người dùng khác đã thêm một todo), React sẽ chạy lại reducer của bạn với `todos` mới để tính toán lại những gì hiển thị. Điều này đảm bảo todo mới của bạn được thêm vào danh sách mới nhất, không phải bản sao lỗi thời.

Một hàm updater như `setOptimistic(prev => [...prev, newItem])` sẽ chỉ thấy trạng thái từ khi Transition bắt đầu, bỏ lỡ bất kỳ cập nhật nào xảy ra trong quá trình làm việc async.

</DeepDive>

---

### Thêm lạc quan vào danh sách {/*optimistically-adding-to-a-list*/}

Khi bạn cần thêm các mục vào danh sách một cách lạc quan, hãy sử dụng `reducer`:

<Sandpack>

```js src/App.js
import { useState, startTransition } from 'react';
import { addTodo } from './actions.js';
import TodoList from './TodoList';

export default function App() {
  const [todos, setTodos] = useState([
    { id: 1, text: 'Learn React' }
  ]);

  async function addTodoAction(newTodo) {
    const savedTodo = await addTodo(newTodo);
    startTransition(() => {
      setTodos(todos => [...todos, savedTodo]);
    });
  }

  return <TodoList todos={todos} addTodoAction={addTodoAction} />;
}
```

```js src/TodoList.js active
import { useOptimistic, startTransition } from 'react';

export default function TodoList({ todos, addTodoAction }) {
  const [optimisticTodos, addOptimisticTodo] = useOptimistic(
    todos,
    (currentTodos, newTodo) => [
      ...currentTodos,
      { id: newTodo.id, text: newTodo.text, pending: true }
    ]
  );

  function handleAddTodo(text) {
    const newTodo = { id: crypto.randomUUID(), text: text };
    startTransition(async () => {
      addOptimisticTodo(newTodo);
      await addTodoAction(newTodo);
    });
  }

  return (
    <div>
      <button onClick={() => handleAddTodo('New todo')}>Add Todo</button>
      <ul>
        {optimisticTodos.map(todo => (
          <li key={todo.id}>
            {todo.text} {todo.pending && "(Adding...)"}
          </li>
        ))}
      </ul>
    </div>
  );
}
```

```js src/actions.js hidden
export async function addTodo(todo) {
  await new Promise((res) => setTimeout(res, 1000));
  // In a real app, this would save to the server
  return { ...todo, pending: false };
}
```

</Sandpack>

`reducer` nhận danh sách todos hiện tại và todo mới cần thêm. Điều này quan trọng vì nếu prop `todos` thay đổi trong khi việc thêm của bạn đang chờ (ví dụ, người dùng khác đã thêm một todo), React sẽ cập nhật trạng thái lạc quan của bạn bằng cách chạy lại reducer với danh sách được cập nhật. Điều này đảm bảo todo mới của bạn được thêm vào danh sách mới nhất, không phải bản sao lỗi thời.

<Note>

Mỗi mục lạc quan bao gồm cờ `pending: true` để bạn có thể hiển thị trạng thái tải cho các mục riêng lẻ. Khi server phản hồi và cha cập nhật danh sách `todos` chính thức với mục đã lưu, trạng thái lạc quan cập nhật thành mục đã xác nhận mà không có cờ chờ.

</Note>

---

### Xử lý nhiều kiểu `action` {/*handling-multiple-action-types*/}

Khi bạn cần xử lý nhiều loại cập nhật lạc quan (như thêm và xóa mục), hãy sử dụng pattern reducer với các object `action`.

Ví dụ giỏ hàng này cho thấy cách xử lý thêm và xóa với một reducer duy nhất:

<Sandpack>

```js src/App.js
import { useState, startTransition } from 'react';
import { addToCart, removeFromCart, updateQuantity } from './actions.js';
import ShoppingCart from './ShoppingCart';

export default function App() {
  const [cart, setCart] = useState([]);

  const cartActions = {
    async add(item) {
      await addToCart(item);
      startTransition(() => {
        setCart(current => {
          const exists = current.find(i => i.id === item.id);
          if (exists) {
            return current.map(i =>
              i.id === item.id ? { ...i, quantity: i.quantity + 1 } : i
            );
          }
          return [...current, { ...item, quantity: 1 }];
        });
      });
    },
    async remove(id) {
      await removeFromCart(id);
      startTransition(() => {
        setCart(current => current.filter(item => item.id !== id));
      });
    },
    async updateQuantity(id, quantity) {
      await updateQuantity(id, quantity);
      startTransition(() => {
        setCart(current =>
          current.map(item =>
            item.id === id ? { ...item, quantity } : item
          )
        );
      });
    }
  };

  return <ShoppingCart cart={cart} cartActions={cartActions} />;
}
```

```js src/ShoppingCart.js active
import { useOptimistic, startTransition } from 'react';

export default function ShoppingCart({ cart, cartActions }) {
  const [optimisticCart, dispatch] = useOptimistic(
    cart,
    (currentCart, action) => {
      switch (action.type) {
        case 'add':
          const exists = currentCart.find(item => item.id === action.item.id);
          if (exists) {
            return currentCart.map(item =>
              item.id === action.item.id
                ? { ...item, quantity: item.quantity + 1, pending: true }
                : item
            );
          }
          return [...currentCart, { ...action.item, quantity: 1, pending: true }];
        case 'remove':
          return currentCart.filter(item => item.id !== action.id);
        case 'update_quantity':
          return currentCart.map(item =>
            item.id === action.id
              ? { ...item, quantity: action.quantity, pending: true }
              : item
          );
        default:
          return currentCart;
      }
    }
  );

  function handleAdd(item) {
    startTransition(async () => {
      dispatch({ type: 'add', item });
      await cartActions.add(item);
    });
  }

  function handleRemove(id) {
    startTransition(async () => {
      dispatch({ type: 'remove', id });
      await cartActions.remove(id);
    });
  }

  function handleUpdateQuantity(id, quantity) {
    startTransition(async () => {
      dispatch({ type: 'update_quantity', id, quantity });
      await cartActions.updateQuantity(id, quantity);
    });
  }

  const total = optimisticCart.reduce(
    (sum, item) => sum + item.price * item.quantity,
    0
  );

  return (
    <div>
      <h2>Shopping Cart</h2>
      <div style={{ marginBottom: 16 }}>
        <button onClick={() => handleAdd({
          id: 1, name: 'T-Shirt', price: 25
        })}>
          Add T-Shirt ($25)
        </button>{' '}
        <button onClick={() => handleAdd({
          id: 2, name: 'Mug', price: 15
        })}>
          Add Mug ($15)
        </button>
      </div>
      {optimisticCart.length === 0 ? (
        <p>Your cart is empty</p>
      ) : (
        <ul>
          {optimisticCart.map(item => (
            <li key={item.id}>
              {item.name} - ${item.price} ×
              {item.quantity}
              {' '}= ${item.price * item.quantity}
              <button
                onClick={() => handleRemove(item.id)}
                style={{ marginLeft: 8 }}
              >
                Remove
              </button>
              {item.pending && ' ...'}
            </li>
          ))}
        </ul>
      )}
      <p><strong>Total: ${total}</strong></p>
    </div>
  );
}
```

```js src/actions.js hidden
export async function addToCart(item) {
  await new Promise((res) => setTimeout(res, 800));
}

export async function removeFromCart(id) {
  await new Promise((res) => setTimeout(res, 800));
}

export async function updateQuantity(id, quantity) {
  await new Promise((res) => setTimeout(res, 800));
}
```

</Sandpack>

Reducer xử lý ba kiểu `action` (`add`, `remove`, `update_quantity`) và trả về trạng thái lạc quan mới cho mỗi kiểu. Mỗi `action` đặt cờ `pending: true` để bạn có thể hiển thị phản hồi trực quan trong khi [Server Function](/reference/rsc/server-functions) chạy.

---

### Xóa lạc quan với phục hồi lỗi {/*optimistic-delete-with-error-recovery*/}

Khi xóa các mục một cách lạc quan, bạn nên xử lý trường hợp Action thất bại.

Ví dụ này cho thấy cách hiển thị thông báo lỗi khi xóa thất bại, và giao diện tự động quay lại để hiển thị mục đó lại.

<Sandpack>

```js src/App.js
import { useState, startTransition } from 'react';
import { deleteItem } from './actions.js';
import ItemList from './ItemList';

export default function App() {
  const [items, setItems] = useState([
    { id: 1, name: 'Learn React' },
    { id: 2, name: 'Build an app' },
    { id: 3, name: 'Deploy to production' },
  ]);

  async function deleteAction(id) {
    await deleteItem(id);
    startTransition(() => {
      setItems(current => current.filter(item => item.id !== id));
    });
  }

  return <ItemList items={items} deleteAction={deleteAction} />;
}
```

```js src/ItemList.js active
import { useState, useOptimistic, startTransition } from 'react';

export default function ItemList({ items, deleteAction }) {
  const [error, setError] = useState(null);
  const [optimisticItems, removeItem] = useOptimistic(
    items,
    (currentItems, idToRemove) =>
      currentItems.map(item =>
        item.id === idToRemove
          ? { ...item, deleting: true }
          : item
      )
  );

  function handleDelete(id) {
    setError(null);
    startTransition(async () => {
      removeItem(id);
      try {
        await deleteAction(id);
      } catch (e) {
        setError(e.message);
      }
    });
  }

  return (
    <div>
      <h2>Your Items</h2>
      <ul>
        {optimisticItems.map(item => (
          <li
            key={item.id}
            style={{
              opacity: item.deleting ? 0.5 : 1,
              textDecoration: item.deleting ? 'line-through' : 'none',
              transition: 'opacity 0.2s'
            }}
          >
            {item.name}
            <button
              onClick={() => handleDelete(item.id)}
              disabled={item.deleting}
              style={{ marginLeft: 8 }}
            >
              {item.deleting ? 'Deleting...' : 'Delete'}
            </button>
          </li>
        ))}
      </ul>
      {error && (
        <p style={{ color: 'red', padding: 8, background: '#fee' }}>
          {error}
        </p>
      )}
    </div>
  );
}
```

```js src/actions.js hidden
export async function deleteItem(id) {
  await new Promise((res) => setTimeout(res, 1000));
  // Item 3 always fails to demonstrate error recovery
  if (id === 3) {
    throw new Error('Cannot delete. Permission denied.');
  }
}
```

</Sandpack>

Thử xóa 'Deploy to production'. Khi việc xóa thất bại, mục đó tự động xuất hiện lại trong danh sách.

---

## Xử lý sự cố {/*troubleshooting*/}

### Tôi nhận được lỗi: "An optimistic state update occurred outside a Transition or Action" {/*an-optimistic-state-update-occurred-outside-a-transition-or-action*/}

Bạn có thể thấy lỗi này:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

An optimistic state update occurred outside a Transition or Action. To fix, move the update to an Action, or wrap with `startTransition`.

</ConsoleLogLine>

</ConsoleBlockMulti>

Hàm setter lạc quan phải được gọi bên trong `startTransition`:

```js
// 🚩 Không đúng: nằm ngoài một Transition
function handleClick() {
  setOptimistic(newValue);  // Warning!
  // ...
}

// ✅ Đúng: bên trong một Transition
function handleClick() {
  startTransition(async () => {
    setOptimistic(newValue);
    // ...
  });
}

// ✅ Cũng đúng: bên trong một Action prop
function submitAction(formData) {
  setOptimistic(newValue);
  // ...
}
```

Khi bạn gọi setter bên ngoài một Action, trạng thái lạc quan sẽ xuất hiện ngắn gọn và sau đó ngay lập tức quay lại giá trị ban đầu. Điều này xảy ra vì không có Transition để "giữ" trạng thái lạc quan trong khi Action của bạn chạy.

### Tôi nhận được lỗi: "Cannot update optimistic state while rendering" {/*cannot-update-optimistic-state-while-rendering*/}

Bạn có thể thấy lỗi này:

<ConsoleBlockMulti>

<ConsoleLogLine level="error">

Cannot update optimistic state while rendering.

</ConsoleLogLine>

</ConsoleBlockMulti>

Lỗi này xảy ra khi bạn gọi setter lạc quan trong giai đoạn render của một component. Bạn chỉ có thể gọi nó từ các event handler, effect, hoặc các callback khác:

```js
// 🚩 Không đúng: gọi trong khi render
function MyComponent({ items }) {
  const [isPending, setPending] = useOptimistic(false);

  // Điều này chạy trong khi render - không được phép!
  setPending(true);

  // ...
}

// ✅ Đúng: gọi bên trong startTransition
function MyComponent({ items }) {
  const [isPending, setPending] = useOptimistic(false);

  function handleClick() {
    startTransition(() => {
      setPending(true);
      // ...
    });
  }

  // ...
}

// ✅ Cũng đúng: gọi từ một Action
function MyComponent({ items }) {
  const [isPending, setPending] = useOptimistic(false);

  function action() {
    setPending(true);
    // ...
  }

  // ...
}
```

### Các cập nhật lạc quan của tôi hiển thị giá trị cũ {/*my-optimistic-updates-show-stale-values*/}

Nếu trạng thái lạc quan của bạn có vẻ dựa trên dữ liệu cũ, hãy xem xét sử dụng hàm updater hoặc reducer để tính toán trạng thái lạc quan tương đối so với trạng thái hiện tại.

```js
// Có thể hiển thị dữ liệu cũ nếu state thay đổi trong khi Action
const [optimistic, setOptimistic] = useOptimistic(count);
setOptimistic(5);  // Luôn đặt thành 5, ngay cả khi count đã thay đổi

// Tốt hơn: các cập nhật tương đối xử lý thay đổi state đúng cách
const [optimistic, adjust] = useOptimistic(count, (current, delta) => current + delta);
adjust(1);  // Luôn cộng 1 vào bất kỳ giá trị hiện tại nào của count
```

Xem [Cập nhật state dựa trên trạng thái hiện tại](#updating-state-based-on-current-state) để biết chi tiết.

### Tôi không biết liệu cập nhật lạc quan của mình có đang chờ không {/*i-dont-know-if-my-optimistic-update-is-pending*/}

Để biết khi nào `useOptimistic` đang chờ, bạn có ba tùy chọn:

1. **Kiểm tra nếu `optimisticValue === value`**

```js
const [optimistic, setOptimistic] = useOptimistic(value);
const isPending = optimistic !== value;
```

Nếu các giá trị không bằng nhau, có một Transition đang diễn ra.

2. **Thêm một `useTransition`**

```js
const [isPending, startTransition] = useTransition();
const [optimistic, setOptimistic] = useOptimistic(value);

//...
startTransition(() => {
  setOptimistic(state);
})
```

Vì `useTransition` sử dụng `useOptimistic` cho `isPending` ở bên dưới, đây tương đương với tùy chọn 1.

3. **Thêm cờ `pending` trong reducer của bạn**

```js
const [optimistic, addOptimistic] = useOptimistic(
  items,
  (state, newItem) => [...state, { ...newItem, isPending: true }]
);
```

Vì mỗi mục lạc quan có cờ riêng của nó, bạn có thể hiển thị trạng thái tải cho các mục riêng lẻ.
