---
title: Quy tắc của Hooks
---

Bạn có thể đang ở đây vì bạn nhận được thông báo lỗi sau:

<ConsoleBlock level="error">

Hooks can only be called inside the body of a function component.

</ConsoleBlock>

Có ba lý do phổ biến mà bạn có thể gặp lỗi này:

1. Bạn có thể đang **vi phạm Quy tắc của Hooks**.
2. Bạn có thể có **phiên bản không khớp** của React và React DOM.
3. Bạn có thể có **nhiều hơn một bản sao của React** trong cùng một ứng dụng.

Hãy xem xét từng trường hợp.

## Vi phạm Quy tắc của Hooks {/*breaking-rules-of-hooks*/}

Các hàm có tên bắt đầu bằng `use` được gọi là [*Hooks*](/reference/react) trong React.

**Không gọi Hooks bên trong vòng lặp, điều kiện, hoặc hàm lồng nhau.** Thay vào đó, luôn sử dụng Hooks ở cấp cao nhất của hàm React của bạn, trước bất kỳ lệnh return sớm nào. Bạn chỉ có thể gọi Hooks khi React đang render một function component:

* ✅ Gọi chúng ở cấp cao nhất trong thân của một [function component](/learn/your-first-component).
* ✅ Gọi chúng ở cấp cao nhất trong thân của một [custom Hook](/learn/reusing-logic-with-custom-hooks).

```js{2-3,8-9}
function Counter() {
  // ✅ Tốt: cấp cao nhất trong một function component
  const [count, setCount] = useState(0);
  // ...
}

function useWindowWidth() {
  // ✅ Tốt: cấp cao nhất trong một custom Hook
  const [width, setWidth] = useState(window.innerWidth);
  // ...
}
```

**Không** được hỗ trợ để gọi Hooks (các hàm bắt đầu bằng `use`) trong bất kỳ trường hợp nào khác, ví dụ:

* 🔴 Không gọi Hooks bên trong điều kiện hoặc vòng lặp.
* 🔴 Không gọi Hooks sau câu lệnh `return` có điều kiện.
* 🔴 Không gọi Hooks trong trình xử lý sự kiện.
* 🔴 Không gọi Hooks trong class component.
* 🔴 Không gọi Hooks bên trong các hàm được truyền cho `useMemo`, `useReducer`, hoặc `useEffect`.

Nếu bạn vi phạm các quy tắc này, bạn có thể gặp lỗi này.

```js{3-4,11-12,20-21}
function Bad({ cond }) {
  if (cond) {
    // 🔴 Sai: bên trong điều kiện (để sửa, di chuyển ra ngoài!)
    const theme = useContext(ThemeContext);
  }
  // ...
}

function Bad() {
  for (let i = 0; i < 10; i++) {
    // 🔴 Sai: bên trong vòng lặp (để sửa, di chuyển ra ngoài!)
    const theme = useContext(ThemeContext);
  }
  // ...
}

function Bad({ cond }) {
  if (cond) {
    return;
  }
  // 🔴 Sai: sau return có điều kiện (để sửa, di chuyển lên trước return!)
  const theme = useContext(ThemeContext);
  // ...
}

function Bad() {
  function handleClick() {
    // 🔴 Sai: bên trong trình xử lý sự kiện (để sửa, di chuyển ra ngoài!)
    const theme = useContext(ThemeContext);
  }
  // ...
}

function Bad() {
  const style = useMemo(() => {
    // 🔴 Sai: bên trong useMemo (để sửa, di chuyển ra ngoài!)
    const theme = useContext(ThemeContext);
    return createStyle(theme);
  });
  // ...
}

class Bad extends React.Component {
  render() {
    // 🔴 Sai: bên trong class component (để sửa, viết function component thay vì class!)
    useEffect(() => {})
    // ...
  }
}
```

