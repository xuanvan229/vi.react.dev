---
title: State như một Snapshot
---

<Intro>

Các state variable có thể trông giống như các biến JavaScript thông thường mà bạn có thể đọc và ghi. Tuy nhiên, state hoạt động giống như một snapshot hơn. Đặt nó không thay đổi state variable bạn đã có, mà thay vào đó trigger một re-render.

</Intro>

<YouWillLearn>

* Cách đặt state trigger re-renders
* Khi nào và cách state cập nhật
* Tại sao state không cập nhật ngay lập tức sau khi bạn đặt nó
* Cách event handler truy cập "snapshot" của state

</YouWillLearn>

## Đặt state trigger renders {/*setting-state-triggers-renders*/}

Bạn có thể nghĩ giao diện người dùng của mình đang thay đổi trực tiếp để phản hồi sự kiện người dùng như một cú nhấp. Trong React, nó hoạt động hơi khác với mô hình tư duy này. Trên trang trước, bạn thấy rằng [đặt state yêu cầu một re-render](/learn/render-and-commit#step-1-trigger-a-render) từ React. Điều này có nghĩa là để một interface phản ứng với sự kiện, bạn cần *cập nhật state*.

Trong ví dụ này, khi bạn nhấn "send", `setIsSent(true)` nói với React để re-render giao diện người dùng:

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [isSent, setIsSent] = useState(false);
  const [message, setMessage] = useState('Hi!');
  if (isSent) {
    return <h1>Your message is on its way!</h1>
  }
  return (
    <form onSubmit={(e) => {
      e.preventDefault();
      setIsSent(true);
      sendMessage(message);
    }}>
      <textarea
        placeholder="Message"
        value={message}
        onChange={e => setMessage(e.target.value)}
      />
      <button type="submit">Send</button>
    </form>
  );
}

