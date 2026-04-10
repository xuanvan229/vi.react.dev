---
title: Giữ cho các Component thuần túy
---

<Intro>

Một số hàm JavaScript là *thuần túy.* Hàm thuần túy chỉ thực hiện tính toán và không làm gì khác. Bằng cách chỉ viết các component của bạn như những hàm thuần túy, bạn có thể tránh được toàn bộ một lớp bug khó hiểu và hành vi không thể đoán trước khi codebase của bạn phát triển. Tuy nhiên, để nhận được những lợi ích này, có một số quy tắc bạn phải tuân theo.

</Intro>

<YouWillLearn>

* Tính thuần túy là gì và nó giúp bạn tránh bug như thế nào
* Cách giữ cho các component thuần túy bằng cách giữ các thay đổi ra khỏi giai đoạn render
* Cách sử dụng Strict Mode để tìm lỗi trong các component của bạn

</YouWillLearn>

## Tính thuần túy: Component như công thức {/*purity-components-as-formulas*/}

Trong khoa học máy tính (và đặc biệt là thế giới lập trình hàm), [hàm thuần túy](https://wikipedia.org/wiki/Pure_function) là một hàm với các đặc điểm sau:

* **Nó lo việc của mình.** Nó không thay đổi bất kỳ object hoặc biến nào đã tồn tại trước khi nó được gọi.
* **Cùng đầu vào, cùng đầu ra.** Với cùng đầu vào, hàm thuần túy luôn trả về cùng kết quả.

Bạn có thể đã quen với một ví dụ về hàm thuần túy: công thức toán học.

Xét công thức toán học này: <Math><MathI>y</MathI> = 2<MathI>x</MathI></Math>.

Nếu <Math><MathI>x</MathI> = 2</Math> thì <Math><MathI>y</MathI> = 4</Math>. Luôn luôn.

Nếu <Math><MathI>x</MathI> = 3</Math> thì <Math><MathI>y</MathI> = 6</Math>. Luôn luôn.

Nếu <Math><MathI>x</MathI> = 3</Math>, <MathI>y</MathI> sẽ không đôi khi là <Math>9</Math> hoặc <Math>–1</Math> hoặc <Math>2.5</Math> tùy thuộc vào thời gian trong ngày hay trạng thái của thị trường chứng khoán.

Nếu <Math><MathI>y</MathI> = 2<MathI>x</MathI></Math> và <Math><MathI>x</MathI> = 3</Math>, <MathI>y</MathI> sẽ _luôn_ là <Math>6</Math>.

Nếu chúng ta biến điều này thành một hàm JavaScript, nó sẽ trông như thế này:

```js
function double(number) {
  return 2 * number;
}
```

Trong ví dụ trên, `double` là một **hàm thuần túy.** Nếu bạn truyền cho nó `3`, nó sẽ trả về `6`. Luôn luôn.

React được thiết kế xung quanh khái niệm này. **React giả định rằng mọi component bạn viết là một hàm thuần túy.** Điều này có nghĩa là các component React bạn viết phải luôn trả về cùng JSX với cùng đầu vào:

<Sandpack>

```js src/App.js
function Recipe({ drinkers }) {
  return (
    <ol>
      <li>Boil {drinkers} cups of water.</li>
      <li>Add {drinkers} spoons of tea and {0.5 * drinkers} spoons of spice.</li>
      <li>Add {0.5 * drinkers} cups of milk to boil and sugar to taste.</li>
    </ol>
  );
}

export default function App() {
  return (
    <section>
      <h1>Spiced Chai Recipe</h1>
      <h2>For two</h2>
      <Recipe drinkers={2} />
      <h2>For a gathering</h2>
      <Recipe drinkers={4} />
    </section>
  );
}
```

</Sandpack>

Khi bạn truyền `drinkers={2}` cho `Recipe`, nó sẽ trả về JSX chứa `2 cups of water`. Luôn luôn.

Nếu bạn truyền `drinkers={4}`, nó sẽ trả về JSX chứa `4 cups of water`. Luôn luôn.

Giống như một công thức toán học.

Bạn có thể nghĩ về các component của mình như công thức nấu ăn: nếu bạn tuân theo chúng và không đưa nguyên liệu mới vào trong quá trình nấu, bạn sẽ nhận được cùng một món mỗi lần. "Món ăn" đó là JSX mà component cung cấp cho React để [render.](/learn/render-and-commit)

<Illustration src="/images/docs/illustrations/i_puritea-recipe.png" alt="A tea recipe for x people: take x cups of water, add x spoons of tea and 0.5x spoons of spices, and 0.5x cups of milk" />

## Side Effect: hậu quả (không) có chủ ý {/*side-effects-unintended-consequences*/}

Quá trình render của React phải luôn luôn thuần túy. Các component chỉ nên *trả về* JSX của chúng, và không *thay đổi* bất kỳ object hoặc biến nào đã tồn tại trước khi render — điều đó sẽ làm chúng trở nên không thuần túy!

Đây là một component vi phạm quy tắc này:

<Sandpack>

```js {expectedErrors: {'react-compiler': [5]}}
let guest = 0;

function Cup() {
  // Bad: changing a preexisting variable!
  guest = guest + 1;
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaSet() {
  return (
    <>
      <Cup />
      <Cup />
      <Cup />
    </>
  );
}
```

</Sandpack>

Component này đang đọc và ghi một biến `guest` được khai báo bên ngoài nó. Điều này có nghĩa là **gọi component này nhiều lần sẽ tạo ra các JSX khác nhau!** Và hơn thế nữa, nếu _các_ component _khác_ đọc `guest`, chúng cũng sẽ tạo ra JSX khác nhau, tùy thuộc vào thời điểm chúng được render! Đó không phải là có thể đoán trước được.

Quay lại công thức <Math><MathI>y</MathI> = 2<MathI>x</MathI></Math> của chúng ta, bây giờ ngay cả khi <Math><MathI>x</MathI> = 2</Math>, chúng ta không thể tin rằng <Math><MathI>y</MathI> = 4</Math>. Các bài test của chúng ta có thể thất bại, người dùng sẽ bối rối, máy bay sẽ rơi khỏi bầu trời — bạn có thể thấy điều này sẽ dẫn đến những bug khó hiểu như thế nào!

Bạn có thể sửa component này bằng cách [truyền `guest` như một prop thay thế](/learn/passing-props-to-a-component):

<Sandpack>

```js
function Cup({ guest }) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaSet() {
  return (
    <>
      <Cup guest={1} />
      <Cup guest={2} />
      <Cup guest={3} />
    </>
  );
}
```

</Sandpack>

Bây giờ component của bạn là thuần túy, vì JSX nó trả về chỉ phụ thuộc vào prop `guest`.

Nói chung, bạn không nên kỳ vọng các component của mình được render theo bất kỳ thứ tự cụ thể nào. Không quan trọng nếu bạn gọi <Math><MathI>y</MathI> = 2<MathI>x</MathI></Math> trước hay sau <Math><MathI>y</MathI> = 5<MathI>x</MathI></Math>: cả hai công thức sẽ giải quyết độc lập với nhau. Theo cách tương tự, mỗi component chỉ nên "tự nghĩ cho mình", và không cố gắng phối hợp hoặc phụ thuộc vào người khác trong quá trình render. Render giống như một bài kiểm tra ở trường: mỗi component nên tự tính toán JSX!

<DeepDive>

#### Phát hiện tính toán không thuần túy với StrictMode {/*detecting-impure-calculations-with-strict-mode*/}

Mặc dù bạn có thể chưa sử dụng hết tất cả, trong React có ba loại đầu vào bạn có thể đọc trong khi render: [props](/learn/passing-props-to-a-component), [state](/learn/state-a-components-memory), và [context.](/learn/passing-data-deeply-with-context) Bạn nên luôn coi các đầu vào này là chỉ đọc.

Khi bạn muốn *thay đổi* điều gì đó để phản hồi đầu vào của người dùng, bạn nên [đặt state](/learn/state-a-components-memory) thay vì ghi vào biến. Bạn không bao giờ nên thay đổi các biến hoặc object đã tồn tại trong khi component của bạn đang render.

React cung cấp "Strict Mode" trong đó nó gọi hàm của mỗi component hai lần trong quá trình phát triển. **Bằng cách gọi các hàm component hai lần, Strict Mode giúp tìm các component vi phạm các quy tắc này.**

Lưu ý cách ví dụ gốc hiển thị "Guest #2", "Guest #4", và "Guest #6" thay vì "Guest #1", "Guest #2", và "Guest #3". Hàm gốc không thuần túy, vì vậy gọi nó hai lần làm hỏng nó. Nhưng phiên bản thuần túy đã sửa vẫn hoạt động ngay cả khi hàm được gọi hai lần mỗi lần. **Hàm thuần túy chỉ tính toán, vì vậy gọi chúng hai lần sẽ không thay đổi gì** -- giống như gọi `double(2)` hai lần không thay đổi những gì được trả về, và giải <Math><MathI>y</MathI> = 2<MathI>x</MathI></Math> hai lần không thay đổi <MathI>y</MathI> là gì. Cùng đầu vào, cùng đầu ra. Luôn luôn.

Strict Mode không có hiệu lực trong môi trường production, vì vậy nó sẽ không làm chậm ứng dụng cho người dùng của bạn. Để chọn Strict Mode, bạn có thể bọc component gốc vào `<React.StrictMode>`. Một số framework làm điều này theo mặc định.

</DeepDive>

### Local mutation: bí mật nhỏ của component {/*local-mutation-your-components-little-secret*/}

Trong ví dụ trên, vấn đề là component đã thay đổi một biến *đã tồn tại trước* trong khi render. Điều này thường được gọi là **"mutation"** để nghe có vẻ đáng sợ hơn một chút. Hàm thuần túy không mutate các biến bên ngoài phạm vi của hàm hoặc các object được tạo ra trước lời gọi — điều đó làm chúng không thuần túy!

Tuy nhiên, **hoàn toàn ổn khi thay đổi các biến và object mà bạn vừa tạo trong khi render.** Trong ví dụ này, bạn tạo một mảng `[]`, gán nó cho biến `cups`, và sau đó `push` một tá cup vào nó:

<Sandpack>

```js
function Cup({ guest }) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaGathering() {
  const cups = [];
  for (let i = 1; i <= 12; i++) {
    cups.push(<Cup key={i} guest={i} />);
  }
  return cups;
}
```

</Sandpack>

Nếu biến `cups` hoặc mảng `[]` được tạo bên ngoài hàm `TeaGathering`, đây sẽ là một vấn đề lớn! Bạn sẽ thay đổi một object *đã tồn tại trước* bằng cách push các mục vào mảng đó.

Tuy nhiên, điều đó ổn vì bạn đã tạo chúng *trong cùng một lần render*, bên trong `TeaGathering`. Không có code bên ngoài `TeaGathering` nào sẽ biết rằng điều này đã xảy ra. Điều này được gọi là **"local mutation"** — nó như bí mật nhỏ của component.

## Nơi bạn _có thể_ gây ra side effect {/*where-you-_can_-cause-side-effects*/}

Trong khi lập trình hàm dựa nhiều vào tính thuần túy, tại một điểm nào đó, ở đâu đó, _điều gì đó_ phải thay đổi. Đó là điểm của lập trình! Những thay đổi này — cập nhật màn hình, bắt đầu animation, thay đổi dữ liệu — được gọi là **side effect.** Chúng là những thứ xảy ra _"ở bên cạnh"_, không phải trong khi render.

Trong React, **side effect thường thuộc về bên trong [event handler.](/learn/responding-to-events)** Event handler là các hàm mà React chạy khi bạn thực hiện một hành động — ví dụ, khi bạn nhấp vào một button. Mặc dù event handler được định nghĩa *bên trong* component của bạn, chúng không chạy *trong khi* render! **Vì vậy event handler không cần phải thuần túy.**

Nếu bạn đã cạn kiệt tất cả các tùy chọn khác và không thể tìm thấy event handler phù hợp cho side effect của mình, bạn vẫn có thể gắn nó vào JSX được trả về của mình với lời gọi [`useEffect`](/reference/react/useEffect) trong component của bạn. Điều này nói với React thực thi nó sau, sau khi render, khi side effect được phép. **Tuy nhiên, cách tiếp cận này nên là biện pháp cuối cùng của bạn.**

Khi có thể, hãy cố gắng diễn đạt logic của bạn chỉ bằng cách render. Bạn sẽ ngạc nhiên điều này có thể đưa bạn đi xa đến đâu!

<DeepDive>

#### Tại sao React quan tâm đến tính thuần túy? {/*why-does-react-care-about-purity*/}

Viết các hàm thuần túy đòi hỏi một số thói quen và kỷ luật. Nhưng nó cũng mở ra những cơ hội tuyệt vời:

* Các component của bạn có thể chạy trong môi trường khác — ví dụ, trên server! Vì chúng trả về cùng kết quả cho cùng đầu vào, một component có thể phục vụ nhiều yêu cầu người dùng.
* Bạn có thể cải thiện hiệu suất bằng cách [bỏ qua render](/reference/react/memo) các component có đầu vào không thay đổi. Điều này an toàn vì hàm thuần túy luôn trả về cùng kết quả, vì vậy chúng an toàn để cache.
* Nếu một số dữ liệu thay đổi ở giữa quá trình render một cây component sâu, React có thể khởi động lại render mà không mất thời gian để hoàn thành render đã lỗi thời. Tính thuần túy giúp dừng tính toán bất kỳ lúc nào an toàn.

Mọi tính năng React mới mà chúng tôi đang xây dựng đều tận dụng tính thuần túy. Từ data fetching đến animation đến hiệu suất, giữ cho các component thuần túy mở khóa sức mạnh của mô hình React.

</DeepDive>

<Recap>

* Một component phải thuần túy, có nghĩa là:
  * **Nó lo việc của mình.** Nó không nên thay đổi bất kỳ object hoặc biến nào đã tồn tại trước khi render.
  * **Cùng đầu vào, cùng đầu ra.** Với cùng đầu vào, một component luôn nên trả về cùng JSX.
* Render có thể xảy ra bất kỳ lúc nào, vì vậy các component không nên phụ thuộc vào trình tự render của nhau.
* Bạn không nên mutate bất kỳ đầu vào nào mà các component của bạn sử dụng để render. Điều đó bao gồm props, state và context. Để cập nhật màn hình, ["đặt" state](/learn/state-a-components-memory) thay vì mutate các object đã tồn tại.
* Cố gắng diễn đạt logic của component trong JSX bạn trả về. Khi bạn cần "thay đổi thứ gì đó", bạn thường sẽ muốn làm điều đó trong một event handler. Như biện pháp cuối cùng, bạn có thể `useEffect`.
* Viết hàm thuần túy cần một chút thực hành, nhưng nó mở khóa sức mạnh của mô hình React.

</Recap>



<Challenges>

#### Sửa đồng hồ bị hỏng {/*fix-a-broken-clock*/}

Component này cố gắng đặt class CSS của `<h1>` thành `"night"` trong thời gian từ nửa đêm đến sáu giờ sáng, và `"day"` vào tất cả các thời điểm khác. Tuy nhiên, nó không hoạt động. Bạn có thể sửa component này không?

Bạn có thể xác minh giải pháp của mình có hoạt động hay không bằng cách tạm thời thay đổi múi giờ của máy tính. Khi thời gian hiện tại là từ nửa đêm đến sáu giờ sáng, đồng hồ nên có màu đảo ngược!

<Hint>

Render là *tính toán*, nó không nên cố gắng "làm" gì. Bạn có thể diễn đạt ý tưởng tương tự theo cách khác không?

</Hint>

<Sandpack>

```js src/Clock.js active
export default function Clock({ time }) {
  const hours = time.getHours();
  if (hours >= 0 && hours <= 6) {
    document.getElementById('time').className = 'night';
  } else {
    document.getElementById('time').className = 'day';
  }
  return (
    <h1 id="time">
      {time.toLocaleTimeString()}
    </h1>
  );
}
```

```js src/App.js hidden
import { useState, useEffect } from 'react';
import Clock from './Clock.js';

function useTime() {
  const [time, setTime] = useState(() => new Date());
  useEffect(() => {
    const id = setInterval(() => {
      setTime(new Date());
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return time;
}

export default function App() {
  const time = useTime();
  return (
    <Clock time={time} />
  );
}
```

```css
body > * {
  width: 100%;
  height: 100%;
}
.day {
  background: #fff;
  color: #222;
}
.night {
  background: #222;
  color: #fff;
}
```

</Sandpack>

<Solution>

Bạn có thể sửa component này bằng cách tính toán `className` và đưa nó vào kết quả render:

<Sandpack>

```js src/Clock.js active
export default function Clock({ time }) {
  const hours = time.getHours();
  let className;
  if (hours >= 0 && hours <= 6) {
    className = 'night';
  } else {
    className = 'day';
  }
  return (
    <h1 className={className}>
      {time.toLocaleTimeString()}
    </h1>
  );
}
```

```js src/App.js hidden
import { useState, useEffect } from 'react';
import Clock from './Clock.js';

function useTime() {
  const [time, setTime] = useState(() => new Date());
  useEffect(() => {
    const id = setInterval(() => {
      setTime(new Date());
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return time;
}

export default function App() {
  const time = useTime();
  return (
    <Clock time={time} />
  );
}
```

```css
body > * {
  width: 100%;
  height: 100%;
}
.day {
  background: #fff;
  color: #222;
}
.night {
  background: #222;
  color: #fff;
}
```

</Sandpack>

Trong ví dụ này, side effect (sửa đổi DOM) hoàn toàn không cần thiết. Bạn chỉ cần trả về JSX.

</Solution>

#### Sửa hồ sơ bị hỏng {/*fix-a-broken-profile*/}

Hai component `Profile` được render cạnh nhau với dữ liệu khác nhau. Nhấn "Collapse" trên hồ sơ đầu tiên, và sau đó "Expand" nó. Bạn sẽ nhận thấy rằng cả hai hồ sơ bây giờ hiển thị cùng một người. Đây là một bug.

Tìm nguyên nhân của bug và sửa nó.

<Hint>

Code bị lỗi nằm trong `Profile.js`. Hãy đọc nó từ đầu đến cuối!

</Hint>

<Sandpack>

```js {expectedErrors: {'react-compiler': [7]}} src/Profile.js
import Panel from './Panel.js';
import { getImageUrl } from './utils.js';

let currentPerson;

export default function Profile({ person }) {
  currentPerson = person;
  return (
    <Panel>
      <Header />
      <Avatar />
    </Panel>
  )
}

function Header() {
  return <h1>{currentPerson.name}</h1>;
}

function Avatar() {
  return (
    <img
      className="avatar"
      src={getImageUrl(currentPerson)}
      alt={currentPerson.name}
      width={50}
      height={50}
    />
  );
}
```

```js src/Panel.js hidden
import { useState } from 'react';

export default function Panel({ children }) {
  const [open, setOpen] = useState(true);
  return (
    <section className="panel">
      <button onClick={() => setOpen(!open)}>
        {open ? 'Collapse' : 'Expand'}
      </button>
      {open && children}
    </section>
  );
}
```

```js src/App.js
import Profile from './Profile.js';

export default function App() {
  return (
    <>
      <Profile person={{
        imageId: 'lrWQx8l',
        name: 'Subrahmanyan Chandrasekhar',
      }} />
      <Profile person={{
        imageId: 'MK3eW3A',
        name: 'Creola Katherine Johnson',
      }} />
    </>
  )
}
```

```js src/utils.js hidden
export function getImageUrl(person, size = 's') {
  return (
    'https://react.dev/images/docs/scientists/' +
    person.imageId +
    size +
    '.jpg'
  );
}
```

```css
.avatar { margin: 5px; border-radius: 50%; }
.panel {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
  width: 200px;
}
h1 { margin: 5px; font-size: 18px; }
```

</Sandpack>

<Solution>

Vấn đề là component `Profile` ghi vào một biến đã tồn tại gọi là `currentPerson`, và các component `Header` và `Avatar` đọc từ nó. Điều này làm *cả ba đều* không thuần túy và khó đoán trước.

Để sửa bug, hãy xóa biến `currentPerson`. Thay vào đó, truyền tất cả thông tin từ `Profile` đến `Header` và `Avatar` thông qua props. Bạn sẽ cần thêm prop `person` cho cả hai component và truyền nó xuống hết.

<Sandpack>

```js src/Profile.js active
import Panel from './Panel.js';
import { getImageUrl } from './utils.js';

export default function Profile({ person }) {
  return (
    <Panel>
      <Header person={person} />
      <Avatar person={person} />
    </Panel>
  )
}

function Header({ person }) {
  return <h1>{person.name}</h1>;
}

function Avatar({ person }) {
  return (
    <img
      className="avatar"
      src={getImageUrl(person)}
      alt={person.name}
      width={50}
      height={50}
    />
  );
}
```

```js src/Panel.js hidden
import { useState } from 'react';

export default function Panel({ children }) {
  const [open, setOpen] = useState(true);
  return (
    <section className="panel">
      <button onClick={() => setOpen(!open)}>
        {open ? 'Collapse' : 'Expand'}
      </button>
      {open && children}
    </section>
  );
}
```

```js src/App.js
import Profile from './Profile.js';

export default function App() {
  return (
    <>
      <Profile person={{
        imageId: 'lrWQx8l',
        name: 'Subrahmanyan Chandrasekhar',
      }} />
      <Profile person={{
        imageId: 'MK3eW3A',
        name: 'Creola Katherine Johnson',
      }} />
    </>
  );
}
```

```js src/utils.js hidden
export function getImageUrl(person, size = 's') {
  return (
    'https://react.dev/images/docs/scientists/' +
    person.imageId +
    size +
    '.jpg'
  );
}
```

```css
.avatar { margin: 5px; border-radius: 50%; }
.panel {
  border: 1px solid #aaa;
  border-radius: 6px;
  margin-top: 20px;
  padding: 10px;
  width: 200px;
}
h1 { margin: 5px; font-size: 18px; }
```

</Sandpack>

Hãy nhớ rằng React không đảm bảo rằng các hàm component sẽ thực thi theo bất kỳ thứ tự cụ thể nào, vì vậy bạn không thể giao tiếp giữa chúng bằng cách đặt các biến. Tất cả giao tiếp phải xảy ra thông qua props.

</Solution>

#### Sửa khay story bị hỏng {/*fix-a-broken-story-tray*/}

Giám đốc điều hành công ty của bạn đang yêu cầu bạn thêm "stories" vào ứng dụng đồng hồ trực tuyến, và bạn không thể nói không. Bạn đã viết một component `StoryTray` chấp nhận danh sách `stories`, theo sau là placeholder "Create Story".

Bạn đã triển khai placeholder "Create Story" bằng cách push thêm một story giả vào cuối mảng `stories` mà bạn nhận như một prop. Nhưng vì lý do nào đó, "Create Story" xuất hiện nhiều hơn một lần. Hãy sửa vấn đề này.

<Sandpack>

```js src/StoryTray.js active
export default function StoryTray({ stories }) {
  stories.push({
    id: 'create',
    label: 'Create Story'
  });

  return (
    <ul>
      {stories.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
    </ul>
  );
}
```

```js {expectedErrors: {'react-compiler': [16]}} src/App.js hidden
import { useState, useEffect } from 'react';
import StoryTray from './StoryTray.js';

const initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  const [stories, setStories] = useState([...initialStories])
  const time = useTime();

  // HACK: Prevent the memory from growing forever while you read docs.
  // We're breaking our own rules here.
  if (stories.length > 100) {
    stories.length = 100;
  }

  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <h2>It is {time.toLocaleTimeString()} now.</h2>
      <StoryTray stories={stories} />
    </div>
  );
}

function useTime() {
  const [time, setTime] = useState(() => new Date());
  useEffect(() => {
    const id = setInterval(() => {
      setTime(new Date());
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return time;
}
```

```css
ul {
  margin: 0;
  list-style-type: none;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  margin-bottom: 20px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

```js sandbox.config.json hidden
{
  "hardReloadOnChange": true
}
```

</Sandpack>

<Solution>

Lưu ý cách mỗi khi đồng hồ cập nhật, "Create Story" được thêm *hai lần*. Điều này gợi ý rằng chúng ta có một mutation trong quá trình render -- Strict Mode gọi các component hai lần để làm cho những vấn đề này dễ nhận ra hơn.

Hàm `StoryTray` không thuần túy. Bằng cách gọi `push` trên mảng `stories` đã nhận (một prop!), nó đang mutate một object được tạo *trước khi* `StoryTray` bắt đầu render. Điều này làm nó có bug và rất khó đoán trước.

Cách sửa đơn giản nhất là không đụng đến mảng chút nào, và render "Create Story" riêng biệt:

<Sandpack>

```js src/StoryTray.js active
export default function StoryTray({ stories }) {
  return (
    <ul>
      {stories.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
      <li>Create Story</li>
    </ul>
  );
}
```

```js {expectedErrors: {'react-compiler': [16]}} src/App.js hidden
import { useState, useEffect } from 'react';
import StoryTray from './StoryTray.js';

const initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  const [stories, setStories] = useState([...initialStories])
  const time = useTime();

  // HACK: Prevent the memory from growing forever while you read docs.
  // We're breaking our own rules here.
  if (stories.length > 100) {
    stories.length = 100;
  }

  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <h2>It is {time.toLocaleTimeString()} now.</h2>
      <StoryTray stories={stories} />
    </div>
  );
}

