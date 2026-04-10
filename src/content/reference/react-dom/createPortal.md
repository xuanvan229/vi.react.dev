---
title: createPortal
---

<Intro>

`createPortal` cho phép bạn render một số phần tử con vào một phần khác của DOM.


```js
<div>
  <SomeComponent />
  {createPortal(children, domNode, key?)}
</div>
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `createPortal(children, domNode, key?)` {/*createportal*/}

Để tạo một portal, gọi `createPortal`, truyền vào một số JSX và DOM node nơi nó sẽ được render:

```js
import { createPortal } from 'react-dom';

// ...

<div>
  <p>This child is placed in the parent div.</p>
  {createPortal(
    <p>This child is placed in the document body.</p>,
    document.body
  )}
</div>
```

[Xem thêm các ví dụ bên dưới.](#usage)

Một portal chỉ thay đổi vị trí vật lý của DOM node. Trong mọi khía cạnh khác, JSX mà bạn render vào một portal hoạt động như một node con của component React render nó. Ví dụ, phần tử con có thể truy cập context được cung cấp bởi cây cha, và các event sẽ bubble lên từ con đến cha theo cây React.

#### Tham số {/*parameters*/}

* `children`: Bất cứ thứ gì có thể được render với React, chẳng hạn như một đoạn JSX (ví dụ `<div />` hoặc `<SomeComponent />`), một [Fragment](/reference/react/Fragment) (`<>...</>`), một chuỗi hoặc số, hoặc một mảng các phần tử này.

* `domNode`: Một DOM node nào đó, chẳng hạn như những node được trả về bởi `document.getElementById()`. Node phải đã tồn tại. Truyền một DOM node khác trong quá trình cập nhật sẽ khiến nội dung portal được tạo lại.

* **tùy chọn** `key`: Một chuỗi hoặc số duy nhất được sử dụng làm [key](/learn/rendering-lists#keeping-list-items-in-order-with-key) của portal.

#### Giá trị trả về {/*returns*/}

`createPortal` trả về một React node có thể được bao gồm trong JSX hoặc trả về từ một component React. Nếu React gặp nó trong kết quả render, nó sẽ đặt `children` được cung cấp vào bên trong `domNode` được cung cấp.

#### Lưu ý {/*caveats*/}

* Các event từ portal lan truyền theo cây React thay vì cây DOM. Ví dụ, nếu bạn click vào bên trong một portal, và portal được bao bọc trong `<div onClick>`, handler `onClick` đó sẽ được kích hoạt. Nếu điều này gây ra vấn đề, hãy ngăn chặn việc lan truyền event từ bên trong portal, hoặc di chuyển portal lên phía trên trong cây React.

---

## Cách sử dụng {/*usage*/}

### Render vào một phần khác của DOM {/*rendering-to-a-different-part-of-the-dom*/}

*Portal* cho phép các component của bạn render một số phần tử con vào một vị trí khác trong DOM. Điều này cho phép một phần của component "thoát ra" khỏi bất kỳ container nào chứa nó. Ví dụ, một component có thể hiển thị một hộp thoại modal hoặc tooltip xuất hiện phía trên và bên ngoài phần còn lại của trang.

Để tạo một portal, render kết quả của `createPortal` với <CodeStep step={1}>một số JSX</CodeStep> và <CodeStep step={2}>DOM node nơi nó sẽ đến</CodeStep>:

```js [[1, 8, "<p>This child is placed in the document body.</p>"], [2, 9, "document.body"]]
import { createPortal } from 'react-dom';

function MyComponent() {
  return (
    <div style={{ border: '2px solid black' }}>
      <p>This child is placed in the parent div.</p>
      {createPortal(
        <p>This child is placed in the document body.</p>,
        document.body
      )}
    </div>
  );
}
```

React sẽ đặt các DOM node cho <CodeStep step={1}>JSX bạn đã truyền</CodeStep> vào bên trong <CodeStep step={2}>DOM node bạn đã cung cấp</CodeStep>.

Nếu không có portal, `<p>` thứ hai sẽ được đặt bên trong `<div>` cha, nhưng portal đã "dịch chuyển" nó vào [`document.body`:](https://developer.mozilla.org/en-US/docs/Web/API/Document/body)

<Sandpack>

```js
import { createPortal } from 'react-dom';

