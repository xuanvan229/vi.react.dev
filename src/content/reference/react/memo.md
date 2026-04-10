---
title: memo
---

<Intro>

`memo` cho phép bạn bỏ qua việc re-render một component khi props của nó không thay đổi.

```
const MemoizedComponent = memo(SomeComponent, arePropsEqual?)
```

</Intro>

<Note>

[React Compiler](/learn/react-compiler) tự động áp dụng tương đương với `memo` cho tất cả các component, giảm nhu cầu memo hóa thủ công. Bạn có thể sử dụng compiler để xử lý việc memo hóa component một cách tự động.

</Note>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `memo(Component, arePropsEqual?)` {/*memo*/}

Bọc một component trong `memo` để có được phiên bản *memo hóa* của component đó. Phiên bản memo hóa của component bạn thường sẽ không bị re-render khi component cha của nó re-render miễn là props của nó không thay đổi. Nhưng React vẫn có thể re-render nó: memo hóa là một tối ưu hóa hiệu suất, không phải là sự đảm bảo.

```js
import { memo } from 'react';

const SomeComponent = memo(function SomeComponent(props) {
  // ...
});
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `Component`: Component mà bạn muốn memo hóa. `memo` không chỉnh sửa component này, mà trả về một component mới, đã được memo hóa. Bất kỳ component React hợp lệ nào, bao gồm các function và component [`forwardRef`](/reference/react/forwardRef), đều được chấp nhận.

* **tùy chọn** `arePropsEqual`: Một hàm chấp nhận hai đối số: props trước đó của component và props mới của nó. Nó nên trả về `true` nếu props cũ và mới bằng nhau: nghĩa là, nếu component sẽ render cùng kết quả và hoạt động theo cùng cách với props mới như với props cũ. Ngược lại nó nên trả về `false`. Thường thì, bạn sẽ không chỉ định hàm này. Mặc định, React sẽ so sánh từng prop với [`Object.is`.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is)

#### Giá trị trả về {/*returns*/}

`memo` trả về một component React mới. Nó hoạt động giống như component được cung cấp cho `memo` ngoại trừ việc React sẽ không luôn luôn re-render nó khi component cha của nó đang được re-render trừ khi props của nó đã thay đổi.

---

## Cách sử dụng {/*usage*/}

### Bỏ qua re-render khi props không thay đổi {/*skipping-re-rendering-when-props-are-unchanged*/}

React thường re-render một component mới khi component cha của nó re-render. Với `memo`, bạn có thể tạo một component mà React sẽ không re-render khi component cha của nó re-render miễn là props mới của nó giống với props cũ. Component như vậy được gọi là *memo hóa*.

Để memo hóa một component, bọc nó trong `memo` và sử dụng giá trị mà nó trả về thay cho component gốc của bạn:

```js
const Greeting = memo(function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
});

export default Greeting;
```

Một component React luôn nên có [logic render thuần khiết.](/learn/keeping-components-pure) Điều này có nghĩa là nó phải trả về cùng kết quả nếu props, state và context của nó không thay đổi. Bằng cách sử dụng `memo`, bạn đang nói với React rằng component của bạn tuân thủ yêu cầu này, vì vậy React không cần re-render miễn là props của nó không thay đổi. Ngay cả với `memo`, component của bạn sẽ re-render nếu state của chính nó thay đổi hoặc nếu một context mà nó đang sử dụng thay đổi.

Trong ví dụ này, lưu ý rằng component `Greeting` re-render mỗi khi `name` thay đổi (vì đó là một trong các props của nó), nhưng không khi `address` thay đổi (vì nó không được truyền cho `Greeting` như một prop):

<Sandpack>

```js
import { memo, useState } from 'react';

export default function MyApp() {
  const [name, setName] = useState('');
  const [address, setAddress] = useState('');
  return (
    <>
      <label>
        Name{': '}
        <input value={name} onChange={e => setName(e.target.value)} />
      </label>
      <label>
        Address{': '}
        <input value={address} onChange={e => setAddress(e.target.value)} />
      </label>
      <Greeting name={name} />
    </>
  );
}