Bạn có thể sử dụng [plugin `eslint-plugin-react-hooks`](https://www.npmjs.com/package/eslint-plugin-react-hooks) để phát hiện các lỗi này.

<Note>

[Custom Hooks](/learn/reusing-logic-with-custom-hooks) *có thể* gọi các Hooks khác (đó là toàn bộ mục đích của chúng). Điều này hoạt động vì custom Hooks cũng chỉ được gọi khi một function component đang render.

</Note>

## Phiên bản không khớp của React và React DOM {/*mismatching-versions-of-react-and-react-dom*/}

Bạn có thể đang sử dụng phiên bản `react-dom` (< 16.8.0) hoặc `react-native` (< 0.59) chưa hỗ trợ Hooks. Bạn có thể chạy `npm ls react-dom` hoặc `npm ls react-native` trong thư mục ứng dụng để kiểm tra phiên bản bạn đang sử dụng. Nếu bạn thấy nhiều hơn một phiên bản, điều này cũng có thể gây ra vấn đề (xem thêm bên dưới).

## React trùng lặp {/*duplicate-react*/}

Để Hooks hoạt động, import `react` từ mã ứng dụng của bạn cần resolve đến cùng một module với import `react` từ bên trong package `react-dom`.

Nếu các import `react` này resolve đến hai object exports khác nhau, bạn sẽ thấy cảnh báo này. Điều này có thể xảy ra nếu bạn **vô tình có hai bản sao** của package `react`.

Nếu bạn sử dụng Node để quản lý package, bạn có thể chạy kiểm tra này trong thư mục dự án:

<TerminalBlock>

npm ls react

</TerminalBlock>

Nếu bạn thấy nhiều hơn một React, bạn sẽ cần tìm hiểu tại sao điều này xảy ra và sửa cây dependency của bạn. Ví dụ, có thể một thư viện bạn đang sử dụng chỉ định sai `react` là dependency (thay vì peer dependency). Cho đến khi thư viện đó được sửa, [Yarn resolutions](https://yarnpkg.com/lang/en/docs/selective-version-resolutions/) là một cách giải quyết khả thi.

Bạn cũng có thể thử debug vấn đề này bằng cách thêm một số log và khởi động lại server phát triển:

```js
// Add this in node_modules/react-dom/index.js
window.React1 = require(‘react’);

// Add this in your component file
require(‘react-dom’);
window.React2 = require(‘react’);
console.log(window.React1 === window.React2);
```

Nếu nó in ra `false` thì bạn có thể có hai React và cần tìm hiểu tại sao điều đó xảy ra. [Issue này](https://github.com/facebook/react/issues/13991) bao gồm một số lý do phổ biến mà cộng đồng đã gặp.

Vấn đề này cũng có thể xảy ra khi bạn sử dụng `npm link` hoặc tương đương. Trong trường hợp đó, bundler của bạn có thể "thấy" hai React -- một trong thư mục ứng dụng và một trong thư mục thư viện. Giả sử `myapp` và `mylib` là các thư mục cùng cấp, một cách sửa khả thi là chạy `npm link ../myapp/node_modules/react` từ `mylib`. Điều này sẽ làm cho thư viện sử dụng bản sao React của ứng dụng.

<Note>

Nói chung, React hỗ trợ sử dụng nhiều bản sao độc lập trên một trang (ví dụ, nếu một ứng dụng và một widget bên thứ ba đều sử dụng nó). Nó chỉ bị lỗi nếu `require(‘react’)` resolve khác nhau giữa component và bản sao `react-dom` mà nó được render cùng.

</Note>

## Các nguyên nhân khác {/*other-causes*/}

Nếu không có cách nào ở trên hiệu quả, vui lòng bình luận trong [issue này](https://github.com/facebook/react/issues/13991) và chúng tôi sẽ cố gắng giúp đỡ. Hãy thử tạo một ví dụ tái tạo nhỏ -- bạn có thể phát hiện ra vấn đề trong quá trình làm điều đó.
