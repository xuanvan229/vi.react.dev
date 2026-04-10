---
title: Render và Commit
---

<Intro>

Trước khi các component của bạn được hiển thị trên màn hình, chúng phải được render bởi React. Hiểu các bước trong quá trình này sẽ giúp bạn suy nghĩ về cách code của bạn thực thi và giải thích hành vi của nó.

</Intro>

<YouWillLearn>

* Rendering có nghĩa là gì trong React
* Khi nào và tại sao React render một component
* Các bước liên quan đến việc hiển thị một component trên màn hình
* Tại sao rendering không phải lúc nào cũng tạo ra một DOM update

</YouWillLearn>

Hãy tưởng tượng các component của bạn là những đầu bếp trong bếp, lắp ráp những món ăn ngon từ các nguyên liệu. Trong kịch bản này, React là người phục vụ tiếp nhận yêu cầu từ khách hàng và mang đến cho họ những món ăn. Quá trình yêu cầu và phục vụ giao diện người dùng này có ba bước:

1. **Triggering** một render (chuyển yêu cầu của khách đến bếp)
2. **Rendering** component (chuẩn bị món ăn trong bếp)
3. **Committing** vào DOM (đặt món ăn lên bàn)

<IllustrationBlock sequential>
  <Illustration caption="Trigger" alt="React as a server in a restaurant, fetching orders from the users and delivering them to the Component Kitchen." src="/images/docs/illustrations/i_render-and-commit1.png" />
  <Illustration caption="Render" alt="The Card Chef gives React a fresh Card component." src="/images/docs/illustrations/i_render-and-commit2.png" />
  <Illustration caption="Commit" alt="React delivers the Card to the user at their table." src="/images/docs/illustrations/i_render-and-commit3.png" />
</IllustrationBlock>

## Bước 1: Trigger một render {/*step-1-trigger-a-render*/}

Có hai lý do để một component render:

1. Đó là **render ban đầu** của component.
2. **State** của component (hoặc một trong các tổ tiên của nó) **đã được cập nhật.**

### Render ban đầu {/*initial-render*/}

Khi ứng dụng của bạn khởi động, bạn cần trigger render ban đầu. Các framework và sandbox đôi khi ẩn code này, nhưng nó được thực hiện bằng cách gọi [`createRoot`](/reference/react-dom/client/createRoot) với node DOM đích, sau đó gọi phương thức `render` của nó với component của bạn:

<Sandpack>

```js src/index.js active
import Image from './Image.js';
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'))
root.render(<Image />);
```

```js src/Image.js
export default function Image() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/ZF6s192.jpg"
      alt="'Floralis Genérica' by Eduardo Catalano: a gigantic metallic flower sculpture with reflective petals"
    />
  );
}
```

</Sandpack>

Hãy thử comment dòng `root.render()` và xem component biến mất!

### Re-render khi state cập nhật {/*re-renders-when-state-updates*/}

