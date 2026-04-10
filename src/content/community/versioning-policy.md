---
title: Chính Sách Phiên Bản
---

<Intro>

Tất cả các bản build ổn định của React đều trải qua quá trình kiểm thử ở mức cao và tuân theo semantic versioning (semver). React cũng cung cấp các kênh phát hành không ổn định để khuyến khích phản hồi sớm về các tính năng thử nghiệm. Trang này mô tả những gì bạn có thể mong đợi từ các bản phát hành React.

</Intro>

Chính sách phiên bản này mô tả cách tiếp cận của chúng tôi đối với số phiên bản cho các package như `react` và `react-dom`. Để xem danh sách các bản phát hành trước đây, hãy xem trang [Phiên Bản](/versions).

## Bản phát hành ổn định {/*stable-releases*/}

Các bản phát hành React ổn định (còn được gọi là kênh phát hành "Latest") tuân theo nguyên tắc [semantic versioning (semver)](https://semver.org/).

Điều đó có nghĩa là với số phiên bản **x.y.z**:

* Khi phát hành **sửa lỗi nghiêm trọng**, chúng tôi tạo một **bản phát hành patch** bằng cách thay đổi số **z** (ví dụ: 15.6.2 thành 15.6.3).
* Khi phát hành **tính năng mới** hoặc **sửa lỗi không nghiêm trọng**, chúng tôi tạo một **bản phát hành minor** bằng cách thay đổi số **y** (ví dụ: 15.6.2 thành 15.7.0).
* Khi phát hành **thay đổi không tương thích ngược**, chúng tôi tạo một **bản phát hành major** bằng cách thay đổi số **x** (ví dụ: 15.6.2 thành 16.0.0).

Các bản phát hành major cũng có thể chứa tính năng mới, và bất kỳ bản phát hành nào cũng có thể bao gồm sửa lỗi.

Các bản phát hành minor là loại phát hành phổ biến nhất.

Chúng tôi biết rằng người dùng tiếp tục sử dụng các phiên bản cũ của React trong production. Nếu chúng tôi phát hiện lỗ hổng bảo mật trong React, chúng tôi sẽ phát hành bản sửa lỗi backport cho tất cả các phiên bản major bị ảnh hưởng bởi lỗ hổng đó.

### Thay đổi không tương thích ngược {/*breaking-changes*/}

Thay đổi không tương thích ngược gây bất tiện cho mọi người, vì vậy chúng tôi cố gắng giảm thiểu số lượng bản phát hành major -- ví dụ, React 15 được phát hành vào tháng 4 năm 2016 và React 16 được phát hành vào tháng 9 năm 2017, và React 17 được phát hành vào tháng 10 năm 2020.

Thay vào đó, chúng tôi phát hành tính năng mới trong các phiên bản minor. Điều đó có nghĩa là các bản phát hành minor thường thú vị và hấp dẫn hơn các bản major, mặc dù tên gọi khiêm tốn của chúng.

### Cam kết về sự ổn định {/*commitment-to-stability*/}

Khi chúng tôi thay đổi React theo thời gian, chúng tôi cố gắng giảm thiểu nỗ lực cần thiết để tận dụng các tính năng mới. Khi có thể, chúng tôi sẽ giữ API cũ hoạt động, ngay cả khi điều đó có nghĩa là đặt nó vào một package riêng. Ví dụ, [mixin đã bị khuyến cáo không sử dụng trong nhiều năm](https://legacy.reactjs.org/blog/2016/07/13/mixins-considered-harmful.html) nhưng chúng vẫn được hỗ trợ cho đến ngày nay [thông qua create-react-class](https://legacy.reactjs.org/docs/react-without-es6.html#mixins) và nhiều codebase tiếp tục sử dụng chúng trong code ổn định, cũ.

Hơn một triệu nhà phát triển sử dụng React, cùng nhau duy trì hàng triệu component. Riêng codebase Facebook có hơn 50.000 React component. Điều đó có nghĩa là chúng tôi cần làm cho việc nâng cấp lên phiên bản mới của React dễ dàng nhất có thể; nếu chúng tôi thực hiện những thay đổi lớn mà không có lộ trình di chuyển, mọi người sẽ bị kẹt ở phiên bản cũ. Chúng tôi kiểm tra các lộ trình nâng cấp này trên chính Facebook -- nếu đội ngũ ít hơn 10 người của chúng tôi có thể cập nhật hơn 50.000 component một mình, chúng tôi hy vọng việc nâng cấp sẽ khả thi cho bất kỳ ai sử dụng React. Trong nhiều trường hợp, chúng tôi viết [script tự động](https://github.com/reactjs/react-codemod) để nâng cấp cú pháp component, sau đó bao gồm trong bản phát hành mã nguồn mở cho mọi người sử dụng.

### Nâng cấp dần dần thông qua cảnh báo {/*gradual-upgrades-via-warnings*/}

Các bản build phát triển của React bao gồm nhiều cảnh báo hữu ích. Bất cứ khi nào có thể, chúng tôi thêm cảnh báo để chuẩn bị cho các thay đổi không tương thích ngược trong tương lai. Bằng cách đó, nếu ứng dụng của bạn không có cảnh báo nào trên bản phát hành mới nhất, nó sẽ tương thích với bản phát hành major tiếp theo. Điều này cho phép bạn nâng cấp ứng dụng từng component một.

Các cảnh báo phát triển sẽ không ảnh hưởng đến hành vi runtime của ứng dụng. Bằng cách đó, bạn có thể tự tin rằng ứng dụng sẽ hoạt động giống nhau giữa bản build phát triển và production -- sự khác biệt duy nhất là bản build production sẽ không ghi log cảnh báo và hiệu quả hơn. (Nếu bạn nhận thấy điều ngược lại, vui lòng tạo issue.)

### Điều gì được coi là thay đổi không tương thích ngược? {/*what-counts-as-a-breaking-change*/}

Nói chung, chúng tôi *không* tăng số phiên bản major cho các thay đổi đối với:

* **Cảnh báo phát triển.** Vì các cảnh báo này không ảnh hưởng đến hành vi production, chúng tôi có thể thêm cảnh báo mới hoặc sửa đổi cảnh báo hiện có giữa các phiên bản major. Trên thực tế, đây là điều cho phép chúng tôi cảnh báo một cách đáng tin cậy về các thay đổi không tương thích ngược sắp tới.
* **Các API bắt đầu bằng `unstable_`.** Đây là các tính năng thử nghiệm mà chúng tôi chưa tự tin về API. Bằng cách phát hành chúng với tiền tố `unstable_`, chúng tôi có thể lặp lại nhanh hơn và đạt được API ổn định sớm hơn.
* **Phiên bản Alpha và Canary của React.** Chúng tôi cung cấp phiên bản alpha của React như một cách để kiểm tra tính năng mới sớm, nhưng chúng tôi cần sự linh hoạt để thực hiện thay đổi dựa trên những gì chúng tôi học được trong giai đoạn alpha. Nếu bạn sử dụng các phiên bản này, lưu ý rằng API có thể thay đổi trước bản phát hành ổn định.
* **API không được ghi nhận và cấu trúc dữ liệu nội bộ.** Nếu bạn truy cập tên thuộc tính nội bộ như `__SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED` hoặc `__reactInternalInstance$uk43rzhitjg`, không có bảo đảm nào. Bạn tự chịu trách nhiệm.

Chính sách này được thiết kế để thực dụng: chắc chắn rằng, chúng tôi không muốn gây đau đầu cho bạn. Nếu chúng tôi tăng phiên bản major cho tất cả các thay đổi này, chúng tôi sẽ phát hành nhiều phiên bản major hơn và cuối cùng gây ra nhiều khó khăn về phiên bản hơn cho cộng đồng. Điều đó cũng có nghĩa là chúng tôi không thể cải thiện React nhanh như mong muốn.

Tuy nhiên, nếu chúng tôi dự đoán rằng một thay đổi trong danh sách này sẽ gây ra vấn đề rộng rãi trong cộng đồng, chúng tôi vẫn sẽ cố gắng hết sức để cung cấp lộ trình di chuyển dần dần.

### Nếu bản phát hành minor không có tính năng mới, tại sao nó không phải là patch? {/*if-a-minor-release-includes-no-new-features-why-isnt-it-a-patch*/}

Có thể xảy ra trường hợp một bản phát hành minor sẽ không bao gồm tính năng mới. [Điều này được semver cho phép](https://semver.org/#spec-item-7), trong đó nêu rõ **"[phiên bản minor] CÓ THỂ được tăng nếu chức năng mới đáng kể hoặc cải tiến được giới thiệu trong code nội bộ. Nó CÓ THỂ bao gồm các thay đổi mức patch."**

Tuy nhiên, điều này đặt ra câu hỏi tại sao các bản phát hành này không được đánh số phiên bản là patch.

Câu trả lời là bất kỳ thay đổi nào đối với React (hoặc phần mềm khác) đều mang theo một số rủi ro bị hỏng theo cách không mong đợi. Hãy tưởng tượng một kịch bản trong đó bản phát hành patch sửa một lỗi lại vô tình gây ra một lỗi khác. Điều này không chỉ gây phiền toái cho các nhà phát triển, mà còn làm giảm niềm tin vào các bản phát hành patch trong tương lai. Đặc biệt đáng tiếc nếu bản sửa lỗi ban đầu là cho một lỗi hiếm gặp trong thực tế.

Chúng tôi có thành tích khá tốt trong việc giữ các bản phát hành React không có lỗi, nhưng các bản phát hành patch có tiêu chuẩn cao hơn về độ tin cậy vì hầu hết các nhà phát triển cho rằng chúng có thể được áp dụng mà không có hậu quả bất lợi.

Vì những lý do này, chúng tôi chỉ dành bản phát hành patch cho các lỗi nghiêm trọng nhất và lỗ hổng bảo mật.

Nếu bản phát hành bao gồm các thay đổi không thiết yếu -- chẳng hạn như tái cấu trúc nội bộ, thay đổi chi tiết triển khai, cải tiến hiệu suất, hoặc sửa lỗi nhỏ -- chúng tôi sẽ tăng phiên bản minor ngay cả khi không có tính năng mới.

## Tất cả các kênh phát hành {/*all-release-channels*/}

React dựa vào một cộng đồng mã nguồn mở sôi động để báo lỗi, mở pull request, và [gửi RFC](https://github.com/reactjs/rfcs). Để khuyến khích phản hồi, đôi khi chúng tôi chia sẻ các bản build đặc biệt của React bao gồm các tính năng chưa phát hành.

<Note>

Phần này sẽ phù hợp nhất cho các nhà phát triển làm việc trên framework, thư viện, hoặc công cụ phát triển. Các nhà phát triển sử dụng React chủ yếu để xây dựng ứng dụng hướng đến người dùng không cần lo lắng về các kênh phát hành trước.

</Note>

Mỗi kênh phát hành của React được thiết kế cho một trường hợp sử dụng riêng biệt:

- [**Latest**](#latest-channel) dành cho các bản phát hành React ổn định, theo semver. Đó là những gì bạn nhận được khi cài đặt React từ npm. Đây là kênh bạn đang sử dụng hiện tại. **Các ứng dụng hướng đến người dùng sử dụng React trực tiếp dùng kênh này.**
- [**Canary**](#canary-channel) theo dõi nhánh main của kho mã nguồn React. Hãy coi đây là các ứng cử viên phát hành cho bản phát hành semver tiếp theo. **[Các framework hoặc thiết lập được quản lý khác có thể chọn sử dụng kênh này với phiên bản React được ghim.](/blog/2023/05/03/react-canaries) Bạn cũng có thể sử dụng Canary cho kiểm thử tích hợp giữa React và các dự án bên thứ ba.**
- [**Experimental**](#experimental-channel) bao gồm các API và tính năng thử nghiệm không có sẵn trong các bản phát hành ổn định. Chúng cũng theo dõi nhánh main, nhưng với các cờ tính năng bổ sung được bật. Sử dụng kênh này để thử các tính năng sắp tới trước khi chúng được phát hành.

Tất cả các bản phát hành đều được công bố trên npm, nhưng chỉ Latest sử dụng semantic versioning. Các bản phát hành trước (trong kênh Canary và Experimental) có phiên bản được tạo từ hash nội dung và ngày commit, ví dụ `18.3.0-canary-388686f29-20230503` cho Canary và `0.0.0-experimental-388686f29-20230503` cho Experimental.

**Cả kênh Latest và Canary đều được hỗ trợ chính thức cho ứng dụng hướng đến người dùng, nhưng với kỳ vọng khác nhau**:

* Bản phát hành Latest tuân theo mô hình semver truyền thống.
* Bản phát hành Canary [phải được ghim phiên bản](/blog/2023/05/03/react-canaries) và có thể bao gồm thay đổi không tương thích ngược. Chúng tồn tại cho các thiết lập được quản lý (như framework) muốn dần dần phát hành tính năng và sửa lỗi React mới theo lịch phát hành riêng.

Bản phát hành Experimental chỉ được cung cấp cho mục đích kiểm thử, và chúng tôi không đảm bảo rằng hành vi sẽ không thay đổi giữa các bản phát hành. Chúng không tuân theo giao thức semver mà chúng tôi sử dụng cho các bản phát hành từ Latest.

Bằng cách công bố các bản phát hành trước trên cùng một registry mà chúng tôi sử dụng cho các bản phát hành ổn định, chúng tôi có thể tận dụng nhiều công cụ hỗ trợ quy trình làm việc npm, như [unpkg](https://unpkg.com) và [CodeSandbox](https://codesandbox.io).

### Kênh Latest {/*latest-channel*/}

Latest là kênh được sử dụng cho các bản phát hành React ổn định. Nó tương ứng với thẻ `latest` trên npm. Đây là kênh được khuyến nghị cho tất cả các ứng dụng React được phân phối đến người dùng thực.

**Nếu bạn không chắc nên sử dụng kênh nào, đó là Latest.** Nếu bạn sử dụng React trực tiếp, đây là những gì bạn đang sử dụng. Bạn có thể mong đợi các bản cập nhật Latest cực kỳ ổn định. Các phiên bản tuân theo mô hình semantic versioning, như [đã mô tả trước đó.](#stable-releases)

### Kênh Canary {/*canary-channel*/}

Kênh Canary là kênh phát hành trước theo dõi nhánh main của kho React. Chúng tôi sử dụng các bản phát hành trước trong kênh Canary làm ứng cử viên phát hành cho kênh Latest. Bạn có thể coi Canary là tập mở rộng của Latest được cập nhật thường xuyên hơn.

Mức độ thay đổi giữa bản phát hành Canary gần nhất và bản phát hành Latest gần nhất xấp xỉ bằng mức bạn thấy giữa hai bản phát hành semver minor. Tuy nhiên, **kênh Canary không tuân thủ semantic versioning.** Bạn nên mong đợi thỉnh thoảng có thay đổi không tương thích ngược giữa các bản phát hành liên tiếp trong kênh Canary.

**Không sử dụng bản phát hành trước trong ứng dụng hướng đến người dùng trực tiếp trừ khi bạn tuân theo [quy trình Canary](/blog/2023/05/03/react-canaries).**

Bản phát hành trong Canary được công bố với thẻ `canary` trên npm. Các phiên bản được tạo từ hash nội dung bản build và ngày commit, ví dụ `18.3.0-canary-388686f29-20230503`.

#### Sử dụng kênh canary cho kiểm thử tích hợp {/*using-the-canary-channel-for-integration-testing*/}

Kênh Canary cũng hỗ trợ kiểm thử tích hợp giữa React và các dự án khác.

Tất cả các thay đổi đối với React đều trải qua kiểm thử nội bộ kỹ lưỡng trước khi được phát hành ra công chúng. Tuy nhiên, có vô số môi trường và cấu hình được sử dụng trong hệ sinh thái React, và chúng tôi không thể kiểm thử trên từng cái một.

Nếu bạn là tác giả của một framework React bên thứ ba, thư viện, công cụ phát triển, hoặc dự án cơ sở hạ tầng tương tự, bạn có thể giúp chúng tôi giữ React ổn định cho người dùng của bạn và toàn bộ cộng đồng React bằng cách định kỳ chạy bộ kiểm thử của bạn với các thay đổi gần nhất. Nếu bạn quan tâm, hãy làm theo các bước sau:

- Thiết lập cron job sử dụng nền tảng tích hợp liên tục ưa thích của bạn. Cron job được hỗ trợ bởi cả [CircleCI](https://circleci.com/docs/2.0/triggers/#scheduled-builds) và [Travis CI](https://docs.travis-ci.com/user/cron-jobs/).
- Trong cron job, cập nhật các package React của bạn lên bản phát hành React mới nhất trong kênh Canary, sử dụng thẻ `canary` trên npm. Sử dụng npm cli:

  ```console
  npm update react@canary react-dom@canary
  ```

  Hoặc yarn:

  ```console
  yarn upgrade react@canary react-dom@canary
  ```
- Chạy bộ kiểm thử của bạn với các package đã cập nhật.
- Nếu mọi thứ đều pass, tuyệt vời! Bạn có thể mong đợi dự án của bạn sẽ hoạt động với bản phát hành React minor tiếp theo.
- Nếu có gì đó bị hỏng bất ngờ, vui lòng cho chúng tôi biết bằng cách [tạo issue](https://github.com/facebook/react/issues).

Một dự án sử dụng quy trình này là Next.js. Bạn có thể tham khảo [cấu hình CircleCI](https://github.com/zeit/next.js/blob/c0a1c0f93966fe33edd93fb53e5fafb0dcd80a9e/.circleci/config.yml) của họ làm ví dụ.

### Kênh Experimental {/*experimental-channel*/}

Giống như Canary, kênh Experimental là kênh phát hành trước theo dõi nhánh main của kho React. Khác với Canary, bản phát hành Experimental bao gồm các tính năng và API bổ sung chưa sẵn sàng để phát hành rộng rãi.

Thông thường, bản cập nhật cho Canary đi kèm với bản cập nhật tương ứng cho Experimental. Chúng dựa trên cùng một revision mã nguồn, nhưng được build với bộ cờ tính năng khác nhau.

Bản phát hành Experimental có thể khác biệt đáng kể so với bản phát hành Canary và Latest. **Không sử dụng bản phát hành Experimental trong ứng dụng hướng đến người dùng.** Bạn nên mong đợi thay đổi không tương thích ngược thường xuyên giữa các bản phát hành trong kênh Experimental.

Bản phát hành trong Experimental được công bố với thẻ `experimental` trên npm. Các phiên bản được tạo từ hash nội dung bản build và ngày commit, ví dụ `0.0.0-experimental-68053d940-20210623`.

#### Bản phát hành experimental bao gồm những gì? {/*what-goes-into-an-experimental-release*/}

Tính năng experimental là những tính năng chưa sẵn sàng để phát hành cho công chúng rộng rãi, và có thể thay đổi đáng kể trước khi được hoàn thiện. Một số thử nghiệm có thể không bao giờ được hoàn thiện -- lý do chúng tôi có các thử nghiệm là để kiểm tra tính khả thi của các thay đổi được đề xuất.

Ví dụ, nếu kênh Experimental đã tồn tại khi chúng tôi công bố Hooks, chúng tôi sẽ phát hành Hooks lên kênh Experimental vài tuần trước khi chúng có sẵn trong Latest.

Bạn có thể thấy việc chạy kiểm thử tích hợp với Experimental có giá trị. Điều này tùy thuộc vào bạn. Tuy nhiên, hãy lưu ý rằng Experimental còn kém ổn định hơn Canary. **Chúng tôi không đảm bảo bất kỳ sự ổn định nào giữa các bản phát hành Experimental.**

#### Làm thế nào để tìm hiểu thêm về tính năng experimental? {/*how-can-i-learn-more-about-experimental-features*/}

Tính năng experimental có thể hoặc không được ghi nhận. Thông thường, các thử nghiệm không được ghi nhận cho đến khi chúng sắp được phát hành trong Canary hoặc Latest.

Nếu một tính năng không được ghi nhận, chúng có thể đi kèm với một [RFC](https://github.com/reactjs/rfcs).

Chúng tôi sẽ đăng bài trên [blog React](/blog) khi chúng tôi sẵn sàng công bố các thử nghiệm mới, nhưng điều đó không có nghĩa là chúng tôi sẽ công khai mọi thử nghiệm.

Bạn luôn có thể tham khảo [lịch sử](https://github.com/facebook/react/commits/main) kho GitHub công khai của chúng tôi để có danh sách đầy đủ các thay đổi.
