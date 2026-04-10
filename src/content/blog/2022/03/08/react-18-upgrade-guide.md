---
title: "Cách Nâng Cấp Lên React 18"
author: Rick Hanlon
date: 2022/03/08
description: Như chúng tôi đã chia sẻ trong bài viết phát hành, React 18 giới thiệu các tính năng được hỗ trợ bởi concurrent renderer mới, với chiến lược áp dụng dần dần cho các ứng dụng hiện tại. Trong bài viết này, chúng tôi sẽ hướng dẫn bạn các bước nâng cấp lên React 18.
---

March 08, 2022 by [Rick Hanlon](https://twitter.com/rickhanlonii)

---

<Intro>

Như chúng tôi đã chia sẻ trong [bài viết phát hành](/blog/2022/03/29/react-v18), React 18 giới thiệu các tính năng được hỗ trợ bởi concurrent renderer mới, với chiến lược áp dụng dần dần cho các ứng dụng hiện tại. Trong bài viết này, chúng tôi sẽ hướng dẫn bạn các bước nâng cấp lên React 18.

Vui lòng [báo cáo bất kỳ vấn đề nào](https://github.com/facebook/react/issues/new/choose) bạn gặp phải trong khi nâng cấp lên React 18.

</Intro>

<Note>

Đối với người dùng React Native, React 18 sẽ được đưa vào phiên bản tương lai của React Native. Điều này vì React 18 dựa vào Kiến Trúc React Native Mới để hưởng lợi từ các khả năng mới được trình bày trong bài blog này. Để biết thêm thông tin, hãy xem [bài keynote React Conf tại đây](https://www.youtube.com/watch?v=FZ0cG47msEk&t=1530s).

</Note>

---

## Cài đặt {/*installing*/}

Để cài đặt phiên bản React mới nhất:

```bash
npm install react react-dom
```

Hoặc nếu bạn đang sử dụng yarn:

```bash
yarn add react react-dom
```

## Cập nhật Client Rendering APIs {/*updates-to-client-rendering-apis*/}

Khi bạn lần đầu cài đặt React 18, bạn sẽ thấy cảnh báo trong console:

<ConsoleBlock level="error">

ReactDOM.render is no longer supported in React 18. Use createRoot instead. Until you switch to the new API, your app will behave as if it’s running React 17. Learn more: https://reactjs.org/link/switch-to-createroot

</ConsoleBlock>

React 18 giới thiệu một root API mới cung cấp ergonomics tốt hơn để quản lý roots. Root API mới cũng kích hoạt concurrent renderer mới, cho phép bạn chọn tham gia vào các tính năng concurrent.

```js
// Before
import { render } from ‘react-dom’;
const container = document.getElementById(‘app’);
render(<App tab="home" />, container);

// After
import { createRoot } from ‘react-dom/client’;
const container = document.getElementById(‘app’);
const root = createRoot(container); // createRoot(container!) if you use TypeScript
root.render(<App tab="home" />);
```

Chúng tôi cũng đã thay đổi `unmountComponentAtNode` thành `root.unmount`:

```js
// Before
unmountComponentAtNode(container);

// After
root.unmount();
```

Chúng tôi cũng đã xóa callback khỏi render, vì nó thường không có kết quả như mong đợi khi sử dụng Suspense:

```js
// Before
const container = document.getElementById(‘app’);
render(<App tab="home" />, container, () => {
  console.log(‘rendered’);
});

// After
function AppWithCallbackAfterRender() {
  useEffect(() => {
    console.log(‘rendered’);
  });

  return <App tab="home" />
}

const container = document.getElementById(‘app’);
const root = createRoot(container);
root.render(<AppWithCallbackAfterRender />);
```

<Note>

Không có sự thay thế một-một cho render callback API cũ - nó phụ thuộc vào use case của bạn. Xem bài đăng working group cho [Replacing render with createRoot](https://github.com/reactwg/react-18/discussions/5) để biết thêm thông tin.

</Note>

Cuối cùng, nếu ứng dụng của bạn sử dụng server-side rendering với hydration, hãy nâng cấp `hydrate` lên `hydrateRoot`:

```js
// Before
import { hydrate } from ‘react-dom’;
const container = document.getElementById(‘app’);
hydrate(<App tab="home" />, container);

// After
import { hydrateRoot } from ‘react-dom/client’;
const container = document.getElementById(‘app’);
const root = hydrateRoot(container, <App tab="home" />);
// Unlike with createRoot, you don’t need a separate root.render() call here.
```

Để biết thêm thông tin, hãy xem [thảo luận working group tại đây](https://github.com/reactwg/react-18/discussions/5).

<Note>

**Nếu ứng dụng của bạn không hoạt động sau khi nâng cấp, hãy kiểm tra xem nó có được bọc trong `<StrictMode>` không.** [Strict Mode đã trở nên nghiêm ngặt hơn trong React 18](#updates-to-strict-mode), và không phải tất cả các component của bạn có thể chịu đựng được các kiểm tra mới mà nó thêm vào ở chế độ development. Nếu việc xóa Strict Mode sửa ứng dụng của bạn, bạn có thể xóa nó trong quá trình nâng cấp, và sau đó thêm lại (ở đầu hoặc cho một phần cây) sau khi bạn sửa các vấn đề mà nó chỉ ra.

</Note>

## Cập nhật Server Rendering APIs {/*updates-to-server-rendering-apis*/}

Trong bản phát hành này, chúng tôi đang cải tổ các API `react-dom/server` của mình để hỗ trợ đầy đủ Suspense trên server và Streaming SSR. Là một phần của những thay đổi này, chúng tôi đang ngừng hỗ trợ Node streaming API cũ, không hỗ trợ incremental Suspense streaming trên server.

Sử dụng API này bây giờ sẽ cảnh báo:

* `renderToNodeStream`: **Deprecated ⛔️️**

Thay vào đó, cho streaming trong môi trường Node, hãy sử dụng:
* `renderToPipeableStream`: **New ✨**

Chúng tôi cũng đang giới thiệu một API mới để hỗ trợ streaming SSR với Suspense cho các môi trường edge runtime hiện đại, chẳng hạn như Deno và Cloudflare workers:
* `renderToReadableStream`: **New ✨**

Các API sau sẽ tiếp tục hoạt động, nhưng với hỗ trợ Suspense hạn chế:
* `renderToString`: **Limited** ⚠️
* `renderToStaticMarkup`: **Limited** ⚠️

Cuối cùng, API này sẽ tiếp tục hoạt động để render email:
* `renderToStaticNodeStream`

Để biết thêm thông tin về các thay đổi đối với server rendering APIs, hãy xem bài đăng working group về [Upgrading to React 18 on the server](https://github.com/reactwg/react-18/discussions/22), phân tích sâu về [kiến trúc Suspense SSR mới](https://github.com/reactwg/react-18/discussions/37), và bài nói của [Shaundai Person](https://twitter.com/shaundai) về [Streaming Server Rendering with Suspense](https://www.youtube.com/watch?v=pj5N-Khihgc) tại React Conf 2021.

## Cập nhật định nghĩa TypeScript {/*updates-to-typescript-definitions*/}

Nếu dự án của bạn sử dụng TypeScript, bạn sẽ cần cập nhật các dependency `@types/react` và `@types/react-dom` lên phiên bản mới nhất. Các kiểu mới an toàn hơn và bắt được các vấn đề trước đây bị bỏ qua bởi type checker. Thay đổi đáng chú ý nhất là prop `children` bây giờ cần được liệt kê rõ ràng khi định nghĩa props, ví dụ:

```typescript{3}
interface MyButtonProps {
  color: string;
  children?: React.ReactNode;
}
```

Xem [React 18 typings pull request](https://github.com/DefinitelyTyped/DefinitelyTyped/pull/56210) để có danh sách đầy đủ các thay đổi chỉ về kiểu. Nó liên kết đến các ví dụ sửa trong kiểu thư viện để bạn có thể thấy cách điều chỉnh mã của mình. Bạn có thể sử dụng [automated migration script](https://github.com/eps1lon/types-react-codemod) để giúp chuyển mã ứng dụng của bạn sang các kiểu mới và an toàn hơn nhanh hơn.

Nếu bạn tìm thấy lỗi trong các kiểu, vui lòng [gửi issue](https://github.com/DefinitelyTyped/DefinitelyTyped/discussions/new?category=issues-with-a-types-package) trong repo DefinitelyTyped.

## Automatic Batching {/*automatic-batching*/}

React 18 thêm các cải tiến hiệu suất sẵn có bằng cách thực hiện nhiều batching hơn theo mặc định. Batching là khi React nhóm nhiều cập nhật state thành một lần re-render để có hiệu suất tốt hơn. Trước React 18, chúng tôi chỉ batch các cập nhật bên trong React event handlers. Các cập nhật bên trong promises, setTimeout, native event handlers, hoặc bất kỳ sự kiện nào khác không được batch trong React theo mặc định:

```js
// Before React 18 only React events were batched

function handleClick() {
  setCount(c => c + 1);
  setFlag(f => !f);
  // React will only re-render once at the end (that’s batching!)
}

setTimeout(() => {
  setCount(c => c + 1);
  setFlag(f => !f);
  // React will render twice, once for each state update (no batching)
}, 1000);
```


Bắt đầu trong React 18 với `createRoot`, tất cả các cập nhật sẽ được tự động batch, bất kể chúng đến từ đâu. Điều này có nghĩa là các cập nhật bên trong timeouts, promises, native event handlers hoặc bất kỳ sự kiện nào khác sẽ batch theo cách giống như các cập nhật bên trong React events:

```js
// After React 18 updates inside of timeouts, promises,
// native event handlers or any other event are batched.

function handleClick() {
  setCount(c => c + 1);
  setFlag(f => !f);
  // React will only re-render once at the end (that’s batching!)
}

setTimeout(() => {
  setCount(c => c + 1);
  setFlag(f => !f);
  // React will only re-render once at the end (that’s batching!)
}, 1000);
```

Đây là một breaking change, nhưng chúng tôi kỳ vọng điều này sẽ dẫn đến ít công việc render hơn, và do đó hiệu suất tốt hơn trong ứng dụng của bạn. Để tắt automatic batching, bạn có thể sử dụng `flushSync`:

```js
import { flushSync } from ‘react-dom’;

function handleClick() {
  flushSync(() => {
    setCounter(c => c + 1);
  });
  // React has updated the DOM by now
  flushSync(() => {
    setFlag(f => !f);
  });
  // React has updated the DOM by now
}
```

Để biết thêm thông tin, hãy xem [Automatic batching deep dive](https://github.com/reactwg/react-18/discussions/21).

## API Mới cho Thư Viện {/*new-apis-for-libraries*/}

Trong React 18 Working Group chúng tôi đã làm việc với những người bảo trì thư viện để tạo ra các API mới cần thiết để hỗ trợ concurrent rendering cho các use case cụ thể của họ trong các lĩnh vực như styles và external stores. Để hỗ trợ React 18, một số thư viện có thể cần chuyển sang một trong các API sau:

* `useSyncExternalStore` là một Hook mới cho phép các external stores hỗ trợ concurrent reads bằng cách buộc các cập nhật lên store phải đồng bộ. API mới này được khuyến nghị cho bất kỳ thư viện nào tích hợp với state bên ngoài React. Để biết thêm thông tin, hãy xem [useSyncExternalStore overview post](https://github.com/reactwg/react-18/discussions/70) và [useSyncExternalStore API details](https://github.com/reactwg/react-18/discussions/86).
* `useInsertionEffect` là một Hook mới cho phép các thư viện CSS-in-JS giải quyết các vấn đề hiệu suất khi inject styles trong render. Trừ khi bạn đã xây dựng một thư viện CSS-in-JS, chúng tôi không mong bạn sẽ dùng đến điều này. Hook này sẽ chạy sau khi DOM được mutate, nhưng trước khi layout effects đọc layout mới. Điều này giải quyết một vấn đề đã tồn tại trong React 17 và các phiên bản trước, nhưng quan trọng hơn trong React 18 vì React nhường cho trình duyệt trong khi concurrent rendering, cho nó cơ hội tính toán lại layout. Để biết thêm thông tin, hãy xem [Library Upgrade Guide for `<style>`](https://github.com/reactwg/react-18/discussions/110).

React 18 cũng giới thiệu các API mới cho concurrent rendering như `startTransition`, `useDeferredValue` và `useId`, mà chúng tôi chia sẻ thêm trong [bài viết phát hành](/blog/2022/03/29/react-v18).

## Cập nhật Strict Mode {/*updates-to-strict-mode*/}

Trong tương lai, chúng tôi muốn thêm một tính năng cho phép React thêm và xóa các phần của UI trong khi bảo tồn state. Ví dụ, khi người dùng chuyển tab khỏi màn hình và quay lại, React nên có thể hiển thị ngay lập tức màn hình trước đó. Để làm điều này, React sẽ unmount và remount trees sử dụng cùng state component như trước.

Tính năng này sẽ mang lại cho React hiệu suất tốt hơn sẵn có, nhưng đòi hỏi các component phải chịu đựng được việc effects được mount và destroy nhiều lần. Hầu hết các effects sẽ hoạt động mà không có bất kỳ thay đổi nào, nhưng một số effects giả định rằng chúng chỉ được mount hoặc destroy một lần.

Để giúp phát hiện các vấn đề này, React 18 giới thiệu một kiểm tra chỉ dành cho development mới cho Strict Mode. Kiểm tra mới này sẽ tự động unmount và remount mọi component, bất cứ khi nào một component mount lần đầu, khôi phục state trước đó ở lần mount thứ hai.

Trước thay đổi này, React sẽ mount component và tạo effects:

```
* React mounts the component.
    * Layout effects are created.
    * Effect effects are created.
```

Với Strict Mode trong React 18, React sẽ giả lập unmounting và remounting component trong chế độ development:

```
* React mounts the component.
    * Layout effects are created.
    * Effect effects are created.
* React simulates unmounting the component.
    * Layout effects are destroyed.
    * Effects are destroyed.
* React simulates mounting the component with the previous state.
    * Layout effect setup code runs
    * Effect setup code runs
```

Để biết thêm thông tin, hãy xem các bài đăng Working Group cho [Adding Reusable State to StrictMode](https://github.com/reactwg/react-18/discussions/19) và [How to support Reusable State in Effects](https://github.com/reactwg/react-18/discussions/18).

## Cấu hình Môi Trường Testing {/*configuring-your-testing-environment*/}

Khi bạn lần đầu cập nhật các test để sử dụng `createRoot`, bạn có thể thấy cảnh báo này trong test console:

<ConsoleBlock level="error">

The current testing environment is not configured to support act(...)

</ConsoleBlock>

Để sửa điều này, hãy đặt `globalThis.IS_REACT_ACT_ENVIRONMENT` thành `true` trước khi chạy test:

```js
// In your test setup file
globalThis.IS_REACT_ACT_ENVIRONMENT = true;
```

Mục đích của flag là để nói với React rằng nó đang chạy trong môi trường giống như unit test. React sẽ ghi nhật ký các cảnh báo hữu ích nếu bạn quên bọc một cập nhật với `act`.

Bạn cũng có thể đặt flag thành `false` để nói với React rằng `act` không cần thiết. Điều này có thể hữu ích cho các end-to-end tests mô phỏng môi trường trình duyệt đầy đủ.

Cuối cùng, chúng tôi kỳ vọng các thư viện testing sẽ cấu hình điều này cho bạn tự động. Ví dụ, [phiên bản tiếp theo của React Testing Library có hỗ trợ tích hợp cho React 18](https://github.com/testing-library/react-testing-library/issues/509#issuecomment-917989936) mà không cần cấu hình thêm.

[Thêm thông tin nền về `act` testing API và các thay đổi liên quan](https://github.com/reactwg/react-18/discussions/102) có sẵn trong working group.

## Ngừng Hỗ Trợ Internet Explorer {/*dropping-support-for-internet-explorer*/}

Trong bản phát hành này, React đang ngừng hỗ trợ Internet Explorer, vốn [sẽ ngừng hỗ trợ vào ngày 15 tháng 6 năm 2022](https://blogs.windows.com/windowsexperience/2021/05/19/the-future-of-internet-explorer-on-windows-10-is-in-microsoft-edge). Chúng tôi thực hiện thay đổi này bây giờ vì các tính năng mới được giới thiệu trong React 18 được xây dựng bằng các tính năng trình duyệt hiện đại như microtasks không thể được polyfill đầy đủ trong IE.

Nếu bạn cần hỗ trợ Internet Explorer, chúng tôi khuyến nghị bạn ở lại với React 17.

## Ngừng hỗ trợ {/*deprecations*/}

* `react-dom`: `ReactDOM.render` đã bị ngừng hỗ trợ. Sử dụng nó sẽ cảnh báo và chạy ứng dụng của bạn ở chế độ React 17.
* `react-dom`: `ReactDOM.hydrate` đã bị ngừng hỗ trợ. Sử dụng nó sẽ cảnh báo và chạy ứng dụng của bạn ở chế độ React 17.
* `react-dom`: `ReactDOM.unmountComponentAtNode` đã bị ngừng hỗ trợ.
* `react-dom`: `ReactDOM.renderSubtreeIntoContainer` đã bị ngừng hỗ trợ.
* `react-dom/server`: `ReactDOMServer.renderToNodeStream` đã bị ngừng hỗ trợ.

## Các Breaking Changes Khác {/*other-breaking-changes*/}

* **Consistent useEffect timing**: React bây giờ luôn đồng bộ flush các hàm effect nếu cập nhật được kích hoạt trong một discrete user input event như click hoặc keydown event. Trước đây, hành vi không phải lúc nào cũng có thể đoán trước hoặc nhất quán.
* **Stricter hydration errors**: Hydration mismatches do thiếu hoặc thừa nội dung text bây giờ được xử lý như lỗi thay vì cảnh báo. React sẽ không còn cố gắng "vá" các node riêng lẻ bằng cách chèn hoặc xóa một node trên client để cố khớp với markup server, và sẽ quay lại client rendering đến `<Suspense>` boundary gần nhất trong cây. Điều này đảm bảo cây được hydrate là nhất quán và tránh các lỗ hổng bảo mật và quyền riêng tư tiềm ẩn có thể được gây ra bởi hydration mismatches.
* **Suspense trees are always consistent:** Nếu một component suspends trước khi được thêm đầy đủ vào cây, React sẽ không thêm nó vào cây ở trạng thái chưa hoàn chỉnh hoặc kích hoạt effects của nó. Thay vào đó, React sẽ loại bỏ hoàn toàn cây mới, đợi thao tác bất đồng bộ hoàn thành, và sau đó thử render lại từ đầu. React sẽ render lần thử lại đồng thời, và không chặn trình duyệt.
* **Layout Effects with Suspense**: Khi một cây re-suspends và quay lại fallback, React bây giờ sẽ dọn sạch layout effects, và sau đó tạo lại chúng khi nội dung bên trong boundary được hiển thị lại. Điều này sửa một vấn đề ngăn các thư viện component đo lường layout đúng cách khi sử dụng với Suspense.
* **New JS Environment Requirements**: React bây giờ phụ thuộc vào các tính năng trình duyệt hiện đại bao gồm `Promise`, `Symbol`, và `Object.assign`. Nếu bạn hỗ trợ các trình duyệt và thiết bị cũ như Internet Explorer không cung cấp các tính năng trình duyệt hiện đại natively hoặc có các triển khai không tuân thủ, hãy cân nhắc bao gồm một global polyfill trong ứng dụng bundled của bạn.

## Các Thay Đổi Đáng Chú Ý Khác {/*other-notable-changes*/}

### React {/*react*/}

* **Components bây giờ có thể render `undefined`:** React không còn cảnh báo nếu bạn trả về `undefined` từ một component. Điều này làm cho các giá trị trả về component được phép nhất quán với các giá trị được phép ở giữa cây component. Chúng tôi đề nghị sử dụng linter để ngăn các lỗi như quên câu lệnh `return` trước JSX.
* **In tests, `act` warnings are now opt-in:** Nếu bạn đang chạy end-to-end tests, các cảnh báo `act` là không cần thiết. Chúng tôi đã giới thiệu cơ chế [opt-in](https://github.com/reactwg/react-18/discussions/102) để bạn có thể kích hoạt chúng chỉ cho unit tests nơi chúng hữu ích và có lợi.
* **No warning about `setState` on unmounted components:** Trước đây, React cảnh báo về memory leaks khi bạn gọi `setState` trên một component đã unmounted. Cảnh báo này được thêm vào cho subscriptions, nhưng mọi người chủ yếu gặp nó trong các tình huống nơi việc đặt state là ổn, và các cách giải quyết khiến mã tệ hơn. Chúng tôi đã [xóa](https://github.com/facebook/react/pull/22114) cảnh báo này.
* **No suppression of console logs:** Khi bạn sử dụng Strict Mode, React render mỗi component hai lần để giúp bạn tìm các side effects không mong muốn. Trong React 17, chúng tôi đã suppress console logs cho một trong hai lần render để làm cho các log dễ đọc hơn. Để đáp lại [phản hồi cộng đồng](https://github.com/facebook/react/issues/21783) về điều này gây nhầm lẫn, chúng tôi đã xóa việc suppression. Thay vào đó, nếu bạn đã cài đặt React DevTools, các render của lần log thứ hai sẽ được hiển thị màu xám, và sẽ có một tùy chọn (tắt theo mặc định) để suppress chúng hoàn toàn.
* **Improved memory usage:** React bây giờ dọn sạch nhiều trường nội bộ hơn khi unmount, làm cho tác động từ các memory leaks chưa được sửa có thể tồn tại trong mã ứng dụng của bạn ít nghiêm trọng hơn.

### React DOM Server {/*react-dom-server*/}

* **`renderToString`:** Sẽ không còn báo lỗi khi suspending trên server. Thay vào đó, nó sẽ emit fallback HTML cho `<Suspense>` boundary gần nhất và sau đó thử render lại cùng nội dung trên client. Vẫn được khuyến nghị rằng bạn chuyển sang streaming API như `renderToPipeableStream` hoặc `renderToReadableStream` thay thế.
* **`renderToStaticMarkup`:** Sẽ không còn báo lỗi khi suspending trên server. Thay vào đó, nó sẽ emit fallback HTML cho `<Suspense>` boundary gần nhất.

## Changelog {/*changelog*/}

Bạn có thể xem [full changelog tại đây](https://github.com/facebook/react/blob/main/CHANGELOG.md).
