---
title: Tạo ứng dụng React
---

<Intro>

Nếu bạn muốn xây dựng một ứng dụng hoặc website mới với React, chúng tôi khuyên bạn nên bắt đầu với một framework.

</Intro>

Nếu ứng dụng của bạn có các ràng buộc không phù hợp với các framework hiện có, bạn thích xây dựng framework riêng, hoặc bạn chỉ muốn tìm hiểu những kiến thức cơ bản của ứng dụng React, bạn có thể [xây dựng ứng dụng React từ đầu](/learn/build-a-react-app-from-scratch).

## Framework full-stack {/*full-stack-frameworks*/}

Các framework được khuyến nghị này hỗ trợ tất cả các tính năng bạn cần để triển khai và mở rộng ứng dụng trong môi trường production. Chúng đã tích hợp các tính năng React mới nhất và tận dụng kiến trúc của React.

<Note>

#### Các framework full-stack không yêu cầu server. {/*react-frameworks-do-not-require-a-server*/}

Tất cả các framework trên trang này đều hỗ trợ render phía client ([CSR](https://developer.mozilla.org/en-US/docs/Glossary/CSR)), ứng dụng trang đơn ([SPA](https://developer.mozilla.org/en-US/docs/Glossary/SPA)), và tạo trang tĩnh ([SSG](https://developer.mozilla.org/en-US/docs/Glossary/SSG)). Các ứng dụng này có thể được triển khai trên [CDN](https://developer.mozilla.org/en-US/docs/Glossary/CDN) hoặc dịch vụ hosting tĩnh mà không cần server. Ngoài ra, các framework này cho phép bạn thêm render phía server trên từng route, khi phù hợp với trường hợp sử dụng của bạn.

Điều này cho phép bạn bắt đầu với ứng dụng chỉ client, và nếu nhu cầu thay đổi sau này, bạn có thể chọn sử dụng các tính năng server trên từng route riêng lẻ mà không cần viết lại ứng dụng. Xem tài liệu framework của bạn để cấu hình chiến lược render.

</Note>

### Next.js (App Router) {/*nextjs-app-router*/}

**[App Router của Next.js](https://nextjs.org/docs) là một React framework tận dụng tối đa kiến trúc của React để xây dựng ứng dụng React full-stack.**

<TerminalBlock>
npx create-next-app@latest
</TerminalBlock>

Next.js được duy trì bởi [Vercel](https://vercel.com/). Bạn có thể [triển khai ứng dụng Next.js](https://nextjs.org/docs/app/building-your-application/deploying) lên bất kỳ nhà cung cấp hosting nào hỗ trợ Node.js hoặc Docker container, hoặc lên server riêng của bạn. Next.js cũng hỗ trợ [xuất tĩnh](https://nextjs.org/docs/app/building-your-application/deploying/static-exports) mà không yêu cầu server.

### React Router (v7) {/*react-router-v7*/}

**[React Router](https://reactrouter.com/start/framework/installation) là thư viện routing phổ biến nhất cho React và có thể kết hợp với Vite để tạo một React framework full-stack**. Nó nhấn mạnh các Web API tiêu chuẩn và có nhiều [template sẵn sàng triển khai](https://github.com/remix-run/react-router-templates) cho các JavaScript runtime và nền tảng khác nhau.

Để tạo dự án React Router framework mới, chạy:

<TerminalBlock>
npx create-react-router@latest
</TerminalBlock>

React Router được duy trì bởi [Shopify](https://www.shopify.com).

### Expo (cho ứng dụng native) {/*expo*/}

**[Expo](https://expo.dev/) là một React framework cho phép bạn tạo các ứng dụng Android, iOS và web đa nền tảng với giao diện native thực sự.** Nó cung cấp SDK cho [React Native](https://reactnative.dev/) giúp sử dụng các phần native dễ dàng hơn. Để tạo dự án Expo mới, chạy:

<TerminalBlock>
npx create-expo-app@latest
</TerminalBlock>

Nếu bạn mới dùng Expo, hãy xem [hướng dẫn Expo](https://docs.expo.dev/tutorial/introduction/).

Expo được duy trì bởi [Expo (công ty)](https://expo.dev/about). Xây dựng ứng dụng với Expo là miễn phí, và bạn có thể đăng chúng lên Google và Apple app store mà không có hạn chế. Expo cũng cung cấp các dịch vụ đám mây trả phí tùy chọn.


## Các framework khác {/*other-frameworks*/}

Có các framework đang phát triển khác đang hướng tới tầm nhìn React full-stack của chúng tôi:

- [TanStack Start (Beta)](https://tanstack.com/start/): TanStack Start là một React framework full-stack được hỗ trợ bởi TanStack Router. Nó cung cấp SSR toàn trang, streaming, server function, bundling, và nhiều tính năng khác sử dụng các công cụ như Nitro và Vite.
- [RedwoodSDK](https://rwsdk.com/): Redwood là một React framework full-stack với nhiều package và cấu hình được cài đặt sẵn giúp dễ dàng xây dựng các ứng dụng web full-stack.

<DeepDive>

#### Những tính năng nào tạo nên tầm nhìn kiến trúc full-stack của đội ngũ React? {/*which-features-make-up-the-react-teams-full-stack-architecture-vision*/}

Bundler App Router của Next.js triển khai đầy đủ [đặc tả React Server Components](https://github.com/reactjs/rfcs/blob/main/text/0188-server-components.md) chính thức. Điều này cho phép bạn kết hợp các component build-time, server-only và tương tác trong cùng một cây React.

Ví dụ, bạn có thể viết một React component chỉ chạy trên server dưới dạng hàm `async` đọc từ cơ sở dữ liệu hoặc file. Sau đó bạn có thể truyền dữ liệu xuống các component tương tác:

```js
// Component này chạy *chỉ* trên server (hoặc trong quá trình build).
async function Talks({ confId }) {
  // 1. Bạn đang trên server, nên có thể giao tiếp với tầng dữ liệu. Không cần API endpoint.
  const talks = await db.Talks.findAll({ confId });

  // 2. Thêm bất kỳ logic render nào. Nó sẽ không làm tăng kích thước JavaScript bundle.
  const videos = talks.map(talk => talk.video);

  // 3. Truyền dữ liệu xuống các component sẽ chạy trong trình duyệt.
  return <SearchableVideoList videos={videos} />;
}
```

App Router của Next.js cũng tích hợp [data fetching với Suspense](/blog/2022/03/29/react-v18#suspense-in-data-frameworks). Điều này cho phép bạn chỉ định trạng thái loading (như placeholder dạng skeleton) cho các phần khác nhau của giao diện người dùng trực tiếp trong cây React:

```js
<Suspense fallback={<TalksLoading />}>
  <Talks confId={conf.id} />
</Suspense>
```

Server Components và Suspense là các tính năng của React chứ không phải của Next.js. Tuy nhiên, việc áp dụng chúng ở cấp framework đòi hỏi sự đầu tư và công việc triển khai không nhỏ. Hiện tại, Next.js App Router là triển khai hoàn chỉnh nhất. Đội ngũ React đang làm việc với các nhà phát triển bundler để làm cho các tính năng này dễ triển khai hơn trong thế hệ framework tiếp theo.

</DeepDive>

## Bắt đầu từ đầu {/*start-from-scratch*/}

Nếu ứng dụng của bạn có các ràng buộc không phù hợp với các framework hiện có, bạn thích xây dựng framework riêng, hoặc bạn chỉ muốn tìm hiểu những kiến thức cơ bản của ứng dụng React, có các tùy chọn khác để bắt đầu dự án React từ đầu.

Bắt đầu từ đầu cho bạn nhiều linh hoạt hơn, nhưng yêu cầu bạn phải đưa ra lựa chọn về công cụ nào sử dụng cho routing, data fetching, và các pattern sử dụng phổ biến khác. Nó giống như xây dựng framework riêng, thay vì sử dụng framework đã có sẵn. Các [framework chúng tôi khuyến nghị](#full-stack-frameworks) đã có sẵn giải pháp cho những vấn đề này.

Nếu bạn muốn xây dựng giải pháp riêng, xem hướng dẫn [xây dựng ứng dụng React từ đầu](/learn/build-a-react-app-from-scratch) để biết cách thiết lập dự án React mới bắt đầu với công cụ build như [Vite](https://vite.dev/), [Parcel](https://parceljs.org/), hoặc [RSbuild](https://rsbuild.dev/).

-----

_Nếu bạn là tác giả framework muốn được đưa vào trang này, [vui lòng cho chúng tôi biết](https://github.com/reactjs/react.dev/issues/new?assignees=&labels=type%3A+framework&projects=&template=3-framework.yml&title=%5BFramework%5D%3A+)._
