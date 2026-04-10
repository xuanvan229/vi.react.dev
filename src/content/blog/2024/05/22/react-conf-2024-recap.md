---
title: "Tóm tắt React Conf 2024"
author: Ricky Hanlon
date: 2024/05/22
description: Tuần trước chúng tôi đã tổ chức React Conf 2024, hội nghị hai ngày tại Henderson, Nevada, nơi hơn 700 người tham dự trực tiếp để thảo luận về những điều mới nhất trong kỹ thuật UI. Trong bài viết này, chúng tôi sẽ tóm tắt các bài nói và thông báo từ sự kiện.
---

May 22, 2024 by [Ricky Hanlon](https://twitter.com/rickhanlonii).

---

<Intro>

Tuần trước chúng tôi đã tổ chức React Conf 2024, hội nghị hai ngày tại Henderson, Nevada, nơi hơn 700 người tham dự trực tiếp để thảo luận về những điều mới nhất trong kỹ thuật UI. Đây là hội nghị trực tiếp đầu tiên của chúng tôi kể từ 2019, và chúng tôi rất vui mừng được quy tụ cộng đồng lại với nhau.

</Intro>

---

Tại React Conf 2024, chúng tôi đã công bố [React 19 RC](/blog/2024/12/05/react-19), [React Native New Architecture Beta](https://github.com/reactwg/react-native-new-architecture/discussions/189), và bản phát hành thử nghiệm của [React Compiler](/learn/react-compiler). Cộng đồng cũng lên sân khấu để công bố [React Router v7](https://remix.run/blog/merging-remix-and-react-router), [Universal Server Components](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=20765s) trong Expo Router, React Server Components trong [RedwoodJS](https://redwoodjs.com/blog/rsc-now-in-redwoodjs), và nhiều hơn nữa.

Toàn bộ livestream [ngày 1](https://www.youtube.com/watch?v=T8TZQ6k4SLE) và [ngày 2](https://www.youtube.com/watch?v=0ckOUBiuxVY) đều có sẵn trực tuyến. Trong bài viết này, chúng tôi sẽ tóm tắt các bài nói và thông báo từ sự kiện.

## Ngày 1 {/*day-1*/}

_[Xem toàn bộ livestream ngày 1 tại đây.](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=973s)_

Để mở đầu ngày 1, CTO của Meta [Andrew "Boz" Bosworth](https://www.threads.net/@boztank) đã chia sẻ lời chào mừng, tiếp theo là phần giới thiệu từ [Seth Webster](https://twitter.com/sethwebster), người quản lý React Org tại Meta, và MC [Ashley Narcisse](https://twitter.com/_darkfadr).

Trong keynote ngày 1, [Joe Savona](https://twitter.com/en_JS) đã chia sẻ mục tiêu và tầm nhìn của chúng tôi cho React nhằm giúp bất kỳ ai cũng dễ dàng xây dựng trải nghiệm người dùng tuyệt vời. [Lauren Tan](https://twitter.com/potetotes) tiếp tục với Tình hình React, nơi cô chia sẻ rằng React đã được tải xuống hơn 1 tỷ lần trong năm 2023, và 37% nhà phát triển mới học lập trình với React. Cuối cùng, cô nhấn mạnh công việc của cộng đồng React trong việc tạo nên React.

Để biết thêm, hãy xem các bài nói từ cộng đồng sau trong hội nghị:

- [Vanilla React](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=5542s) bởi [Ryan Florence](https://twitter.com/ryanflorence)
- [React Rhythm & Blues](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=12728s) bởi [Lee Robinson](https://twitter.com/leeerob)
- [RedwoodJS, now with React Server Components](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=26815s) bởi [Amy Dutton](https://twitter.com/selfteachme)
- [Introducing Universal React Server Components in Expo Router](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=20765s) bởi [Evan Bacon](https://twitter.com/Baconbrix)

Tiếp theo trong keynote, [Josh Story](https://twitter.com/joshcstory) và [Andrew Clark](https://twitter.com/acdlite) đã chia sẻ các tính năng mới sắp có trong React 19, và công bố React 19 RC đã sẵn sàng để thử nghiệm trong production. Hãy xem tất cả các tính năng trong [bài viết phát hành React 19](/blog/2024/12/05/react-19), và xem các bài nói này để tìm hiểu sâu về các tính năng mới:

- [What's new in React 19](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=8880s) bởi [Lydia Hallie](https://twitter.com/lydiahallie)
- [React Unpacked: A Roadmap to React 19](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=10112s) bởi [Sam Selikoff](https://twitter.com/samselikoff)
- [React 19 Deep Dive: Coordinating HTML](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=24916s) bởi [Josh Story](https://twitter.com/joshcstory)
- [Enhancing Forms with React Server Components](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=25280s) bởi [Aurora Walberg Scharff](https://twitter.com/aurorascharff)
- [React for Two Computers](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=18825s) bởi [Dan Abramov](https://bsky.app/profile/danabra.mov)
- [And Now You Understand React Server Components](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=11256s) bởi [Kent C. Dodds](https://twitter.com/kentcdodds)

Cuối cùng, chúng tôi kết thúc keynote với [Joe Savona](https://twitter.com/en_JS), [Sathya Gunasekaran](https://twitter.com/_gsathya), và [Mofei Zhang](https://twitter.com/zmofei) công bố rằng React Compiler hiện đã là [mã nguồn mở](https://github.com/facebook/react/pull/29061), và chia sẻ phiên bản thử nghiệm của React Compiler để thử.

Để biết thêm thông tin về cách sử dụng Compiler và cách nó hoạt động, hãy xem [tài liệu](/learn/react-compiler) và các bài nói này:

- [Forget About Memo](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=12020s) bởi [Lauren Tan](https://twitter.com/potetotes)
- [React Compiler Deep Dive](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=9313s) bởi [Sathya Gunasekaran](https://twitter.com/_gsathya) và [Mofei Zhang](https://twitter.com/zmofei)

Xem toàn bộ keynote ngày 1 tại đây:

<YouTubeIframe src="https://www.youtube.com/embed/T8TZQ6k4SLE?t=973s" />

## Ngày 2 {/*day-2*/}

_[Xem toàn bộ livestream ngày 2 tại đây.](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=1720s)_

Để mở đầu ngày 2, [Seth Webster](https://twitter.com/sethwebster) đã chia sẻ lời chào mừng, tiếp theo là lời cảm ơn từ [Eli White](https://x.com/Eli_White) và phần giới thiệu từ Chief Vibes Officer [Ashley Narcisse](https://twitter.com/_darkfadr).

Trong keynote ngày 2, [Nicola Corti](https://twitter.com/cortinico) đã chia sẻ Tình hình React Native, bao gồm 78 triệu lượt tải trong năm 2023. Anh cũng nhấn mạnh các ứng dụng sử dụng React Native bao gồm hơn 2000 màn hình được sử dụng bên trong Meta; trang chi tiết sản phẩm trong Facebook Marketplace, được truy cập hơn 2 tỷ lần mỗi ngày; và một phần của menu Start trên Microsoft Windows cùng một số tính năng trong hầu hết mọi sản phẩm Microsoft Office trên di động và desktop.

Nicola cũng nhấn mạnh tất cả công việc cộng đồng làm để hỗ trợ React Native bao gồm thư viện, framework, và nhiều nền tảng. Để biết thêm, hãy xem các bài nói từ cộng đồng:

- [Extending React Native beyond Mobile and Desktop Apps](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=5798s) bởi [Chris Traganos](https://twitter.com/chris_trag) và [Anisha Malde](https://twitter.com/anisha_malde)
- [Spatial computing with React](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=22525s) bởi [Michał Pierzchała](https://twitter.com/thymikee)

[Riccardo Cipolleschi](https://twitter.com/cipolleschir) tiếp tục keynote ngày 2 bằng việc công bố rằng React Native New Architecture hiện đang ở giai đoạn Beta và sẵn sàng để các ứng dụng áp dụng trong production. Anh chia sẻ các tính năng mới và cải tiến trong kiến trúc mới, và chia sẻ lộ trình cho tương lai của React Native. Để biết thêm, hãy xem:

- [Cross Platform React](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=26569s) bởi [Olga Zinoveva](https://github.com/SlyCaptainFlint) và [Naman Goel](https://twitter.com/naman34)

Tiếp theo trong keynote, Nicola công bố rằng chúng tôi hiện khuyến nghị bắt đầu với một framework như Expo cho tất cả ứng dụng mới được tạo với React Native. Với thay đổi này, anh cũng công bố trang chủ React Native mới và tài liệu Bắt đầu mới. Bạn có thể xem hướng dẫn Bắt đầu mới trong [tài liệu React Native](https://reactnative.dev/docs/next/environment-setup).

Cuối cùng, để kết thúc keynote, [Kadi Kraman](https://twitter.com/kadikraman) đã chia sẻ các tính năng và cải tiến mới nhất trong Expo, và cách bắt đầu phát triển với React Native sử dụng Expo.

Xem toàn bộ keynote ngày 2 tại đây:

<YouTubeIframe src="https://www.youtube.com/embed/0ckOUBiuxVY?t=1720s" />

## Hỏi & Đáp {/*q-and-a*/}

Các nhóm React và React Native cũng kết thúc mỗi ngày với phiên Hỏi & Đáp:

- [Hỏi & Đáp React](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=27518s) do [Michael Chan](https://twitter.com/chantastic) dẫn dắt
- [Hỏi & Đáp React Native](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=27935s) do [Jamon Holmgren](https://twitter.com/jamonholmgren) dẫn dắt

## Và nhiều hơn nữa... {/*and-more*/}

Chúng tôi cũng nghe các bài nói về accessibility, báo cáo lỗi, CSS, và nhiều hơn nữa:

- [Demystifying accessibility in React apps](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=20655s) bởi [Kateryna Porshnieva](https://twitter.com/krambertech)
- [Pigment CSS, CSS in the server component age](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=21696s) bởi [Olivier Tassinari](https://twitter.com/olivtassinari)
- [Real-time React Server Components](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=24070s) bởi [Sunil Pai](https://twitter.com/threepointone)
- [Let's break React Rules](https://www.youtube.com/watch?v=T8TZQ6k4SLE&t=25862s) bởi [Charlotte Isambert](https://twitter.com/c_isambert)
- [Solve 100% of your errors](https://www.youtube.com/watch?v=0ckOUBiuxVY&t=19881s) bởi [Ryan Albrecht](https://github.com/ryan953)

## Cảm ơn {/*thank-you*/}

Cảm ơn tất cả nhân viên, diễn giả, và người tham dự đã làm cho React Conf 2024 trở thành hiện thực. Có quá nhiều người để liệt kê, nhưng chúng tôi muốn cảm ơn đặc biệt một số người.

Cảm ơn [Barbara Markiewicz](https://twitter.com/barbara_markie), đội ngũ tại [Callstack](https://www.callstack.com/), và Developer Advocate của React Team [Matt Carroll](https://twitter.com/mattcarrollcode) đã giúp lên kế hoạch toàn bộ sự kiện; và cảm ơn [Sunny Leggett](https://zeroslopeevents.com/about) và mọi người từ [Zero Slope](https://zeroslopeevents.com) đã giúp tổ chức sự kiện.

Cảm ơn [Ashley Narcisse](https://twitter.com/_darkfadr) vì đã là MC và Chief Vibes Officer; và cảm ơn [Michael Chan](https://twitter.com/chantastic) và [Jamon Holmgren](https://twitter.com/jamonholmgren) vì đã dẫn dắt các phiên Hỏi & Đáp.

Cảm ơn [Seth Webster](https://twitter.com/sethwebster) và [Eli White](https://x.com/Eli_White) vì đã chào đón chúng tôi mỗi ngày và cung cấp định hướng về cấu trúc và nội dung; và cảm ơn [Tom Occhino](https://twitter.com/tomocchino) vì đã tham gia cùng chúng tôi với thông điệp đặc biệt trong bữa tiệc sau sự kiện.

Cảm ơn [Ricky Hanlon](https://www.youtube.com/watch?v=FxTZL2U-uKg&t=1263s) vì đã cung cấp phản hồi chi tiết về các bài nói, làm việc trên thiết kế slide, và nói chung là lấp đầy các khoảng trống để chăm chút từng chi tiết.

Cảm ơn [Callstack](https://www.callstack.com/) vì đã xây dựng website hội nghị; và cảm ơn [Kadi Kraman](https://twitter.com/kadikraman) và đội ngũ [Expo](https://expo.dev/) vì đã xây dựng ứng dụng di động cho hội nghị.

Cảm ơn tất cả nhà tài trợ đã làm cho sự kiện trở thành hiện thực: [Remix](https://remix.run/), [Amazon](https://developer.amazon.com/apps-and-games?cmp=US_2024_05_3P_React-Conf-2024&ch=prtnr&chlast=prtnr&pub=ref&publast=ref&type=org&typelast=org), [MUI](https://mui.com/), [Sentry](https://sentry.io/for/react/?utm_source=sponsored-conf&utm_medium=sponsored-event&utm_campaign=frontend-fy25q2-evergreen&utm_content=logo-reactconf2024-learnmore), [Abbott](https://www.jobs.abbott/software), [Expo](https://expo.dev/), [RedwoodJS](https://rwsdk.com/), và [Vercel](https://vercel.com).

Cảm ơn đội ngũ AV vì hình ảnh, sân khấu, và âm thanh; và cảm ơn khách sạn Westin vì đã đón tiếp chúng tôi.

Cảm ơn tất cả diễn giả đã chia sẻ kiến thức và kinh nghiệm với cộng đồng.

Cuối cùng, cảm ơn tất cả mọi người đã tham dự trực tiếp và trực tuyến để thể hiện điều gì tạo nên React. React không chỉ là một thư viện, nó là một cộng đồng, và thật truyền cảm hứng khi thấy mọi người cùng nhau chia sẻ và học hỏi.

Hẹn gặp lại lần sau!

