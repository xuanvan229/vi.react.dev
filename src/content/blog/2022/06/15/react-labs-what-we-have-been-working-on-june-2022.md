---
title: "React Labs: Chúng tôi đã làm gì – Tháng 6 năm 2022"
author:  Andrew Clark, Dan Abramov, Jan Kassens, Joseph Savona, Josh Story, Lauren Tan, Luna Ruan, Mengdi Chen, Rick Hanlon, Robert Zhang, Sathya Gunasekaran, Sebastian Markbage, and Xuan Huang
date: 2022/06/15
description: React 18 đã mất nhiều năm để hoàn thành, và cùng với đó đã mang lại những bài học quý giá cho nhóm React. Việc phát hành của nó là kết quả của nhiều năm nghiên cứu và khám phá nhiều con đường. Một số con đường đó đã thành công; nhiều con đường hơn là những con đường cụt dẫn đến những hiểu biết mới. Một bài học chúng tôi đã học được là thật bực bội khi cộng đồng phải chờ đợi các tính năng mới mà không có cái nhìn về những con đường chúng tôi đang khám phá.
---

Ngày 15 tháng 6, 2022 bởi [Andrew Clark](https://twitter.com/acdlite), [Dan Abramov](https://bsky.app/profile/danabra.mov), [Jan Kassens](https://twitter.com/kassens), [Joseph Savona](https://twitter.com/en_JS), [Josh Story](https://twitter.com/joshcstory), [Lauren Tan](https://twitter.com/potetotes), [Luna Ruan](https://twitter.com/lunaruan), [Mengdi Chen](https://twitter.com/mengdi_en), [Rick Hanlon](https://twitter.com/rickhanlonii), [Robert Zhang](https://twitter.com/jiaxuanzhang01), [Sathya Gunasekaran](https://twitter.com/_gsathya), [Sebastian Markbåge](https://twitter.com/sebmarkbage), và [Xuan Huang](https://twitter.com/Huxpro)

---

<Intro>

[React 18](/blog/2022/03/29/react-v18) đã mất nhiều năm để hoàn thành, và cùng với đó đã mang lại những bài học quý giá cho nhóm React. Việc phát hành của nó là kết quả của nhiều năm nghiên cứu và khám phá nhiều con đường. Một số con đường đó đã thành công; nhiều con đường hơn là những con đường cụt dẫn đến những hiểu biết mới. Một bài học chúng tôi đã học được là thật bực bội khi cộng đồng phải chờ đợi các tính năng mới mà không có cái nhìn về những con đường chúng tôi đang khám phá.

</Intro>

---

Chúng tôi thường có một số dự án đang được thực hiện bất kỳ lúc nào, từ những dự án thử nghiệm hơn đến những dự án được xác định rõ ràng. Nhìn về phía trước, chúng tôi muốn bắt đầu chia sẻ thường xuyên hơn về những gì chúng tôi đã làm với cộng đồng trên các dự án này.

Để đặt kỳ vọng, đây không phải là một lộ trình với các mốc thời gian rõ ràng. Nhiều dự án trong số này đang được nghiên cứu tích cực và khó đặt ngày phát hành cụ thể. Chúng thậm chí có thể không bao giờ được phát hành trong lần lặp hiện tại tùy thuộc vào những gì chúng tôi học được. Thay vào đó, chúng tôi muốn chia sẻ với bạn các không gian vấn đề mà chúng tôi đang tích cực nghĩ đến, và những gì chúng tôi đã học được cho đến nay.

## Server Components {/*server-components*/}

Chúng tôi đã thông báo [demo thử nghiệm về React Server Components](https://legacy.reactjs.org/blog/2020/12/21/data-fetching-with-react-server-components.html) (RSC) vào tháng 12 năm 2020. Kể từ đó, chúng tôi đã hoàn thiện các dependencies của nó trong React 18, và làm việc trên các thay đổi được lấy cảm hứng từ phản hồi thử nghiệm.

Đặc biệt, chúng tôi đang từ bỏ ý tưởng có các thư viện I/O được fork (ví dụ: react-fetch), và thay vào đó áp dụng mô hình async/await để tương thích tốt hơn. Điều này không chặn về mặt kỹ thuật việc phát hành RSC vì bạn cũng có thể sử dụng routers để fetch dữ liệu. Một thay đổi khác là chúng tôi cũng đang chuyển từ cách tiếp cận phần mở rộng file sang [chú thích boundaries](https://github.com/reactjs/rfcs/pull/189#issuecomment-1116482278).

Chúng tôi đang làm việc cùng với Vercel và Shopify để thống nhất hỗ trợ bundler cho ngữ nghĩa chia sẻ trong cả webpack và Vite. Trước khi ra mắt, chúng tôi muốn đảm bảo rằng ngữ nghĩa của RSC là như nhau trên toàn bộ hệ sinh thái React. Đây là trở ngại chính để đạt được stable.

## Tải Asset {/*asset-loading*/}

Hiện tại, các asset như scripts, external styles, fonts và images thường được preload và tải bằng các hệ thống bên ngoài. Điều này có thể làm cho việc điều phối trên các môi trường mới như streaming, Server Components, và nhiều hơn nữa trở nên phức tạp.
Chúng tôi đang xem xét việc thêm các API để preload và tải các external asset deduplicated thông qua các API React hoạt động trong tất cả các môi trường React.

Chúng tôi cũng đang xem xét việc có những hỗ trợ Suspense này để bạn có thể có hình ảnh, CSS và fonts chặn hiển thị cho đến khi chúng được tải nhưng không chặn streaming và concurrent rendering. Điều này có thể giúp tránh ["popcorning"](https://twitter.com/sebmarkbage/status/1516852731251724293) khi các hình ảnh bật lên và layout thay đổi.

## Tối ưu hóa Static Server Rendering {/*static-server-rendering-optimizations*/}

Static Site Generation (SSG) và Incremental Static Regeneration (ISR) là những cách tuyệt vời để đạt được hiệu suất cho các trang có thể cache, nhưng chúng tôi nghĩ chúng tôi có thể thêm các tính năng để cải thiện hiệu suất của Server Side Rendering (SSR) động – đặc biệt khi hầu hết nhưng không phải tất cả nội dung có thể cache. Chúng tôi đang khám phá các cách để tối ưu hóa server rendering sử dụng compilation và static passes.

## React Optimizing Compiler {/*react-compiler*/}

Chúng tôi đã đưa ra [bản xem trước sớm](https://www.youtube.com/watch?v=lGEMwh32soc) về React Forget tại React Conf 2021. Đây là một compiler tự động tạo ra tương đương các lời gọi `useMemo` và `useCallback` để giảm thiểu chi phí re-render, trong khi vẫn giữ nguyên mô hình lập trình của React.

Gần đây, chúng tôi đã hoàn thành việc viết lại compiler để làm cho nó đáng tin cậy hơn và có khả năng hơn. Kiến trúc mới này cho phép chúng tôi phân tích và memoize các pattern phức tạp hơn như việc sử dụng [local mutations](/learn/keeping-components-pure#local-mutation-your-components-little-secret), và mở ra nhiều cơ hội tối ưu hóa compile-time mới ngoài việc chỉ ngang bằng với các memoization Hooks.

Chúng tôi cũng đang làm việc trên một playground để khám phá nhiều khía cạnh của compiler. Mặc dù mục tiêu của playground là làm cho việc phát triển compiler dễ dàng hơn, chúng tôi nghĩ nó sẽ giúp dễ dàng thử nghiệm và xây dựng trực giác về những gì compiler làm. Nó tiết lộ nhiều hiểu biết khác nhau về cách nó hoạt động bên dưới, và render trực tiếp đầu ra của compiler khi bạn gõ. Điều này sẽ được phát hành cùng với compiler khi nó được phát hành.

## Offscreen {/*offscreen*/}

Ngày nay, nếu bạn muốn ẩn và hiển thị một component, bạn có hai tùy chọn. Một là thêm hoặc xóa nó khỏi cây hoàn toàn. Vấn đề với cách tiếp cận này là trạng thái UI của bạn bị mất mỗi khi bạn unmount, bao gồm cả trạng thái được lưu trong DOM, như vị trí scroll.

Tùy chọn khác là giữ component được mount và chuyển đổi sự xuất hiện trực quan bằng cách sử dụng CSS. Điều này bảo tồn trạng thái của UI của bạn, nhưng đi kèm với chi phí hiệu suất, vì React phải tiếp tục render component ẩn và tất cả các con của nó mỗi khi nó nhận được các cập nhật mới.

Offscreen giới thiệu một tùy chọn thứ ba: ẩn UI trực quan, nhưng hạ thấp ưu tiên nội dung của nó. Ý tưởng tương tự về tinh thần với thuộc tính CSS `content-visibility`: khi nội dung bị ẩn, nó không cần phải đồng bộ với phần còn lại của UI. React có thể trì hoãn công việc rendering cho đến khi phần còn lại của ứng dụng rảnh rỗi, hoặc cho đến khi nội dung trở nên hiển thị trở lại.

Offscreen là một khả năng cấp thấp mở khóa các tính năng cấp cao. Tương tự như các tính năng concurrent khác của React như `startTransition`, trong hầu hết các trường hợp bạn sẽ không tương tác trực tiếp với API Offscreen, mà thông qua một framework có quan điểm để triển khai các pattern như:

* **Transitions tức thời.** Một số framework routing đã prefetch dữ liệu để tăng tốc các điều hướng tiếp theo, như khi di chuột qua một liên kết. Với Offscreen, họ cũng sẽ có thể prerender màn hình tiếp theo trong nền.
* **Reusable state.** Tương tự, khi điều hướng giữa các routes hoặc tabs, bạn có thể sử dụng Offscreen để bảo tồn trạng thái của màn hình trước đó để bạn có thể chuyển lại và tiếp tục từ nơi bạn đã dừng.
* **Virtualized list rendering.** Khi hiển thị các danh sách lớn các mục, các framework danh sách ảo hóa sẽ prerender nhiều hàng hơn những gì hiện đang hiển thị. Bạn có thể sử dụng Offscreen để prerender các hàng ẩn ở mức ưu tiên thấp hơn so với các mục hiển thị trong danh sách.
* **Nội dung nền.** Chúng tôi cũng đang khám phá một tính năng liên quan để hạ thấp ưu tiên nội dung trong nền mà không ẩn nó, như khi hiển thị một lớp phủ modal.

## Transition Tracing {/*transition-tracing*/}

Hiện tại, React có hai công cụ profiling. [Profiler gốc](https://legacy.reactjs.org/blog/2018/09/10/introducing-the-react-profiler.html) hiển thị tổng quan về tất cả các commit trong một phiên profiling. Đối với mỗi commit, nó cũng hiển thị tất cả các component đã render và thời gian để chúng render. Chúng tôi cũng có phiên bản beta của [Timeline Profiler](https://github.com/reactwg/react-18/discussions/76) được giới thiệu trong React 18 hiển thị khi các component lên lịch cập nhật và khi React làm việc trên các cập nhật này. Cả hai profilers này giúp các nhà phát triển xác định các vấn đề hiệu suất trong code của họ.

Chúng tôi đã nhận ra rằng các nhà phát triển không thấy việc biết về các commit chậm hoặc component riêng lẻ bên ngoài ngữ cảnh là hữu ích. Sẽ hữu ích hơn khi biết về những gì thực sự gây ra các commit chậm. Và rằng các nhà phát triển muốn có thể theo dõi các tương tác cụ thể (ví dụ: một lần nhấp nút, một tải ban đầu, hoặc một điều hướng trang) để theo dõi các hồi quy hiệu suất và hiểu tại sao một tương tác lại chậm và cách khắc phục nó.

Trước đây, chúng tôi đã cố gắng giải quyết vấn đề này bằng cách tạo một [Interaction Tracing API](https://gist.github.com/bvaughn/8de925562903afd2e7a12554adcdda16), nhưng nó có một số lỗi thiết kế cơ bản làm giảm độ chính xác của việc theo dõi tại sao một tương tác lại chậm và đôi khi dẫn đến các tương tác không bao giờ kết thúc. Chúng tôi cuối cùng đã [xóa API này](https://github.com/facebook/react/pull/20037) vì những vấn đề này.

Chúng tôi đang làm việc trên một phiên bản mới cho Interaction Tracing API (tạm gọi là Transition Tracing vì nó được khởi xướng thông qua `startTransition`) giải quyết những vấn đề này.

## Tài liệu React mới {/*new-react-docs*/}

Năm ngoái, chúng tôi đã thông báo phiên bản beta của trang tài liệu React mới ([được phát hành sau như react.dev](/blog/2023/03/16/introducing-react-dev)). Các tài liệu học tập mới dạy Hooks trước và có các sơ đồ, hình minh họa mới, cũng như nhiều ví dụ và thử thách tương tác. Chúng tôi đã tạm dừng công việc đó để tập trung vào việc phát hành React 18, nhưng bây giờ React 18 đã ra mắt, chúng tôi đang tích cực làm việc để hoàn thành và phát hành tài liệu mới.

Chúng tôi hiện đang viết một phần chi tiết về effects, vì chúng tôi đã nghe đó là một trong những chủ đề khó khăn hơn cho cả người dùng React mới và có kinh nghiệm. [Đồng bộ hóa với Effects](/learn/synchronizing-with-effects) là trang đầu tiên được xuất bản trong loạt bài, và có nhiều hơn nữa sẽ đến trong những tuần tiếp theo. Khi chúng tôi bắt đầu viết phần chi tiết về effects, chúng tôi đã nhận ra rằng nhiều pattern effect phổ biến có thể được đơn giản hóa bằng cách thêm một primitive mới vào React. Chúng tôi đã chia sẻ một số suy nghĩ ban đầu về điều đó trong [useEvent RFC](https://github.com/reactjs/rfcs/pull/220). Nó hiện đang trong giai đoạn nghiên cứu ban đầu, và chúng tôi vẫn đang lặp lại ý tưởng. Chúng tôi đánh giá cao các nhận xét của cộng đồng về RFC cho đến nay, cũng như [phản hồi](https://github.com/reactjs/react.dev/issues/3308) và đóng góp cho việc viết lại tài liệu đang diễn ra. Chúng tôi đặc biệt muốn cảm ơn [Harish Kumar](https://github.com/harish-sethuraman) vì đã gửi và xem xét nhiều cải tiến cho triển khai trang web mới.

*Cảm ơn [Sophie Alpert](https://twitter.com/sophiebits) đã xem xét bài viết blog này!*