export default function MyComponent() {
  return (
    <div style={{ border: '2px solid black' }}>
      <p>This child is placed in the parent div.</p>
      {createPortal(
        <p>This child is placed in the document body.</p>,
        document.body
      )}
    </div>
  );
}
```

</Sandpack>

Lưu ý rằng đoạn văn thứ hai hiển thị trực quan bên ngoài `<div>` cha có viền. Nếu bạn kiểm tra cấu trúc DOM với công cụ nhà phát triển, bạn sẽ thấy `<p>` thứ hai được đặt trực tiếp vào `<body>`:

```html {4-6,9}
<body>
  <div id="root">
    ...
      <div style="border: 2px solid black">
        <p>This child is placed inside the parent div.</p>
      </div>
    ...
  </div>
  <p>This child is placed in the document body.</p>
</body>
```

Một portal chỉ thay đổi vị trí vật lý của DOM node. Trong mọi khía cạnh khác, JSX mà bạn render vào một portal hoạt động như một node con của component React render nó. Ví dụ, phần tử con có thể truy cập context được cung cấp bởi cây cha, và các event vẫn bubble lên từ con đến cha theo cây React.

---

### Render một hộp thoại modal với portal {/*rendering-a-modal-dialog-with-a-portal*/}

Bạn có thể sử dụng portal để tạo một hộp thoại modal nổi phía trên phần còn lại của trang, ngay cả khi component gọi hộp thoại nằm bên trong một container có `overflow: hidden` hoặc các style khác ảnh hưởng đến hộp thoại.

Trong ví dụ này, hai container có các style làm gián đoạn hộp thoại modal, nhưng cái được render vào portal không bị ảnh hưởng vì trong DOM, modal không được chứa trong các phần tử JSX cha.

<Sandpack>

```js src/App.js active
import NoPortalExample from './NoPortalExample';
import PortalExample from './PortalExample';

export default function App() {
  return (
    <>
      <div className="clipping-container">
        <NoPortalExample  />
      </div>
      <div className="clipping-container">
        <PortalExample />
      </div>
    </>
  );
}
```

```js src/NoPortalExample.js
import { useState } from 'react';
import ModalContent from './ModalContent.js';

export default function NoPortalExample() {
  const [showModal, setShowModal] = useState(false);
  return (
    <>
      <button onClick={() => setShowModal(true)}>
        Show modal without a portal
      </button>
      {showModal && (
        <ModalContent onClose={() => setShowModal(false)} />
      )}
    </>
  );
}
```

```js src/PortalExample.js active
import { useState } from 'react';
import { createPortal } from 'react-dom';
import ModalContent from './ModalContent.js';

export default function PortalExample() {
  const [showModal, setShowModal] = useState(false);
  return (
    <>
      <button onClick={() => setShowModal(true)}>
        Show modal using a portal
      </button>
      {showModal && createPortal(
        <ModalContent onClose={() => setShowModal(false)} />,
        document.body
      )}
    </>
  );
}
```

```js src/ModalContent.js
export default function ModalContent({ onClose }) {
  return (
    <div className="modal">
      <div>I'm a modal dialog</div>
      <button onClick={onClose}>Close</button>
    </div>
  );
}
```


```css src/styles.css
.clipping-container {
  position: relative;
  border: 1px solid #aaa;
  margin-bottom: 12px;
  padding: 12px;
  width: 250px;
  height: 80px;
  overflow: hidden;
}

.modal {
  display: flex;
  justify-content: space-evenly;
  align-items: center;
  box-shadow: rgba(100, 100, 111, 0.3) 0px 7px 29px 0px;
  background-color: white;
  border: 2px solid rgb(240, 240, 240);
  border-radius: 12px;
  position:  absolute;
  width: 250px;
  top: 70px;
  left: calc(50% - 125px);
  bottom: 70px;
}
```

</Sandpack>

<Pitfall>

Điều quan trọng là đảm bảo ứng dụng của bạn có thể truy cập được khi sử dụng portal. Ví dụ, bạn có thể cần quản lý focus bàn phím để người dùng có thể di chuyển focus vào và ra khỏi portal một cách tự nhiên.

Hãy tuân theo [WAI-ARIA Modal Authoring Practices](https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal) khi tạo modal. Nếu bạn sử dụng một package của cộng đồng, hãy đảm bảo rằng nó có thể truy cập được và tuân theo các hướng dẫn này.

</Pitfall>

---

### Render các component React vào markup server không phải React {/*rendering-react-components-into-non-react-server-markup*/}

Portal có thể hữu ích nếu root React của bạn chỉ là một phần của trang tĩnh hoặc được render từ server mà không được xây dựng bằng React. Ví dụ, nếu trang của bạn được xây dựng với một framework server như Rails, bạn có thể tạo các vùng tương tác trong các khu vực tĩnh như sidebar. So với việc có [nhiều root React riêng biệt,](/reference/react-dom/client/createRoot#rendering-a-page-partially-built-with-react) portal cho phép bạn xử lý ứng dụng như một cây React duy nhất với state được chia sẻ ngay cả khi các phần của nó render vào các phần khác nhau của DOM.

<Sandpack>

```html public/index.html
<!DOCTYPE html>
<html>
  <head><title>My app</title></head>
  <body>
    <h1>Welcome to my hybrid app</h1>
    <div class="parent">
      <div class="sidebar">
        This is server non-React markup
        <div id="sidebar-content"></div>
      </div>
      <div id="root"></div>
    </div>
  </body>