Sau khi component đã được render ban đầu, bạn có thể trigger thêm các lần render bằng cách cập nhật state của nó với [hàm `set`.](/reference/react/useState#setstate) Cập nhật state của component tự động đưa vào hàng đợi một render. (Bạn có thể hình dung những điều này như một vị khách nhà hàng gọi thêm trà, tráng miệng, và đủ thứ sau khi đặt món đầu tiên, tùy thuộc vào tình trạng khát hay đói của họ.)

<IllustrationBlock sequential>
  <Illustration caption="State update..." alt="React as a server in a restaurant, serving a Card UI to the user, represented as a patron with a cursor for their head. The patron expresses they want a pink card, not a black one!" src="/images/docs/illustrations/i_rerender1.png" />
  <Illustration caption="...triggers..." alt="React returns to the Component Kitchen and tells the Card Chef they need a pink Card." src="/images/docs/illustrations/i_rerender2.png" />
  <Illustration caption="...render!" alt="The Card Chef gives React the pink Card." src="/images/docs/illustrations/i_rerender3.png" />
</IllustrationBlock>

## Bước 2: React render các component của bạn {/*step-2-react-renders-your-components*/}

Sau khi bạn trigger một render, React gọi các component của bạn để tìm ra những gì hiển thị trên màn hình. **"Rendering" là React đang gọi các component của bạn.**

* **Trong render ban đầu,** React sẽ gọi component gốc.
* **Đối với các lần render tiếp theo,** React sẽ gọi function component có state update đã trigger render.

Quá trình này đệ quy: nếu component đã cập nhật trả về một component khác, React sẽ render _component đó_ tiếp theo, và nếu component đó cũng trả về gì đó, nó sẽ render _component đó_ tiếp theo, và cứ thế tiếp tục. Quá trình sẽ tiếp tục cho đến khi không còn component lồng nhau nào nữa và React biết chính xác những gì sẽ được hiển thị trên màn hình.

Trong ví dụ sau, React sẽ gọi `Gallery()` và `Image()` nhiều lần:

<Sandpack>

```js src/Gallery.js active
export default function Gallery() {
  return (
    <section>
      <h1>Inspiring Sculptures</h1>
      <Image />
      <Image />
      <Image />
    </section>
  );
}

function Image() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/ZF6s192.jpg"
      alt="'Floralis Genérica' by Eduardo Catalano: a gigantic metallic flower sculpture with reflective petals"
    />
  );
}
```

```js src/index.js
import Gallery from './Gallery.js';
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'))
root.render(<Gallery />);
```

```css
img { margin: 0 10px 10px 0; }
```

</Sandpack>

* **Trong render ban đầu,** React sẽ [tạo các DOM node](https://developer.mozilla.org/docs/Web/API/Document/createElement) cho `<section>`, `<h1>`, và ba thẻ `<img>`.
* **Trong một re-render,** React sẽ tính toán những thuộc tính nào của chúng, nếu có, đã thay đổi kể từ lần render trước. Nó sẽ không làm gì với thông tin đó cho đến bước tiếp theo, giai đoạn commit.

<Pitfall>

Rendering phải luôn là một [phép tính thuần túy](/learn/keeping-components-pure):

* **Đầu vào giống nhau, đầu ra giống nhau.** Với cùng một đầu vào, một component luôn phải trả về cùng một JSX. (Khi ai đó đặt món salad với cà chua, họ không nên nhận được salad với hành tây!)
* **Nó chỉ quan tâm đến công việc của mình.** Nó không nên thay đổi bất kỳ đối tượng hay biến nào tồn tại trước khi rendering. (Một đơn hàng không nên thay đổi đơn hàng của người khác.)

Nếu không, bạn có thể gặp phải các lỗi khó hiểu và hành vi không thể đoán trước khi codebase của bạn ngày càng phức tạp. Khi phát triển trong "Strict Mode", React gọi hàm của mỗi component hai lần, điều này có thể giúp phát hiện các lỗi gây ra bởi các hàm không thuần túy.

</Pitfall>

<DeepDive>

#### Tối ưu hóa hiệu suất {/*optimizing-performance*/}

Hành vi mặc định của việc render tất cả các component lồng nhau trong component đã cập nhật là không tối ưu cho hiệu suất nếu component đã cập nhật ở rất cao trong cây. Nếu bạn gặp vấn đề về hiệu suất, có một số cách tùy chọn để giải quyết nó được mô tả trong phần [Performance](https://reactjs.org/docs/optimizing-performance.html). **Đừng tối ưu hóa sớm!**

</DeepDive>

## Bước 3: React commit các thay đổi vào DOM {/*step-3-react-commits-changes-to-the-dom*/}

Sau khi rendering (gọi) các component của bạn, React sẽ sửa đổi DOM.

* **Đối với render ban đầu,** React sẽ sử dụng API DOM [`appendChild()`](https://developer.mozilla.org/docs/Web/API/Node/appendChild) để đặt tất cả các DOM node mà nó đã tạo lên màn hình.
* **Đối với re-render,** React sẽ áp dụng các thao tác tối thiểu cần thiết (được tính toán trong khi rendering!) để làm cho DOM khớp với đầu ra rendering mới nhất.

**React chỉ thay đổi các DOM node nếu có sự khác biệt giữa các lần render.** Ví dụ, đây là một component re-render với các props khác nhau được truyền từ cha mỗi giây. Lưu ý cách bạn có thể thêm một số văn bản vào `<input>`, cập nhật `value` của nó, nhưng văn bản không biến mất khi component re-render:

<Sandpack>

```js src/Clock.js active
export default function Clock({ time }) {
  return (
    <>
      <h1>{time}</h1>
      <input />
    </>
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
    <Clock time={time.toLocaleTimeString()} />
  );
}
```

</Sandpack>

Điều này hoạt động vì trong bước cuối cùng này, React chỉ cập nhật nội dung của `<h1>` với `time` mới. Nó thấy rằng `<input>` xuất hiện trong JSX ở cùng vị trí với lần trước, vì vậy React không chạm vào `<input>`—hay `value` của nó!
## Phần kết: Trình duyệt vẽ lại {/*epilogue-browser-paint*/}

Sau khi rendering hoàn tất và React đã cập nhật DOM, trình duyệt sẽ vẽ lại màn hình. Mặc dù quá trình này được biết đến là "browser rendering", chúng ta sẽ gọi nó là "painting" để tránh nhầm lẫn trong suốt tài liệu.

<Illustration alt="A browser painting 'still life with card element'." src="/images/docs/illustrations/i_browser-paint.png" />

<Recap>

* Bất kỳ cập nhật màn hình nào trong ứng dụng React đều xảy ra theo ba bước:
  1. Trigger
  2. Render
  3. Commit
* Bạn có thể sử dụng Strict Mode để tìm lỗi trong các component của mình
* React không chạm vào DOM nếu kết quả rendering giống với lần trước

</Recap>
