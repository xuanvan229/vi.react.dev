---
title: 'Bạn Có Thể Không Cần Effect'
---

<Intro>

Effect là một cửa thoát khỏi mô hình React. Chúng cho phép bạn "bước ra ngoài" React và đồng bộ hóa các component của bạn với một hệ thống bên ngoài như một widget không phải React, mạng, hoặc DOM trình duyệt. Nếu không có hệ thống bên ngoài nào liên quan (ví dụ: nếu bạn muốn cập nhật state của một component khi props hoặc state thay đổi), bạn không cần Effect. Loại bỏ các Effect không cần thiết sẽ làm cho code của bạn dễ theo dõi hơn, chạy nhanh hơn và ít bị lỗi hơn.

</Intro>

<YouWillLearn>

* Tại sao và cách loại bỏ các Effect không cần thiết khỏi các component của bạn
* Cách cache các tính toán tốn kém mà không cần Effect
* Cách reset và điều chỉnh state của component mà không cần Effect
* Cách chia sẻ logic giữa các event handler
* Logic nào nên được chuyển sang event handler
* Cách thông báo cho các component cha về các thay đổi

</YouWillLearn>

## Cách loại bỏ các Effect không cần thiết {/*how-to-remove-unnecessary-effects*/}

Có hai trường hợp phổ biến mà bạn không cần Effect:

* **Bạn không cần Effect để chuyển đổi dữ liệu để render.** Ví dụ, giả sử bạn muốn lọc một danh sách trước khi hiển thị nó. Bạn có thể bị cám dỗ viết một Effect cập nhật state variable khi danh sách thay đổi. Tuy nhiên, điều này không hiệu quả. Khi bạn cập nhật state, React sẽ đầu tiên gọi các hàm component của bạn để tính toán những gì nên hiển thị trên màn hình. Sau đó React sẽ ["commit"](/learn/render-and-commit) những thay đổi này vào DOM, cập nhật màn hình. Sau đó React sẽ chạy các Effect của bạn. Nếu Effect của bạn *cũng* ngay lập tức cập nhật state, điều này sẽ khởi động lại toàn bộ quá trình từ đầu! Để tránh các lần render không cần thiết, hãy chuyển đổi tất cả dữ liệu ở cấp độ cao nhất của các component của bạn. Code đó sẽ tự động chạy lại bất cứ khi nào props hoặc state thay đổi.
* **Bạn không cần Effect để xử lý các sự kiện người dùng.** Ví dụ, giả sử bạn muốn gửi một POST request `/api/buy` và hiển thị thông báo khi người dùng mua sản phẩm. Trong event handler click của nút Buy, bạn biết chính xác điều gì đã xảy ra. Vào thời điểm Effect chạy, bạn không biết *người dùng đã làm gì* (ví dụ: nút nào đã được nhấp). Đây là lý do tại sao bạn thường xử lý các sự kiện người dùng trong các event handler tương ứng.

