---
title: captureOwnerStack
---

<Intro>

`captureOwnerStack` đọc Owner Stack hiện tại trong quá trình phát triển và trả về nó dưới dạng chuỗi nếu có sẵn.

```js
const stack = captureOwnerStack();
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `captureOwnerStack()` {/*captureownerstack*/}

Gọi `captureOwnerStack` để lấy Owner Stack hiện tại.

```js {5,5}
import * as React from 'react';

function Component() {
  if (process.env.NODE_ENV !== 'production') {
    const ownerStack = React.captureOwnerStack();
    console.log(ownerStack);
  }
}
```

#### Tham số {/*parameters*/}

`captureOwnerStack` không nhận tham số nào.

#### Giá trị trả về {/*returns*/}

`captureOwnerStack` trả về `string | null`.

Owner Stack có sẵn trong
- Render component
- Effect (ví dụ: `useEffect`)
- Các event handler của React (ví dụ: `<button onClick={...} />`)
- Các error handler của React ([tùy chọn React Root](/reference/react-dom/client/createRoot#parameters) `onCaughtError`, `onRecoverableError`, và `onUncaughtError`)

Nếu không có Owner Stack nào có sẵn, `null` được trả về (xem [Xử lý sự cố: Owner Stack là `null`](#the-owner-stack-is-null)).

#### Lưu ý {/*caveats*/}

- Owner Stack chỉ có sẵn trong quá trình phát triển. `captureOwnerStack` sẽ luôn trả về `null` bên ngoài môi trường phát triển.

<DeepDive>

#### Owner Stack so với Component Stack {/*owner-stack-vs-component-stack*/}

Owner Stack khác với Component Stack có sẵn trong các error handler của React như [`errorInfo.componentStack` trong `onUncaughtError`](/reference/react-dom/client/hydrateRoot#error-logging-in-production).

Ví dụ, hãy xem xét code sau:

<Sandpack>

```js src/App.js
import {Suspense} from 'react';

function SubComponent({disabled}) {
  if (disabled) {
    throw new Error('disabled');
  }
}

export function Component({label}) {
  return (
    <fieldset>
      <legend>{label}</legend>
      <SubComponent key={label} disabled={label === 'disabled'} />
    </fieldset>
  );
}

function Navigation() {
  return null;
}

export default function App({children}) {
  return (
    <Suspense fallback="loading...">
      <main>
        <Navigation />
        {children}
      </main>
    </Suspense>
  );
}
```

```js src/index.js
import {captureOwnerStack} from 'react';
import {createRoot} from 'react-dom/client';
import App, {Component} from './App.js';
import './styles.css';

createRoot(document.createElement('div'), {
  onUncaughtError: (error, errorInfo) => {
    // The stacks are logged instead of showing them in the UI directly to
    // highlight that browsers will apply sourcemaps to the logged stacks.
    // Note that sourcemapping is only applied in the real browser console not
    // in the fake one displayed on this page.
    // Press "fork" to be able to view the sourcemapped stack in a real console.
    console.log(errorInfo.componentStack);
    console.log(captureOwnerStack());
  },
}).render(
  <App>
    <Component label="disabled" />
  </App>
);
```

```html public/index.html hidden
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <p>Check the console output.</p>
  </body>
</html>
```

</Sandpack>

`SubComponent` sẽ ném ra một lỗi.
Component Stack của lỗi đó sẽ là

```
at SubComponent
at fieldset
at Component
at main
at React.Suspense
at App
```

Tuy nhiên, Owner Stack chỉ đọc

```
at Component
```

Cả `App` lẫn các component DOM (ví dụ: `fieldset`) không được coi là Owner trong Stack này vì chúng không đóng góp vào việc "tạo ra" node chứa `SubComponent`. `App` và các component DOM chỉ chuyển tiếp node. `App` chỉ render node `children` thay vì `Component` tạo ra một node chứa `SubComponent` thông qua `<SubComponent />`.

Cả `Navigation` lẫn `legend` đều không có trong stack vì chúng chỉ là anh em của node chứa `<SubComponent />`.

`SubComponent` bị bỏ qua vì nó đã là một phần của callstack.

</DeepDive>

## Cách sử dụng {/*usage*/}

### Nâng cao một error overlay tùy chỉnh {/*enhance-a-custom-error-overlay*/}

```js [[1, 5, "console.error"], [4, 7, "captureOwnerStack"]]
import { captureOwnerStack } from "react";
import { instrumentedConsoleError } from "./errorOverlay";

const originalConsoleError = console.error;
console.error = function patchedConsoleError(...args) {
  originalConsoleError.apply(console, args);
  const ownerStack = captureOwnerStack();
  onConsoleError({
    // Keep in mind that in a real application, console.error can be
    // called with multiple arguments which you should account for.
    consoleMessage: args[0],
    ownerStack,
  });
};
```

Nếu bạn chặn các lần gọi <CodeStep step={1}>`console.error`</CodeStep> để làm nổi bật chúng trong một error overlay, bạn có thể gọi <CodeStep step={2}>`captureOwnerStack`</CodeStep> để bao gồm Owner Stack.

<Sandpack>

```css src/styles.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

