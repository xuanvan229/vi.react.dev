---
title: "React Compiler Beta Release"
author: Lauren Tan
date: 2024/10/21
description: Tại React Conf 2024, chúng tôi đã công bố bản phát hành thử nghiệm của React Compiler, một công cụ build-time tối ưu hóa ứng dụng React của bạn thông qua memoization tự động. Trong bài viết này, chúng tôi muốn chia sẻ những gì tiếp theo cho mã nguồn mở và tiến trình của chúng tôi trên compiler.

---

October 21, 2024 by [Lauren Tan](https://twitter.com/potetotes).

---

<Note>

### React Compiler hiện đã ổn định! {/*react-compiler-is-now-in-rc*/}

Vui lòng xem [bài viết phát hành ổn định](/blog/2025/10/07/react-compiler-1) để biết chi tiết.

</Note>

<Intro>

Nhóm React hào hứng chia sẻ các cập nhật mới:

</Intro>

1. Chúng tôi đang phát hành React Compiler Beta hôm nay, để những người áp dụng sớm và người bảo trì thư viện có thể thử và cung cấp phản hồi.
2. Chúng tôi chính thức hỗ trợ React Compiler cho các ứng dụng trên React 17+, thông qua gói tùy chọn `react-compiler-runtime`.
3. Chúng tôi đang mở rộng tư cách thành viên công khai của [React Compiler Working Group](https://github.com/reactwg/react-compiler) để chuẩn bị cộng đồng cho việc áp dụng compiler dần dần.

---

Tại [React Conf 2024](/blog/2024/05/22/react-conf-2024-recap), chúng tôi đã công bố bản phát hành thử nghiệm của React Compiler, một công cụ build-time tối ưu hóa ứng dụng React của bạn thông qua memoization tự động. [Bạn có thể tìm thấy phần giới thiệu về React Compiler tại đây](/learn/react-compiler).

Kể từ lần phát hành đầu tiên, chúng tôi đã sửa nhiều lỗi được báo cáo bởi cộng đồng React, nhận được một số bản sửa lỗi và đóng góp[^1] chất lượng cao cho compiler, làm cho compiler trở nên linh hoạt hơn với sự đa dạng rộng lớn của các mẫu JavaScript, và tiếp tục triển khai compiler rộng rãi hơn tại Meta.

Trong bài viết này, chúng tôi muốn chia sẻ những gì tiếp theo cho React Compiler.

## Thử React Compiler Beta ngay hôm nay {/*try-react-compiler-beta-today*/}

Tại [React India 2024](https://www.youtube.com/watch?v=qd5yk2gxbtg), chúng tôi đã chia sẻ cập nhật về React Compiler. Hôm nay, chúng tôi hào hứng công bố bản phát hành Beta mới của React Compiler và plugin ESLint. Các bản beta mới được xuất bản lên npm sử dụng tag `@beta`.

Để cài đặt React Compiler Beta:

<TerminalBlock>
npm install -D babel-plugin-react-compiler@beta eslint-plugin-react-compiler@beta
</TerminalBlock>

Hoặc, nếu bạn đang dùng Yarn:

<TerminalBlock>
yarn add -D babel-plugin-react-compiler@beta eslint-plugin-react-compiler@beta
</TerminalBlock>

Bạn có thể xem bài nói của [Sathya Gunasekaran](https://twitter.com/_gsathya) tại React India tại đây:

<YouTubeIframe src="https://www.youtube.com/embed/qd5yk2gxbtg" />

## Chúng tôi khuyến nghị tất cả mọi người dùng React Compiler linter ngay hôm nay {/*we-recommend-everyone-use-the-react-compiler-linter-today*/}

Plugin ESLint của React Compiler giúp các nhà phát triển chủ động xác định và sửa các vi phạm [Rules of React](/reference/rules). **Chúng tôi khuyến nghị mạnh mẽ tất cả mọi người dùng linter ngay hôm nay**. Linter không yêu cầu bạn phải cài đặt compiler, vì vậy bạn có thể sử dụng nó độc lập, ngay cả khi bạn chưa sẵn sàng thử compiler.

Để chỉ cài đặt linter:

<TerminalBlock>
npm install -D eslint-plugin-react-compiler@beta
</TerminalBlock>

Hoặc, nếu bạn đang dùng Yarn:

<TerminalBlock>
yarn add -D eslint-plugin-react-compiler@beta
</TerminalBlock>

Sau khi cài đặt, bạn có thể kích hoạt linter bằng cách [thêm nó vào cấu hình ESLint của bạn](/learn/react-compiler/installation#eslint-integration). Việc sử dụng linter giúp xác định các vi phạm Rules of React, giúp việc áp dụng compiler dễ dàng hơn khi nó được phát hành đầy đủ.

## Tương thích ngược {/*backwards-compatibility*/}

React Compiler tạo ra code phụ thuộc vào các runtime API được thêm vào trong React 19, nhưng chúng tôi đã thêm hỗ trợ cho compiler để cũng hoạt động với React 17 và 18. Nếu bạn chưa dùng React 19, trong bản Beta bạn có thể thử React Compiler bằng cách chỉ định `target` tối thiểu trong cấu hình compiler của bạn, và thêm `react-compiler-runtime` như một dependency. [Bạn có thể tìm tài liệu về điều này tại đây](/reference/react-compiler/configuration#react-17-18).

## Sử dụng React Compiler trong thư viện {/*using-react-compiler-in-libraries*/}

Lần phát hành ban đầu của chúng tôi tập trung vào việc xác định các vấn đề lớn khi sử dụng compiler trong các ứng dụng. Chúng tôi đã nhận được phản hồi tuyệt vời và đã cải thiện đáng kể compiler kể từ đó. Chúng tôi hiện đã sẵn sàng cho phản hồi rộng rãi từ cộng đồng, và cho các tác giả thư viện thử compiler để cải thiện hiệu suất và trải nghiệm phát triển khi duy trì thư viện của bạn.

React Compiler cũng có thể được dùng để compile thư viện. Vì React Compiler cần chạy trên mã nguồn gốc trước bất kỳ biến đổi code nào, không thể để pipeline build của ứng dụng compile các thư viện mà chúng sử dụng. Do đó, khuyến nghị của chúng tôi là để người bảo trì thư viện độc lập compile và kiểm tra thư viện của họ với compiler, và giao code đã được compile lên npm.

Vì code của bạn đã được pre-compiled, người dùng thư viện của bạn sẽ không cần phải bật compiler để được hưởng lợi từ memoization tự động được áp dụng cho thư viện của bạn. Nếu thư viện của bạn nhắm đến các ứng dụng chưa có React 19, hãy chỉ định `target` tối thiểu và thêm `react-compiler-runtime` như một direct dependency. Gói runtime sẽ sử dụng triển khai đúng của các API tùy thuộc vào phiên bản của ứng dụng, và polyfill các API còn thiếu nếu cần.

[Bạn có thể tìm thêm tài liệu về điều này tại đây.](/reference/react-compiler/compiling-libraries)

## Mở rộng React Compiler Working Group cho mọi người {/*opening-up-react-compiler-working-group-to-everyone*/}

Chúng tôi đã công bố trước đó [React Compiler Working Group](https://github.com/reactwg/react-compiler) chỉ theo lời mời tại React Conf để cung cấp phản hồi, đặt câu hỏi, và cộng tác về bản phát hành thử nghiệm của compiler.

Từ hôm nay, cùng với bản phát hành Beta của React Compiler, chúng tôi đang mở rộng tư cách thành viên Working Group cho mọi người. Mục tiêu của React Compiler Working Group là chuẩn bị hệ sinh thái cho việc áp dụng React Compiler một cách suôn sẻ, dần dần bởi các ứng dụng và thư viện hiện có. Vui lòng tiếp tục nộp báo cáo lỗi trong [repo React](https://github.com/facebook/react), nhưng hãy để lại phản hồi, đặt câu hỏi, hoặc chia sẻ ý tưởng trong [diễn đàn thảo luận Working Group](https://github.com/reactwg/react-compiler/discussions).

Nhóm cốt lõi cũng sẽ sử dụng repo thảo luận để chia sẻ các phát hiện nghiên cứu của chúng tôi. Khi Stable Release ngày càng gần hơn, mọi thông tin quan trọng cũng sẽ được đăng trên diễn đàn này.

## React Compiler tại Meta {/*react-compiler-at-meta*/}

Tại [React Conf](/blog/2024/05/22/react-conf-2024-recap), chúng tôi đã chia sẻ rằng việc triển khai compiler trên Quest Store và Instagram đã thành công. Kể từ đó, chúng tôi đã triển khai React Compiler trên một số ứng dụng web lớn hơn tại Meta, bao gồm [Facebook](https://www.facebook.com) và [Threads](https://www.threads.net). Điều đó có nghĩa là nếu bạn đã sử dụng bất kỳ ứng dụng nào trong số này gần đây, trải nghiệm của bạn có thể đã được hỗ trợ bởi compiler. Chúng tôi đã có thể tích hợp các ứng dụng này vào compiler với ít thay đổi code cần thiết, trong một monorepo với hơn 100,000 React component.

Chúng tôi đã thấy cải thiện hiệu suất đáng kể trên tất cả các ứng dụng này. Khi chúng tôi triển khai, chúng tôi tiếp tục thấy kết quả theo thứ tự của [các thành quả chúng tôi đã chia sẻ trước đó tại ReactConf](https://youtu.be/lyEKhv8-3n0?t=3223). Các ứng dụng này đã được điều chỉnh thủ công và tối ưu hóa kỹ lưỡng bởi các kỹ sư Meta và các chuyên gia React trong nhiều năm, vì vậy ngay cả những cải thiện khoảng vài phần trăm cũng là một thắng lợi to lớn đối với chúng tôi.

Chúng tôi cũng kỳ vọng lợi ích về năng suất của nhà phát triển từ React Compiler. Để đo lường điều này, chúng tôi đã hợp tác với các đối tác khoa học dữ liệu tại Meta[^2] để tiến hành phân tích thống kê kỹ lưỡng về tác động của memoization thủ công đến năng suất. Trước khi triển khai compiler tại Meta, chúng tôi phát hiện rằng chỉ khoảng 8% pull request React sử dụng memoization thủ công và những pull request này mất 31-46% thời gian lâu hơn để tạo[^3]. Điều này xác nhận trực giác của chúng tôi rằng memoization thủ công gây ra overhead nhận thức, và chúng tôi dự đoán rằng React Compiler sẽ dẫn đến việc tạo và review code hiệu quả hơn. Đáng chú ý, React Compiler cũng đảm bảo rằng *tất cả* code được memoize theo mặc định, không chỉ 8% (trong trường hợp của chúng tôi) nơi các nhà phát triển áp dụng memoization một cách rõ ràng.

## Lộ trình đến Stable {/*roadmap-to-stable*/}

*Đây không phải là lộ trình cuối cùng, và có thể thay đổi.*

Chúng tôi có kế hoạch phát hành Release Candidate của compiler trong tương lai gần sau bản Beta, khi phần lớn các ứng dụng và thư viện tuân theo Rules of React đã được chứng minh là hoạt động tốt với compiler. Sau một giai đoạn phản hồi cuối cùng từ cộng đồng, chúng tôi lên kế hoạch phát hành Stable Release cho compiler. Stable Release sẽ đánh dấu sự khởi đầu của một nền tảng mới cho React, và tất cả các ứng dụng và thư viện sẽ được khuyến nghị mạnh mẽ sử dụng compiler và plugin ESLint.

* ✅ Experimental: Phát hành tại React Conf 2024, chủ yếu để nhận phản hồi từ những người áp dụng sớm.
* ✅ Public Beta: Có sẵn hôm nay, để nhận phản hồi từ cộng đồng rộng hơn.
* 🚧 Release Candidate (RC): React Compiler hoạt động cho phần lớn các ứng dụng và thư viện tuân theo quy tắc mà không gặp vấn đề.
* 🚧 General Availability: Sau giai đoạn phản hồi cuối cùng từ cộng đồng.

Các bản phát hành này cũng bao gồm plugin ESLint của compiler, hiển thị các chẩn đoán được phân tích tĩnh bởi compiler. Chúng tôi có kế hoạch kết hợp plugin eslint-plugin-react-hooks hiện có với plugin ESLint của compiler, để chỉ cần cài đặt một plugin.

Sau Stable, chúng tôi có kế hoạch thêm nhiều tối ưu hóa và cải tiến compiler hơn. Điều này bao gồm cả những cải tiến liên tục cho memoization tự động và các tối ưu hóa mới hoàn toàn, với sự thay đổi tối thiểu đến không có của code sản phẩm. Việc nâng cấp lên mỗi bản phát hành mới của compiler nhằm mục đích đơn giản, và mỗi lần nâng cấp sẽ tiếp tục cải thiện hiệu suất và thêm khả năng xử lý tốt hơn các mẫu JavaScript và React đa dạng.

Trong suốt quá trình này, chúng tôi cũng có kế hoạch tạo nguyên mẫu cho một IDE extension cho React. Vẫn còn rất sớm trong nghiên cứu, vì vậy chúng tôi hy vọng có thể chia sẻ thêm các phát hiện của mình với bạn trong một bài viết React Labs tương lai.

---

Cảm ơn [Sathya Gunasekaran](https://twitter.com/_gsathya), [Joe Savona](https://twitter.com/en_JS), [Ricky Hanlon](https://twitter.com/rickhanlonii), [Alex Taylor](https://github.com/alexmckenley), [Jason Bonta](https://twitter.com/someextent), và [Eli White](https://twitter.com/Eli_White) đã review và chỉnh sửa bài viết này.

---

[^1]: Cảm ơn [@nikeee](https://github.com/facebook/react/pulls?q=is%3Apr+author%3Anikeee), [@henryqdineen](https://github.com/facebook/react/pulls?q=is%3Apr+author%3Ahenryqdineen), [@TrickyPi](https://github.com/facebook/react/pulls?q=is%3Apr+author%3ATrickyPi), và một số người khác vì đóng góp của họ cho compiler.

[^2]: Cảm ơn [Vaishali Garg](https://www.linkedin.com/in/vaishaligarg09) đã dẫn dắt nghiên cứu này về React Compiler tại Meta, và đã review bài viết này.

[^3]: Sau khi kiểm soát thâm niên tác giả, độ dài/phức tạp của diff, và các yếu tố gây nhiễu tiềm năng khác.