</html>
```

```js src/index.js
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import App from './App.js';
import './styles.css';

const root = createRoot(document.getElementById('root'));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

```js src/App.js active
import { createPortal } from 'react-dom';

const sidebarContentEl = document.getElementById('sidebar-content');

export default function App() {
  return (
    <>
      <MainContent />
      {createPortal(
        <SidebarContent />,
        sidebarContentEl
      )}
    </>
  );
}

function MainContent() {
  return <p>This part is rendered by React</p>;
}

function SidebarContent() {
  return <p>This part is also rendered by React!</p>;
}
```

```css
.parent {
  display: flex;
  flex-direction: row;
}

#root {
  margin-top: 12px;
}

.sidebar {
  padding:  12px;
  background-color: #eee;
  width: 200px;
  height: 200px;
  margin-right: 12px;
}

#sidebar-content {
  margin-top: 18px;
  display: block;
  background-color: white;
}

p {
  margin: 0;
}
```

</Sandpack>

---

### Render các component React vào DOM node không phải React {/*rendering-react-components-into-non-react-dom-nodes*/}

Bạn cũng có thể sử dụng portal để quản lý nội dung của một DOM node được quản lý bên ngoài React. Ví dụ, giả sử bạn đang tích hợp với một widget bản đồ không phải React và bạn muốn render nội dung React bên trong một popup. Để làm điều này, khai báo một biến state `popupContainer` để lưu trữ DOM node mà bạn sẽ render vào:

```js
const [popupContainer, setPopupContainer] = useState(null);
```

Khi bạn tạo widget bên thứ ba, lưu trữ DOM node được trả về bởi widget để bạn có thể render vào đó:

```js {5-6}
useEffect(() => {
  if (mapRef.current === null) {
    const map = createMapWidget(containerRef.current);
    mapRef.current = map;
    const popupDiv = addPopupToMapWidget(map);
    setPopupContainer(popupDiv);
  }
}, []);
```

Điều này cho phép bạn sử dụng `createPortal` để render nội dung React vào `popupContainer` khi nó sẵn sàng:

```js {3-6}
return (
  <div style={{ width: 250, height: 250 }} ref={containerRef}>
    {popupContainer !== null && createPortal(
      <p>Hello from React!</p>,
      popupContainer
    )}
  </div>
);
```

Đây là một ví dụ hoàn chỉnh bạn có thể thử:

<Sandpack>

```json package.json hidden
{
  "dependencies": {
    "leaflet": "1.9.1",
    "react": "latest",
    "react-dom": "latest",
    "react-scripts": "latest",
    "remarkable": "2.0.1"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

```js src/App.js
import { useRef, useEffect, useState } from 'react';
import { createPortal } from 'react-dom';
import { createMapWidget, addPopupToMapWidget } from './map-widget.js';

export default function Map() {
  const containerRef = useRef(null);
  const mapRef = useRef(null);
  const [popupContainer, setPopupContainer] = useState(null);

  useEffect(() => {
    if (mapRef.current === null) {
      const map = createMapWidget(containerRef.current);
      mapRef.current = map;
      const popupDiv = addPopupToMapWidget(map);
      setPopupContainer(popupDiv);
    }
  }, []);

  return (
    <div style={{ width: 250, height: 250 }} ref={containerRef}>
      {popupContainer !== null && createPortal(
        <p>Hello from React!</p>,
        popupContainer
      )}
    </div>
  );
}
```

```js src/map-widget.js
import 'leaflet/dist/leaflet.css';
import * as L from 'leaflet';

export function createMapWidget(containerDomNode) {
  const map = L.map(containerDomNode);
  map.setView([0, 0], 0);
  L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap'
  }).addTo(map);
  return map;
}

export function addPopupToMapWidget(map) {
  const popupDiv = document.createElement('div');
  L.popup()
    .setLatLng([0, 0])
    .setContent(popupDiv)
    .openOn(map);
  return popupDiv;
}
```

```css
button { margin: 5px; }
```

</Sandpack>