h1 {
  margin-top: 0;
  font-size: 22px;
}

h2 {
  margin-top: 0;
  font-size: 20px;
}

code {
  font-size: 1.2em;
}

ul {
  padding-inline-start: 20px;
}

label, button { display: block; margin-bottom: 20px; }
html, body { min-height: 300px; }

#error-dialog {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background-color: white;
  padding: 15px;
  opacity: 0.9;
  text-wrap: wrap;
  overflow: scroll;
}

.text-red {
  color: red;
}

.-mb-20 {
  margin-bottom: -20px;
}

.mb-0 {
  margin-bottom: 0;
}

.mb-10 {
  margin-bottom: 10px;
}

pre {
  text-wrap: wrap;
}

pre.nowrap {
  text-wrap: nowrap;
}

.hidden {
 display: none;
}
```

```html public/index.html hidden
<!DOCTYPE html>
<html>
<head>
  <title>My app</title>
</head>
<body>
<!--
  Error dialog in raw HTML
  since an error in the React app may crash.
-->
<div id="error-dialog" class="hidden">
  <h1 id="error-title" class="text-red">Error</h1>
  <p>
    <pre id="error-body"></pre>
  </p>
  <h2 class="-mb-20">Owner Stack:</h4>
  <pre id="error-owner-stack" class="nowrap"></pre>
  <button
    id="error-close"
    class="mb-10"
    onclick="document.getElementById('error-dialog').classList.add('hidden')"
  >
    Close
  </button>
</div>
<!-- This is the DOM node -->
<div id="root"></div>
</body>
</html>

```

```js src/errorOverlay.js

export function onConsoleError({ consoleMessage, ownerStack }) {
  const errorDialog = document.getElementById("error-dialog");
  const errorBody = document.getElementById("error-body");
  const errorOwnerStack = document.getElementById("error-owner-stack");

  // Display console.error() message
  errorBody.innerText = consoleMessage;

  // Display owner stack
  errorOwnerStack.innerText = ownerStack;

  // Show the dialog
  errorDialog.classList.remove("hidden");
}
```

```js src/index.js active
import { captureOwnerStack } from "react";
import { createRoot } from "react-dom/client";
import App from './App';
import { onConsoleError } from "./errorOverlay";
import './styles.css';

const originalConsoleError = console.error;
console.error = function patchedConsoleError(...args) {
  originalConsoleError.apply(console, args);
  const ownerStack = captureOwnerStack();
  onConsoleError({
    // Keep in mind that in a real application, console.error can be
    // called with multiple arguments which you should account for.
    consoleMessage: args[0],
    ownerStack,
  });
};

const container = document.getElementById("root");
createRoot(container).render(<App />);
```

```js src/App.js
function Component() {
  return <button onClick={() => console.error('Some console error')}>Trigger console.error()</button>;
}

export default function App() {
  return <Component />;
}
```

</Sandpack>

## Xử lý sự cố {/*troubleshooting*/}

### Owner Stack là `null` {/*the-owner-stack-is-null*/}

Lần gọi `captureOwnerStack` xảy ra bên ngoài một hàm được React kiểm soát, ví dụ: trong một callback `setTimeout`, sau một lần gọi `fetch` hoặc trong một event handler DOM tùy chỉnh. Trong quá trình render, Effect, các event handler của React và các error handler của React (ví dụ: `hydrateRoot#options.onCaughtError`) thì Owner Stack nên có sẵn.

Trong ví dụ bên dưới, click vào button sẽ ghi lại một Owner Stack rỗng vì `captureOwnerStack` được gọi trong một event handler DOM tùy chỉnh. Owner Stack phải được capture sớm hơn, ví dụ bằng cách di chuyển lần gọi `captureOwnerStack` vào phần thân Effect.
<Sandpack>

```js
import {captureOwnerStack, useEffect} from 'react';

export default function App() {
  useEffect(() => {
    // Should call `captureOwnerStack` here.
    function handleEvent() {
      // Calling it in a custom DOM event handler is too late.
      // The Owner Stack will be `null` at this point.
      console.log('Owner Stack: ', captureOwnerStack());
    }

    document.addEventListener('click', handleEvent);

    return () => {
      document.removeEventListener('click', handleEvent);
    }
  })

  return <button>Click me to see that Owner Stacks are not available in custom DOM event handlers</button>;
}
```

</Sandpack>

### `captureOwnerStack` không có sẵn {/*captureownerstack-is-not-available*/}

`captureOwnerStack` chỉ được export trong các bản build phát triển. Nó sẽ là `undefined` trong các bản build production. Nếu `captureOwnerStack` được sử dụng trong các tệp được bundle cho cả production và development, bạn nên truy cập nó có điều kiện từ một namespace import.

```js
// Don't use named imports of `captureOwnerStack` in files that are bundled for development and production.
import {captureOwnerStack} from 'react';
// Use a namespace import instead and access `captureOwnerStack` conditionally.
import * as React from 'react';

if (process.env.NODE_ENV !== 'production') {
  const ownerStack = React.captureOwnerStack();
  console.log('Owner Stack', ownerStack);
}
```
