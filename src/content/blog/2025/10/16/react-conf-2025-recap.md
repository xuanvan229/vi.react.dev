---
title: "Tóm tắt React Conf 2025"
author: Matt Carroll and Ricky Hanlon
date: 2025/10/16
description: Tuần trước chúng tôi đã tổ chức React Conf 2025, trong bài viết này, chúng tôi tóm tắt các bài nói và thông báo từ sự kiện...
---

Oct 16, 2025 by [Matt Carroll](https://x.com/mattcarrollcode) và [Ricky Hanlon](https://bsky.app/profile/ricky.fm)

---

<Intro>

Tuần trước chúng tôi đã tổ chức React Conf 2025 nơi chúng tôi công bố [React Foundation](/blog/2025/10/07/introducing-the-react-foundation) và trình bày các tính năng mới sắp có trong React và React Native.

</Intro>

---

React Conf 2025 được tổ chức vào ngày 7-8 tháng 10 năm 2025, tại Henderson, Nevada.

Toàn bộ livestream [ngày 1](https://www.youtube.com/watch?v=zyVRg2QR6LA&t=1067s) và [ngày 2](https://www.youtube.com/watch?v=p9OcztRyDl0&t=2299s) đều có sẵn trực tuyến, và bạn có thể xem ảnh từ sự kiện [tại đây](https://conf.react.dev/photos).

Trong bài viết này, chúng tôi sẽ tóm tắt các bài nói và thông báo từ sự kiện.


## Keynote Ngày 1 {/*day-1-keynote*/}

_Xem toàn bộ livestream ngày 1 [tại đây.](https://www.youtube.com/watch?v=zyVRg2QR6LA&t=1067s)_

Trong keynote ngày 1, Joe Savona đã chia sẻ các cập nhật từ nhóm và cộng đồng kể từ React Conf trước đó và những điểm nổi bật từ React 19.0 và 19.1.

Mofei Zhang đã nhấn mạnh các tính năng mới trong React 19.2 bao gồm:
* [`<Activity />`](https://react.dev/reference/react/Activity) — một component mới để quản lý hiển thị.
* [`useEffectEvent`](https://react.dev/reference/react/useEffectEvent) để phát sự kiện từ Effect.
* [Performance Tracks](https://react.dev/reference/dev-tools/react-performance-tracks) — công cụ profiling mới trong DevTools.
* [Partial Pre-Rendering](https://react.dev/blog/2025/10/01/react-19-2#partial-pre-rendering) để pre-render một phần ứng dụng trước, và tiếp tục render sau.

Jack Pope đã công bố các tính năng mới trong Canary bao gồm:

* [`<ViewTransition />`](https://react.dev/reference/react/ViewTransition) — một component mới để tạo hiệu ứng chuyển trang.
* [Fragment Refs](https://react.dev/reference/react/Fragment#fragmentinstance) — cách mới để tương tác với các DOM node được bao bọc bởi Fragment.

Lauren Tan đã công bố [React Compiler v1.0](https://react.dev/blog/2025/10/07/react-compiler-1) và khuyến nghị tất cả ứng dụng sử dụng React Compiler cho các lợi ích như:
* [Tự động memoization](/learn/react-compiler/introduction#what-does-react-compiler-do) hiểu code React.
* [Quy tắc lint mới](/learn/react-compiler/installation#eslint-integration) được hỗ trợ bởi React Compiler để dạy các phương pháp tốt nhất.
* [Hỗ trợ mặc định](/learn/react-compiler/installation#basic-setup) cho ứng dụng mới trong Vite, Next.js, và Expo.
* [Hướng dẫn migration](/learn/react-compiler/incremental-adoption) cho ứng dụng hiện tại chuyển sang React Compiler.

Cuối cùng, Seth Webster đã công bố [React Foundation](/blog/2025/10/07/introducing-the-react-foundation) để quản lý phát triển mã nguồn mở và cộng đồng React.

Xem ngày 1 tại đây:

<YouTubeIframe src="https://www.youtube.com/embed/zyVRg2QR6LA?si=z-8t_xCc12HwGJH_&t=1067s" />

## Keynote Ngày 2 {/*day-2-keynote*/}

_Xem toàn bộ livestream ngày 2 [tại đây.](https://www.youtube.com/watch?v=p9OcztRyDl0&t=2299s)_

Jorge Cohen và Nicola Corti đã mở đầu ngày 2 nhấn mạnh sự tăng trưởng đáng kinh ngạc của React Native với 4 triệu lượt tải hàng tuần (tăng 100% so với năm trước), và một số cuộc migration đáng chú ý từ Shopify, Zalando, và HelloFresh, các ứng dụng đoạt giải như RISE, RUNNA, và Partyful, và các ứng dụng AI từ Mistral, Replit, và v0.

Riccardo Cipolleschi đã chia sẻ hai thông báo lớn cho React Native:
- [React Native 0.82 sẽ chỉ có New Architecture](https://reactnative.dev/blog/2025/10/08/react-native-0.82#new-architecture-only)
- [Hỗ trợ thử nghiệm Hermes V1](https://reactnative.dev/blog/2025/10/08/react-native-0.82#experimental-hermes-v1)

Ruben Norte và Alex Hunt kết thúc keynote bằng việc công bố:
- [DOM API mới tương thích web](https://reactnative.dev/blog/2025/10/08/react-native-0.82#dom-node-apis) để cải thiện tương thích với React trên web.
- [Performance API mới](https://reactnative.dev/blog/2025/10/08/react-native-0.82#web-performance-apis-canary) với network panel mới và ứng dụng desktop.

Xem ngày 2 tại đây:

<YouTubeIframe src="https://www.youtube.com/embed/p9OcztRyDl0?si=qPTHftsUE07cjZpS&t=2299s" />


## Các bài nói của nhóm React {/*react-team-talks*/}

Trong suốt hội nghị, có các bài nói từ nhóm React bao gồm:
* [Async React Part I](https://www.youtube.com/watch?v=zyVRg2QR6LA&t=10907s) và [Part II](https://www.youtube.com/watch?v=p9OcztRyDl0&t=29073s) [(Ricky Hanlon)](https://x.com/rickhanlonii) cho thấy những gì có thể với 10 năm đổi mới.
* [Exploring React Performance](https://www.youtube.com/watch?v=zyVRg2QR6LA&t=20274s) [(Joe Savona)](https://x.com/en_js) cho thấy kết quả nghiên cứu hiệu năng React.
* [Reimagining Lists in React Native](https://www.youtube.com/watch?v=p9OcztRyDl0&t=10382s) [(Luna Wei)](https://x.com/lunaleaps) giới thiệu Virtual View, một primitive mới cho danh sách quản lý hiển thị với render dựa trên mode (hidden/pre-render/visible).
* [Profiling with React Performance tracks](https://www.youtube.com/watch?v=zyVRg2QR6LA&t=8276s) [(Ruslan Lesiutin)](https://x.com/ruslanlesiutin) cho thấy cách sử dụng React Performance Tracks mới để debug các vấn đề hiệu năng và xây dựng ứng dụng tốt.
* [React Strict DOM](https://www.youtube.com/watch?v=p9OcztRyDl0&t=9026s) [(Nicolas Gallagher)](https://nicolasgallagher.com/) nói về cách tiếp cận của Meta để sử dụng code web trên native.
* [View Transitions and Activity](https://www.youtube.com/watch?v=zyVRg2QR6LA&t=4870s) [(Chance Strickland)](https://x.com/chancethedev) — Chance đã làm việc với nhóm React để trình bày cách sử dụng `<Activity />` và `<ViewTransition />` để xây dựng hiệu ứng nhanh, giống native.
* [In case you missed the memo](https://www.youtube.com/watch?v=zyVRg2QR6LA&t=9525s) [(Cody Olsen)](https://bsky.app/profile/codey.bsky.social) - Cody đã làm việc với nhóm React để áp dụng Compiler tại Sanity Studio, và chia sẻ kết quả.
## Các bài nói về React framework {/*react-framework-talks*/}

Nửa sau của ngày 2 có một loạt bài nói từ các nhóm React Framework bao gồm:

* [React Native, Amplified](https://www.youtube.com/watch?v=p9OcztRyDl0&t=5737s) bởi [Giovanni Laquidara](https://x.com/giolaq) và [Eric Fahsl](https://x.com/efahsl).
* [React Everywhere: Bringing React Into Native Apps](https://www.youtube.com/watch?v=p9OcztRyDl0&t=18213s) bởi [Mike Grabowski](https://x.com/grabbou).
* [How Parcel Bundles React Server Components](https://www.youtube.com/watch?v=p9OcztRyDl0&t=19538s) bởi [Devon Govett](https://x.com/devonovett).
* [Designing Page Transitions](https://www.youtube.com/watch?v=p9OcztRyDl0&t=20640s) bởi [Delba de Oliveira](https://x.com/delba_oliveira).
* [Build Fast, Deploy Faster — Expo in 2025](https://www.youtube.com/watch?v=p9OcztRyDl0&t=21350s) bởi [Evan Bacon](https://x.com/baconbrix).
* [The React Router's take on RSC](https://www.youtube.com/watch?v=p9OcztRyDl0&t=22367s) bởi [Kent C. Dodds](https://x.com/kentcdodds).
* [RedwoodSDK: Web Standards Meet Full-Stack React](https://www.youtube.com/watch?v=p9OcztRyDl0&t=24992s) bởi [Peter Pistorius](https://x.com/appfactory) và [Aurora Scharff](https://x.com/aurorascharff).
* [TanStack Start](https://www.youtube.com/watch?v=p9OcztRyDl0&t=26065s) bởi [Tanner Linsley](https://x.com/tannerlinsley).

## Hỏi & Đáp {/*q-and-a*/}
Có ba phiên Hỏi & Đáp trong hội nghị:

* [Hỏi & Đáp nhóm React tại Meta](https://www.youtube.com/watch?v=zyVRg2QR6LA&t=26304s) do [Shruti Kapoor](https://x.com/shrutikapoor08) dẫn dắt
* [Hỏi & Đáp React Framework](https://www.youtube.com/watch?v=p9OcztRyDl0&t=26812s) do [Jack Herrington](https://x.com/jherr) dẫn dắt
* [Panel React và AI](https://www.youtube.com/watch?v=zyVRg2QR6LA&t=18741s) do [Lee Robinson](https://x.com/leerob) dẫn dắt

## Và nhiều hơn nữa... {/*and-more*/}

Chúng tôi cũng nghe các bài nói từ cộng đồng bao gồm:
* [Building an MCP Server](https://www.youtube.com/watch?v=zyVRg2QR6LA&t=24204s) bởi [James Swinton](https://x.com/JamesSwintonDev) ([AG Grid](https://www.ag-grid.com/?utm_source=react-conf&utm_medium=react-conf-homepage&utm_campaign=react-conf-sponsorship-2025))
* [Modern Emails using React](https://www.youtube.com/watch?v=zyVRg2QR6LA&t=25521s) bởi [Zeno Rocha](https://x.com/zenorocha) ([Resend](https://resend.com/))
* [Why React Native Apps Make All the Money](https://www.youtube.com/watch?v=zyVRg2QR6LA&t=24917s) bởi [Perttu Lähteenlahti](https://x.com/plahteenlahti) ([RevenueCat](https://www.revenuecat.com/))
* [The invisible craft of great UX](https://www.youtube.com/watch?v=zyVRg2QR6LA&t=23400s) bởi [Michał Dudak](https://x.com/michaldudak) ([MUI](https://mui.com/))

## Cảm ơn {/*thanks*/}

Cảm ơn tất cả nhân viên, diễn giả, và người tham dự đã làm cho React Conf 2025 trở thành hiện thực. Có quá nhiều người để liệt kê, nhưng chúng tôi muốn cảm ơn đặc biệt một số người.

Cảm ơn [Matt Carroll](https://x.com/mattcarrollcode) đã lên kế hoạch toàn bộ sự kiện và xây dựng website hội nghị.

Cảm ơn [Michael Chan](https://x.com/chantastic) đã làm MC React Conf với sự tận tâm và năng lượng đáng kinh ngạc, mang đến những phần giới thiệu diễn giả chu đáo, những câu đùa vui, và sự nhiệt tình chân thành trong suốt sự kiện. Cảm ơn [Jorge Cohen](https://x.com/JorgeWritesCode) đã dẫn dắt livestream, phỏng vấn từng diễn giả, và mang trải nghiệm React Conf trực tiếp lên mạng.

Cảm ơn [Mateusz Kornacki](https://x.com/mat_kornacki), [Mike Grabowski](https://x.com/grabbou), [Kris Lis](https://www.linkedin.com/in/krzysztoflisakakris/) và đội ngũ tại [Callstack](https://www.callstack.com/) đã đồng tổ chức React Conf và cung cấp hỗ trợ thiết kế, kỹ thuật, và marketing. Cảm ơn [đội ngũ ZeroSlope](https://zeroslopeevents.com/contact-us/): Sunny Leggett, Tracey Harrison, Tara Larish, Whitney Pogue, và Brianne Smythia đã giúp tổ chức sự kiện.

Cảm ơn [Jorge Cabiedes Acosta](https://github.com/jorge-cab), [Gijs Weterings](https://x.com/gweterings), [Tim Yung](https://x.com/yungsters), và [Jason Bonta](https://x.com/someextent) đã mang câu hỏi từ Discord đến livestream. Cảm ơn [Lynn Yu](https://github.com/lynnshaoyu) đã dẫn dắt việc quản lý Discord. Cảm ơn [Seth Webster](https://x.com/sethwebster) đã chào đón chúng tôi mỗi ngày; và cảm ơn [Christopher Chedeau](https://x.com/vjeux), [Kevin Gozali](https://x.com/fkgozali), và [Pieter De Baets](https://x.com/Javache) đã tham gia cùng chúng tôi với thông điệp đặc biệt trong bữa tiệc sau sự kiện.

Cảm ơn [Kadi Kraman](https://x.com/kadikraman), [Beto](https://x.com/betomoedano) và [Nicolas Solerieu](https://www.linkedin.com/in/nicolas-solerieu/) đã xây dựng ứng dụng di động cho hội nghị. Cảm ơn [Wojtek Szafraniec](https://x.com/wojteg1337) đã giúp đỡ website hội nghị. Cảm ơn [Mustache](https://www.mustachepower.com/) & [Cornerstone](https://cornerstoneav.com/) vì hình ảnh, sân khấu, và âm thanh; và cảm ơn khách sạn Westin đã đón tiếp chúng tôi.

Cảm ơn tất cả nhà tài trợ đã làm cho sự kiện trở thành hiện thực: [Amazon](https://www.developer.amazon.com), [MUI](https://mui.com/), [Vercel](https://vercel.com/), [Expo](https://expo.dev/), [RedwoodSDK](https://rwsdk.com), [Ag Grid](https://www.ag-grid.com), [RevenueCat](https://www.revenuecat.com/), [Resend](https://resend.com), [Mux](https://www.mux.com/), [Old Mission](https://www.oldmissioncapital.com/), [Arcjet](https://arcjet.com), [Infinite Red](https://infinite.red/), và [RenderATL](https://renderatl.com).

Cảm ơn tất cả diễn giả đã chia sẻ kiến thức và kinh nghiệm với cộng đồng.

Cuối cùng, cảm ơn tất cả mọi người đã tham dự trực tiếp và trực tuyến để thể hiện điều gì tạo nên React. React không chỉ là một thư viện, nó là một cộng đồng, và thật truyền cảm hứng khi thấy mọi người cùng nhau chia sẻ và học hỏi.

Hẹn gặp lại lần sau!