function sendMessage(message) {
  // ...
}
```

```css
label, textarea { margin-bottom: 10px; display: block; }
```

</Sandpack>

Đây là những gì xảy ra khi bạn nhấp vào nút:

1. Event handler `onSubmit` thực thi.
2. `setIsSent(true)` đặt `isSent` thành `true` và đưa vào hàng đợi một render mới.
3. React re-render component theo giá trị `isSent` mới.

Hãy xem xét kỹ hơn mối quan hệ giữa state và rendering.

## Rendering chụp một snapshot trong thời gian {/*rendering-takes-a-snapshot-in-time*/}

["Rendering"](/learn/render-and-commit#step-2-react-renders-your-components) có nghĩa là React đang gọi component của bạn, đó là một hàm. JSX bạn trả về từ hàm đó giống như một snapshot của giao diện người dùng trong thời gian. Props, event handler, và các biến cục bộ của nó đều được tính toán **sử dụng state của nó tại thời điểm render.**

Không giống như một bức ảnh hay một khung phim, "snapshot" giao diện người dùng bạn trả về là tương tác. Nó bao gồm logic như event handler xác định những gì xảy ra để phản hồi các input. React cập nhật màn hình để khớp với snapshot này và kết nối các event handler. Kết quả là, nhấn một nút sẽ trigger click handler từ JSX của bạn.

Khi React re-render một component:

1. React gọi lại hàm của bạn.
2. Hàm của bạn trả về một JSX snapshot mới.
3. React sau đó cập nhật màn hình để khớp với snapshot mà hàm của bạn trả về.

<IllustrationBlock sequential>
    <Illustration caption="React executing the function" src="/images/docs/illustrations/i_render1.png" />
    <Illustration caption="Calculating the snapshot" src="/images/docs/illustrations/i_render2.png" />
    <Illustration caption="Updating the DOM tree" src="/images/docs/illustrations/i_render3.png" />
</IllustrationBlock>

Là bộ nhớ của component, state không giống như một biến thông thường biến mất sau khi hàm của bạn trả về. State thực sự "sống" trong React--như thể trên một kệ!--bên ngoài hàm của bạn. Khi React gọi component của bạn, nó cho bạn một snapshot của state cho lần render cụ thể đó. Component của bạn trả về một snapshot của giao diện người dùng với một tập hợp mới các props và event handler trong JSX của nó, tất cả được tính toán **sử dụng các giá trị state từ lần render đó!**

<IllustrationBlock sequential>
  <Illustration caption="You tell React to update the state" src="/images/docs/illustrations/i_state-snapshot1.png" />
  <Illustration caption="React updates the state value" src="/images/docs/illustrations/i_state-snapshot2.png" />
  <Illustration caption="React passes a snapshot of the state value into the component" src="/images/docs/illustrations/i_state-snapshot3.png" />
</IllustrationBlock>

Đây là một thí nghiệm nhỏ để cho bạn thấy cách điều này hoạt động. Trong ví dụ này, bạn có thể mong đợi rằng nhấp vào nút "+3" sẽ tăng bộ đếm ba lần vì nó gọi `setNumber(number + 1)` ba lần.

Xem điều gì xảy ra khi bạn nhấp vào nút "+3":

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 1);
        setNumber(number + 1);
        setNumber(number + 1);
      }}>+3</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

Lưu ý rằng `number` chỉ tăng một lần mỗi lần nhấp!

**Đặt state chỉ thay đổi nó cho *lần render tiếp theo*.** Trong lần render đầu tiên, `number` là `0`. Đây là lý do tại sao, trong handler `onClick` của *lần render đó*, giá trị của `number` vẫn là `0` ngay cả sau khi `setNumber(number + 1)` được gọi:

```js
<button onClick={() => {
  setNumber(number + 1);
  setNumber(number + 1);
  setNumber(number + 1);
}}>+3</button>
```

Đây là những gì click handler của nút này nói với React cần làm:

1. `setNumber(number + 1)`: `number` là `0` vì vậy `setNumber(0 + 1)`.
    - React chuẩn bị thay đổi `number` thành `1` trong lần render tiếp theo.
2. `setNumber(number + 1)`: `number` là `0` vì vậy `setNumber(0 + 1)`.
    - React chuẩn bị thay đổi `number` thành `1` trong lần render tiếp theo.
3. `setNumber(number + 1)`: `number` là `0` vì vậy `setNumber(0 + 1)`.
    - React chuẩn bị thay đổi `number` thành `1` trong lần render tiếp theo.

Mặc dù bạn đã gọi `setNumber(number + 1)` ba lần, trong event handler của *lần render này*, `number` luôn là `0`, vì vậy bạn đặt state thành `1` ba lần. Đây là lý do tại sao, sau khi event handler của bạn hoàn thành, React re-render component với `number` bằng `1` thay vì `3`.

Bạn cũng có thể hình dung điều này bằng cách thay thế tinh thần các state variable với các giá trị của chúng trong code của bạn. Vì state variable `number` là `0` cho *lần render này*, event handler của nó trông như thế này:

```js
<button onClick={() => {
  setNumber(0 + 1);
  setNumber(0 + 1);
  setNumber(0 + 1);
}}>+3</button>
```

Đối với lần render tiếp theo, `number` là `1`, vì vậy click handler của *lần render đó* trông như thế này:

```js
<button onClick={() => {
  setNumber(1 + 1);
  setNumber(1 + 1);
  setNumber(1 + 1);
}}>+3</button>
```

Đây là lý do tại sao nhấp vào nút một lần nữa sẽ đặt bộ đếm thành `2`, sau đó thành `3` ở lần nhấp tiếp theo, và cứ thế tiếp tục.

## State theo thời gian {/*state-over-time*/}

Thú vị đấy. Hãy đoán xem nhấp vào nút này sẽ alert gì:

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        alert(number);
      }}>+5</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

Nếu bạn sử dụng phương pháp thay thế từ trước, bạn có thể đoán rằng alert hiển thị "0":

```js
setNumber(0 + 5);
alert(0);
```

Nhưng nếu bạn đặt một timer trên alert, để nó chỉ kích hoạt _sau_ khi component re-render thì sao? Nó sẽ hiển thị "0" hay "5"? Hãy đoán thử!

<Sandpack>

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        setTimeout(() => {
          alert(number);
        }, 3000);
      }}>+5</button>
    </>
  )
}
```

```css
button { display: inline-block; margin: 10px; font-size: 20px; }
h1 { display: inline-block; margin: 10px; width: 30px; text-align: center; }
```

</Sandpack>

Bất ngờ chưa? Nếu bạn sử dụng phương pháp thay thế, bạn có thể thấy "snapshot" của state được truyền cho alert.

```js
setNumber(0 + 5);
setTimeout(() => {
  alert(0);
}, 3000);
```

State được lưu trữ trong React có thể đã thay đổi vào thời điểm alert chạy, nhưng nó được lên lịch sử dụng một snapshot của state tại thời điểm người dùng tương tác với nó!

**Giá trị của state variable không bao giờ thay đổi trong một lần render,** ngay cả khi code của event handler là bất đồng bộ. Bên trong `onClick` của *lần render đó*, giá trị của `number` tiếp tục là `0` ngay cả sau khi `setNumber(number + 5)` được gọi. Giá trị của nó đã được "cố định" khi React "chụp snapshot" của giao diện người dùng bằng cách gọi component của bạn.

Đây là ví dụ về cách điều đó làm cho event handler của bạn ít bị lỗi timing hơn. Dưới đây là một form gửi tin nhắn với độ trễ năm giây. Hãy tưởng tượng kịch bản này:

1. Bạn nhấn nút "Send", gửi "Hello" cho Alice.
2. Trước khi kết thúc độ trễ năm giây, bạn thay đổi giá trị của trường "To" thành "Bob".

