---
title: "Sunsetting Create React App"
author: Matt Carroll and Ricky Hanlon
date: 2025/02/14
description: Hôm nay, chúng tôi đang deprecated Create React App cho các ứng dụng mới và khuyến khích các ứng dụng hiện có chuyển sang một framework, hoặc chuyển sang một build tool như Vite, Parcel, hoặc RSBuild. Chúng tôi cũng cung cấp tài liệu cho khi một framework không phù hợp với dự án của bạn, bạn muốn xây dựng framework riêng, hoặc bạn chỉ muốn học cách React hoạt động bằng cách xây dựng ứng dụng React từ đầu.
---

February 14, 2025 by [Matt Carroll](https://twitter.com/mattcarrollcode) and [Ricky Hanlon](https://bsky.app/profile/ricky.fm)

---

<Intro>

Hôm nay, chúng tôi đang deprecated [Create React App](https://create-react-app.dev/) cho các ứng dụng mới, và khuyến khích các ứng dụng hiện có chuyển sang một [framework](#how-to-migrate-to-a-framework), hoặc [chuyển sang một build tool](#how-to-migrate-to-a-build-tool) như Vite, Parcel, hoặc RSBuild.

Chúng tôi cũng cung cấp tài liệu cho khi một framework không phù hợp với dự án của bạn, bạn muốn xây dựng framework riêng, hoặc bạn chỉ muốn học cách React hoạt động bằng cách [xây dựng ứng dụng React từ đầu](/learn/build-a-react-app-from-scratch).

</Intro>

-----

Khi chúng tôi phát hành Create React App vào năm 2016, không có cách rõ ràng nào để xây dựng một ứng dụng React mới.

Để tạo một ứng dụng React, bạn phải cài đặt nhiều công cụ và kết nối chúng lại với nhau để hỗ trợ các tính năng cơ bản như JSX, linting, và hot reloading. Điều này rất khó để làm đúng, vì vậy [cộng đồng](https://github.com/react-boilerplate/react-boilerplate) [đã](https://github.com/kriasoft/react-starter-kit) [tạo](https://github.com/petehunt/react-boilerplate) [boilerplates](https://github.com/gaearon/react-hot-boilerplate) cho [các](https://github.com/erikras/react-redux-universal-hot-example) thiết lập phổ biến. Tuy nhiên, boilerplates khó cập nhật và sự phân mảnh làm cho React khó phát hành các tính năng mới.

Create React App đã giải quyết các vấn đề này bằng cách kết hợp nhiều công cụ vào một cấu hình được khuyến nghị duy nhất. Điều này cho phép các ứng dụng có cách đơn giản để nâng cấp lên các tính năng công cụ mới, và cho phép nhóm React triển khai các thay đổi công cụ phức tạp (hỗ trợ Fast Refresh, quy tắc lint React Hooks) đến đông đảo khán giả nhất có thể.

Mô hình này đã trở nên phổ biến đến mức ngày nay có cả một danh mục công cụ hoạt động theo cách này.

## Deprecated Create React App {/*deprecating-create-react-app*/}

Mặc dù Create React App giúp bắt đầu dễ dàng, [có một số hạn chế](#limitations-of-build-tools) làm cho việc xây dựng các ứng dụng production hiệu suất cao trở nên khó khăn. Về nguyên tắc, chúng tôi có thể giải quyết các vấn đề này bằng cách cơ bản phát triển nó thành một [framework](#why-we-recommend-frameworks).

Tuy nhiên, vì Create React App hiện không có người bảo trì tích cực, và đã có nhiều framework hiện có giải quyết các vấn đề này rồi, chúng tôi đã quyết định deprecated Create React App.

Bắt đầu từ hôm nay, nếu bạn cài đặt một ứng dụng mới, bạn sẽ thấy cảnh báo deprecated:

<ConsoleBlockMulti>
<ConsoleLogLine level="error">

create-react-app is deprecated.
{'\n\n'}
You can find a list of up-to-date React frameworks on react.dev
For more info see: react.dev/link/cra
{'\n\n'}
This error message will only be shown once per install.

</ConsoleLogLine>
</ConsoleBlockMulti>

Chúng tôi cũng đã thêm thông báo deprecated vào [website](https://create-react-app.dev/) và GitHub [repo](https://github.com/facebook/create-react-app) của Create React App. Create React App sẽ tiếp tục hoạt động trong chế độ maintenance, và chúng tôi đã xuất bản phiên bản mới của Create React App để hoạt động với React 19.

## Cách chuyển sang Framework {/*how-to-migrate-to-a-framework*/}
Chúng tôi khuyến nghị [tạo các ứng dụng React mới](/learn/creating-a-react-app) với một framework. Tất cả các framework chúng tôi khuyến nghị đều hỗ trợ client-side rendering ([CSR](https://developer.mozilla.org/en-US/docs/Glossary/CSR)) và single-page apps ([SPA](https://developer.mozilla.org/en-US/docs/Glossary/SPA)), và có thể được triển khai lên CDN hoặc dịch vụ lưu trữ tĩnh mà không cần server.

Đối với các ứng dụng hiện có, các hướng dẫn này sẽ giúp bạn chuyển sang SPA chỉ client:

* [Hướng dẫn migration Create React App của Next.js](https://nextjs.org/docs/app/building-your-application/upgrading/from-create-react-app)
* [Hướng dẫn áp dụng framework của React Router](https://reactrouter.com/upgrading/component-routes).
* [Hướng dẫn migration từ Expo webpack sang Expo Router](https://docs.expo.dev/router/migrate/from-expo-webpack/)

## Cách chuyển sang Build Tool {/*how-to-migrate-to-a-build-tool*/}

Nếu ứng dụng của bạn có các ràng buộc bất thường, hoặc bạn muốn giải quyết các vấn đề này bằng cách xây dựng framework riêng, hoặc bạn chỉ muốn học cách React hoạt động từ đầu, bạn có thể tạo thiết lập tùy chỉnh riêng với React sử dụng Vite, Parcel hoặc Rsbuild.

Đối với các ứng dụng hiện có, các hướng dẫn này sẽ giúp bạn chuyển sang build tool:

* [Hướng dẫn migration Vite Create React App](https://www.robinwieruch.de/vite-create-react-app/)
* [Hướng dẫn migration Parcel Create React App](https://parceljs.org/migration/cra/)
* [Hướng dẫn migration Rsbuild Create React App](https://rsbuild.dev/guide/migration/cra)

Để giúp bắt đầu với Vite, Parcel hoặc Rsbuild, chúng tôi đã thêm tài liệu mới cho [Xây dựng ứng dụng React từ đầu](/learn/build-a-react-app-from-scratch).

<DeepDive>

#### Tôi có cần framework không? {/*do-i-need-a-framework*/}

Hầu hết các ứng dụng sẽ được hưởng lợi từ framework, nhưng có những trường hợp hợp lệ để xây dựng ứng dụng React từ đầu. Một quy tắc tốt là nếu ứng dụng của bạn cần routing, bạn có thể sẽ được hưởng lợi từ framework.

Giống như Svelte có Sveltekit, Vue có Nuxt, và Solid có SolidStart, [React khuyến nghị sử dụng framework](#why-we-recommend-frameworks) tích hợp đầy đủ routing vào các tính năng như data-fetching và code-splitting ngay từ đầu. Điều này tránh được sự đau đớn khi cần viết các cấu hình phức tạp của riêng bạn và về cơ bản tự xây dựng một framework.

Tuy nhiên, bạn luôn có thể [xây dựng ứng dụng React từ đầu](/learn/build-a-react-app-from-scratch) sử dụng build tool như Vite, Parcel, hoặc Rsbuild.

</DeepDive>

Tiếp tục đọc để tìm hiểu thêm về [hạn chế của build tools](#limitations-of-build-tools) và [lý do chúng tôi khuyến nghị frameworks](#why-we-recommend-frameworks).

## Hạn chế của Build Tools {/*limitations-of-build-tools*/}

Create React App và các build tools tương tự giúp bắt đầu xây dựng ứng dụng React dễ dàng. Sau khi chạy `npx create-react-app my-app`, bạn có được một ứng dụng React được cấu hình đầy đủ với server phát triển, linting, và production build.

Ví dụ, nếu bạn đang xây dựng một công cụ quản trị nội bộ, bạn có thể bắt đầu với landing page:

```js
export default function App() {
  return (
    <div>
      <h1>Welcome to the Admin Tool!</h1>
    </div>
  )
}
```

Điều này cho phép bạn ngay lập tức bắt đầu code trong React với các tính năng như JSX, các quy tắc linting mặc định, và một bundler để chạy cả trong phát triển và production. Tuy nhiên, thiết lập này thiếu các công cụ bạn cần để xây dựng một ứng dụng production thực sự.

Hầu hết các ứng dụng production cần các giải pháp cho các vấn đề như routing, data fetching, và code splitting.

### Routing {/*routing*/}

Create React App không bao gồm một giải pháp routing cụ thể. Nếu bạn mới bắt đầu, một tùy chọn là sử dụng `useState` để chuyển đổi giữa các route. Nhưng làm điều này có nghĩa là bạn không thể chia sẻ liên kết đến ứng dụng của mình - mọi liên kết đều sẽ đi đến cùng một trang - và cấu trúc ứng dụng của bạn trở nên khó khăn theo thời gian:

```js
import {useState} from 'react';

import Home from './Home';
import Dashboard from './Dashboard';

export default function App() {
  // ❌ Routing in state does not create URLs
  const [route, setRoute] = useState('home');
  return (
    <div>
      {route === 'home' && <Home />}
      {route === 'dashboard' && <Dashboard />}
    </div>
  )
}
```

Đây là lý do tại sao hầu hết các ứng dụng sử dụng Create React App giải quyết bằng cách thêm routing với một thư viện routing như [React Router](https://reactrouter.com/) hoặc [Tanstack Router](https://tanstack.com/router/latest). Với thư viện routing, bạn có thể thêm các route bổ sung vào ứng dụng, cung cấp ý kiến về cấu trúc ứng dụng của bạn, và cho phép bạn bắt đầu chia sẻ liên kết đến các route. Ví dụ, với React Router bạn có thể định nghĩa các route:

```js
import {RouterProvider, createBrowserRouter} from 'react-router';

import Home from './Home';
import Dashboard from './Dashboard';

// ✅ Each route has it's own URL
const router = createBrowserRouter([
  {path: '/', element: <Home />},
  {path: '/dashboard', element: <Dashboard />}
]);

export default function App() {
  return (
    <RouterProvider value={router} />
  )
}
```

Với thay đổi này, bạn có thể chia sẻ liên kết đến `/dashboard` và ứng dụng sẽ điều hướng đến trang dashboard. Một khi bạn có thư viện routing, bạn có thể thêm các tính năng bổ sung như nested routes, route guards, và route transitions, vốn khó thực hiện mà không có thư viện routing.

Có một sự đánh đổi được thực hiện ở đây: thư viện routing thêm độ phức tạp cho ứng dụng, nhưng nó cũng thêm các tính năng khó thực hiện nếu không có nó.

### Data Fetching {/*data-fetching*/}

Một vấn đề phổ biến khác trong Create React App là data fetching. Create React App không bao gồm một giải pháp data fetching cụ thể. Nếu bạn mới bắt đầu, một tùy chọn phổ biến là sử dụng `fetch` trong một effect để tải dữ liệu.

Nhưng làm điều này có nghĩa là dữ liệu được fetch sau khi component render, có thể gây ra network waterfalls. Network waterfalls được gây ra bởi việc fetch dữ liệu khi ứng dụng của bạn render thay vì song song trong khi code đang tải xuống:

```js
export default function Dashboard() {
  const [data, setData] = useState(null);

  // ❌ Fetching data in a component causes network waterfalls
  useEffect(() => {
    fetch('/api/data')
      .then(response => response.json())
      .then(data => setData(data));
  }, []);

  return (
    <div>
      {data.map(item => <div key={item.id}>{item.name}</div>)}
    </div>
  )
}
```

Fetching trong effect có nghĩa là người dùng phải chờ lâu hơn để thấy nội dung, mặc dù dữ liệu có thể đã được fetch sớm hơn. Để giải quyết điều này, bạn có thể sử dụng thư viện data fetching như [TanStack Query](https://tanstack.com/query/), [SWR](https://swr.vercel.app/), [Apollo](https://www.apollographql.com/docs/react), hoặc [Relay](https://relay.dev/) cung cấp các tùy chọn để prefetch dữ liệu để yêu cầu được bắt đầu trước khi component render.

Các thư viện này hoạt động tốt nhất khi được tích hợp với mẫu "loader" routing của bạn để chỉ định các phụ thuộc dữ liệu ở cấp route, cho phép router tối ưu hóa các lần fetch dữ liệu của bạn:

```js
export async function loader() {
  const response = await fetch(`/api/data`);
  const data = await response.json();
  return data;
}

// ✅ Fetching data in parallel while the code is downloading
export default function Dashboard({loaderData}) {
  return (
    <div>
      {loaderData.map(item => <div key={item.id}>{item.name}</div>)}
    </div>
  )
}
```

Tại lần tải ban đầu, router có thể fetch dữ liệu ngay trước khi route được render. Khi người dùng điều hướng xung quanh ứng dụng, router có thể fetch cả dữ liệu và route cùng lúc, song song hóa các lần fetch. Điều này giảm thời gian cần để thấy nội dung trên màn hình và có thể cải thiện trải nghiệm người dùng.

Tuy nhiên, điều này yêu cầu cấu hình đúng các loader trong ứng dụng của bạn và đánh đổi sự phức tạp lấy hiệu suất.

### Code Splitting {/*code-splitting*/}

Một vấn đề phổ biến khác trong Create React App là [code splitting](https://www.patterns.dev/vanilla/bundle-splitting). Create React App không bao gồm một giải pháp code splitting cụ thể. Nếu bạn mới bắt đầu, bạn có thể hoàn toàn không xem xét code splitting.

Điều này có nghĩa là ứng dụng của bạn được giao như một bundle duy nhất:

```txt
- bundle.js    75kb
```

Nhưng để có hiệu suất lý tưởng, bạn nên "tách" code của mình thành các bundle riêng biệt để người dùng chỉ cần tải xuống những gì họ cần. Điều này giảm thời gian người dùng cần đợi để tải ứng dụng của bạn, bằng cách chỉ tải xuống code họ cần để xem trang họ đang ở.

```txt
- core.js      25kb
- home.js      25kb
- dashboard.js 25kb
```

Một cách để thực hiện code-splitting là với `React.lazy`. Tuy nhiên, điều này có nghĩa là code không được fetch cho đến khi component render, điều này có thể gây ra network waterfalls. Một giải pháp tối ưu hơn là sử dụng tính năng router fetch code song song trong khi code đang tải xuống. Ví dụ, React Router cung cấp tùy chọn `lazy` để chỉ định rằng một route nên được code split và tối ưu hóa khi nó được tải:

```js
import Home from './Home';
import Dashboard from './Dashboard';

// ✅ Routes are downloaded before rendering
const router = createBrowserRouter([
  {path: '/', lazy: () => import('./Home')},
  {path: '/dashboard', lazy: () => import('Dashboard')}
]);
```

Code-splitting được tối ưu hóa rất khó để làm đúng, và dễ mắc lỗi có thể khiến người dùng tải xuống nhiều code hơn mức cần thiết. Nó hoạt động tốt nhất khi được tích hợp với các giải pháp router và tải dữ liệu của bạn để tối đa hóa caching, song song hóa các lần fetch, và hỗ trợ các mẫu ["import on interaction"](https://www.patterns.dev/vanilla/import-on-interaction).

### Và nhiều hơn nữa... {/*and-more*/}

Đây chỉ là một vài ví dụ về hạn chế của Create React App.

Một khi bạn đã tích hợp routing, data-fetching, và code splitting, bạn cũng cần xem xét các trạng thái pending, ngắt navigation, thông báo lỗi cho người dùng, và revalidation dữ liệu. Có những danh mục vấn đề hoàn chỉnh mà người dùng cần giải quyết như:

<div style={{display: 'flex', width: '100%', justifyContent: 'space-around'}}>
  <ul>
    <li>Accessibility</li>
    <li>Asset loading</li>
    <li>Authentication</li>
    <li>Caching</li>
  </ul>
  <ul>
    <li>Error handling</li>
    <li>Mutating data</li>
    <li>Navigations</li>
    <li>Optimistic updates</li>
  </ul>
  <ul>
    <li>Progressive enhancement</li>
    <li>Server-side rendering</li>
    <li>Static site generation</li>
    <li>Streaming</li>
  </ul>
</div>

Tất cả những điều này hoạt động cùng nhau để tạo ra [loading sequence](https://www.patterns.dev/vanilla/loading-sequence) tối ưu nhất.

Việc giải quyết từng vấn đề này riêng lẻ trong Create React App có thể khó khăn vì mỗi vấn đề liên kết với nhau và có thể yêu cầu chuyên môn sâu trong các lĩnh vực mà người dùng có thể không quen thuộc. Để giải quyết các vấn đề này, người dùng cuối cùng xây dựng các giải pháp riêng của họ trên Create React App, đây chính là vấn đề mà Create React App ban đầu cố gắng giải quyết.

## Tại sao chúng tôi khuyến nghị Frameworks {/*why-we-recommend-frameworks*/}

Mặc dù bạn có thể tự giải quyết tất cả các phần này trong một build tool như Create React App, Vite, hoặc Parcel, nhưng rất khó để làm tốt. Giống như khi Create React App tự tích hợp nhiều build tool lại với nhau, bạn cần một công cụ để tích hợp tất cả các tính năng này lại với nhau để cung cấp trải nghiệm tốt nhất cho người dùng.

Danh mục công cụ này tích hợp build tools, rendering, routing, data fetching, và code splitting được gọi là "frameworks" -- hoặc nếu bạn muốn gọi React là một framework, bạn có thể gọi chúng là "metaframeworks".

Frameworks áp đặt một số ý kiến về cách cấu trúc ứng dụng của bạn để cung cấp trải nghiệm người dùng tốt hơn nhiều, giống như cách build tools áp đặt một số ý kiến để làm cho tooling dễ dàng hơn. Đây là lý do tại sao chúng tôi bắt đầu khuyến nghị các framework như [Next.js](https://nextjs.org/), [React Router](https://reactrouter.com/), và [Expo](https://expo.dev/) cho các dự án mới.

Frameworks cung cấp trải nghiệm bắt đầu tương tự như Create React App, nhưng cũng cung cấp các giải pháp cho các vấn đề mà người dùng cần giải quyết trong các ứng dụng production thực sự.

<DeepDive>

#### Server rendering là tùy chọn {/*server-rendering-is-optional*/}

Tất cả các framework chúng tôi khuyến nghị đều cung cấp tùy chọn để tạo ứng dụng [client-side rendered (CSR)](https://developer.mozilla.org/en-US/docs/Glossary/CSR).

Trong một số trường hợp, CSR là lựa chọn đúng cho một trang, nhưng nhiều khi không phải vậy. Ngay cả khi hầu hết ứng dụng của bạn là phía client, thường có các trang riêng lẻ có thể được hưởng lợi từ các tính năng server rendering như [static-site generation (SSG)](https://developer.mozilla.org/en-US/docs/Glossary/SSG) hoặc [server-side rendering (SSR)](https://developer.mozilla.org/en-US/docs/Glossary/SSR), ví dụ trang Điều khoản dịch vụ, hoặc tài liệu.

Server rendering thường gửi ít JavaScript hơn đến client, và một tài liệu HTML đầy đủ tạo ra [First Contentful Paint (FCP)](https://web.dev/articles/fcp) nhanh hơn bằng cách giảm [Total Blocking Time (TBD)](https://web.dev/articles/tbt), cũng có thể giảm [Interaction to Next Paint (INP)](https://web.dev/articles/inp). Đây là lý do tại sao [nhóm Chrome đã khuyến khích](https://web.dev/articles/rendering-on-the-web) các nhà phát triển xem xét render tĩnh hoặc phía server hơn là tiếp cận hoàn toàn phía client để đạt hiệu suất tốt nhất có thể.

Có những đánh đổi khi sử dụng server, và không phải lúc nào cũng là lựa chọn tốt nhất cho mọi trang. Tạo các trang trên server phát sinh chi phí bổ sung và mất thời gian để tạo có thể tăng [Time to First Byte (TTFB)](https://web.dev/articles/ttfb). Các ứng dụng có hiệu suất tốt nhất có thể chọn chiến lược rendering phù hợp trên cơ sở từng trang, dựa trên sự đánh đổi của mỗi chiến lược.

Frameworks cung cấp tùy chọn sử dụng server trên bất kỳ trang nào nếu bạn muốn, nhưng không ép buộc bạn sử dụng server. Điều này cho phép bạn chọn chiến lược rendering phù hợp cho mỗi trang trong ứng dụng của bạn.

#### Còn Server Components thì sao {/*server-components*/}

Các framework chúng tôi khuyến nghị cũng bao gồm hỗ trợ cho React Server Components.

Server Components giúp giải quyết các vấn đề này bằng cách chuyển routing và data fetching lên server, và cho phép code splitting được thực hiện cho các client component dựa trên dữ liệu bạn render, thay vì chỉ route được render, và giảm lượng JavaScript được giao cho [loading sequence](https://www.patterns.dev/vanilla/loading-sequence) tốt nhất có thể.

Server Components không yêu cầu server. Chúng có thể chạy tại build time trên CI server của bạn để tạo ứng dụng static-site generated (SSG), tại runtime trên web server cho ứng dụng server-side rendered (SSR).

Xem [Giới thiệu React Server Components có kích thước bundle bằng không](/blog/2020/12/21/data-fetching-with-react-server-components) và [tài liệu](/reference/rsc/server-components) để biết thêm thông tin.

</DeepDive>

<Note>

#### Server Rendering không chỉ dành cho SEO {/*server-rendering-is-not-just-for-seo*/}

Một hiểu lầm phổ biến là server rendering chỉ dành cho [SEO](https://developer.mozilla.org/en-US/docs/Glossary/SEO).

Trong khi server rendering có thể cải thiện SEO, nó cũng cải thiện hiệu suất bằng cách giảm lượng JavaScript người dùng cần tải xuống và phân tích trước khi họ có thể thấy nội dung trên màn hình.

Đây là lý do tại sao nhóm Chrome [đã khuyến khích](https://web.dev/articles/rendering-on-the-web) các nhà phát triển xem xét render tĩnh hoặc phía server hơn là tiếp cận hoàn toàn phía client để đạt hiệu suất tốt nhất có thể.

</Note>

---

_Cảm ơn [Dan Abramov](https://bsky.app/profile/danabra.mov) đã tạo ra Create React App, và [Joe Haddad](https://github.com/Timer), [Ian Schmitz](https://github.com/ianschmitz), [Brody McKee](https://github.com/mrmckeb), và [nhiều người khác](https://github.com/facebook/create-react-app/graphs/contributors) đã duy trì Create React App trong nhiều năm. Cảm ơn [Brooks Lybrand](https://bsky.app/profile/brookslybrand.bsky.social), [Dan Abramov](https://bsky.app/profile/danabra.mov), [Devon Govett](https://bsky.app/profile/devongovett.bsky.social), [Eli White](https://x.com/Eli_White), [Jack Herrington](https://bsky.app/profile/jherr.dev), [Joe Savona](https://x.com/en_JS), [Lauren Tan](https://bsky.app/profile/no.lol), [Lee Robinson](https://x.com/leeerob), [Mark Erikson](https://bsky.app/profile/acemarke.dev), [Ryan Florence](https://x.com/ryanflorence), [Sophie Alpert](https://bsky.app/profile/sophiebits.com), [Tanner Linsley](https://bsky.app/profile/tannerlinsley.com), và [Theo Browne](https://x.com/theo) đã review và cung cấp phản hồi cho bài viết này._