Bạn *thực sự* cần Effect để [đồng bộ hóa](/learn/synchronizing-with-effects#what-are-effects-and-how-are-they-different-from-events) với các hệ thống bên ngoài. Ví dụ, bạn có thể viết một Effect giữ cho một jQuery widget được đồng bộ hóa với state React. Bạn cũng có thể fetch dữ liệu với Effect: ví dụ, bạn có thể đồng bộ hóa kết quả tìm kiếm với câu truy vấn tìm kiếm hiện tại. Hãy nhớ rằng các [framework](/learn/creating-a-react-app#full-stack-frameworks) hiện đại cung cấp các cơ chế fetch dữ liệu tích hợp hiệu quả hơn so với việc viết Effect trực tiếp trong các component của bạn.

Để giúp bạn có được trực giác đúng đắn, hãy xem xét một số ví dụ cụ thể phổ biến!

### Cập nhật state dựa trên props hoặc state {/*updating-state-based-on-props-or-state*/}

Giả sử bạn có một component với hai state variable: `firstName` và `lastName`. Bạn muốn tính toán một `fullName` từ chúng bằng cách ghép nối chúng. Hơn nữa, bạn muốn `fullName` cập nhật bất cứ khi nào `firstName` hoặc `lastName` thay đổi. Phản ứng đầu tiên của bạn có thể là thêm một `fullName` state variable và cập nhật nó trong một Effect:

```js {expectedErrors: {'react-compiler': [8]}} {5-9}
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');

  // 🔴 Tránh: state dư thừa và Effect không cần thiết
  const [fullName, setFullName] = useState('');
  useEffect(() => {
    setFullName(firstName + ' ' + lastName);
  }, [firstName, lastName]);
  // ...
}
```

Điều này phức tạp hơn mức cần thiết. Nó cũng không hiệu quả: nó thực hiện toàn bộ một lần render với giá trị cũ cho `fullName`, sau đó ngay lập tức render lại với giá trị đã cập nhật. Hãy xóa state variable và Effect:

```js {4-5}
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');
  // ✅ Tốt: được tính toán trong quá trình render
  const fullName = firstName + ' ' + lastName;
  // ...
}
```

**Khi một thứ gì đó có thể được tính toán từ props hoặc state hiện có, [đừng đưa nó vào state.](/learn/choosing-the-state-structure#avoid-redundant-state) Thay vào đó, hãy tính toán nó trong quá trình render.** Điều này làm cho code của bạn nhanh hơn (bạn tránh được các cập nhật "cascading" thêm), đơn giản hơn (bạn loại bỏ một số code), và ít bị lỗi hơn (bạn tránh được các bug do các state variable khác nhau không đồng bộ với nhau). Nếu cách tiếp cận này cảm thấy mới mẻ với bạn, [Suy nghĩ theo cách của React](/learn/thinking-in-react#step-3-find-the-minimal-but-complete-representation-of-ui-state) giải thích những gì nên đưa vào state.

### Cache các tính toán tốn kém {/*caching-expensive-calculations*/}

Component này tính toán `visibleTodos` bằng cách lấy `todos` nhận được qua props và lọc chúng theo prop `filter`. Bạn có thể bị cám dỗ lưu kết quả vào state và cập nhật nó từ một Effect:

```js {expectedErrors: {'react-compiler': [7]}} {4-8}
function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');

  // 🔴 Tránh: state dư thừa và Effect không cần thiết
  const [visibleTodos, setVisibleTodos] = useState([]);
  useEffect(() => {
    setVisibleTodos(getFilteredTodos(todos, filter));
  }, [todos, filter]);

  // ...
}
```

Giống như ví dụ trước, điều này vừa không cần thiết vừa không hiệu quả. Đầu tiên, hãy xóa state và Effect:

```js {3-4}
function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  // ✅ Ổn nếu getFilteredTodos() không chậm.
  const visibleTodos = getFilteredTodos(todos, filter);
  // ...
}
```

Thường thì code này ổn! Nhưng có thể `getFilteredTodos()` chậm hoặc bạn có nhiều `todos`. Trong trường hợp đó, bạn không muốn tính toán lại `getFilteredTodos()` nếu một state variable không liên quan như `newTodo` đã thay đổi.

Bạn có thể cache (hay ["memoize"](https://en.wikipedia.org/wiki/Memoization)) một tính toán tốn kém bằng cách bọc nó trong một Hook [`useMemo`](/reference/react/useMemo):

<Note>

[React Compiler](/learn/react-compiler) có thể tự động memoize các tính toán tốn kém cho bạn, loại bỏ nhu cầu `useMemo` thủ công trong nhiều trường hợp.

</Note>

```js {5-8}
import { useMemo, useState } from 'react';

function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  const visibleTodos = useMemo(() => {
    // ✅ Không chạy lại trừ khi todos hoặc filter thay đổi
    return getFilteredTodos(todos, filter);
  }, [todos, filter]);
  // ...
}
```

Hoặc, viết dưới dạng một dòng:

```js {5-6}
import { useMemo, useState } from 'react';

function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  // ✅ Không chạy lại getFilteredTodos() trừ khi todos hoặc filter thay đổi
  const visibleTodos = useMemo(() => getFilteredTodos(todos, filter), [todos, filter]);
  // ...
}
```

**Điều này cho React biết rằng bạn không muốn hàm bên trong chạy lại trừ khi `todos` hoặc `filter` đã thay đổi.** React sẽ ghi nhớ giá trị trả về của `getFilteredTodos()` trong lần render đầu tiên. Trong các lần render tiếp theo, nó sẽ kiểm tra xem `todos` hoặc `filter` có khác không. Nếu chúng giống như lần trước, `useMemo` sẽ trả về kết quả cuối cùng đã lưu. Nhưng nếu chúng khác, React sẽ gọi lại hàm bên trong (và lưu kết quả của nó).

Hàm bạn bọc trong [`useMemo`](/reference/react/useMemo) chạy trong quá trình render, vì vậy điều này chỉ hoạt động cho [các tính toán thuần túy.](/learn/keeping-components-pure)

<DeepDive>

#### Làm thế nào để biết liệu một tính toán có tốn kém không? {/*how-to-tell-if-a-calculation-is-expensive*/}

Nói chung, trừ khi bạn đang tạo hoặc lặp qua hàng nghìn đối tượng, nó có lẽ không tốn kém. Nếu bạn muốn chắc chắn hơn, bạn có thể thêm log console để đo thời gian sử dụng trong một đoạn code:

```js {1,3}
console.time('filter array');
const visibleTodos = getFilteredTodos(todos, filter);
console.timeEnd('filter array');
```

Thực hiện tương tác bạn đang đo (ví dụ: gõ vào input). Sau đó bạn sẽ thấy các log như `filter array: 0.15ms` trong console. Nếu tổng thời gian được log thêm lên đến một lượng đáng kể (chẳng hạn, `1ms` trở lên), có thể hợp lý để memoize tính toán đó. Như một thí nghiệm, sau đó bạn có thể bọc tính toán trong `useMemo` để xác minh xem tổng thời gian được log có giảm cho tương tác đó hay không:

```js
console.time('filter array');
const visibleTodos = useMemo(() => {
  return getFilteredTodos(todos, filter); // Bỏ qua nếu todos và filter không thay đổi
}, [todos, filter]);
console.timeEnd('filter array');
```

`useMemo` sẽ không làm cho lần render *đầu tiên* nhanh hơn. Nó chỉ giúp bạn bỏ qua công việc không cần thiết khi cập nhật.

Hãy nhớ rằng máy của bạn có thể nhanh hơn máy của người dùng, vì vậy tốt hơn là kiểm tra hiệu suất với việc làm chậm nhân tạo. Ví dụ, Chrome cung cấp tùy chọn [CPU Throttling](https://developer.chrome.com/blog/new-in-devtools-61/#throttling) cho điều này.

Cũng lưu ý rằng việc đo hiệu suất trong môi trường development sẽ không cho bạn kết quả chính xác nhất. (Ví dụ, khi [Strict Mode](/reference/react/StrictMode) bật, bạn sẽ thấy mỗi component render hai lần thay vì một lần.) Để có thời gian chính xác nhất, hãy build ứng dụng của bạn cho production và kiểm tra nó trên thiết bị giống như người dùng có.

</DeepDive>

### Reset toàn bộ state khi một prop thay đổi {/*resetting-all-state-when-a-prop-changes*/}

Component `ProfilePage` này nhận một `userId` prop. Trang chứa một ô nhập bình luận, và bạn sử dụng một `comment` state variable để giữ giá trị của nó. Một ngày, bạn nhận thấy một vấn đề: khi bạn điều hướng từ profile này sang profile khác, `comment` state không được reset. Kết quả là, dễ dàng vô tình đăng bình luận trên profile của người dùng sai. Để khắc phục vấn đề, bạn muốn xóa `comment` state variable bất cứ khi nào `userId` thay đổi:

```js {expectedErrors: {'react-compiler': [6]}} {4-7}
export default function ProfilePage({ userId }) {
  const [comment, setComment] = useState('');

  // 🔴 Tránh: Reset state khi prop thay đổi trong một Effect
  useEffect(() => {
    setComment('');
  }, [userId]);
  // ...
}
```

Điều này không hiệu quả vì `ProfilePage` và các component con của nó sẽ đầu tiên render với giá trị cũ, sau đó render lại. Nó cũng phức tạp vì bạn sẽ cần làm điều này trong *mọi* component có state bên trong `ProfilePage`. Ví dụ, nếu UI bình luận được lồng vào, bạn cũng muốn xóa state bình luận lồng.

Thay vào đó, bạn có thể cho React biết rằng profile của mỗi người dùng về mặt khái niệm là một profile _khác_ bằng cách cung cấp cho nó một key rõ ràng. Tách component của bạn thành hai và truyền một thuộc tính `key` từ component bên ngoài sang component bên trong:

```js {5,11-12}
export default function ProfilePage({ userId }) {
  return (
    <Profile
      userId={userId}
      key={userId}
    />
  );
}

function Profile({ userId }) {
  // ✅ State này và bất kỳ state nào khác bên dưới sẽ tự động reset khi key thay đổi
  const [comment, setComment] = useState('');
  // ...
}
```

Thông thường, React bảo tồn state khi cùng một component được render ở cùng một vị trí. **Bằng cách truyền `userId` như là `key` cho component `Profile`, bạn đang yêu cầu React xử lý hai component `Profile` với `userId` khác nhau như hai component khác nhau không nên chia sẻ bất kỳ state nào.** Bất cứ khi nào key (mà bạn đã đặt là `userId`) thay đổi, React sẽ tạo lại DOM và [reset state](/learn/preserving-and-resetting-state#option-2-resetting-state-with-a-key) của component `Profile` và tất cả các component con của nó. Bây giờ trường `comment` sẽ tự động xóa khi điều hướng giữa các profile.

Lưu ý rằng trong ví dụ này, chỉ có component `ProfilePage` bên ngoài được xuất và hiển thị cho các file khác trong project. Các component render `ProfilePage` không cần truyền key cho nó: chúng truyền `userId` như một prop thông thường. Việc `ProfilePage` truyền nó như `key` cho component `Profile` bên trong là một chi tiết triển khai.

### Điều chỉnh một số state khi một prop thay đổi {/*adjusting-some-state-when-a-prop-changes*/}

Đôi khi, bạn có thể muốn reset hoặc điều chỉnh một phần state khi một prop thay đổi, nhưng không phải tất cả.

Component `List` này nhận một danh sách `items` như là một prop, và duy trì item được chọn trong `selection` state variable. Bạn muốn reset `selection` về `null` bất cứ khi nào prop `items` nhận được một mảng khác:

```js {expectedErrors: {'react-compiler': [7]}} {5-8}
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selection, setSelection] = useState(null);

  // 🔴 Tránh: Điều chỉnh state khi prop thay đổi trong một Effect
  useEffect(() => {
    setSelection(null);
  }, [items]);
  // ...
}
```

Điều này cũng không lý tưởng. Mỗi khi `items` thay đổi, `List` và các component con của nó sẽ đầu tiên render với giá trị `selection` cũ. Sau đó React sẽ cập nhật DOM và chạy các Effect. Cuối cùng, lệnh gọi `setSelection(null)` sẽ gây ra một lần render lại khác của `List` và các component con của nó, khởi động lại toàn bộ quá trình này.

Hãy bắt đầu bằng cách xóa Effect. Thay vào đó, hãy điều chỉnh state trực tiếp trong quá trình render:

```js {5-11}
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selection, setSelection] = useState(null);

  // Tốt hơn: Điều chỉnh state trong quá trình render
  const [prevItems, setPrevItems] = useState(items);
  if (items !== prevItems) {
    setPrevItems(items);
    setSelection(null);
  }
  // ...
}
```

[Lưu trữ thông tin từ các lần render trước](/reference/react/useState#storing-information-from-previous-renders) như thế này có thể khó hiểu, nhưng tốt hơn là cập nhật cùng state trong một Effect. Trong ví dụ trên, `setSelection` được gọi trực tiếp trong quá trình render. React sẽ render lại `List` *ngay lập tức* sau khi nó thoát ra với câu lệnh `return`. React chưa render các component con của `List` hoặc cập nhật DOM, vì vậy điều này cho phép các component con của `List` bỏ qua việc render giá trị `selection` cũ.

Khi bạn cập nhật một component trong quá trình render, React ném đi JSX đã trả về và ngay lập tức thử lại render. Để tránh các lần thử lại cascading rất chậm, React chỉ cho phép bạn cập nhật state của *cùng một* component trong quá trình render. Nếu bạn cập nhật state của component khác trong quá trình render, bạn sẽ thấy lỗi. Một điều kiện như `items !== prevItems` là cần thiết để tránh vòng lặp. Bạn có thể điều chỉnh state như thế này, nhưng bất kỳ tác dụng phụ nào khác (như thay đổi DOM hoặc đặt timeouts) nên ở trong event handler hoặc Effect để [giữ cho component thuần túy.](/learn/keeping-components-pure)

**Mặc dù pattern này hiệu quả hơn Effect, hầu hết các component cũng không cần nó.** Dù bạn làm thế nào, điều chỉnh state dựa trên props hoặc state khác làm cho luồng dữ liệu của bạn khó hiểu và debug hơn. Luôn kiểm tra xem bạn có thể [reset toàn bộ state với một key](#resetting-all-state-when-a-prop-changes) hay [tính toán mọi thứ trong quá trình render](#updating-state-based-on-props-or-state) không. Ví dụ, thay vì lưu trữ (và reset) *item* đã chọn, bạn có thể lưu trữ *ID item* đã chọn:

```js {3-5}
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selectedId, setSelectedId] = useState(null);
  // ✅ Tốt nhất: Tính toán mọi thứ trong quá trình render
  const selection = items.find(item => item.id === selectedId) ?? null;
  // ...
}
```

Bây giờ không cần "điều chỉnh" state nào cả. Nếu item với ID đã chọn có trong danh sách, nó vẫn được chọn. Nếu không, `selection` được tính toán trong quá trình render sẽ là `null` vì không tìm thấy item phù hợp. Hành vi này khác, nhưng có thể tốt hơn vì hầu hết các thay đổi đối với `items` sẽ bảo tồn lựa chọn.

### Chia sẻ logic giữa các event handler {/*sharing-logic-between-event-handlers*/}

Giả sử bạn có một trang sản phẩm với hai nút (Buy và Checkout) đều cho phép bạn mua sản phẩm đó. Bạn muốn hiển thị thông báo bất cứ khi nào người dùng đặt sản phẩm vào giỏ hàng. Việc gọi `showNotification()` trong handler click của cả hai nút có vẻ lặp lại, vì vậy bạn có thể bị cám dỗ đặt logic này trong một Effect:

```js {2-7}
function ProductPage({ product, addToCart }) {
  // 🔴 Tránh: Logic dành riêng cho sự kiện trong Effect
  useEffect(() => {
    if (product.isInCart) {
      showNotification(`Added ${product.name} to the shopping cart!`);
    }
  }, [product]);

  function handleBuyClick() {
    addToCart(product);
  }

  function handleCheckoutClick() {
    addToCart(product);
    navigateTo('/checkout');
  }
  // ...
}
```

Effect này không cần thiết. Nó cũng rất có thể gây ra bug. Ví dụ, giả sử ứng dụng của bạn "ghi nhớ" giỏ hàng giữa các lần tải lại trang. Nếu bạn thêm sản phẩm vào giỏ hàng một lần và làm mới trang, thông báo sẽ xuất hiện lại. Nó sẽ tiếp tục xuất hiện mỗi khi bạn làm mới trang sản phẩm đó. Đây là vì `product.isInCart` đã là `true` khi tải trang, vì vậy Effect trên sẽ gọi `showNotification()`.

**Khi bạn không chắc chắn liệu một số code nên ở trong Effect hay trong event handler, hãy tự hỏi *tại sao* code này cần chạy. Chỉ sử dụng Effect cho code nên chạy *vì* component được hiển thị cho người dùng.** Trong ví dụ này, thông báo nên xuất hiện vì người dùng *nhấn nút*, không phải vì trang được hiển thị! Xóa Effect và đặt logic chung vào một hàm được gọi từ cả hai event handler:

```js {2-6,9,13}
function ProductPage({ product, addToCart }) {
  // ✅ Tốt: Logic dành riêng cho sự kiện được gọi từ event handler
  function buyProduct() {
    addToCart(product);
    showNotification(`Added ${product.name} to the shopping cart!`);
  }

  function handleBuyClick() {
    buyProduct();
  }

  function handleCheckoutClick() {
    buyProduct();
    navigateTo('/checkout');
  }
  // ...
}
```

Điều này vừa loại bỏ Effect không cần thiết vừa sửa bug.

### Gửi POST request {/*sending-a-post-request*/}

Component `Form` này gửi hai loại POST request. Nó gửi một sự kiện analytics khi mount. Khi bạn điền vào form và nhấp nút Submit, nó sẽ gửi POST request đến endpoint `/api/register`:

```js {5-8,10-16}
function Form() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');

  // ✅ Tốt: Logic này nên chạy vì component được hiển thị
  useEffect(() => {
    post('/analytics/event', { eventName: 'visit_form' });
  }, []);

  // 🔴 Tránh: Logic dành riêng cho sự kiện trong Effect
  const [jsonToSubmit, setJsonToSubmit] = useState(null);
  useEffect(() => {
    if (jsonToSubmit !== null) {
      post('/api/register', jsonToSubmit);
    }
  }, [jsonToSubmit]);

  function handleSubmit(e) {
    e.preventDefault();
    setJsonToSubmit({ firstName, lastName });
  }
  // ...
}
```

Hãy áp dụng cùng tiêu chí như trong ví dụ trước.

POST request analytics nên ở trong Effect. Đây là vì _lý do_ để gửi sự kiện analytics là form đã được hiển thị. (Nó sẽ kích hoạt hai lần trong development, nhưng [xem ở đây](/learn/synchronizing-with-effects#sending-analytics) để biết cách xử lý điều đó.)

Tuy nhiên, POST request `/api/register` không được gây ra bởi form được _hiển thị_. Bạn chỉ muốn gửi request tại một thời điểm cụ thể: khi người dùng nhấn nút. Nó chỉ nên xảy ra _trong tương tác cụ thể đó_. Xóa Effect thứ hai và chuyển POST request đó vào event handler:

```js {12-13}
function Form() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');

  // ✅ Tốt: Logic này chạy vì component được hiển thị
  useEffect(() => {
    post('/analytics/event', { eventName: 'visit_form' });
  }, []);

  function handleSubmit(e) {
    e.preventDefault();
    // ✅ Tốt: Logic dành riêng cho sự kiện ở trong event handler
    post('/api/register', { firstName, lastName });
  }
  // ...
}
```

Khi bạn chọn đặt một số logic vào event handler hay Effect, câu hỏi chính bạn cần trả lời là _loại logic nào_ nó là từ góc độ người dùng. Nếu logic này được gây ra bởi một tương tác cụ thể, hãy giữ nó trong event handler. Nếu nó được gây ra bởi việc người dùng _nhìn thấy_ component trên màn hình, hãy giữ nó trong Effect.

### Chuỗi các tính toán {/*chains-of-computations*/}

Đôi khi bạn có thể bị cám dỗ kết nối các Effect mà mỗi Effect điều chỉnh một phần state dựa trên state khác:

```js {7-29}
function Game() {
  const [card, setCard] = useState(null);
  const [goldCardCount, setGoldCardCount] = useState(0);
  const [round, setRound] = useState(1);
  const [isGameOver, setIsGameOver] = useState(false);

  // 🔴 Tránh: Chuỗi các Effect điều chỉnh state chỉ để kích hoạt lẫn nhau
  useEffect(() => {
    if (card !== null && card.gold) {
      setGoldCardCount(c => c + 1);
    }
  }, [card]);

  useEffect(() => {
    if (goldCardCount > 3) {
      setRound(r => r + 1)
      setGoldCardCount(0);
    }
  }, [goldCardCount]);

  useEffect(() => {
    if (round > 5) {
      setIsGameOver(true);
    }
  }, [round]);

  useEffect(() => {
    alert('Good game!');
  }, [isGameOver]);

  function handlePlaceCard(nextCard) {
    if (isGameOver) {
      throw Error('Game already ended.');
    } else {
      setCard(nextCard);
    }
  }

  // ...
```

Có hai vấn đề với code này.

Vấn đề đầu tiên là nó rất không hiệu quả: component (và các component con của nó) phải render lại giữa mỗi lần gọi `set` trong chuỗi. Trong ví dụ trên, trong trường hợp xấu nhất (`setCard` → render → `setGoldCardCount` → render → `setRound` → render → `setIsGameOver` → render) có ba lần render không cần thiết của cây bên dưới.

Vấn đề thứ hai là ngay cả khi nó không chậm, khi code của bạn phát triển, bạn sẽ gặp các trường hợp "chuỗi" bạn đã viết không phù hợp với các yêu cầu mới. Hãy tưởng tượng bạn đang thêm một cách để duyệt qua lịch sử các nước đi trong game. Bạn sẽ làm điều đó bằng cách cập nhật mỗi state variable về một giá trị từ quá khứ. Tuy nhiên, việc đặt state `card` về một giá trị từ quá khứ sẽ kích hoạt chuỗi Effect một lần nữa và thay đổi dữ liệu bạn đang hiển thị. Code như vậy thường cứng nhắc và dễ vỡ.

Trong trường hợp này, tốt hơn là tính toán những gì bạn có thể trong quá trình render, và điều chỉnh state trong event handler:

```js {6-7,14-26}
function Game() {
  const [card, setCard] = useState(null);
  const [goldCardCount, setGoldCardCount] = useState(0);
  const [round, setRound] = useState(1);

  // ✅ Tính toán những gì bạn có thể trong quá trình render
  const isGameOver = round > 5;

  function handlePlaceCard(nextCard) {
    if (isGameOver) {
      throw Error('Game already ended.');
    }

    // ✅ Tính toán tất cả state tiếp theo trong event handler
    setCard(nextCard);
    if (nextCard.gold) {
      if (goldCardCount < 3) {
        setGoldCardCount(goldCardCount + 1);
      } else {
        setGoldCardCount(0);
        setRound(round + 1);
        if (round === 5) {
          alert('Good game!');
        }
      }
    }
  }

  // ...
```

Điều này hiệu quả hơn rất nhiều. Ngoài ra, nếu bạn triển khai cách xem lịch sử game, bây giờ bạn sẽ có thể đặt mỗi state variable về một nước đi từ quá khứ mà không kích hoạt chuỗi Effect điều chỉnh mỗi giá trị khác. Nếu bạn cần tái sử dụng logic giữa nhiều event handler, bạn có thể [trích xuất một hàm](#sharing-logic-between-event-handlers) và gọi nó từ các handler đó.

Hãy nhớ rằng bên trong event handler, [state hoạt động như một snapshot.](/learn/state-as-a-snapshot) Ví dụ, ngay cả sau khi bạn gọi `setRound(round + 1)`, biến `round` sẽ phản ánh giá trị tại thời điểm người dùng nhấp nút. Nếu bạn cần sử dụng giá trị tiếp theo cho các tính toán, hãy định nghĩa nó thủ công như `const nextRound = round + 1`.

Trong một số trường hợp, bạn *không thể* tính toán state tiếp theo trực tiếp trong event handler. Ví dụ, hãy tưởng tượng một form với nhiều dropdown mà các tùy chọn của dropdown tiếp theo phụ thuộc vào giá trị đã chọn của dropdown trước. Sau đó, một chuỗi Effect là phù hợp vì bạn đang đồng bộ hóa với mạng.

### Khởi tạo ứng dụng {/*initializing-the-application*/}

Một số logic chỉ nên chạy một lần khi ứng dụng tải.

Bạn có thể bị cám dỗ đặt nó trong một Effect trong component cấp cao nhất:

```js {2-6}
function App() {
  // 🔴 Tránh: Effect với logic chỉ nên chạy một lần
  useEffect(() => {
    loadDataFromLocalStorage();
    checkAuthToken();
  }, []);
  // ...
}
```

Tuy nhiên, bạn sẽ nhanh chóng phát hiện ra rằng nó [chạy hai lần trong development.](/learn/synchronizing-with-effects#how-to-handle-the-effect-firing-twice-in-development) Điều này có thể gây ra vấn đề--ví dụ, có thể nó làm mất hiệu lực auth token vì hàm không được thiết kế để được gọi hai lần. Nói chung, các component của bạn nên có khả năng chịu được việc remount. Điều này bao gồm component `App` cấp cao nhất của bạn.

Mặc dù nó có thể không bao giờ được remount trong thực tế trong production, việc tuân theo các ràng buộc tương tự trong tất cả các component giúp việc di chuyển và tái sử dụng code dễ dàng hơn. Nếu một số logic phải chạy *một lần mỗi lần tải ứng dụng* thay vì *một lần mỗi lần mount component*, hãy thêm một biến cấp cao nhất để theo dõi xem nó đã thực thi chưa:

```js {1,5-6,10}
let didInit = false;

function App() {
  useEffect(() => {
    if (!didInit) {
      didInit = true;
      // ✅ Chỉ chạy một lần mỗi lần tải ứng dụng
      loadDataFromLocalStorage();
      checkAuthToken();
    }
  }, []);
  // ...
}
```

Bạn cũng có thể chạy nó trong quá trình khởi tạo module và trước khi ứng dụng render:

```js {1,5}
if (typeof window !== 'undefined') { // Kiểm tra xem chúng ta có đang chạy trong trình duyệt không.
   // ✅ Chỉ chạy một lần mỗi lần tải ứng dụng
  checkAuthToken();
  loadDataFromLocalStorage();
}

function App() {
  // ...
}
```

Code ở cấp cao nhất chạy một lần khi component của bạn được import--ngay cả khi nó không được render. Để tránh làm chậm hoặc hành vi bất ngờ khi import các component tùy ý, đừng lạm dụng pattern này. Giữ logic khởi tạo toàn ứng dụng vào các module component gốc như `App.js` hoặc trong điểm vào của ứng dụng.

### Thông báo cho các component cha về thay đổi state {/*notifying-parent-components-about-state-changes*/}

Giả sử bạn đang viết một component `Toggle` với `isOn` state nội bộ có thể là `true` hoặc `false`. Có một vài cách khác nhau để toggle nó (bằng cách nhấp hoặc kéo). Bạn muốn thông báo cho component cha bất cứ khi nào state nội bộ của `Toggle` thay đổi, vì vậy bạn expose một `onChange` event và gọi nó từ một Effect:

```js {4-7}
function Toggle({ onChange }) {
  const [isOn, setIsOn] = useState(false);

  // 🔴 Tránh: Handler onChange chạy quá muộn
  useEffect(() => {
    onChange(isOn);
  }, [isOn, onChange])

  function handleClick() {
    setIsOn(!isOn);
  }

  function handleDragEnd(e) {
    if (isCloserToRightEdge(e)) {
      setIsOn(true);
    } else {
      setIsOn(false);
    }
  }

  // ...
}
```

Giống như trước, điều này không lý tưởng. `Toggle` cập nhật state của nó trước, và React cập nhật màn hình. Sau đó React chạy Effect, gọi hàm `onChange` được truyền từ component cha. Bây giờ component cha sẽ cập nhật state của chính nó, bắt đầu một lần render khác. Tốt hơn là làm mọi thứ trong một lần.

Xóa Effect và thay vào đó cập nhật state của *cả hai* component trong cùng event handler:

```js {5-7,11,16,18}
function Toggle({ onChange }) {
  const [isOn, setIsOn] = useState(false);

  function updateToggle(nextIsOn) {
    // ✅ Tốt: Thực hiện tất cả cập nhật trong sự kiện đã gây ra chúng
    setIsOn(nextIsOn);
    onChange(nextIsOn);
  }

  function handleClick() {
    updateToggle(!isOn);
  }

  function handleDragEnd(e) {
    if (isCloserToRightEdge(e)) {
      updateToggle(true);
    } else {
      updateToggle(false);
    }
  }

  // ...
}
```

Với cách tiếp cận này, cả component `Toggle` và component cha của nó cập nhật state của chúng trong sự kiện. React [gộp các cập nhật](/learn/queueing-a-series-of-state-updates) từ các component khác nhau lại với nhau, vì vậy sẽ chỉ có một lần render.

Bạn cũng có thể loại bỏ state hoàn toàn, và thay vào đó nhận `isOn` từ component cha:

```js {1,2}
// ✅ Cũng tốt: component được kiểm soát hoàn toàn bởi cha của nó
function Toggle({ isOn, onChange }) {
  function handleClick() {
    onChange(!isOn);
  }

  function handleDragEnd(e) {
    if (isCloserToRightEdge(e)) {
      onChange(true);
    } else {
      onChange(false);
    }
  }

  // ...
}
```

["Lifting state up"](/learn/sharing-state-between-components) cho phép component cha kiểm soát hoàn toàn `Toggle` bằng cách toggle state của chính component cha. Điều này có nghĩa là component cha sẽ phải chứa nhiều logic hơn, nhưng sẽ có ít state hơn để lo lắng. Bất cứ khi nào bạn cố gắng giữ cho hai state variable khác nhau được đồng bộ hóa, hãy thử lifting state up thay thế!

### Truyền dữ liệu cho component cha {/*passing-data-to-the-parent*/}

Component `Child` này fetch một số dữ liệu và sau đó truyền nó cho component `Parent` trong một Effect:

```js {9-14}
function Parent() {
  const [data, setData] = useState(null);
  // ...
  return <Child onFetched={setData} />;
}

function Child({ onFetched }) {
  const data = useSomeAPI();
  // 🔴 Tránh: Truyền dữ liệu cho cha trong Effect
  useEffect(() => {
    if (data) {
      onFetched(data);
    }
  }, [onFetched, data]);
  // ...
}
```

Trong React, dữ liệu chảy từ các component cha xuống các component con của chúng. Khi bạn thấy điều gì đó sai trên màn hình, bạn có thể truy tìm nguồn gốc thông tin bằng cách đi lên chuỗi component cho đến khi bạn tìm thấy component nào truyền prop sai hoặc có state sai. Khi các component con cập nhật state của component cha trong Effect, luồng dữ liệu trở nên rất khó theo dõi. Vì cả component con và cha đều cần dữ liệu giống nhau, hãy để component cha fetch dữ liệu đó và *truyền nó xuống* cho component con thay thế:

```js {4-5}
function Parent() {
  const data = useSomeAPI();
  // ...
  // ✅ Tốt: Truyền dữ liệu xuống component con
  return <Child data={data} />;
}

function Child({ data }) {
  // ...
}
```

Điều này đơn giản hơn và giữ cho luồng dữ liệu có thể dự đoán được: dữ liệu chảy xuống từ cha xuống con.

### Đăng ký theo dõi external store {/*subscribing-to-an-external-store*/}

Đôi khi, các component của bạn có thể cần đăng ký theo dõi một số dữ liệu bên ngoài state React. Dữ liệu này có thể từ thư viện bên thứ ba hoặc API trình duyệt tích hợp. Vì dữ liệu này có thể thay đổi mà không có sự biết của React, bạn cần đăng ký thủ công các component của bạn với nó. Điều này thường được thực hiện với Effect, ví dụ:

```js {2-17}
function useOnlineStatus() {
  // Không lý tưởng: Đăng ký store thủ công trong Effect
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function updateState() {
      setIsOnline(navigator.onLine);
    }

    updateState();

    window.addEventListener('online', updateState);
    window.addEventListener('offline', updateState);
    return () => {
      window.removeEventListener('online', updateState);
      window.removeEventListener('offline', updateState);
    };
  }, []);
  return isOnline;
}

function ChatIndicator() {
  const isOnline = useOnlineStatus();
  // ...
}
```

Ở đây, component đăng ký với external data store (trong trường hợp này, API `navigator.onLine` của trình duyệt). Vì API này không tồn tại trên server (vì vậy nó không thể được sử dụng cho HTML ban đầu), ban đầu state được đặt là `true`. Bất cứ khi nào giá trị của data store đó thay đổi trong trình duyệt, component cập nhật state của nó.

Mặc dù việc sử dụng Effect cho điều này là phổ biến, React có một Hook được xây dựng có mục đích để đăng ký với external store được ưa thích hơn. Xóa Effect và thay thế nó bằng một lần gọi [`useSyncExternalStore`](/reference/react/useSyncExternalStore):

```js {11-16}
function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}

function useOnlineStatus() {
  // ✅ Tốt: Đăng ký với external store bằng Hook tích hợp
  return useSyncExternalStore(
    subscribe, // React sẽ không đăng ký lại miễn là bạn truyền cùng một hàm
    () => navigator.onLine, // Cách lấy giá trị trên client
    () => true // Cách lấy giá trị trên server
  );
}

function ChatIndicator() {
  const isOnline = useOnlineStatus();
  // ...
}
```

Cách tiếp cận này ít bị lỗi hơn so với việc đồng bộ thủ công dữ liệu có thể thay đổi vào state React với Effect. Thông thường, bạn sẽ viết một custom Hook như `useOnlineStatus()` ở trên để bạn không cần lặp lại code này trong các component riêng lẻ. [Đọc thêm về việc đăng ký với external store từ các component React.](/reference/react/useSyncExternalStore)

### Fetching dữ liệu {/*fetching-data*/}

Nhiều ứng dụng sử dụng Effect để bắt đầu fetch dữ liệu. Khá phổ biến khi viết một Effect fetch dữ liệu như thế này:

```js {5-10}
function SearchResults({ query }) {
  const [results, setResults] = useState([]);
  const [page, setPage] = useState(1);

  useEffect(() => {
    // 🔴 Tránh: Fetching mà không có logic cleanup
    fetchResults(query, page).then(json => {
      setResults(json);
    });
  }, [query, page]);

  function handleNextPageClick() {
    setPage(page + 1);
  }
  // ...
}
```

Bạn *không* cần chuyển fetch này sang event handler.

Điều này có vẻ mâu thuẫn với các ví dụ trước mà bạn cần đặt logic vào event handler! Tuy nhiên, hãy xem xét rằng không phải *sự kiện gõ phím* là lý do chính để fetch. Các input tìm kiếm thường được điền sẵn từ URL, và người dùng có thể điều hướng Quay lại và Tiến mà không chạm vào input.

Không quan trọng `page` và `query` đến từ đâu. Trong khi component này hiển thị, bạn muốn giữ `results` [đồng bộ hóa](/learn/synchronizing-with-effects) với dữ liệu từ mạng cho `page` và `query` hiện tại. Đây là lý do tại sao nó là một Effect.

Tuy nhiên, code trên có một bug. Hãy tưởng tượng bạn gõ `"hello"` nhanh. Sau đó `query` sẽ thay đổi từ `"h"`, đến `"he"`, `"hel"`, `"hell"`, và `"hello"`. Điều này sẽ khởi động các fetch riêng biệt, nhưng không có gì đảm bảo thứ tự phản hồi sẽ đến. Ví dụ, phản hồi `"hell"` có thể đến *sau* phản hồi `"hello"`. Vì nó sẽ gọi `setResults()` cuối cùng, bạn sẽ hiển thị kết quả tìm kiếm sai. Đây được gọi là ["race condition"](https://en.wikipedia.org/wiki/Race_condition): hai request khác nhau đã "đua" với nhau và đến theo thứ tự khác với dự kiến.

**Để khắc phục race condition, bạn cần [thêm hàm cleanup](/learn/synchronizing-with-effects#fetching-data) để bỏ qua các phản hồi cũ:**

```js {5,7,9,11-13}
function SearchResults({ query }) {
  const [results, setResults] = useState([]);
  const [page, setPage] = useState(1);
  useEffect(() => {
    let ignore = false;
    fetchResults(query, page).then(json => {
      if (!ignore) {
        setResults(json);
      }
    });
    return () => {
      ignore = true;
    };
  }, [query, page]);

  function handleNextPageClick() {
    setPage(page + 1);
  }
  // ...
}
```

Điều này đảm bảo rằng khi Effect của bạn fetch dữ liệu, tất cả các phản hồi ngoại trừ cái được request cuối cùng sẽ bị bỏ qua.

Xử lý race condition không phải là khó khăn duy nhất khi triển khai data fetching. Bạn cũng có thể muốn nghĩ về việc cache các phản hồi (để người dùng có thể nhấp Quay lại và xem màn hình trước ngay lập tức), cách fetch dữ liệu trên server (để HTML render phía server ban đầu chứa nội dung đã fetch thay vì spinner), và cách tránh network waterfall (để component con có thể fetch dữ liệu mà không cần chờ mỗi component cha).

**Những vấn đề này áp dụng cho bất kỳ UI library nào, không chỉ React. Giải quyết chúng không đơn giản, đó là lý do tại sao các [framework](/learn/creating-a-react-app#full-stack-frameworks) hiện đại cung cấp các cơ chế fetch dữ liệu tích hợp hiệu quả hơn so với fetch dữ liệu trong Effect.**

Nếu bạn không sử dụng framework (và không muốn xây dựng của riêng bạn) nhưng muốn làm cho việc fetch dữ liệu từ Effect tiện dụng hơn, hãy xem xét trích xuất logic fetch của bạn vào một custom Hook như trong ví dụ này:

```js {4}
function SearchResults({ query }) {
  const [page, setPage] = useState(1);
  const params = new URLSearchParams({ query, page });
  const results = useData(`/api/search?${params}`);

  function handleNextPageClick() {
    setPage(page + 1);
  }
  // ...
}

function useData(url) {
  const [data, setData] = useState(null);
  useEffect(() => {
    let ignore = false;
    fetch(url)
      .then(response => response.json())
      .then(json => {
        if (!ignore) {
          setData(json);
        }
      });
    return () => {
      ignore = true;
    };
  }, [url]);
  return data;
}
```

Bạn cũng có thể muốn thêm một số logic để xử lý lỗi và theo dõi xem nội dung có đang tải không. Bạn có thể tự xây dựng một Hook như thế này hoặc sử dụng một trong nhiều giải pháp đã có sẵn trong hệ sinh thái React. **Mặc dù điều này một mình sẽ không hiệu quả bằng việc sử dụng cơ chế fetch dữ liệu tích hợp của framework, việc chuyển logic fetch dữ liệu vào một custom Hook sẽ giúp dễ dàng áp dụng chiến lược fetch dữ liệu hiệu quả sau này.**

Nói chung, bất cứ khi nào bạn phải viết Effect, hãy chú ý đến khi nào bạn có thể trích xuất một phần chức năng vào một custom Hook với API khai báo và có mục đích cụ thể hơn như `useData` ở trên. Càng ít lần gọi `useEffect` thuần túy trong các component của bạn, bạn sẽ càng dễ dàng maintain ứng dụng.

<Recap>

- Nếu bạn có thể tính toán gì đó trong quá trình render, bạn không cần Effect.
- Để cache các tính toán tốn kém, hãy thêm `useMemo` thay vì `useEffect`.
- Để reset state của toàn bộ cây component, hãy truyền một `key` khác cho nó.
- Để reset một phần state cụ thể để phản hồi một thay đổi prop, hãy đặt nó trong quá trình render.
- Code chạy vì component được *hiển thị* nên ở trong Effect, phần còn lại nên ở trong các event.
- Nếu bạn cần cập nhật state của nhiều component, tốt hơn là làm điều đó trong một sự kiện duy nhất.
- Bất cứ khi nào bạn cố gắng đồng bộ hóa state variable trong các component khác nhau, hãy xem xét lifting state up.
- Bạn có thể fetch dữ liệu với Effect, nhưng bạn cần triển khai cleanup để tránh race condition.

</Recap>

<Challenges>

#### Chuyển đổi dữ liệu mà không cần Effect {/*transform-data-without-effects*/}

`TodoList` bên dưới hiển thị một danh sách các todo. Khi checkbox "Show only active todos" được chọn, các todo đã hoàn thành không được hiển thị trong danh sách. Bất kể todo nào hiển thị, footer hiển thị số lượng todo chưa hoàn thành.

Đơn giản hóa component này bằng cách loại bỏ tất cả state và Effect không cần thiết.

<Sandpack>

```js {expectedErrors: {'react-compiler': [12, 16, 20]}}
import { useState, useEffect } from 'react';
import { initialTodos, createTodo } from './todos.js';

export default function TodoList() {
  const [todos, setTodos] = useState(initialTodos);
  const [showActive, setShowActive] = useState(false);
  const [activeTodos, setActiveTodos] = useState([]);
  const [visibleTodos, setVisibleTodos] = useState([]);
  const [footer, setFooter] = useState(null);

  useEffect(() => {
    setActiveTodos(todos.filter(todo => !todo.completed));
  }, [todos]);

  useEffect(() => {
    setVisibleTodos(showActive ? activeTodos : todos);
  }, [showActive, todos, activeTodos]);

  useEffect(() => {
    setFooter(
      <footer>
        {activeTodos.length} todos left
      </footer>
    );
  }, [activeTodos]);

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={showActive}
          onChange={e => setShowActive(e.target.checked)}
        />
        Show only active todos
      </label>
      <NewTodo onAdd={newTodo => setTodos([...todos, newTodo])} />
      <ul>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ? <s>{todo.text}</s> : todo.text}
          </li>
        ))}
      </ul>
      {footer}
    </>
  );
}

function NewTodo({ onAdd }) {
  const [text, setText] = useState('');

  function handleAddClick() {
    setText('');
    onAdd(createTodo(text));
  }

  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={handleAddClick}>
        Add
      </button>
    </>
  );
}
```

```js src/todos.js
let nextId = 0;

export function createTodo(text, completed = false) {
  return {
    id: nextId++,
    text,
    completed
  };
}

export const initialTodos = [
  createTodo('Get apples', true),
  createTodo('Get oranges', true),
  createTodo('Get carrots'),
];
```

```css
label { display: block; }
input { margin-top: 10px; }
```

</Sandpack>

<Hint>

Nếu bạn có thể tính toán gì đó trong quá trình render, bạn không cần state hoặc Effect để cập nhật nó.

</Hint>

<Solution>

Chỉ có hai phần state thiết yếu trong ví dụ này: danh sách `todos` và state variable `showActive` đại diện cho việc checkbox có được chọn hay không. Tất cả các state variable khác là [dư thừa](/learn/choosing-the-state-structure#avoid-redundant-state) và có thể được tính toán trong quá trình render thay thế. Điều này bao gồm `footer` mà bạn có thể chuyển trực tiếp vào JSX xung quanh.

Kết quả của bạn sẽ trông như thế này:

<Sandpack>

```js
import { useState } from 'react';
import { initialTodos, createTodo } from './todos.js';

export default function TodoList() {
  const [todos, setTodos] = useState(initialTodos);
  const [showActive, setShowActive] = useState(false);
  const activeTodos = todos.filter(todo => !todo.completed);
  const visibleTodos = showActive ? activeTodos : todos;

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={showActive}
          onChange={e => setShowActive(e.target.checked)}
        />
        Show only active todos
      </label>
      <NewTodo onAdd={newTodo => setTodos([...todos, newTodo])} />
      <ul>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ? <s>{todo.text}</s> : todo.text}
          </li>
        ))}
      </ul>
      <footer>
        {activeTodos.length} todos left
      </footer>
    </>
  );
}

function NewTodo({ onAdd }) {
  const [text, setText] = useState('');

  function handleAddClick() {
    setText('');
    onAdd(createTodo(text));
  }

  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={handleAddClick}>
        Add
      </button>
    </>
  );
}
```

```js src/todos.js
let nextId = 0;

export function createTodo(text, completed = false) {
  return {
    id: nextId++,
    text,
    completed
  };
}

export const initialTodos = [
  createTodo('Get apples', true),
  createTodo('Get oranges', true),
  createTodo('Get carrots'),
];
```

```css
label { display: block; }
input { margin-top: 10px; }
```

</Sandpack>

</Solution>

#### Cache một tính toán mà không cần Effect {/*cache-a-calculation-without-effects*/}

Trong ví dụ này, việc lọc các todo được trích xuất thành một hàm riêng biệt gọi là `getVisibleTodos()`. Hàm này chứa một lời gọi `console.log()` bên trong giúp bạn nhận thấy khi nào nó được gọi. Toggle "Show only active todos" và chú ý rằng nó khiến `getVisibleTodos()` chạy lại. Điều này là dự kiến vì các todo hiển thị thay đổi khi bạn toggle cái nào để hiển thị.

Nhiệm vụ của bạn là loại bỏ Effect tính toán lại danh sách `visibleTodos` trong component `TodoList`. Tuy nhiên, bạn cần đảm bảo rằng `getVisibleTodos()` *không* chạy lại (và do đó không in bất kỳ log nào) khi bạn gõ vào input.

<Hint>

Một giải pháp là thêm lời gọi `useMemo` để cache các todo hiển thị. Cũng có một giải pháp khác, ít rõ ràng hơn.

</Hint>

<Sandpack>

```js {expectedErrors: {'react-compiler': [11]}}
import { useState, useEffect } from 'react';
import { initialTodos, createTodo, getVisibleTodos } from './todos.js';

export default function TodoList() {
  const [todos, setTodos] = useState(initialTodos);
  const [showActive, setShowActive] = useState(false);
  const [text, setText] = useState('');
  const [visibleTodos, setVisibleTodos] = useState([]);

  useEffect(() => {
    setVisibleTodos(getVisibleTodos(todos, showActive));
  }, [todos, showActive]);

  function handleAddClick() {
    setText('');
    setTodos([...todos, createTodo(text)]);
  }

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={showActive}
          onChange={e => setShowActive(e.target.checked)}
        />
        Show only active todos
      </label>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={handleAddClick}>
        Add
      </button>
      <ul>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ? <s>{todo.text}</s> : todo.text}
          </li>
        ))}
      </ul>
    </>
  );
}
```

```js src/todos.js
let nextId = 0;
let calls = 0;

export function getVisibleTodos(todos, showActive) {
  console.log(`getVisibleTodos() was called ${++calls} times`);
  const activeTodos = todos.filter(todo => !todo.completed);
  const visibleTodos = showActive ? activeTodos : todos;
  return visibleTodos;
}

export function createTodo(text, completed = false) {
  return {
    id: nextId++,
    text,
    completed
  };
}

export const initialTodos = [
  createTodo('Get apples', true),
  createTodo('Get oranges', true),
  createTodo('Get carrots'),
];
```

```css
label { display: block; }
input { margin-top: 10px; }
```

</Sandpack>

<Solution>

Hãy xóa state variable và Effect, và thay vào đó thêm lời gọi `useMemo` để cache kết quả của lời gọi `getVisibleTodos()`:

<Sandpack>

```js
import { useState, useMemo } from 'react';
import { initialTodos, createTodo, getVisibleTodos } from './todos.js';

export default function TodoList() {
  const [todos, setTodos] = useState(initialTodos);
  const [showActive, setShowActive] = useState(false);
  const [text, setText] = useState('');
  const visibleTodos = useMemo(
    () => getVisibleTodos(todos, showActive),
    [todos, showActive]
  );

  function handleAddClick() {
    setText('');
    setTodos([...todos, createTodo(text)]);
  }

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={showActive}
          onChange={e => setShowActive(e.target.checked)}
        />
        Show only active todos
      </label>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={handleAddClick}>
        Add
      </button>
      <ul>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ? <s>{todo.text}</s> : todo.text}
          </li>
        ))}
      </ul>
    </>
  );
}
```

```js src/todos.js
let nextId = 0;
let calls = 0;

export function getVisibleTodos(todos, showActive) {
  console.log(`getVisibleTodos() was called ${++calls} times`);
  const activeTodos = todos.filter(todo => !todo.completed);
  const visibleTodos = showActive ? activeTodos : todos;
  return visibleTodos;
}

export function createTodo(text, completed = false) {
  return {
    id: nextId++,
    text,
    completed
  };
}

export const initialTodos = [
  createTodo('Get apples', true),
  createTodo('Get oranges', true),
  createTodo('Get carrots'),
];
```

```css
label { display: block; }
input { margin-top: 10px; }
```

</Sandpack>

Với thay đổi này, `getVisibleTodos()` sẽ chỉ được gọi nếu `todos` hoặc `showActive` thay đổi. Gõ vào input chỉ thay đổi state variable `text`, vì vậy nó không kích hoạt lời gọi đến `getVisibleTodos()`.

Cũng có một giải pháp khác không cần `useMemo`. Vì state variable `text` không thể ảnh hưởng đến danh sách todo, bạn có thể trích xuất form `NewTodo` thành một component riêng biệt, và chuyển state variable `text` vào bên trong nó:

<Sandpack>

```js
import { useState, useMemo } from 'react';
import { initialTodos, createTodo, getVisibleTodos } from './todos.js';

export default function TodoList() {
  const [todos, setTodos] = useState(initialTodos);
  const [showActive, setShowActive] = useState(false);
  const visibleTodos = getVisibleTodos(todos, showActive);

  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={showActive}
          onChange={e => setShowActive(e.target.checked)}
        />
        Show only active todos
      </label>
      <NewTodo onAdd={newTodo => setTodos([...todos, newTodo])} />
      <ul>
        {visibleTodos.map(todo => (
          <li key={todo.id}>
            {todo.completed ? <s>{todo.text}</s> : todo.text}
          </li>
        ))}
      </ul>
    </>
  );
}

function NewTodo({ onAdd }) {
  const [text, setText] = useState('');

  function handleAddClick() {
    setText('');
    onAdd(createTodo(text));
  }

  return (
    <>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={handleAddClick}>
        Add
      </button>
    </>
  );
}
```

```js src/todos.js
let nextId = 0;
let calls = 0;

export function getVisibleTodos(todos, showActive) {
  console.log(`getVisibleTodos() was called ${++calls} times`);
  const activeTodos = todos.filter(todo => !todo.completed);
  const visibleTodos = showActive ? activeTodos : todos;
  return visibleTodos;
}

export function createTodo(text, completed = false) {
  return {
    id: nextId++,
    text,
    completed
  };
}

export const initialTodos = [
  createTodo('Get apples', true),
  createTodo('Get oranges', true),
  createTodo('Get carrots'),
];
```

```css
label { display: block; }
input { margin-top: 10px; }
```

</Sandpack>

Cách tiếp cận này cũng thỏa mãn các yêu cầu. Khi bạn gõ vào input, chỉ có state variable `text` cập nhật. Vì state variable `text` nằm trong component con `NewTodo`, component cha `TodoList` sẽ không được render lại. Đây là lý do tại sao `getVisibleTodos()` không được gọi khi bạn gõ. (Nó vẫn sẽ được gọi nếu `TodoList` render lại vì lý do khác.)

</Solution>

#### Reset state mà không cần Effect {/*reset-state-without-effects*/}

Component `EditContact` này nhận một đối tượng contact có dạng `{ id, name, email }` như là prop `savedContact`. Hãy thử chỉnh sửa các trường input tên và email. Khi bạn nhấn Save, nút contact ở trên form cập nhật sang tên đã chỉnh sửa. Khi bạn nhấn Reset, bất kỳ thay đổi đang chờ nào trong form sẽ bị loại bỏ. Hãy thử UI này để cảm nhận.

Khi bạn chọn một contact với các nút ở trên, form reset để phản ánh chi tiết của contact đó. Điều này được thực hiện với một Effect bên trong `EditContact.js`. Hãy xóa Effect này. Tìm cách khác để reset form khi `savedContact.id` thay đổi.

<Sandpack>

```js src/App.js hidden
import { useState } from 'react';
import ContactList from './ContactList.js';
import EditContact from './EditContact.js';

export default function ContactManager() {
  const [
    contacts,
    setContacts
  ] = useState(initialContacts);
  const [
    selectedId,
    setSelectedId
  ] = useState(0);
  const selectedContact = contacts.find(c =>
    c.id === selectedId
  );

  function handleSave(updatedData) {
    const nextContacts = contacts.map(c => {
      if (c.id === updatedData.id) {
        return updatedData;
      } else {
        return c;
      }
    });
    setContacts(nextContacts);
  }

  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedId={selectedId}
        onSelect={id => setSelectedId(id)}
      />
      <hr />
      <EditContact
        savedContact={selectedContact}
        onSave={handleSave}
      />
    </div>
  )
}

const initialContacts = [
  { id: 0, name: 'Taylor', email: 'taylor@mail.com' },
  { id: 1, name: 'Alice', email: 'alice@mail.com' },
  { id: 2, name: 'Bob', email: 'bob@mail.com' }
];
```

```js src/ContactList.js hidden
export default function ContactList({
  contacts,
  selectedId,
  onSelect
}) {
  return (
    <section>
      <ul>
        {contacts.map(contact =>
          <li key={contact.id}>
            <button onClick={() => {
              onSelect(contact.id);
            }}>
              {contact.id === selectedId ?
                <b>{contact.name}</b> :
                contact.name
              }
            </button>
          </li>
        )}
      </ul>
    </section>
  );
}
```

```js {expectedErrors: {'react-compiler': [8, 9]}} src/EditContact.js active
import { useState, useEffect } from 'react';

export default function EditContact({ savedContact, onSave }) {
  const [name, setName] = useState(savedContact.name);
  const [email, setEmail] = useState(savedContact.email);

  useEffect(() => {
    setName(savedContact.name);
    setEmail(savedContact.email);
  }, [savedContact]);

  return (
    <section>
      <label>
        Name:{' '}
        <input
          type="text"
          value={name}
          onChange={e => setName(e.target.value)}
        />
      </label>
      <label>
        Email:{' '}
        <input
          type="email"
          value={email}
          onChange={e => setEmail(e.target.value)}
        />
      </label>
      <button onClick={() => {
        const updatedData = {
          id: savedContact.id,
          name: name,
          email: email
        };
        onSave(updatedData);
      }}>
        Save
      </button>
      <button onClick={() => {
        setName(savedContact.name);
        setEmail(savedContact.email);
      }}>
        Reset
      </button>
    </section>
  );
}
```

```css
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li { display: inline-block; }
li button {
  padding: 10px;
}
label {
  display: block;
  margin: 10px 0;
}
button {
  margin-right: 10px;
  margin-bottom: 10px;
}
```

</Sandpack>

<Hint>

Sẽ hay nếu có một cách để nói với React rằng khi `savedContact.id` khác, form `EditContact` về mặt khái niệm là _form của một contact khác_ và không nên bảo tồn state. Bạn có nhớ cách nào như vậy không?

</Hint>

<Solution>

Tách component `EditContact` thành hai. Chuyển tất cả state form vào component `EditForm` bên trong. Export component `EditContact` bên ngoài, và làm cho nó truyền `savedContact.id` như là `key` cho component `EditForm` bên trong. Kết quả là, component `EditForm` bên trong reset tất cả state form và tạo lại DOM bất cứ khi nào bạn chọn một contact khác.

<Sandpack>

```js src/App.js hidden
import { useState } from 'react';
import ContactList from './ContactList.js';
import EditContact from './EditContact.js';

export default function ContactManager() {
  const [
    contacts,
    setContacts
  ] = useState(initialContacts);
  const [
    selectedId,
    setSelectedId
  ] = useState(0);
  const selectedContact = contacts.find(c =>
    c.id === selectedId
  );

  function handleSave(updatedData) {
    const nextContacts = contacts.map(c => {
      if (c.id === updatedData.id) {
        return updatedData;
      } else {
        return c;
      }
    });
    setContacts(nextContacts);
  }

  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedId={selectedId}
        onSelect={id => setSelectedId(id)}
      />
      <hr />
      <EditContact
        savedContact={selectedContact}
        onSave={handleSave}
      />
    </div>
  )
}

const initialContacts = [
  { id: 0, name: 'Taylor', email: 'taylor@mail.com' },
  { id: 1, name: 'Alice', email: 'alice@mail.com' },
  { id: 2, name: 'Bob', email: 'bob@mail.com' }
];
```

```js src/ContactList.js hidden
export default function ContactList({
  contacts,
  selectedId,
  onSelect
}) {
  return (
    <section>
      <ul>
        {contacts.map(contact =>
          <li key={contact.id}>
            <button onClick={() => {
              onSelect(contact.id);
            }}>
              {contact.id === selectedId ?
                <b>{contact.name}</b> :
                contact.name
              }
            </button>
          </li>
        )}
      </ul>
    </section>
  );
}
```

```js src/EditContact.js active
import { useState } from 'react';

export default function EditContact(props) {
  return (
    <EditForm
      {...props}
      key={props.savedContact.id}
    />
  );
}

function EditForm({ savedContact, onSave }) {
  const [name, setName] = useState(savedContact.name);
  const [email, setEmail] = useState(savedContact.email);

  return (
    <section>
      <label>
        Name:{' '}
        <input
          type="text"
          value={name}
          onChange={e => setName(e.target.value)}
        />
      </label>
      <label>
        Email:{' '}
        <input
          type="email"
          value={email}
          onChange={e => setEmail(e.target.value)}
        />
      </label>
      <button onClick={() => {
        const updatedData = {
          id: savedContact.id,
          name: name,
          email: email
        };
        onSave(updatedData);
      }}>
        Save
      </button>
      <button onClick={() => {
        setName(savedContact.name);
        setEmail(savedContact.email);
      }}>
        Reset
      </button>
    </section>
  );
}
```

```css
ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li { display: inline-block; }
li button {
  padding: 10px;
}
label {
  display: block;
  margin: 10px 0;
}
button {
  margin-right: 10px;
  margin-bottom: 10px;
}
```

</Sandpack>

</Solution>

#### Gửi form mà không cần Effect {/*submit-a-form-without-effects*/}

Component `Form` này cho phép bạn gửi tin nhắn cho bạn bè. Khi bạn submit form, state variable `showForm` được đặt là `false`. Điều này kích hoạt một Effect gọi `sendMessage(message)`, gửi tin nhắn (bạn có thể thấy nó trong console). Sau khi tin nhắn được gửi, bạn thấy hộp thoại "Thank you" với nút "Open chat" cho phép bạn quay lại form.

Người dùng ứng dụng của bạn đang gửi quá nhiều tin nhắn. Để làm cho việc chat khó hơn một chút, bạn đã quyết định hiển thị hộp thoại "Thank you" *trước* thay vì form. Thay đổi state variable `showForm` để khởi tạo là `false` thay vì `true`. Ngay sau khi bạn thực hiện thay đổi đó, console sẽ hiển thị rằng một tin nhắn trống đã được gửi. Có gì đó trong logic này sai!

Nguyên nhân gốc rễ của vấn đề này là gì? Và bạn có thể sửa nó như thế nào?

<Hint>

Tin nhắn có nên được gửi _vì_ người dùng đã thấy hộp thoại "Thank you" không? Hay ngược lại?

</Hint>

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function Form() {
  const [showForm, setShowForm] = useState(true);
  const [message, setMessage] = useState('');

  useEffect(() => {
    if (!showForm) {
      sendMessage(message);
    }
  }, [showForm, message]);

  function handleSubmit(e) {
    e.preventDefault();
    setShowForm(false);
  }

  if (!showForm) {
    return (
      <>
        <h1>Thanks for using our services!</h1>
        <button onClick={() => {
          setMessage('');
          setShowForm(true);
        }}>
          Open chat
        </button>
      </>
    );
  }

  return (
    <form onSubmit={handleSubmit}>
      <textarea
        placeholder="Message"
        value={message}
        onChange={e => setMessage(e.target.value)}
      />
      <button type="submit" disabled={message === ''}>
        Send
      </button>
    </form>
  );
}

function sendMessage(message) {
  console.log('Sending message: ' + message);
}
```

```css
label, textarea { margin-bottom: 10px; display: block; }
```

</Sandpack>

<Solution>

State variable `showForm` xác định xem có hiển thị form hay hộp thoại "Thank you". Tuy nhiên, bạn không gửi tin nhắn vì hộp thoại "Thank you" đã được _hiển thị_. Bạn muốn gửi tin nhắn vì người dùng đã _submit form_. Xóa Effect gây nhầm lẫn và chuyển lời gọi `sendMessage` vào trong event handler `handleSubmit`:

<Sandpack>

```js
import { useState, useEffect } from 'react';

export default function Form() {
  const [showForm, setShowForm] = useState(true);
  const [message, setMessage] = useState('');

  function handleSubmit(e) {
    e.preventDefault();
    setShowForm(false);
    sendMessage(message);
  }

  if (!showForm) {
    return (
      <>
        <h1>Thanks for using our services!</h1>
        <button onClick={() => {
          setMessage('');
          setShowForm(true);
        }}>
          Open chat
        </button>
      </>
    );
  }

  return (
    <form onSubmit={handleSubmit}>
      <textarea
        placeholder="Message"
        value={message}
        onChange={e => setMessage(e.target.value)}
      />
      <button type="submit" disabled={message === ''}>
        Send
      </button>
    </form>
  );
}

function sendMessage(message) {
  console.log('Sending message: ' + message);
}
```

```css
label, textarea { margin-bottom: 10px; display: block; }
```

</Sandpack>

Chú ý cách trong phiên bản này, chỉ _việc submit form_ (đây là một sự kiện) mới khiến tin nhắn được gửi. Nó hoạt động tốt như nhau bất kể `showForm` được khởi tạo là `true` hay `false`. (Đặt nó thành `false` và chú ý không có thêm log console nào.)

</Solution>

</Challenges>
