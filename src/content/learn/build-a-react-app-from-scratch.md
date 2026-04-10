---
title: Xây dựng ứng dụng React từ đầu
---

<Intro>

Nếu ứng dụng của bạn có các ràng buộc mà các framework hiện tại không đáp ứng tốt, bạn muốn tự xây dựng framework riêng, hoặc bạn chỉ muốn tìm hiểu những kiến thức cơ bản của một ứng dụng React, bạn có thể xây dựng ứng dụng React từ đầu.

</Intro>

<DeepDive>

#### Cân nhắc sử dụng framework {/*consider-using-a-framework*/}

Bắt đầu từ đầu là cách dễ dàng để bắt đầu sử dụng React, nhưng một sự đánh đổi lớn cần lưu ý là con đường này thường giống như tự xây dựng framework ad-hoc của riêng bạn. Khi yêu cầu phát triển, bạn có thể cần giải quyết nhiều vấn đề giống framework mà các framework được đề xuất của chúng tôi đã có các giải pháp được phát triển tốt và hỗ trợ đầy đủ.

Ví dụ, nếu trong tương lai ứng dụng của bạn cần hỗ trợ server-side rendering (SSR), static site generation (SSG), và/hoặc React Server Components (RSC), bạn sẽ phải tự triển khai những thứ đó. Tương tự, các tính năng React trong tương lai yêu cầu tích hợp ở cấp framework sẽ phải được bạn tự triển khai nếu muốn sử dụng.

Các framework được đề xuất cũng giúp bạn xây dựng ứng dụng có hiệu suất tốt hơn. Ví dụ, giảm hoặc loại bỏ waterfall từ các request mạng tạo ra trải nghiệm người dùng tốt hơn. Điều này có thể không phải ưu tiên cao khi bạn đang xây dựng dự án thử nghiệm, nhưng nếu ứng dụng của bạn có người dùng, bạn có thể muốn cải thiện hiệu suất.

Đi theo con đường này cũng khiến việc nhận hỗ trợ khó khăn hơn, vì cách bạn phát triển routing, data-fetching và các tính năng khác sẽ là duy nhất cho tình huống của bạn. Bạn chỉ nên chọn tùy chọn này nếu bạn thoải mái giải quyết các vấn đề này một mình, hoặc nếu bạn tự tin rằng bạn sẽ không bao giờ cần các tính năng này.

Để xem danh sách các framework được đề xuất, hãy kiểm tra [Tạo ứng dụng React](/learn/creating-a-react-app).

</DeepDive>


## Bước 1: Cài đặt công cụ build {/*step-1-install-a-build-tool*/}

Bước đầu tiên là cài đặt một công cụ build như `vite`, `parcel`, hoặc `rsbuild`. Các công cụ build này cung cấp các tính năng để đóng gói và chạy mã nguồn, cung cấp máy chủ phát triển cho phát triển cục bộ và lệnh build để triển khai ứng dụng lên máy chủ production.

### Vite {/*vite*/}

