---
title: "Kế Hoạch Cho React 18"
author: Andrew Clark, Brian Vaughn, Christine Abernathy, Dan Abramov, Rachel Nabors, Rick Hanlon, Sebastian Markbage, and Seth Webster
date: 2021/06/08
description: Đội ngũ React vui mừng chia sẻ một số cập nhật. Chúng tôi đã bắt đầu làm việc trên bản phát hành React 18, sẽ là phiên bản chính tiếp theo. Chúng tôi đã tạo Working Group để chuẩn bị cho cộng đồng áp dụng dần dần các tính năng mới trong React 18. Chúng tôi đã phát hành React 18 Alpha để các tác giả thư viện có thể thử và cung cấp phản hồi...
---

June 8, 2021 by [Andrew Clark](https://twitter.com/acdlite), [Brian Vaughn](https://github.com/bvaughn), [Christine Abernathy](https://twitter.com/abernathyca), [Dan Abramov](https://bsky.app/profile/danabra.mov), [Rachel Nabors](https://twitter.com/rachelnabors), [Rick Hanlon](https://twitter.com/rickhanlonii), [Sebastian Markbage](https://twitter.com/sebmarkbage), and [Seth Webster](https://twitter.com/sethwebster)

---

<Intro>

Đội ngũ React vui mừng chia sẻ một số cập nhật:

1. Chúng tôi đã bắt đầu làm việc trên bản phát hành React 18, sẽ là phiên bản chính tiếp theo.
2. Chúng tôi đã tạo Working Group để chuẩn bị cho cộng đồng áp dụng dần dần các tính năng mới trong React 18.
3. Chúng tôi đã phát hành React 18 Alpha để các tác giả thư viện có thể thử và cung cấp phản hồi.

Các cập nhật này chủ yếu hướng đến các nhà bảo trì thư viện bên thứ ba. Nếu bạn đang học, giảng dạy, hoặc sử dụng React để xây dựng ứng dụng cho người dùng, bạn có thể bỏ qua bài viết này. Nhưng bạn luôn có thể theo dõi các cuộc thảo luận trong React 18 Working Group nếu bạn tò mò!

---

</Intro>

## Những gì sắp có trong React 18 {/*whats-coming-in-react-18*/}

Khi được phát hành, React 18 sẽ bao gồm các cải tiến có sẵn ngay (như [automatic batching](https://github.com/reactwg/react-18/discussions/21)), API mới (như [`startTransition`](https://github.com/reactwg/react-18/discussions/41)), và [streaming server renderer mới](https://github.com/reactwg/react-18/discussions/37) với hỗ trợ tích hợp cho `React.lazy`.

Các tính năng này có thể thực hiện được nhờ một cơ chế tùy chọn mới mà chúng tôi đang thêm vào React 18. Nó được gọi là "concurrent rendering" và cho phép React chuẩn bị nhiều phiên bản UI cùng một lúc. Thay đổi này chủ yếu ở hậu trường, nhưng nó mở ra các khả năng mới để cải thiện cả hiệu suất thực tế và cảm nhận của ứng dụng.

Nếu bạn đã theo dõi nghiên cứu của chúng tôi về tương lai của React (chúng tôi không mong đợi bạn phải làm vậy!), bạn có thể đã nghe về thứ gọi là "concurrent mode" hoặc rằng nó có thể làm hỏng ứng dụng của bạn. Để đáp ứng phản hồi này từ cộng đồng, chúng tôi đã thiết kế lại chiến lược nâng cấp để áp dụng dần dần. Thay vì chế độ "tất cả hoặc không gì", concurrent rendering chỉ được kích hoạt cho các cập nhật được kích hoạt bởi một trong những tính năng mới. Trong thực tế, điều này có nghĩa là **bạn sẽ có thể áp dụng React 18 mà không cần viết lại và thử các tính năng mới theo tốc độ của riêng mình.**

## Chiến lược áp dụng dần dần {/*a-gradual-adoption-strategy*/}

Vì concurrency trong React 18 là tùy chọn, không có thay đổi không tương thích ngược đáng kể nào đối với hành vi của component. **Bạn có thể nâng cấp lên React 18 với ít hoặc không có thay đổi đối với mã ứng dụng, với mức độ nỗ lực tương đương với một bản phát hành chính React thông thường**. Dựa trên kinh nghiệm chuyển đổi nhiều ứng dụng sang React 18, chúng tôi kỳ vọng rằng nhiều người dùng sẽ có thể nâng cấp trong một buổi chiều.

Chúng tôi đã thành công triển khai các tính năng concurrent cho hàng chục nghìn component tại Facebook, và theo kinh nghiệm của chúng tôi, hầu hết các React component "hoạt động bình thường" mà không cần thay đổi thêm. Chúng tôi cam kết đảm bảo đây là quá trình nâng cấp suôn sẻ cho toàn bộ cộng đồng, và hôm nay chúng tôi thông báo React 18 Working Group.

## Làm việc cùng cộng đồng {/*working-with-the-community*/}

Chúng tôi đang thử một điều mới cho lần phát hành này: Chúng tôi đã mời một nhóm chuyên gia, nhà phát triển, tác giả thư viện, và nhà giáo dục từ khắp cộng đồng React tham gia vào [React 18 Working Group](https://github.com/reactwg/react-18) để cung cấp phản hồi, đặt câu hỏi, và cộng tác trong lần phát hành này. Chúng tôi không thể mời tất cả những người mà chúng tôi muốn vào nhóm ban đầu nhỏ này, nhưng nếu thí nghiệm này thành công, chúng tôi hy vọng sẽ có nhiều hơn trong tương lai!

**Mục tiêu của React 18 Working Group là chuẩn bị hệ sinh thái cho việc áp dụng suôn sẻ, dần dần React 18 bởi các ứng dụng và thư viện hiện có.** Working Group được lưu trữ trên [GitHub Discussions](https://github.com/reactwg/react-18/discussions) và có sẵn cho công chúng đọc. Thành viên của working group có thể gửi phản hồi, đặt câu hỏi, và chia sẻ ý tưởng. Đội ngũ core cũng sẽ sử dụng repo discussions để chia sẻ kết quả nghiên cứu. Khi bản phát hành ổn định đến gần hơn, mọi thông tin quan trọng cũng sẽ được đăng trên blog này.

Để biết thêm thông tin về việc nâng cấp lên React 18, hoặc tài nguyên bổ sung về bản phát hành, xem [bài thông báo React 18](https://github.com/reactwg/react-18/discussions/4).

## Truy cập React 18 Working Group {/*accessing-the-react-18-working-group*/}

Mọi người đều có thể đọc các cuộc thảo luận trong [React 18 Working Group repo](https://github.com/reactwg/react-18).

Vì chúng tôi dự kiến sẽ có đợt quan tâm ban đầu lớn đối với Working Group, chỉ những thành viên được mời mới có thể tạo hoặc bình luận trên các chuỗi thảo luận. Tuy nhiên, các chuỗi hoàn toàn hiển thị công khai, nên mọi người đều có quyền truy cập cùng thông tin. Chúng tôi tin rằng đây là sự cân bằng tốt giữa việc tạo môi trường làm việc hiệu quả cho các thành viên working group, đồng thời duy trì tính minh bạch với cộng đồng rộng hơn.

Như mọi khi, bạn có thể gửi báo cáo lỗi, câu hỏi, và phản hồi chung đến [issue tracker](https://github.com/facebook/react/issues) của chúng tôi.

## Cách thử React 18 Alpha hôm nay {/*how-to-try-react-18-alpha-today*/}

Các bản alpha mới được [phát hành thường xuyên trên npm sử dụng tag `@alpha`](https://github.com/reactwg/react-18/discussions/9). Các bản phát hành này được build từ commit mới nhất của repo chính. Khi một tính năng hoặc bản sửa lỗi được merge, nó sẽ xuất hiện trong bản alpha vào ngày làm việc tiếp theo.

Có thể có những thay đổi đáng kể về hành vi hoặc API giữa các bản alpha. Xin nhớ rằng **các bản alpha không được khuyến nghị cho các ứng dụng sản xuất hướng đến người dùng**.

## Lịch trình phát hành React 18 dự kiến {/*projected-react-18-release-timeline*/}

Chúng tôi chưa có ngày phát hành cụ thể, nhưng chúng tôi kỳ vọng sẽ cần vài tháng phản hồi và tinh chỉnh trước khi React 18 sẵn sàng cho hầu hết các ứng dụng sản xuất.

* Library Alpha: Có sẵn hôm nay
* Public Beta: Ít nhất vài tháng
* Release Candidate (RC): Ít nhất vài tuần sau Beta
* General Availability: Ít nhất vài tuần sau RC

Thêm chi tiết về lịch trình phát hành dự kiến [có sẵn trong Working Group](https://github.com/reactwg/react-18/discussions/9). Chúng tôi sẽ đăng cập nhật trên blog này khi gần đến bản phát hành công khai hơn.