const Greeting = memo(function Greeting({ name }) {
  console.log("Greeting was rendered at", new Date().toLocaleTimeString());
  return <h3>Hello{name && ', '}{name}!</h3>;
});
```

```css
label {
  display: block;
  margin-bottom: 16px;
}
```

</Sandpack>

<Note>

**Bạn chỉ nên dựa vào `memo` như một tối ưu hóa hiệu suất.** Nếu code của bạn không hoạt động mà không có nó, hãy tìm vấn đề căn bản và sửa nó trước. Sau đó bạn có thể thêm `memo` để cải thiện hiệu suất.

</Note>

<DeepDive>

#### Bạn có nên thêm memo ở mọi nơi? {/*should-you-add-memo-everywhere*/}

Nếu ứng dụng của bạn giống như trang web này, và hầu hết các tương tác là thô (như thay thế một trang hoặc toàn bộ một phần), memo hóa thường là không cần thiết. Mặt khác, nếu ứng dụng của bạn giống như một trình soạn thảo vẽ, và hầu hết các tương tác là chi tiết (như di chuyển các hình dạng), thì bạn có thể thấy memo hóa rất hữu ích.

Tối ưu hóa với `memo` chỉ có giá trị khi component của bạn re-render thường xuyên với cùng props chính xác, và logic re-render của nó tốn kém. Nếu không có độ trễ đáng chú ý khi component của bạn re-render, `memo` là không cần thiết. Hãy nhớ rằng `memo` hoàn toàn vô ích nếu props được truyền cho component của bạn *luôn luôn khác nhau*, chẳng hạn như nếu bạn truyền một object hoặc một hàm thuần túy được định nghĩa trong quá trình render. Đây là lý do tại sao bạn thường cần [`useMemo`](/reference/react/useMemo#skipping-re-rendering-of-components) và [`useCallback`](/reference/react/useCallback#skipping-re-rendering-of-components) cùng với `memo`.

Không có lợi ích gì khi bọc một component trong `memo` trong các trường hợp khác. Cũng không có hại đáng kể khi làm điều đó, vì vậy một số đội nhóm chọn không suy nghĩ về các trường hợp riêng lẻ, và memo hóa càng nhiều càng tốt. Nhược điểm của cách tiếp cận này là code trở nên khó đọc hơn. Ngoài ra, không phải tất cả memo hóa đều hiệu quả: một giá trị duy nhất "luôn luôn mới" là đủ để phá vỡ memo hóa cho toàn bộ component.

**Trong thực tế, bạn có thể làm cho nhiều memo hóa trở nên không cần thiết bằng cách tuân theo một vài nguyên tắc:**

1. Khi một component bọc trực quan các component khác, hãy để nó [chấp nhận JSX như children.](/learn/passing-props-to-a-component#passing-jsx-as-children) Bằng cách này, khi component bao bọc cập nhật state của chính nó, React biết rằng children của nó không cần re-render.
1. Ưu tiên state cục bộ và không [nâng state lên](/learn/sharing-state-between-components) xa hơn mức cần thiết. Ví dụ, không giữ state tạm thời như form và việc một item đang được hover ở đỉnh cây của bạn hoặc trong thư viện state toàn cục.
1. Giữ cho [logic render của bạn thuần khiết.](/learn/keeping-components-pure) Nếu re-render một component gây ra vấn đề hoặc tạo ra hiện tượng trực quan đáng chú ý, đó là lỗi trong component của bạn! Sửa lỗi thay vì thêm memo hóa.
1. Tránh [các Effect không cần thiết cập nhật state.](/learn/you-might-not-need-an-effect) Hầu hết các vấn đề hiệu suất trong các ứng dụng React là do chuỗi cập nhật bắt nguồn từ các Effect khiến các component của bạn render liên tục.
1. Thử [loại bỏ các dependency không cần thiết khỏi các Effect của bạn.](/learn/removing-effect-dependencies) Ví dụ, thay vì memo hóa, thường đơn giản hơn là di chuyển một object hoặc một hàm vào bên trong Effect hoặc ra ngoài component.

Nếu một tương tác cụ thể vẫn cảm thấy chậm, [sử dụng React Developer Tools profiler](https://legacy.reactjs.org/blog/2018/09/10/introducing-the-react-profiler.html) để xem các component nào sẽ hưởng lợi nhiều nhất từ memo hóa, và thêm memo hóa khi cần. Các nguyên tắc này làm cho các component của bạn dễ debug và hiểu hơn, vì vậy tốt là tuân theo chúng trong mọi trường hợp. Về lâu dài, chúng tôi đang nghiên cứu [thực hiện memo hóa chi tiết tự động](https://www.youtube.com/watch?v=lGEMwh32soc) để giải quyết vấn đề này một lần cho tất cả.

</DeepDive>

---

### Cập nhật một component đã memo hóa sử dụng state {/*updating-a-memoized-component-using-state*/}

Ngay cả khi một component đã được memo hóa, nó vẫn sẽ re-render khi state của chính nó thay đổi. Memo hóa chỉ liên quan đến props được truyền cho component từ component cha của nó.

<Sandpack>

```js
import { memo, useState } from 'react';