Bạn mong đợi `alert` hiển thị gì? Nó sẽ hiển thị "You said Hello to Alice"? Hay nó sẽ hiển thị "You said Hello to Bob"? Hãy đoán dựa trên những gì bạn biết, và sau đó thử:

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [to, setTo] = useState('Alice');
  const [message, setMessage] = useState('Hello');

  function handleSubmit(e) {
    e.preventDefault();
    setTimeout(() => {
      alert(`You said ${message} to ${to}`);
    }, 5000);
  }

  return (
    <form onSubmit={handleSubmit}>
      <label>
        To:{' '}
        <select
          value={to}
          onChange={e => setTo(e.target.value)}>
          <option value="Alice">Alice</option>
          <option value="Bob">Bob</option>
        </select>
      </label>
      <textarea
        placeholder="Message"
        value={message}
        onChange={e => setMessage(e.target.value)}
      />
      <button type="submit">Send</button>
    </form>
  );
}
```

```css
label, textarea { margin-bottom: 10px; display: block; }
```

</Sandpack>

**React giữ các giá trị state "cố định" trong các event handler của một lần render.** Bạn không cần lo lắng liệu state có thay đổi trong khi code đang chạy không.

Nhưng nếu bạn muốn đọc state mới nhất trước một re-render thì sao? Bạn sẽ muốn sử dụng một [hàm cập nhật state](/learn/queueing-a-series-of-state-updates), được đề cập ở trang tiếp theo!

<Recap>

* Đặt state yêu cầu một render mới.
* React lưu trữ state bên ngoài component của bạn, như thể trên một kệ.
* Khi bạn gọi `useState`, React cho bạn một snapshot của state *cho lần render đó*.
* Các biến và event handler không "tồn tại" qua các lần re-render. Mỗi lần render có event handler riêng của nó.
* Mỗi lần render (và các hàm bên trong nó) sẽ luôn "thấy" snapshot của state mà React đã cung cấp cho *lần render đó*.
* Bạn có thể thay thế state trong event handler về mặt tinh thần, tương tự như cách bạn nghĩ về JSX được render.
* Các event handler được tạo trong quá khứ có các giá trị state từ lần render mà chúng được tạo ra.

</Recap>



<Challenges>

#### Triển khai đèn giao thông {/*implement-a-traffic-light*/}

Đây là một component đèn giao thông dành cho người đi bộ bật/tắt khi nhấn nút:

<Sandpack>

```js
import { useState } from 'react';

export default function TrafficLight() {
  const [walk, setWalk] = useState(true);

  function handleClick() {
    setWalk(!walk);
  }

  return (
    <>
      <button onClick={handleClick}>
        Change to {walk ? 'Stop' : 'Walk'}
      </button>
      <h1 style={{
        color: walk ? 'darkgreen' : 'darkred'
      }}>
        {walk ? 'Walk' : 'Stop'}
      </h1>
    </>
  );
}
```

```css
h1 { margin-top: 20px; }
```

</Sandpack>

Thêm một `alert` vào click handler. Khi đèn xanh và hiển thị "Walk", nhấp vào nút nên hiển thị "Stop is next". Khi đèn đỏ và hiển thị "Stop", nhấp vào nút nên hiển thị "Walk is next".

Có sự khác biệt không khi bạn đặt `alert` trước hay sau lệnh gọi `setWalk`?

<Solution>

Your `alert` should look like this:

<Sandpack>

```js
import { useState } from 'react';

export default function TrafficLight() {
  const [walk, setWalk] = useState(true);

  function handleClick() {
    setWalk(!walk);
    alert(walk ? 'Stop is next' : 'Walk is next');
  }

  return (
    <>
      <button onClick={handleClick}>
        Change to {walk ? 'Stop' : 'Walk'}
      </button>
      <h1 style={{
        color: walk ? 'darkgreen' : 'darkred'
      }}>
        {walk ? 'Walk' : 'Stop'}
      </h1>
    </>
  );
}
```

```css
h1 { margin-top: 20px; }
```

</Sandpack>

Dù bạn đặt nó trước hay sau lệnh gọi `setWalk` đều không có sự khác biệt. Giá trị của `walk` trong lần render đó là cố định. Gọi `setWalk` chỉ thay đổi nó cho *lần render tiếp theo*, nhưng sẽ không ảnh hưởng đến event handler từ lần render trước.

Dòng này ban đầu có vẻ trái với trực giác:

```js
alert(walk ? 'Stop is next' : 'Walk is next');
```

Nhưng nó có nghĩa nếu bạn đọc nó là: "Nếu đèn giao thông hiển thị 'Walk now', thông báo nên hiển thị 'Stop is next.'" Biến `walk` bên trong event handler của bạn khớp với giá trị của `walk` trong lần render đó và không thay đổi.

Bạn có thể xác minh điều này là đúng bằng cách áp dụng phương pháp thay thế. Khi `walk` là `true`, bạn nhận được:

```js
<button onClick={() => {
  setWalk(false);
  alert('Stop is next');
}}>
  Change to Stop
</button>
<h1 style={{color: 'darkgreen'}}>
  Walk
</h1>
```

Vì vậy nhấp "Change to Stop" đưa vào hàng đợi một render với `walk` được đặt thành `false`, và alert "Stop is next".

</Solution>

</Challenges>
