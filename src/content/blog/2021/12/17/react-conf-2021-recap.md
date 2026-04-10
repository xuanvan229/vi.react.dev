---
title: "Tổng Kết React Conf 2021"
author: Jesslyn Tannady and Rick Hanlon
date: 2021/12/17
description: Tuần trước chúng tôi đã tổ chức React Conf lần thứ 6. Trong những năm trước, chúng tôi đã sử dụng sân khấu React Conf để đưa ra các thông báo thay đổi ngành như React Native và React Hooks. Năm nay, chúng tôi chia sẻ tầm nhìn đa nền tảng cho React, bắt đầu với việc phát hành React 18 và áp dụng dần dần các tính năng concurrent.
---

December 17, 2021 by [Jesslyn Tannady](https://twitter.com/jtannady) and [Rick Hanlon](https://twitter.com/rickhanlonii)

---

<Intro>

Tuần trước chúng tôi đã tổ chức React Conf lần thứ 6. Trong những năm trước, chúng tôi đã sử dụng sân khấu React Conf để đưa ra các thông báo thay đổi ngành như [_React Native_](https://engineering.fb.com/2015/03/26/android/react-native-bringing-modern-web-techniques-to-mobile/) và [_React Hooks_](https://reactjs.org/docs/hooks-intro.html). Năm nay, chúng tôi chia sẻ tầm nhìn đa nền tảng cho React, bắt đầu với việc phát hành React 18 và áp dụng dần dần các tính năng concurrent.

</Intro>

---

Đây là lần đầu tiên React Conf được tổ chức trực tuyến, và được phát trực tiếp miễn phí, được dịch sang 8 ngôn ngữ khác nhau. Người tham gia từ khắp nơi trên thế giới đã tham gia Discord hội nghị và sự kiện phát lại của chúng tôi để dễ tiếp cận ở tất cả múi giờ. Hơn 50.000 người đã đăng ký, với hơn 60.000 lượt xem 19 bài nói, và 5.000 người tham gia Discord trong cả hai sự kiện.

Tất cả các bài nói đều [có sẵn để xem trực tuyến](https://www.youtube.com/watch?v=FZ0cG47msEk&list=PLNG_1j3cPCaZZ7etkzWA7JfdmKWT0pMsa).

Dưới đây là tóm tắt những gì đã được chia sẻ trên sân khấu:

## React 18 và các tính năng concurrent {/*react-18-and-concurrent-features*/}

Trong bài keynote, chúng tôi đã chia sẻ tầm nhìn về tương lai của React bắt đầu với React 18.

React 18 thêm concurrent renderer được chờ đợi từ lâu và cập nhật cho Suspense mà không có bất kỳ thay đổi phá vỡ lớn nào. Các ứng dụng có thể nâng cấp lên React 18 và bắt đầu dần dần áp dụng các tính năng concurrent với lượng nỗ lực tương đương với bất kỳ bản phát hành chính nào khác.

**Điều này có nghĩa là không có concurrent mode, chỉ có các tính năng concurrent.**

Trong bài keynote, chúng tôi cũng chia sẻ tầm nhìn về Suspense, Server Components, các working group React mới, và tầm nhìn dài hạn nhiều nền tảng cho React Native.

Xem bài keynote đầy đủ từ [Andrew Clark](https://twitter.com/acdlite), [Juan Tejada](https://twitter.com/_jstejada), [Lauren Tan](https://twitter.com/potetotes), và [Rick Hanlon](https://twitter.com/rickhanlonii) tại đây:

<YouTubeIframe src="https://www.youtube.com/embed/FZ0cG47msEk" />

## React 18 cho Nhà Phát Triển Ứng Dụng {/*react-18-for-application-developers*/}

Trong bài keynote, chúng tôi cũng thông báo rằng React 18 RC đã có sẵn để thử ngay bây giờ. Chờ thêm phản hồi, đây là phiên bản React chính xác mà chúng tôi sẽ công bố ổn định vào đầu năm sau.

Để thử React 18 RC, hãy nâng cấp các dependency của bạn:

```bash
npm install react@rc react-dom@rc
```

và chuyển sang API `createRoot` mới:

```js
// before
const container = document.getElementById(‘root’);
ReactDOM.render(<App />, container);

// after
const container = document.getElementById(‘root’);
const root = ReactDOM.createRoot(container);
root.render(<App/>);
```

Để xem demo về việc nâng cấp lên React 18, hãy xem bài nói của [Shruti Kapoor](https://twitter.com/shrutikapoor08) tại đây:

<YouTubeIframe src="https://www.youtube.com/embed/ytudH8je5ko" />

## Streaming Server Rendering với Suspense {/*streaming-server-rendering-with-suspense*/}

React 18 cũng bao gồm các cải tiến về hiệu suất server-side rendering sử dụng Suspense.

Streaming server rendering cho phép bạn tạo HTML từ các React component trên server, và stream HTML đó đến người dùng. Trong React 18, bạn có thể sử dụng `Suspense` để chia nhỏ ứng dụng của mình thành các đơn vị độc lập nhỏ hơn có thể được stream độc lập với nhau mà không chặn phần còn lại của ứng dụng. Điều này có nghĩa là người dùng sẽ thấy nội dung của bạn sớm hơn và có thể bắt đầu tương tác với nó nhanh hơn nhiều.

Để tìm hiểu sâu hơn, hãy xem bài nói của [Shaundai Person](https://twitter.com/shaundai) tại đây:

<YouTubeIframe src="https://www.youtube.com/embed/pj5N-Khihgc" />

## Working group React đầu tiên {/*the-first-react-working-group*/}

Cho React 18, chúng tôi đã tạo Working Group đầu tiên để cộng tác với một ban chuyên gia, nhà phát triển, người bảo trì thư viện, và nhà giáo dục. Cùng nhau chúng tôi đã làm việc để tạo chiến lược áp dụng dần dần và tinh chỉnh các API mới như `useId`, `useSyncExternalStore`, và `useInsertionEffect`.

Để có tổng quan về công việc này, hãy xem bài nói của [Aakansha’ Doshi](https://twitter.com/aakansha1216):

<YouTubeIframe src="https://www.youtube.com/embed/qn7gRClrC9U" />

## React Developer Tooling {/*react-developer-tooling*/}

Để hỗ trợ các tính năng mới trong bản phát hành này, chúng tôi cũng thông báo về đội ngũ React DevTools mới được thành lập và một Timeline Profiler mới để giúp các nhà phát triển debug ứng dụng React của họ.

Để biết thêm thông tin và demo về các tính năng DevTools mới, hãy xem bài nói của [Brian Vaughn](https://twitter.com/brian_d_vaughn):

<YouTubeIframe src="https://www.youtube.com/embed/oxDfrke8rZg" />

## React không cần memo {/*react-without-memo*/}

Nhìn xa hơn vào tương lai, [Xuan Huang (黄玄)](https://twitter.com/Huxpro) đã chia sẻ một cập nhật từ nghiên cứu React Labs của chúng tôi về compiler tự động memoization. Hãy xem bài nói này để biết thêm thông tin và demo về prototype compiler:

<YouTubeIframe src="https://www.youtube.com/embed/lGEMwh32soc" />

## Bài keynote tài liệu React {/*react-docs-keynote*/}

[Rachel Nabors](https://twitter.com/rachelnabors) đã mở đầu một phần các bài nói về học và thiết kế với React bằng một bài keynote về sự đầu tư của chúng tôi vào tài liệu mới của React ([hiện đã ra mắt là react.dev](/blog/2023/03/16/introducing-react-dev)):

<YouTubeIframe src="https://www.youtube.com/embed/mneDaMYOKP8" />

## Và nhiều hơn nữa... {/*and-more*/}

**Chúng tôi cũng đã nghe các bài nói về học và thiết kế với React:**

* Debbie O’Brien: [Things I learnt from the new React docs](https://youtu.be/-7odLW_hG7s).
* Sarah Rainsberger: [Learning in the Browser](https://youtu.be/5X-WEQflCL0).
* Linton Ye: [The ROI of Designing with React](https://youtu.be/7cPWmID5XAk).
* Delba de Oliveira: [Interactive playgrounds with React](https://youtu.be/zL8cz2W0z34).

**Các bài nói từ đội ngũ Relay, React Native, và PyTorch:**

* Robert Balicki: [Re-introducing Relay](https://youtu.be/lhVGdErZuN4).
* Eric Rozell and Steven Moyes: [React Native Desktop](https://youtu.be/9L4FFrvwJwY).
* Roman Rädle: [On-device Machine Learning for React Native](https://youtu.be/NLj73vrc2I8)

**Và các bài nói từ cộng đồng về accessibility, tooling, và Server Components:**

* Daishi Kato: [React 18 for External Store Libraries](https://youtu.be/oPfSC5bQPR8).
* Diego Haz: [Building Accessible Components in React 18](https://youtu.be/dcm8fjBfro8).
* Tafu Nakazaki: [Accessible Japanese Form Components with React](https://youtu.be/S4a0QlsH0pU).
* Lyle Troxell: [UI tools for artists](https://youtu.be/b3l4WxipFsE).
* Helen Lin: [Hydrogen + React 18](https://youtu.be/HS6vIYkSNks).

## Lời cảm ơn {/*thank-you*/}

Đây là năm đầu tiên chúng tôi tự lên kế hoạch tổ chức hội nghị, và chúng tôi có rất nhiều người cần cảm ơn.

Trước tiên, cảm ơn tất cả các diễn giả của chúng tôi [Aakansha Doshi](https://twitter.com/aakansha1216), [Andrew Clark](https://twitter.com/acdlite), [Brian Vaughn](https://twitter.com/brian_d_vaughn), [Daishi Kato](https://twitter.com/dai_shi), [Debbie O’Brien](https://twitter.com/debs_obrien), [Delba de Oliveira](https://twitter.com/delba_oliveira), [Diego Haz](https://twitter.com/diegohaz), [Eric Rozell](https://twitter.com/EricRozell), [Helen Lin](https://twitter.com/wizardlyhel), [Juan Tejada](https://twitter.com/_jstejada), [Lauren Tan](https://twitter.com/potetotes), [Linton Ye](https://twitter.com/lintonye), [Lyle Troxell](https://twitter.com/lyle), [Rachel Nabors](https://twitter.com/rachelnabors), [Rick Hanlon](https://twitter.com/rickhanlonii), [Robert Balicki](https://twitter.com/StatisticsFTW), [Roman Rädle](https://twitter.com/raedle), [Sarah Rainsberger](https://twitter.com/sarah11918), [Shaundai Person](https://twitter.com/shaundai), [Shruti Kapoor](https://twitter.com/shrutikapoor08), [Steven Moyes](https://twitter.com/moyessa), [Tafu Nakazaki](https://twitter.com/hawaiiman0), và [Xuan Huang (黄玄)](https://twitter.com/Huxpro).

Cảm ơn tất cả mọi người đã giúp cung cấp phản hồi về các bài nói bao gồm [Andrew Clark](https://twitter.com/acdlite), [Dan Abramov](https://bsky.app/profile/danabra.mov), [Dave McCabe](https://twitter.com/mcc_abe), [Eli White](https://twitter.com/Eli_White), [Joe Savona](https://twitter.com/en_JS), [Lauren Tan](https://twitter.com/potetotes), [Rachel Nabors](https://twitter.com/rachelnabors), và [Tim Yung](https://twitter.com/yungsters).

Cảm ơn [Lauren Tan](https://twitter.com/potetotes) đã thiết lập Discord hội nghị và phục vụ với tư cách quản trị viên Discord của chúng tôi.

Cảm ơn [Seth Webster](https://twitter.com/sethwebster) đã phản hồi về định hướng tổng thể và đảm bảo chúng tôi tập trung vào đa dạng và hòa nhập.

Cảm ơn [Rachel Nabors](https://twitter.com/rachelnabors) đã dẫn đầu nỗ lực kiểm duyệt của chúng tôi, và [Aisha Blake](https://twitter.com/AishaBlake) đã tạo hướng dẫn kiểm duyệt, dẫn đầu đội ngũ kiểm duyệt, đào tạo các dịch giả và người kiểm duyệt, và giúp kiểm duyệt cả hai sự kiện.

Cảm ơn các người kiểm duyệt [Jesslyn Tannady](https://twitter.com/jtannady), [Suzie Grange](https://twitter.com/missuze), [Becca Bailey](https://twitter.com/beccaliz), [Luna Wei](https://twitter.com/lunaleaps), [Joe Previte](https://twitter.com/jsjoeio), [Nicola Corti](https://twitter.com/Cortinico), [Gijs Weterings](https://twitter.com/gweterings), [Claudio Procida](https://twitter.com/claudiopro), Julia Neumann, Mengdi Chen, Jean Zhang, Ricky Li, và [Xuan Huang (黄玄)](https://twitter.com/Huxpro).

Cảm ơn [Manjula Dube](https://twitter.com/manjula_dube), [Sahil Mhapsekar](https://twitter.com/apheri0), và Vihang Patel từ [React India](https://www.reactindia.io/), và [Jasmine Xie](https://twitter.com/jasmine_xby), [QiChang Li](https://twitter.com/QCL15), và [YanLun Li](https://twitter.com/anneincoding) từ [React China](https://twitter.com/ReactChina) đã giúp kiểm duyệt sự kiện phát lại của chúng tôi và giữ cho nó hấp dẫn với cộng đồng.

Cảm ơn Vercel đã công bố [Virtual Event Starter Kit](https://vercel.com/virtual-event-starter-kit) của họ, trang web hội nghị được xây dựng trên đó, và [Lee Robinson](https://twitter.com/leeerob) và [Delba de Oliveira](https://twitter.com/delba_oliveira) đã chia sẻ kinh nghiệm tổ chức Next.js Conf của họ.

Cảm ơn [Leah Silber](https://twitter.com/wifelette) đã chia sẻ kinh nghiệm tổ chức hội nghị, bài học từ việc tổ chức [RustConf](https://rustconf.com/), và cuốn sách [Event Driven](https://leanpub.com/eventdriven/) của bà và lời khuyên trong đó về việc tổ chức hội nghị.

Cảm ơn [Kevin Lewis](https://twitter.com/_phzn) và [Rachel Nabors](https://twitter.com/rachelnabors) đã chia sẻ kinh nghiệm tổ chức Women of React Conf.

Cảm ơn [Aakansha Doshi](https://twitter.com/aakansha1216), [Laurie Barth](https://twitter.com/laurieontech), [Michael Chan](https://twitter.com/chantastic), và [Shaundai Person](https://twitter.com/shaundai) đã cung cấp lời khuyên và ý tưởng trong suốt quá trình lên kế hoạch.

Cảm ơn [Dan Lebowitz](https://twitter.com/lebo) đã giúp thiết kế và xây dựng trang web hội nghị và vé.

Cảm ơn Laura Podolak Waddell, Desmond Osei-Acheampong, Mark Rossi, Josh Toberman và những người khác trong đội ngũ Facebook Video Productions đã ghi lại các video cho Keynote và các bài nói của nhân viên Meta.

Cảm ơn đối tác HitPlay của chúng tôi đã giúp tổ chức hội nghị, chỉnh sửa tất cả các video trong stream, dịch tất cả các bài nói, và kiểm duyệt Discord bằng nhiều ngôn ngữ.

Cuối cùng, cảm ơn tất cả người tham gia đã làm cho đây là một React Conf tuyệt vời!