[Vite](https://vite.dev/) là một công cụ build nhằm cung cấp trải nghiệm phát triển nhanh hơn và gọn nhẹ hơn cho các dự án web hiện đại.

<TerminalBlock>
npm create vite@latest my-app -- --template react-ts
</TerminalBlock>

Vite có quan điểm rõ ràng và đi kèm với các cài đặt mặc định hợp lý. Vite có một hệ sinh thái plugin phong phú để hỗ trợ fast refresh, JSX, Babel/SWC và các tính năng phổ biến khác. Xem [React plugin](https://vite.dev/plugins/#vitejs-plugin-react) hoặc [React SWC plugin](https://vite.dev/plugins/#vitejs-plugin-react-swc) của Vite và [dự án ví dụ React SSR](https://vite.dev/guide/ssr.html#example-projects) để bắt đầu.

Vite đã được sử dụng làm công cụ build trong một trong các [framework được đề xuất](/learn/creating-a-react-app) của chúng tôi: [React Router](https://reactrouter.com/start/framework/installation).

### Parcel {/*parcel*/}

[Parcel](https://parceljs.org/) kết hợp trải nghiệm phát triển tuyệt vời ngay từ đầu với kiến trúc có thể mở rộng, có thể đưa dự án của bạn từ giai đoạn bắt đầu đến các ứng dụng production quy mô lớn.

<TerminalBlock>
npm install --save-dev parcel
</TerminalBlock>

Parcel hỗ trợ fast refresh, JSX, TypeScript, Flow và styling ngay từ đầu. Xem [công thức React của Parcel](https://parceljs.org/recipes/react/#getting-started) để bắt đầu.

### Rsbuild {/*rsbuild*/}

[Rsbuild](https://rsbuild.dev/) là công cụ build dựa trên Rspack cung cấp trải nghiệm phát triển liền mạch cho các ứng dụng React. Nó đi kèm với các cài đặt mặc định được tinh chỉnh cẩn thận và tối ưu hóa hiệu suất sẵn sàng sử dụng.

<TerminalBlock>
npx create-rsbuild --template react
</TerminalBlock>

Rsbuild bao gồm hỗ trợ tích hợp cho các tính năng React như fast refresh, JSX, TypeScript và styling. Xem [hướng dẫn React của Rsbuild](https://rsbuild.dev/guide/framework/react) để bắt đầu.

<Note>

#### Metro cho React Native {/*react-native*/}

Nếu bạn bắt đầu từ đầu với React Native, bạn sẽ cần sử dụng [Metro](https://metrobundler.dev/), trình đóng gói JavaScript cho React Native. Metro hỗ trợ đóng gói cho các nền tảng như iOS và Android, nhưng thiếu nhiều tính năng so với các công cụ ở đây. Chúng tôi khuyến nghị bắt đầu với Vite, Parcel hoặc Rsbuild trừ khi dự án của bạn yêu cầu hỗ trợ React Native.

</Note>

## Bước 2: Xây dựng các mẫu ứng dụng phổ biến {/*step-2-build-common-application-patterns*/}

Các công cụ build được liệt kê ở trên bắt đầu với ứng dụng single-page (SPA) chỉ chạy trên client, nhưng không bao gồm các giải pháp thêm cho các chức năng phổ biến như routing, data fetching, hoặc styling.

Hệ sinh thái React bao gồm nhiều công cụ cho các vấn đề này. Chúng tôi đã liệt kê một số được sử dụng rộng rãi như điểm khởi đầu, nhưng bạn có thể tự do chọn các công cụ khác nếu chúng phù hợp hơn với bạn.

### Routing {/*routing*/}

Routing xác định nội dung hoặc trang nào sẽ hiển thị khi người dùng truy cập một URL cụ thể. Bạn cần thiết lập router để ánh xạ URL đến các phần khác nhau của ứng dụng. Bạn cũng cần xử lý các route lồng nhau, tham số route và tham số query. Router có thể được cấu hình trong code của bạn, hoặc được định nghĩa dựa trên cấu trúc thư mục và file component.

Router là phần cốt lõi của các ứng dụng hiện đại, và thường được tích hợp với data fetching (bao gồm prefetching dữ liệu cho toàn bộ trang để tải nhanh hơn), code splitting (để giảm thiểu kích thước bundle client), và các phương pháp render trang (để quyết định cách mỗi trang được tạo ra).

Chúng tôi gợi ý sử dụng:

- [React Router](https://reactrouter.com/start/data/custom)
- [Tanstack Router](https://tanstack.com/router/latest)


### Data Fetching {/*data-fetching*/}

Lấy dữ liệu từ máy chủ hoặc nguồn dữ liệu khác là phần quan trọng của hầu hết các ứng dụng. Thực hiện đúng cách đòi hỏi xử lý trạng thái loading, trạng thái lỗi và cache dữ liệu đã lấy, điều này có thể phức tạp.

Các thư viện data fetching chuyên dụng thực hiện công việc khó khăn là lấy và cache dữ liệu cho bạn, cho phép bạn tập trung vào dữ liệu mà ứng dụng cần và cách hiển thị nó. Các thư viện này thường được sử dụng trực tiếp trong component, nhưng cũng có thể được tích hợp vào route loader để prefetching nhanh hơn và hiệu suất tốt hơn, cũng như trong server rendering.

Lưu ý rằng việc lấy dữ liệu trực tiếp trong component có thể dẫn đến thời gian tải chậm hơn do waterfall request mạng, vì vậy chúng tôi khuyến nghị prefetching dữ liệu trong route loader hoặc trên server càng nhiều càng tốt! Điều này cho phép dữ liệu của trang được lấy cùng lúc khi trang đang được hiển thị.

Nếu bạn đang lấy dữ liệu từ hầu hết các backend hoặc API kiểu REST, chúng tôi gợi ý sử dụng:

- [TanStack Query](https://tanstack.com/query/)
- [SWR](https://swr.vercel.app/)
- [RTK Query](https://redux-toolkit.js.org/rtk-query/overview)

Nếu bạn đang lấy dữ liệu từ GraphQL API, chúng tôi gợi ý sử dụng:

- [Apollo](https://www.apollographql.com/docs/react)
- [Relay](https://relay.dev/)


### Code-splitting {/*code-splitting*/}

Code-splitting là quá trình chia ứng dụng thành các bundle nhỏ hơn có thể được tải theo yêu cầu. Kích thước code của ứng dụng tăng lên với mỗi tính năng mới và dependency bổ sung. Ứng dụng có thể trở nên chậm khi tải vì tất cả code cho toàn bộ ứng dụng cần được gửi trước khi có thể sử dụng. Cache, giảm tính năng/dependency, và chuyển một số code để chạy trên server có thể giúp giảm thiểu tải chậm nhưng là giải pháp không đầy đủ có thể hy sinh chức năng nếu lạm dụng.

Tương tự, nếu bạn dựa vào các ứng dụng sử dụng framework để tách code, bạn có thể gặp tình huống tải trở nên chậm hơn so với khi không tách code. Ví dụ, [lazy loading](/reference/react/lazy) một biểu đồ trì hoãn việc gửi code cần thiết để render biểu đồ, tách code biểu đồ khỏi phần còn lại của ứng dụng. [Parcel hỗ trợ code splitting với React.lazy](https://parceljs.org/recipes/react/#code-splitting). Tuy nhiên, nếu biểu đồ tải dữ liệu *sau* khi nó được render ban đầu, bạn giờ đang chờ hai lần. Đây là waterfall: thay vì lấy dữ liệu cho biểu đồ và gửi code để render nó đồng thời, bạn phải chờ từng bước hoàn thành lần lượt.

Tách code theo route, khi tích hợp với bundling và data fetching, có thể giảm thời gian tải ban đầu của ứng dụng và thời gian cần thiết để nội dung hiển thị lớn nhất của ứng dụng render ([Largest Contentful Paint](https://web.dev/articles/lcp)).

Để xem hướng dẫn code-splitting, hãy xem tài liệu công cụ build của bạn:
- [Tối ưu build của Vite](https://vite.dev/guide/features.html#build-optimizations)
- [Code splitting của Parcel](https://parceljs.org/features/code-splitting/)
- [Code splitting của Rsbuild](https://rsbuild.dev/guide/optimization/code-splitting)

### Cải thiện hiệu suất ứng dụng {/*improving-application-performance*/}

Vì công cụ build bạn chọn chỉ hỗ trợ single page app (SPA), bạn sẽ cần triển khai các [mẫu rendering](https://www.patterns.dev/vanilla/rendering-patterns) khác như server-side rendering (SSR), static site generation (SSG), và/hoặc React Server Components (RSC). Ngay cả khi bạn không cần các tính năng này lúc đầu, trong tương lai có thể có một số route sẽ được hưởng lợi từ SSR, SSG hoặc RSC.

* **Single-page app (SPA)** tải một trang HTML duy nhất và cập nhật trang động khi người dùng tương tác với ứng dụng. SPA dễ bắt đầu hơn, nhưng có thể có thời gian tải ban đầu chậm hơn. SPA là kiến trúc mặc định cho hầu hết các công cụ build.

* **Streaming Server-side rendering (SSR)** render trang trên server và gửi trang đã render đầy đủ cho client. SSR có thể cải thiện hiệu suất, nhưng có thể phức tạp hơn để thiết lập và bảo trì so với single-page app. Với việc thêm streaming, SSR có thể rất phức tạp để thiết lập và bảo trì. Xem [hướng dẫn SSR của Vite](https://vite.dev/guide/ssr).

* **Static site generation (SSG)** tạo các file HTML tĩnh cho ứng dụng tại thời điểm build. SSG có thể cải thiện hiệu suất, nhưng có thể phức tạp hơn để thiết lập và bảo trì so với server-side rendering. Xem [hướng dẫn SSG của Vite](https://vite.dev/guide/ssr.html#pre-rendering-ssg).

* **React Server Components (RSC)** cho phép bạn kết hợp các component build-time, server-only và interactive trong một cây React duy nhất. RSC có thể cải thiện hiệu suất, nhưng hiện tại đòi hỏi chuyên môn sâu để thiết lập và bảo trì. Xem [ví dụ RSC của Parcel](https://github.com/parcel-bundler/rsc-examples).

Các chiến lược rendering của bạn cần tích hợp với router để các ứng dụng xây dựng bằng framework có thể chọn chiến lược rendering ở cấp từng route. Điều này sẽ cho phép các chiến lược rendering khác nhau mà không cần viết lại toàn bộ ứng dụng. Ví dụ, trang landing của ứng dụng có thể được hưởng lợi từ việc được tạo tĩnh (SSG), trong khi trang có feed nội dung có thể hoạt động tốt nhất với server-side rendering.

Sử dụng đúng chiến lược rendering cho đúng route có thể giảm thời gian cần thiết để byte nội dung đầu tiên được tải ([Time to First Byte](https://web.dev/articles/ttfb)), phần nội dung đầu tiên render ([First Contentful Paint](https://web.dev/articles/fcp)), và nội dung hiển thị lớn nhất của ứng dụng render ([Largest Contentful Paint](https://web.dev/articles/lcp)).

### Và nhiều hơn nữa... {/*and-more*/}

Đây chỉ là một vài ví dụ về các tính năng mà ứng dụng mới cần cân nhắc khi xây dựng từ đầu. Nhiều hạn chế bạn gặp phải có thể khó giải quyết vì mỗi vấn đề liên kết với nhau và có thể đòi hỏi chuyên môn sâu trong các lĩnh vực bạn có thể chưa quen thuộc.

Nếu bạn không muốn tự giải quyết các vấn đề này, bạn có thể [bắt đầu với một framework](/learn/creating-a-react-app) cung cấp sẵn các tính năng này.