export default function MyApp() {
  const [name, setName] = useState('');
  const [address, setAddress] = useState('');
  return (
    <>
      <label>
        Name{': '}
        <input value={name} onChange={e => setName(e.target.value)} />
      </label>
      <label>
        Address{': '}
        <input value={address} onChange={e => setAddress(e.target.value)} />
      </label>
      <Greeting name={name} />
    </>
  );
}

const Greeting = memo(function Greeting({ name }) {
  console.log('Greeting was rendered at', new Date().toLocaleTimeString());
  const [greeting, setGreeting] = useState('Hello');
  return (
    <>
      <h3>{greeting}{name && ', '}{name}!</h3>
      <GreetingSelector value={greeting} onChange={setGreeting} />
    </>
  );
});

function GreetingSelector({ value, onChange }) {
  return (
    <>
      <label>
        <input
          type="radio"
          checked={value === 'Hello'}
          onChange={e => onChange('Hello')}
        />
        Regular greeting
      </label>
      <label>
        <input
          type="radio"
          checked={value === 'Hello and welcome'}
          onChange={e => onChange('Hello and welcome')}
        />
        Enthusiastic greeting
      </label>
    </>
  );
}
```

```css
label {
  display: block;
  margin-bottom: 16px;
}
```

</Sandpack>

Nếu bạn đặt một biến state thành giá trị hiện tại của nó, React sẽ bỏ qua việc re-render component của bạn ngay cả khi không có `memo`. Bạn vẫn có thể thấy hàm component của bạn được gọi thêm một lần, nhưng kết quả sẽ bị bỏ qua.

---

### Cập nhật một component đã memo hóa sử dụng context {/*updating-a-memoized-component-using-a-context*/}

Ngay cả khi một component đã được memo hóa, nó vẫn sẽ re-render khi một context mà nó đang sử dụng thay đổi. Memo hóa chỉ liên quan đến props được truyền cho component từ component cha của nó.

<Sandpack>

```js
import { createContext, memo, useContext, useState } from 'react';

const ThemeContext = createContext(null);

export default function MyApp() {
  const [theme, setTheme] = useState('dark');

  function handleClick() {
    setTheme(theme === 'dark' ? 'light' : 'dark');
  }

  return (
    <ThemeContext value={theme}>
      <button onClick={handleClick}>
        Switch theme
      </button>
      <Greeting name="Taylor" />
    </ThemeContext>
  );
}

const Greeting = memo(function Greeting({ name }) {
  console.log("Greeting was rendered at", new Date().toLocaleTimeString());
  const theme = useContext(ThemeContext);
  return (
    <h3 className={theme}>Hello, {name}!</h3>
  );
});
```

```css
label {
  display: block;
  margin-bottom: 16px;
}

.light {
  color: black;
  background-color: white;
}

