---
title: useLayoutEffect
---

<Pitfall>

`useLayoutEffect` có thể ảnh hưởng đến hiệu suất. Ưu tiên sử dụng [`useEffect`](/reference/react/useEffect) khi có thể.

</Pitfall>

<Intro>

`useLayoutEffect` là một phiên bản của [`useEffect`](/reference/react/useEffect) kích hoạt trước khi trình duyệt vẽ lại màn hình.

```js
useLayoutEffect(setup, dependencies?)
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `useLayoutEffect(setup, dependencies?)` {/*useinsertioneffect*/}

Gọi `useLayoutEffect` để thực hiện các phép đo layout trước khi trình duyệt vẽ lại màn hình:

```js
import { useState, useRef, useLayoutEffect } from 'react';

function Tooltip() {
  const ref = useRef(null);
  const [tooltipHeight, setTooltipHeight] = useState(0);

  useLayoutEffect(() => {
    const { height } = ref.current.getBoundingClientRect();
    setTooltipHeight(height);
  }, []);
  // ...
```


[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `setup`: Hàm chứa logic của Effect của bạn. Hàm setup của bạn cũng có thể tùy chọn trả về một hàm *cleanup*. Trước khi [component của bạn commit](/learn/render-and-commit#step-3-react-commits-changes-to-the-dom), React sẽ chạy hàm setup của bạn. Sau mỗi lần commit với các dependency đã thay đổi, React sẽ trước tiên chạy hàm cleanup (nếu bạn đã cung cấp) với các giá trị cũ, và sau đó chạy hàm setup của bạn với các giá trị mới. Trước khi component của bạn bị xóa khỏi DOM, React sẽ chạy hàm cleanup của bạn.

* **tùy chọn** `dependencies`: Danh sách tất cả các giá trị reactive được tham chiếu bên trong code `setup`. Các giá trị reactive bao gồm props, state, và tất cả các biến và hàm được khai báo trực tiếp bên trong thân component của bạn. Nếu bộ linter của bạn được [cấu hình cho React](/learn/editor-setup#linting), nó sẽ xác minh rằng mọi giá trị reactive được chỉ định chính xác như một dependency. Danh sách dependency phải có số lượng mục cố định và được viết inline như `[dep1, dep2, dep3]`. React sẽ so sánh mỗi dependency với giá trị trước đó của nó bằng phép so sánh [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is). Nếu bạn bỏ qua đối số này, Effect của bạn sẽ chạy lại sau mỗi lần commit của component.

#### Giá trị trả về {/*returns*/}

`useLayoutEffect` trả về `undefined`.

#### Lưu ý {/*caveats*/}

* `useLayoutEffect` là một Hook, vì vậy bạn chỉ có thể gọi nó **ở cấp cao nhất của component** hoặc các Hook của riêng bạn. Bạn không thể gọi nó bên trong vòng lặp hoặc điều kiện. Nếu bạn cần điều đó, hãy trích xuất một component và chuyển Effect vào đó.

* Khi Strict Mode được bật, React sẽ **chạy thêm một chu kỳ setup+cleanup chỉ dành cho development** trước khi setup thực sự đầu tiên. Đây là bài kiểm tra stress đảm bảo rằng logic cleanup của bạn "phản chiếu" logic setup và nó dừng hoặc hoàn tác bất cứ điều gì setup đang làm. Nếu điều này gây ra vấn đề, [hãy triển khai hàm cleanup.](/learn/synchronizing-with-effects#how-to-handle-the-effect-firing-twice-in-development)

* Nếu một số dependency của bạn là các object hoặc hàm được định nghĩa bên trong component, có nguy cơ chúng sẽ **làm Effect chạy lại thường xuyên hơn cần thiết.** Để khắc phục điều này, hãy xóa các dependency [object](/reference/react/useEffect#removing-unnecessary-object-dependencies) và [function](/reference/react/useEffect#removing-unnecessary-function-dependencies) không cần thiết. Bạn cũng có thể [trích xuất cập nhật state](/reference/react/useEffect#updating-state-based-on-previous-state-from-an-effect) và [logic không reactive](/reference/react/useEffect#reading-the-latest-props-and-state-from-an-effect) ra ngoài Effect của bạn.

* Các Effect **chỉ chạy trên client.** Chúng không chạy trong quá trình server rendering.

* Code bên trong `useLayoutEffect` và tất cả các cập nhật state được lên lịch từ nó **chặn trình duyệt vẽ lại màn hình.** Khi sử dụng quá mức, điều này làm chậm ứng dụng của bạn. Khi có thể, hãy ưu tiên [`useEffect`.](/reference/react/useEffect)

* Nếu bạn kích hoạt cập nhật state bên trong `useLayoutEffect`, React sẽ thực thi tất cả các Effect còn lại ngay lập tức bao gồm `useEffect`.

---

## Cách sử dụng {/*usage*/}

### Đo lường layout trước khi trình duyệt vẽ lại màn hình {/*measuring-layout-before-the-browser-repaints-the-screen*/}

Hầu hết các component không cần biết vị trí và kích thước của chúng trên màn hình để quyết định render gì. Chúng chỉ trả về một số JSX. Sau đó trình duyệt tính toán *layout* của chúng (vị trí và kích thước) và vẽ lại màn hình.

Đôi khi, điều đó là không đủ. Hãy tưởng tượng một tooltip xuất hiện bên cạnh một phần tử khi hover. Nếu có đủ không gian, tooltip sẽ xuất hiện phía trên phần tử, nhưng nếu không vừa, nó nên xuất hiện bên dưới. Để render tooltip ở vị trí cuối cùng đúng, bạn cần biết chiều cao của nó (tức là liệu nó có vừa ở trên hay không).

Để làm điều này, bạn cần render trong hai lượt:

1. Render tooltip ở bất kỳ đâu (ngay cả với vị trí sai).
2. Đo chiều cao của nó và quyết định nơi đặt tooltip.
3. Render tooltip *lại* ở đúng vị trí.

**Tất cả điều này cần xảy ra trước khi trình duyệt vẽ lại màn hình.** Bạn không muốn người dùng thấy tooltip di chuyển. Gọi `useLayoutEffect` để thực hiện các phép đo layout trước khi trình duyệt vẽ lại màn hình:

```js {5-8}
function Tooltip() {
  const ref = useRef(null);
  const [tooltipHeight, setTooltipHeight] = useState(0); // Bạn chưa biết chiều cao thực

  useLayoutEffect(() => {
    const { height } = ref.current.getBoundingClientRect();
    setTooltipHeight(height); // Re-render ngay bây giờ khi bạn biết chiều cao thực
  }, []);

  // ...sử dụng tooltipHeight trong logic rendering bên dưới...
}
```

Đây là cách nó hoạt động từng bước:

1. `Tooltip` render với `tooltipHeight = 0` ban đầu (vì vậy tooltip có thể bị đặt sai vị trí).
2. React đặt nó vào DOM và chạy code trong `useLayoutEffect`.
3. `useLayoutEffect` của bạn [đo chiều cao](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect) của nội dung tooltip và kích hoạt re-render ngay lập tức.
4. `Tooltip` render lại với `tooltipHeight` thực (vì vậy tooltip được đặt đúng vị trí).
5. React cập nhật nó trong DOM, và cuối cùng trình duyệt hiển thị tooltip.

Hover qua các nút bên dưới và xem cách tooltip điều chỉnh vị trí của nó tùy thuộc vào việc nó có vừa không:

<Sandpack>

```js
import ButtonWithTooltip from './ButtonWithTooltip.js';

export default function App() {
  return (
    <div>
      <ButtonWithTooltip
        tooltipContent={
          <div>
            This tooltip does not fit above the button.
            <br />
            This is why it's displayed below instead!
          </div>
        }
      >
        Hover over me (tooltip above)
      </ButtonWithTooltip>
      <div style={{ height: 50 }} />
      <ButtonWithTooltip
        tooltipContent={
          <div>This tooltip fits above the button</div>
        }
      >
        Hover over me (tooltip below)
      </ButtonWithTooltip>
      <div style={{ height: 50 }} />
      <ButtonWithTooltip
        tooltipContent={
          <div>This tooltip fits above the button</div>
        }
      >
        Hover over me (tooltip below)
      </ButtonWithTooltip>
    </div>
  );
}
```

```js src/ButtonWithTooltip.js
import { useState, useRef } from 'react';
import Tooltip from './Tooltip.js';

export default function ButtonWithTooltip({ tooltipContent, ...rest }) {
  const [targetRect, setTargetRect] = useState(null);
  const buttonRef = useRef(null);
  return (
    <>
      <button
        {...rest}
        ref={buttonRef}
        onPointerEnter={() => {
          const rect = buttonRef.current.getBoundingClientRect();
          setTargetRect({
            left: rect.left,
            top: rect.top,
            right: rect.right,
            bottom: rect.bottom,
          });
        }}
        onPointerLeave={() => {
          setTargetRect(null);
        }}
      />
      {targetRect !== null && (
        <Tooltip targetRect={targetRect}>
          {tooltipContent}
        </Tooltip>
      )
    }
    </>
  );
}
```

```js src/Tooltip.js active
import { useRef, useLayoutEffect, useState } from 'react';
import { createPortal } from 'react-dom';
import TooltipContainer from './TooltipContainer.js';

export default function Tooltip({ children, targetRect }) {
  const ref = useRef(null);
  const [tooltipHeight, setTooltipHeight] = useState(0);

  useLayoutEffect(() => {
    const { height } = ref.current.getBoundingClientRect();
    setTooltipHeight(height);
    console.log('Measured tooltip height: ' + height);
  }, []);

  let tooltipX = 0;
  let tooltipY = 0;
  if (targetRect !== null) {
    tooltipX = targetRect.left;
    tooltipY = targetRect.top - tooltipHeight;
    if (tooltipY < 0) {
      // It doesn't fit above, so place below.
      tooltipY = targetRect.bottom;
    }
  }

  return createPortal(
    <TooltipContainer x={tooltipX} y={tooltipY} contentRef={ref}>
      {children}
    </TooltipContainer>,
    document.body
  );
}
```

```js src/TooltipContainer.js
export default function TooltipContainer({ children, x, y, contentRef }) {
  return (
    <div
      style={{
        position: 'absolute',
        pointerEvents: 'none',
        left: 0,
        top: 0,
        transform: `translate3d(${x}px, ${y}px, 0)`
      }}
    >
      <div ref={contentRef} className="tooltip">
        {children}
      </div>
    </div>
  );
}
```

```css
.tooltip {
  color: white;
  background: #222;
  border-radius: 4px;
  padding: 4px;
}
```

</Sandpack>

Lưu ý rằng mặc dù component `Tooltip` phải render trong hai lượt (đầu tiên với `tooltipHeight` được khởi tạo là `0` và sau đó với chiều cao thực được đo), bạn chỉ thấy kết quả cuối cùng. Đây là lý do tại sao bạn cần `useLayoutEffect` thay vì [`useEffect`](/reference/react/useEffect) cho ví dụ này. Hãy xem sự khác biệt chi tiết bên dưới.

<Recipes titleText="useLayoutEffect vs useEffect" titleId="examples">

#### `useLayoutEffect` chặn trình duyệt vẽ lại {/*uselayouteffect-blocks-the-browser-from-repainting*/}

React đảm bảo rằng code bên trong `useLayoutEffect` và bất kỳ cập nhật state nào được lên lịch bên trong nó sẽ được xử lý **trước khi trình duyệt vẽ lại màn hình.** Điều này cho phép bạn render tooltip, đo nó, và render lại tooltip mà người dùng không nhận thấy lần render đầu tiên thêm. Nói cách khác, `useLayoutEffect` chặn trình duyệt không vẽ.

<Sandpack>

```js
import ButtonWithTooltip from './ButtonWithTooltip.js';

export default function App() {
  return (
    <div>
      <ButtonWithTooltip
        tooltipContent={
          <div>
            This tooltip does not fit above the button.
            <br />
            This is why it's displayed below instead!
          </div>
        }
      >
        Hover over me (tooltip above)
      </ButtonWithTooltip>
      <div style={{ height: 50 }} />
      <ButtonWithTooltip
        tooltipContent={
          <div>This tooltip fits above the button</div>
        }
      >
        Hover over me (tooltip below)
      </ButtonWithTooltip>
      <div style={{ height: 50 }} />
      <ButtonWithTooltip
        tooltipContent={
          <div>This tooltip fits above the button</div>
        }
      >
        Hover over me (tooltip below)
      </ButtonWithTooltip>
    </div>
  );
}
```

```js src/ButtonWithTooltip.js
import { useState, useRef } from 'react';
import Tooltip from './Tooltip.js';

export default function ButtonWithTooltip({ tooltipContent, ...rest }) {
  const [targetRect, setTargetRect] = useState(null);
  const buttonRef = useRef(null);
  return (
    <>
      <button
        {...rest}
        ref={buttonRef}
        onPointerEnter={() => {
          const rect = buttonRef.current.getBoundingClientRect();
          setTargetRect({
            left: rect.left,
            top: rect.top,
            right: rect.right,
            bottom: rect.bottom,
          });
        }}
        onPointerLeave={() => {
          setTargetRect(null);
        }}
      />
      {targetRect !== null && (
        <Tooltip targetRect={targetRect}>
          {tooltipContent}
        </Tooltip>
      )
    }
    </>
  );
}
```

```js src/Tooltip.js active
import { useRef, useLayoutEffect, useState } from 'react';
import { createPortal } from 'react-dom';
import TooltipContainer from './TooltipContainer.js';

export default function Tooltip({ children, targetRect }) {
  const ref = useRef(null);
  const [tooltipHeight, setTooltipHeight] = useState(0);

  useLayoutEffect(() => {
    const { height } = ref.current.getBoundingClientRect();
    setTooltipHeight(height);
  }, []);

  let tooltipX = 0;
  let tooltipY = 0;
  if (targetRect !== null) {
    tooltipX = targetRect.left;
    tooltipY = targetRect.top - tooltipHeight;
    if (tooltipY < 0) {
      // It doesn't fit above, so place below.
      tooltipY = targetRect.bottom;
    }
  }

  return createPortal(
    <TooltipContainer x={tooltipX} y={tooltipY} contentRef={ref}>
      {children}
    </TooltipContainer>,
    document.body
  );
}
```

```js src/TooltipContainer.js
export default function TooltipContainer({ children, x, y, contentRef }) {
  return (
    <div
      style={{
        position: 'absolute',
        pointerEvents: 'none',
        left: 0,
        top: 0,
        transform: `translate3d(${x}px, ${y}px, 0)`
      }}
    >
      <div ref={contentRef} className="tooltip">
        {children}
      </div>
    </div>
  );
}
```

```css
.tooltip {
  color: white;
  background: #222;
  border-radius: 4px;
  padding: 4px;
}
```

</Sandpack>

<Solution />

#### `useEffect` không chặn trình duyệt {/*useeffect-does-not-block-the-browser*/}

Đây là cùng ví dụ, nhưng với [`useEffect`](/reference/react/useEffect) thay vì `useLayoutEffect`. Nếu bạn đang dùng thiết bị chậm hơn, bạn có thể nhận thấy đôi khi tooltip "nhấp nháy" và bạn thấy thoáng qua vị trí ban đầu trước vị trí đã được hiệu chỉnh.

<Sandpack>

```js
import ButtonWithTooltip from './ButtonWithTooltip.js';

export default function App() {
  return (
    <div>
      <ButtonWithTooltip
        tooltipContent={
          <div>
            This tooltip does not fit above the button.
            <br />
            This is why it's displayed below instead!
          </div>
        }
      >
        Hover over me (tooltip above)
      </ButtonWithTooltip>
      <div style={{ height: 50 }} />
      <ButtonWithTooltip
        tooltipContent={
          <div>This tooltip fits above the button</div>
        }
      >
        Hover over me (tooltip below)
      </ButtonWithTooltip>
      <div style={{ height: 50 }} />
      <ButtonWithTooltip
        tooltipContent={
          <div>This tooltip fits above the button</div>
        }
      >
        Hover over me (tooltip below)
      </ButtonWithTooltip>
    </div>
  );
}
```

```js src/ButtonWithTooltip.js
import { useState, useRef } from 'react';
import Tooltip from './Tooltip.js';

export default function ButtonWithTooltip({ tooltipContent, ...rest }) {
  const [targetRect, setTargetRect] = useState(null);
  const buttonRef = useRef(null);
  return (
    <>
      <button
        {...rest}
        ref={buttonRef}
        onPointerEnter={() => {
          const rect = buttonRef.current.getBoundingClientRect();
          setTargetRect({
            left: rect.left,
            top: rect.top,
            right: rect.right,
            bottom: rect.bottom,
          });
        }}
        onPointerLeave={() => {
          setTargetRect(null);
        }}
      />
      {targetRect !== null && (
        <Tooltip targetRect={targetRect}>
          {tooltipContent}
        </Tooltip>
      )
    }
    </>
  );
}
```

```js src/Tooltip.js active
import { useRef, useEffect, useState } from 'react';
import { createPortal } from 'react-dom';
import TooltipContainer from './TooltipContainer.js';

export default function Tooltip({ children, targetRect }) {
  const ref = useRef(null);
  const [tooltipHeight, setTooltipHeight] = useState(0);

  useEffect(() => {
    const { height } = ref.current.getBoundingClientRect();
    setTooltipHeight(height);
  }, []);

  let tooltipX = 0;
  let tooltipY = 0;
  if (targetRect !== null) {
    tooltipX = targetRect.left;
    tooltipY = targetRect.top - tooltipHeight;
    if (tooltipY < 0) {
      // It doesn't fit above, so place below.
      tooltipY = targetRect.bottom;
    }
  }

  return createPortal(
    <TooltipContainer x={tooltipX} y={tooltipY} contentRef={ref}>
      {children}
    </TooltipContainer>,
    document.body
  );
}
```

```js src/TooltipContainer.js
export default function TooltipContainer({ children, x, y, contentRef }) {
  return (
    <div
      style={{
        position: 'absolute',
        pointerEvents: 'none',
        left: 0,
        top: 0,
        transform: `translate3d(${x}px, ${y}px, 0)`
      }}
    >
      <div ref={contentRef} className="tooltip">
        {children}
      </div>
    </div>
  );
}
```

```css
.tooltip {
  color: white;
  background: #222;
  border-radius: 4px;
  padding: 4px;
}
```

</Sandpack>

Để làm cho lỗi dễ tái hiện hơn, phiên bản này thêm độ trễ nhân tạo trong quá trình rendering. React sẽ cho phép trình duyệt vẽ màn hình trước khi nó xử lý cập nhật state bên trong `useEffect`. Kết quả là, tooltip nhấp nháy:

<Sandpack>

```js
import ButtonWithTooltip from './ButtonWithTooltip.js';

export default function App() {
  return (
    <div>
      <ButtonWithTooltip
        tooltipContent={
          <div>
            This tooltip does not fit above the button.
            <br />
            This is why it's displayed below instead!
          </div>
        }
      >
        Hover over me (tooltip above)
      </ButtonWithTooltip>
      <div style={{ height: 50 }} />
      <ButtonWithTooltip
        tooltipContent={
          <div>This tooltip fits above the button</div>
        }
      >
        Hover over me (tooltip below)
      </ButtonWithTooltip>
      <div style={{ height: 50 }} />
      <ButtonWithTooltip
        tooltipContent={
          <div>This tooltip fits above the button</div>
        }
      >
        Hover over me (tooltip below)
      </ButtonWithTooltip>
    </div>
  );
}
```

```js src/ButtonWithTooltip.js
import { useState, useRef } from 'react';
import Tooltip from './Tooltip.js';

export default function ButtonWithTooltip({ tooltipContent, ...rest }) {
  const [targetRect, setTargetRect] = useState(null);
  const buttonRef = useRef(null);
  return (
    <>
      <button
        {...rest}
        ref={buttonRef}
        onPointerEnter={() => {
          const rect = buttonRef.current.getBoundingClientRect();
          setTargetRect({
            left: rect.left,
            top: rect.top,
            right: rect.right,
            bottom: rect.bottom,
          });
        }}
        onPointerLeave={() => {
          setTargetRect(null);
        }}
      />
      {targetRect !== null && (
        <Tooltip targetRect={targetRect}>
          {tooltipContent}
        </Tooltip>
      )
    }
    </>
  );
}
```

```js {expectedErrors: {'react-compiler': [10, 11]}} src/Tooltip.js active
import { useRef, useEffect, useState } from 'react';
import { createPortal } from 'react-dom';
import TooltipContainer from './TooltipContainer.js';

export default function Tooltip({ children, targetRect }) {
  const ref = useRef(null);
  const [tooltipHeight, setTooltipHeight] = useState(0);

  // This artificially slows down rendering
  let now = performance.now();
  while (performance.now() - now < 100) {
    // Do nothing for a bit...
  }

  useEffect(() => {
    const { height } = ref.current.getBoundingClientRect();
    setTooltipHeight(height);
  }, []);

  let tooltipX = 0;
  let tooltipY = 0;
  if (targetRect !== null) {
    tooltipX = targetRect.left;
    tooltipY = targetRect.top - tooltipHeight;
    if (tooltipY < 0) {
      // It doesn't fit above, so place below.
      tooltipY = targetRect.bottom;
    }
  }

  return createPortal(
    <TooltipContainer x={tooltipX} y={tooltipY} contentRef={ref}>
      {children}
    </TooltipContainer>,
    document.body
  );
}
```

```js src/TooltipContainer.js
export default function TooltipContainer({ children, x, y, contentRef }) {
  return (
    <div
      style={{
        position: 'absolute',
        pointerEvents: 'none',
        left: 0,
        top: 0,
        transform: `translate3d(${x}px, ${y}px, 0)`
      }}
    >
      <div ref={contentRef} className="tooltip">
        {children}
      </div>
    </div>
  );
}
```

```css
.tooltip {
  color: white;
  background: #222;
  border-radius: 4px;
  padding: 4px;
}
```

</Sandpack>

Chỉnh sửa ví dụ này thành `useLayoutEffect` và quan sát rằng nó chặn việc vẽ ngay cả khi rendering bị làm chậm.

<Solution />

</Recipes>

<Note>

Rendering trong hai lượt và chặn trình duyệt ảnh hưởng đến hiệu suất. Hãy cố gắng tránh điều này khi bạn có thể.

</Note>

---

## Xử lý sự cố {/*troubleshooting*/}

### Tôi nhận được lỗi: "`useLayoutEffect` does nothing on the server" {/*im-getting-an-error-uselayouteffect-does-nothing-on-the-server*/}

Mục đích của `useLayoutEffect` là để cho phép component của bạn [sử dụng thông tin layout để rendering:](#measuring-layout-before-the-browser-repaints-the-screen)

1. Render nội dung ban đầu.
2. Đo layout *trước khi trình duyệt vẽ lại màn hình.*
3. Render nội dung cuối cùng bằng cách sử dụng thông tin layout bạn đã đọc.

Khi bạn hoặc framework của bạn sử dụng [server rendering,](/reference/react-dom/server) ứng dụng React của bạn render thành HTML trên server cho lần render ban đầu. Điều này cho phép bạn hiển thị HTML ban đầu trước khi code JavaScript tải.

Vấn đề là trên server, không có thông tin layout.

Trong [ví dụ trước đó](#measuring-layout-before-the-browser-repaints-the-screen), lời gọi `useLayoutEffect` trong component `Tooltip` cho phép nó tự định vị đúng (bên trên hoặc bên dưới nội dung) tùy thuộc vào chiều cao nội dung. Nếu bạn cố gắng render `Tooltip` như một phần của HTML server ban đầu, điều này sẽ không thể xác định được. Trên server, chưa có layout! Vì vậy, ngay cả khi bạn render nó trên server, vị trí của nó sẽ "nhảy" trên client sau khi JavaScript tải và chạy.

Thông thường, các component dựa vào thông tin layout không cần render trên server. Ví dụ, có lẽ không có ý nghĩa khi hiển thị `Tooltip` trong lần render ban đầu. Nó được kích hoạt bởi tương tác client.

Tuy nhiên, nếu bạn gặp phải vấn đề này, bạn có một vài lựa chọn khác nhau:

- Thay thế `useLayoutEffect` bằng [`useEffect`.](/reference/react/useEffect) Điều này cho React biết rằng không sao khi hiển thị kết quả render ban đầu mà không chặn việc vẽ (vì HTML gốc sẽ hiển thị trước khi Effect của bạn chạy).

- Ngoài ra, [đánh dấu component của bạn là chỉ dành cho client.](/reference/react/Suspense#providing-a-fallback-for-server-errors-and-client-only-content) Điều này cho React biết phải thay thế nội dung của nó lên đến ranh giới [`<Suspense>`](/reference/react/Suspense) gần nhất bằng một fallback tải (ví dụ: spinner hoặc glimmer) trong quá trình server rendering.

- Ngoài ra, bạn có thể render một component với `useLayoutEffect` chỉ sau khi hydration. Giữ một boolean state `isMounted` được khởi tạo là `false`, và đặt nó thành `true` bên trong một lời gọi `useEffect`. Logic rendering của bạn có thể như sau `return isMounted ? <RealContent /> : <FallbackContent />`. Trên server và trong quá trình hydration, người dùng sẽ thấy `FallbackContent` mà không nên gọi `useLayoutEffect`. Sau đó React sẽ thay thế nó bằng `RealContent` chạy chỉ trên client và có thể bao gồm các lời gọi `useLayoutEffect`.

- Nếu bạn đồng bộ hóa component của bạn với một external data store và dựa vào `useLayoutEffect` vì lý do khác ngoài việc đo layout, hãy cân nhắc [`useSyncExternalStore`](/reference/react/useSyncExternalStore) thay thế, nó [hỗ trợ server rendering.](/reference/react/useSyncExternalStore#adding-support-for-server-rendering)