function useTime() {
  const [time, setTime] = useState(() => new Date());
  useEffect(() => {
    const id = setInterval(() => {
      setTime(new Date());
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return time;
}
```

```css
ul {
  margin: 0;
  list-style-type: none;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  margin-bottom: 20px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

</Sandpack>

Ngoài ra, bạn có thể tạo một mảng _mới_ (bằng cách sao chép mảng hiện có) trước khi push một mục vào nó:

<Sandpack>

```js src/StoryTray.js active
export default function StoryTray({ stories }) {
  // Copy the array!
  const storiesToDisplay = stories.slice();

  // Does not affect the original array:
  storiesToDisplay.push({
    id: 'create',
    label: 'Create Story'
  });

  return (
    <ul>
      {storiesToDisplay.map(story => (
        <li key={story.id}>
          {story.label}
        </li>
      ))}
    </ul>
  );
}
```

```js {expectedErrors: {'react-compiler': [16]}} src/App.js hidden
import { useState, useEffect } from 'react';
import StoryTray from './StoryTray.js';

const initialStories = [
  {id: 0, label: "Ankit's Story" },
  {id: 1, label: "Taylor's Story" },
];

export default function App() {
  const [stories, setStories] = useState([...initialStories])
  const time = useTime();

  // HACK: Prevent the memory from growing forever while you read docs.
  // We're breaking our own rules here.
  if (stories.length > 100) {
    stories.length = 100;
  }

  return (
    <div
      style={{
        width: '100%',
        height: '100%',
        textAlign: 'center',
      }}
    >
      <h2>It is {time.toLocaleTimeString()} now.</h2>
      <StoryTray stories={stories} />
    </div>
  );
}

function useTime() {
  const [time, setTime] = useState(() => new Date());
  useEffect(() => {
    const id = setInterval(() => {
      setTime(new Date());
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return time;
}
```

```css
ul {
  margin: 0;
  list-style-type: none;
}

li {
  border: 1px solid #aaa;
  border-radius: 6px;
  float: left;
  margin: 5px;
  margin-bottom: 20px;
  padding: 5px;
  width: 70px;
  height: 100px;
}
```

</Sandpack>

Điều này giữ cho mutation của bạn là cục bộ và hàm render của bạn thuần túy. Tuy nhiên, bạn vẫn cần cẩn thận: ví dụ, nếu bạn cố thay đổi bất kỳ mục hiện có nào của mảng, bạn cũng phải clone những mục đó.

Hữu ích khi nhớ các thao tác nào trên mảng mutate chúng, và cái nào không. Ví dụ, `push`, `pop`, `reverse`, và `sort` sẽ mutate mảng gốc, nhưng `slice`, `filter`, và `map` sẽ tạo một mảng mới.

</Solution>

</Challenges>