.dark {
  color: white;
  background-color: black;
}
```

</Sandpack>

Để component của bạn chỉ re-render khi một _phần_ của một context nào đó thay đổi, hãy tách component của bạn thành hai. Đọc những gì bạn cần từ context trong component bên ngoài, và truyền nó xuống cho component con đã memo hóa như một prop.

---

### Giảm thiểu thay đổi props {/*minimizing-props-changes*/}

Khi bạn sử dụng `memo`, component của bạn re-render mỗi khi bất kỳ prop nào không *bằng nông* với giá trị trước đó của nó. Điều này có nghĩa là React so sánh mỗi prop trong component của bạn với giá trị trước đó của nó sử dụng phép so sánh [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is). Lưu ý rằng `Object.is(3, 3)` là `true`, nhưng `Object.is({}, {})` là `false`.


Để tận dụng tối đa `memo`, hãy giảm thiểu số lần props thay đổi. Ví dụ, nếu prop là một object, hãy ngăn component cha tạo lại object đó mỗi lần bằng cách sử dụng [`useMemo`:](/reference/react/useMemo)

```js {5-8}
function Page() {
  const [name, setName] = useState('Taylor');
  const [age, setAge] = useState(42);

  const person = useMemo(
    () => ({ name, age }),
    [name, age]
  );

  return <Profile person={person} />;
}

const Profile = memo(function Profile({ person }) {
  // ...
});
```

Một cách tốt hơn để giảm thiểu thay đổi props là đảm bảo component chấp nhận thông tin tối thiểu cần thiết trong props của nó. Ví dụ, nó có thể chấp nhận các giá trị riêng lẻ thay vì một object nguyên vẹn:

```js {4,7}
function Page() {
  const [name, setName] = useState('Taylor');
  const [age, setAge] = useState(42);
  return <Profile name={name} age={age} />;
}

const Profile = memo(function Profile({ name, age }) {
  // ...
});
```

Ngay cả các giá trị riêng lẻ đôi khi cũng có thể được chiếu thành những giá trị ít thay đổi hơn. Ví dụ, ở đây một component chấp nhận một boolean chỉ ra sự hiện diện của một giá trị thay vì chính giá trị đó:

```js {3}
function GroupsLanding({ person }) {
  const hasGroups = person.groups !== null;
  return <CallToAction hasGroups={hasGroups} />;
}

const CallToAction = memo(function CallToAction({ hasGroups }) {
  // ...
});
```

Khi bạn cần truyền một hàm cho component đã memo hóa, hãy khai báo nó bên ngoài component của bạn để nó không bao giờ thay đổi, hoặc [`useCallback`](/reference/react/useCallback#skipping-re-rendering-of-components) để cache định nghĩa của nó giữa các lần re-render.

---

### Chỉ định hàm so sánh tùy chỉnh {/*specifying-a-custom-comparison-function*/}

Trong các trường hợp hiếm, có thể không khả thi để giảm thiểu thay đổi props của một component đã memo hóa. Trong trường hợp đó, bạn có thể cung cấp một hàm so sánh tùy chỉnh, mà React sẽ sử dụng để so sánh props cũ và mới thay vì sử dụng bằng nông. Hàm này được truyền như đối số thứ hai cho `memo`. Nó chỉ nên trả về `true` nếu props mới sẽ cho cùng kết quả như props cũ; ngược lại nó nên trả về `false`.

```js {3}
const Chart = memo(function Chart({ dataPoints }) {
  // ...
}, arePropsEqual);

function arePropsEqual(oldProps, newProps) {
  return (
    oldProps.dataPoints.length === newProps.dataPoints.length &&
    oldProps.dataPoints.every((oldPoint, index) => {
      const newPoint = newProps.dataPoints[index];
      return oldPoint.x === newPoint.x && oldPoint.y === newPoint.y;
    })
  );
}
```

Nếu bạn làm điều này, hãy sử dụng bảng Performance trong developer tools của trình duyệt để đảm bảo rằng hàm so sánh của bạn thực sự nhanh hơn việc re-render component. Bạn có thể sẽ ngạc nhiên.

Khi bạn thực hiện đo hiệu suất, hãy đảm bảo React đang chạy trong chế độ production.

<Pitfall>

Nếu bạn cung cấp một triển khai `arePropsEqual` tùy chỉnh, **bạn phải so sánh mọi prop, bao gồm cả các hàm.** Các hàm thường [đóng gói (close over)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures) props và state của các component cha. Nếu bạn trả về `true` khi `oldProps.onClick !== newProps.onClick`, component của bạn sẽ tiếp tục "nhìn thấy" props và state từ lần render trước bên trong handler `onClick` của nó, dẫn đến các lỗi rất khó hiểu.

Tránh thực hiện kiểm tra bằng sâu bên trong `arePropsEqual` trừ khi bạn chắc chắn 100% rằng cấu trúc dữ liệu bạn đang làm việc có độ sâu giới hạn đã biết. **Kiểm tra bằng sâu có thể trở nên cực kỳ chậm** và có thể đóng băng ứng dụng của bạn trong nhiều giây nếu ai đó thay đổi cấu trúc dữ liệu sau này.

</Pitfall>

---

### Tôi còn cần React.memo nếu tôi sử dụng React Compiler không? {/*react-compiler-memo*/}

Khi bạn bật [React Compiler](/learn/react-compiler), bạn thường không cần `React.memo` nữa. Compiler tự động tối ưu hóa việc re-render component cho bạn.

Đây là cách nó hoạt động:

**Không có React Compiler**, bạn cần `React.memo` để ngăn các re-render không cần thiết:

```js
// Component cha re-render mỗi giây
function Parent() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds(s => s + 1);
    }, 1000);
    return () => clearInterval(interval);
  }, []);

  return (
    <>
      <h1>Seconds: {seconds}</h1>
      <ExpensiveChild name="John" />
    </>
  );
}

// Không có memo, component này re-render mỗi giây mặc dù props không thay đổi
const ExpensiveChild = memo(function ExpensiveChild({ name }) {
  console.log('ExpensiveChild rendered');
  return <div>Hello, {name}!</div>;
});
```

**Với React Compiler được bật**, cùng tối ưu hóa diễn ra tự động:

```js
// Không cần memo - compiler tự động ngăn re-render
function ExpensiveChild({ name }) {
  console.log('ExpensiveChild rendered');
  return <div>Hello, {name}!</div>;
}
```

Đây là phần chính của những gì React Compiler tạo ra:

```js {6-12}
function Parent() {
  const $ = _c(7);
  const [seconds, setSeconds] = useState(0);
  // ... code khác ...

  let t3;
  if ($[4] === Symbol.for("react.memo_cache_sentinel")) {
    t3 = <ExpensiveChild name="John" />;
    $[4] = t3;
  } else {
    t3 = $[4];
  }
  // ... câu lệnh return ...
}
```

Lưu ý các dòng được đánh dấu: Compiler bọc `<ExpensiveChild name="John" />` trong một kiểm tra cache. Vì prop `name` luôn là `"John"`, JSX này được tạo một lần và tái sử dụng trong mỗi lần re-render của component cha. Đây chính xác là những gì `React.memo` làm - nó ngăn component con re-render khi props của nó không thay đổi.

React Compiler tự động:
1. Theo dõi rằng prop `name` được truyền cho `ExpensiveChild` không thay đổi
2. Tái sử dụng JSX đã tạo trước đó cho `<ExpensiveChild name="John" />`
3. Bỏ qua hoàn toàn việc re-render `ExpensiveChild`

Điều này có nghĩa là **bạn có thể an toàn loại bỏ `React.memo` khỏi các component của bạn khi sử dụng React Compiler**. Compiler cung cấp cùng tối ưu hóa một cách tự động, làm cho code của bạn sạch hơn và dễ bảo trì hơn.

<Note>

Tối ưu hóa của compiler thực sự toàn diện hơn `React.memo`. Nó cũng memo hóa các giá trị trung gian và các phép tính tốn kém bên trong các component của bạn, tương tự như việc kết hợp `React.memo` với `useMemo` trong toàn bộ cây component của bạn.

</Note>

---

## Xử lý sự cố {/*troubleshooting*/}
### Component của tôi re-render khi một prop là object, array, hoặc function {/*my-component-rerenders-when-a-prop-is-an-object-or-array*/}

React so sánh props cũ và mới bằng cách bằng nông: nghĩa là, nó xem xét mỗi prop mới có tham chiếu bằng với prop cũ hay không. Nếu bạn tạo một object hoặc array mới mỗi khi component cha re-render, ngay cả khi các phần tử riêng lẻ giống nhau, React vẫn sẽ coi nó là đã thay đổi. Tương tự, nếu bạn tạo một hàm mới khi render component cha, React sẽ coi nó là đã thay đổi ngay cả khi hàm có cùng định nghĩa. Để tránh điều này, [đơn giản hóa props hoặc memo hóa props trong component cha](#minimizing-props-changes).
