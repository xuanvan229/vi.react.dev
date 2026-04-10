---
title: "React v18.0"
author: The React Team
date: 2022/03/08
description: React 18 hiện đã có trên npm! Trong bài viết trước, chúng tôi đã chia sẻ hướng dẫn từng bước để nâng cấp ứng dụng của bạn lên React 18. Trong bài viết này, chúng tôi sẽ tổng quan những điểm mới trong React 18 và ý nghĩa của chúng cho tương lai.
---

Ngày 29 tháng 3, 2022 bởi [The React Team](/community/team)

---

<Intro>

React 18 hiện đã có trên npm! Trong bài viết trước, chúng tôi đã chia sẻ hướng dẫn từng bước để [nâng cấp ứng dụng của bạn lên React 18](/blog/2022/03/08/react-18-upgrade-guide). Trong bài viết này, chúng tôi sẽ tổng quan những điểm mới trong React 18, và ý nghĩa của chúng cho tương lai.

</Intro>

---

Phiên bản major mới nhất của chúng tôi bao gồm các cải tiến có sẵn như automatic batching, các API mới như startTransition, và streaming server-side rendering với hỗ trợ Suspense.

Nhiều tính năng trong React 18 được xây dựng trên nền tảng concurrent renderer mới của chúng tôi, một thay đổi phía sau hậu trường giúp mở khóa các khả năng mạnh mẽ mới. Concurrent React là opt-in — nó chỉ được kích hoạt khi bạn sử dụng một tính năng concurrent — nhưng chúng tôi nghĩ nó sẽ có tác động lớn đến cách mọi người xây dựng ứng dụng.

Chúng tôi đã dành nhiều năm nghiên cứu và phát triển hỗ trợ cho concurrency trong React, và chúng tôi đã đặc biệt chú ý để cung cấp con đường áp dụng dần dần cho người dùng hiện tại. Mùa hè vừa rồi, [chúng tôi đã thành lập React 18 Working Group](/blog/2021/06/08/the-plan-for-react-18) để thu thập phản hồi từ các chuyên gia trong cộng đồng và đảm bảo trải nghiệm nâng cấp suôn sẻ cho toàn bộ hệ sinh thái React.

Trong trường hợp bạn đã bỏ lỡ, chúng tôi đã chia sẻ nhiều tầm nhìn này tại React Conf 2021:

* Trong [phần keynote](https://www.youtube.com/watch?v=FZ0cG47msEk&list=PLNG_1j3cPCaZZ7etkzWA7JfdmKWT0pMsa), chúng tôi giải thích cách React 18 phù hợp với sứ mệnh của chúng tôi là giúp các nhà phát triển dễ dàng xây dựng trải nghiệm người dùng tuyệt vời
* [Shruti Kapoor](https://twitter.com/shrutikapoor08) đã [demo cách sử dụng các tính năng mới trong React 18](https://www.youtube.com/watch?v=ytudH8je5ko&list=PLNG_1j3cPCaZZ7etkzWA7JfdmKWT0pMsa&index=2)
* [Shaundai Person](https://twitter.com/shaundai) đã đưa ra tổng quan về [streaming server rendering với Suspense](https://www.youtube.com/watch?v=pj5N-Khihgc&list=PLNG_1j3cPCaZZ7etkzWA7JfdmKWT0pMsa&index=3)

Dưới đây là tổng quan đầy đủ về những gì cần mong đợi trong bản phát hành này, bắt đầu với Concurrent Rendering.

<Note>

Đối với người dùng React Native, React 18 sẽ đi kèm trong React Native với Kiến trúc React Native mới. Để biết thêm thông tin, hãy xem [phần keynote React Conf tại đây](https://www.youtube.com/watch?v=FZ0cG47msEk&t=1530s).

</Note>

## Concurrent React là gì? {/*what-is-concurrent-react*/}

Bổ sung quan trọng nhất trong React 18 là điều chúng tôi hy vọng bạn không bao giờ phải nghĩ đến: concurrency. Chúng tôi nghĩ điều này phần lớn đúng với các nhà phát triển ứng dụng, mặc dù câu chuyện có thể phức tạp hơn một chút đối với những người duy trì thư viện.

Concurrency không phải là một tính năng riêng lẻ. Đó là một cơ chế mới phía sau hậu trường cho phép React chuẩn bị nhiều phiên bản UI cùng một lúc. Bạn có thể coi concurrency như một chi tiết triển khai — nó có giá trị vì những tính năng mà nó mở khóa. React sử dụng các kỹ thuật tinh vi trong triển khai nội bộ của mình, như priority queues và multiple buffering. Nhưng bạn sẽ không thấy những khái niệm đó ở bất kỳ đâu trong các API công khai của chúng tôi.

Khi thiết kế API, chúng tôi cố gắng ẩn chi tiết triển khai khỏi các nhà phát triển. Là một nhà phát triển React, bạn tập trung vào *những gì* bạn muốn trải nghiệm người dùng trông như thế nào, và React xử lý *cách* để cung cấp trải nghiệm đó. Vì vậy, chúng tôi không mong đợi các nhà phát triển React biết concurrency hoạt động như thế nào bên dưới.

Tuy nhiên, Concurrent React quan trọng hơn một chi tiết triển khai thông thường — đó là một cập nhật nền tảng cho mô hình rendering cốt lõi của React. Vì vậy, mặc dù không quá quan trọng để biết cách concurrency hoạt động, nhưng có thể đáng để biết nó là gì ở cấp độ cao.

Một thuộc tính quan trọng của Concurrent React là rendering có thể bị ngắt. Khi bạn lần đầu nâng cấp lên React 18, trước khi thêm bất kỳ tính năng concurrent nào, các cập nhật được render giống như trong các phiên bản trước của React — trong một giao dịch đồng bộ đơn lẻ, không bị gián đoạn. Với synchronous rendering, một khi một cập nhật bắt đầu render, không có gì có thể ngắt nó cho đến khi người dùng có thể thấy kết quả trên màn hình.

Trong một concurrent render, điều này không phải lúc nào cũng đúng. React có thể bắt đầu render một cập nhật, tạm dừng ở giữa, sau đó tiếp tục sau. Nó thậm chí có thể từ bỏ hoàn toàn một render đang tiến hành. React đảm bảo rằng UI sẽ xuất hiện nhất quán ngay cả khi một render bị ngắt. Để làm điều này, nó chờ để thực hiện các đột biến DOM cho đến cuối, sau khi toàn bộ cây đã được đánh giá. Với khả năng này, React có thể chuẩn bị các màn hình mới trong nền mà không chặn luồng chính. Điều này có nghĩa là UI có thể phản hồi ngay lập tức với đầu vào của người dùng ngay cả khi đang ở giữa một tác vụ rendering lớn, tạo ra trải nghiệm người dùng mượt mà.

Một ví dụ khác là reusable state. Concurrent React có thể xóa các phần của UI khỏi màn hình, sau đó thêm lại chúng sau trong khi tái sử dụng trạng thái trước đó. Ví dụ: khi người dùng chuyển tab ra khỏi màn hình và quay lại, React nên có thể khôi phục màn hình trước đó trong cùng trạng thái mà nó đã có trước đó. Trong một minor sắp tới, chúng tôi đang lên kế hoạch thêm một component mới gọi là `<Offscreen>` để triển khai pattern này. Tương tự, bạn sẽ có thể sử dụng Offscreen để chuẩn bị UI mới trong nền để nó sẵn sàng trước khi người dùng tiết lộ nó.

Concurrent rendering là một công cụ mới mạnh mẽ trong React và hầu hết các tính năng mới của chúng tôi được xây dựng để tận dụng nó, bao gồm Suspense, transitions, và streaming server rendering. Nhưng React 18 chỉ là sự khởi đầu của những gì chúng tôi đặt mục tiêu xây dựng trên nền tảng mới này.

## Áp dụng dần dần các tính năng Concurrent {/*gradually-adopting-concurrent-features*/}

Về mặt kỹ thuật, concurrent rendering là một breaking change. Vì concurrent rendering có thể bị ngắt, các component hoạt động hơi khác khi nó được bật.

Trong các bài kiểm tra của chúng tôi, chúng tôi đã nâng cấp hàng nghìn component lên React 18. Những gì chúng tôi tìm thấy là gần như tất cả các component hiện có "hoạt động bình thường" với concurrent rendering, không cần thay đổi gì. Tuy nhiên, một số trong số chúng có thể cần thêm một chút nỗ lực di chuyển. Mặc dù các thay đổi thường nhỏ, bạn vẫn sẽ có khả năng thực hiện chúng theo tốc độ của riêng bạn. Hành vi rendering mới trong React 18 **chỉ được kích hoạt trong các phần của ứng dụng của bạn sử dụng các tính năng mới.**

Chiến lược nâng cấp tổng thể là đưa ứng dụng của bạn chạy trên React 18 mà không phá vỡ code hiện tại. Sau đó, bạn có thể dần dần bắt đầu thêm các tính năng concurrent theo tốc độ của riêng bạn. Bạn có thể sử dụng [`<StrictMode>`](/reference/react/StrictMode) để giúp phát hiện các lỗi liên quan đến concurrency trong quá trình phát triển. Strict Mode không ảnh hưởng đến hành vi production, nhưng trong quá trình phát triển nó sẽ ghi thêm các cảnh báo và gọi đôi các hàm được dự kiến là idempotent. Nó sẽ không bắt tất cả mọi thứ, nhưng nó hiệu quả trong việc ngăn chặn các loại lỗi phổ biến nhất.

Sau khi bạn nâng cấp lên React 18, bạn sẽ có thể bắt đầu sử dụng các tính năng concurrent ngay lập tức. Ví dụ, bạn có thể sử dụng startTransition để điều hướng giữa các màn hình mà không chặn đầu vào người dùng. Hoặc useDeferredValue để throttle các re-render tốn kém.

Tuy nhiên, về lâu dài, chúng tôi kỳ vọng cách chính bạn sẽ thêm concurrency vào ứng dụng của mình là bằng cách sử dụng một thư viện hoặc framework hỗ trợ concurrent. Trong hầu hết các trường hợp, bạn sẽ không tương tác trực tiếp với các API concurrent. Ví dụ: thay vì các nhà phát triển gọi startTransition mỗi khi họ điều hướng đến một màn hình mới, các thư viện router sẽ tự động bọc các điều hướng trong startTransition.

Có thể mất một thời gian để các thư viện nâng cấp để tương thích với concurrent. Chúng tôi đã cung cấp các API mới để giúp các thư viện dễ dàng tận dụng các tính năng concurrent hơn. Trong thời gian chờ đợi, hãy kiên nhẫn với những người duy trì khi chúng tôi làm việc để dần dần di chuyển hệ sinh thái React.

Để biết thêm thông tin, hãy xem bài viết trước của chúng tôi: [Cách nâng cấp lên React 18](/blog/2022/03/08/react-18-upgrade-guide).

## Suspense trong các Data Framework {/*suspense-in-data-frameworks*/}

Trong React 18, bạn có thể bắt đầu sử dụng [Suspense](/reference/react/Suspense) để fetch dữ liệu trong các framework có quan điểm như Relay, Next.js, Hydrogen, hoặc Remix. Việc fetch dữ liệu ad hoc với Suspense là khả thi về mặt kỹ thuật, nhưng vẫn không được khuyến nghị như một chiến lược chung.

Trong tương lai, chúng tôi có thể cung cấp các primitive bổ sung có thể giúp truy cập dữ liệu của bạn dễ dàng hơn với Suspense, có thể mà không cần sử dụng framework có quan điểm. Tuy nhiên, Suspense hoạt động tốt nhất khi nó được tích hợp sâu vào kiến trúc ứng dụng của bạn: router của bạn, lớp dữ liệu của bạn, và môi trường server rendering của bạn. Vì vậy, ngay cả về lâu dài, chúng tôi kỳ vọng rằng các thư viện và framework sẽ đóng vai trò quan trọng trong hệ sinh thái React.

Như trong các phiên bản trước của React, bạn cũng có thể sử dụng Suspense để code splitting trên client với React.lazy. Nhưng tầm nhìn của chúng tôi về Suspense luôn không chỉ về việc loading code — mục tiêu là mở rộng hỗ trợ cho Suspense để cuối cùng, cùng một Suspense fallback khai báo có thể xử lý bất kỳ hoạt động bất đồng bộ nào (loading code, dữ liệu, hình ảnh, v.v.).

## Server Components vẫn đang trong quá trình phát triển {/*server-components-is-still-in-development*/}

[**Server Components**](/blog/2020/12/21/data-fetching-with-react-server-components) là một tính năng sắp tới cho phép các nhà phát triển xây dựng ứng dụng trải dài trên server và client, kết hợp tính tương tác phong phú của các ứng dụng client-side với hiệu suất cải thiện của server rendering truyền thống. Server Components không vốn đi kèm với Concurrent React, nhưng nó được thiết kế để hoạt động tốt nhất với các tính năng concurrent như Suspense và streaming server rendering.

Server Components vẫn còn thử nghiệm, nhưng chúng tôi kỳ vọng sẽ phát hành một phiên bản ban đầu trong một bản phát hành minor 18.x. Trong thời gian đó, chúng tôi đang làm việc với các framework như Next.js, Hydrogen, và Remix để phát triển đề xuất và chuẩn bị cho việc áp dụng rộng rãi.

## Những gì mới trong React 18 {/*whats-new-in-react-18*/}

### Tính năng mới: Automatic Batching {/*new-feature-automatic-batching*/}

Batching là khi React nhóm nhiều cập nhật state thành một lần re-render duy nhất để có hiệu suất tốt hơn. Không có automatic batching, chúng tôi chỉ batch các cập nhật bên trong React event handlers. Các cập nhật bên trong promises, setTimeout, native event handlers, hoặc bất kỳ sự kiện nào khác không được batch trong React theo mặc định. Với automatic batching, các cập nhật này sẽ được batch tự động:


```js
// Before: only React events were batched.
setTimeout(() => {
  setCount(c => c + 1);
  setFlag(f => !f);
  // React will render twice, once for each state update (no batching)
}, 1000);

// After: updates inside of timeouts, promises,
// native event handlers or any other event are batched.
setTimeout(() => {
  setCount(c => c + 1);
  setFlag(f => !f);
  // React will only re-render once at the end (that's batching!)
}, 1000);
```

Để biết thêm thông tin, hãy xem bài viết này về [Automatic batching cho ít render hơn trong React 18](https://github.com/reactwg/react-18/discussions/21).

### Tính năng mới: Transitions {/*new-feature-transitions*/}

Một transition là một khái niệm mới trong React để phân biệt giữa các cập nhật khẩn cấp và không khẩn cấp.

* **Cập nhật khẩn cấp** phản ánh tương tác trực tiếp, như gõ phím, nhấp chuột, nhấn, và tương tự.
* **Cập nhật transition** chuyển đổi UI từ một view sang một view khác.

Các cập nhật khẩn cấp như gõ phím, nhấp chuột, hoặc nhấn, cần phản hồi ngay lập tức để phù hợp với trực giác của chúng ta về cách các vật thể vật lý hoạt động. Nếu không, chúng cảm thấy "sai". Tuy nhiên, transitions khác nhau vì người dùng không mong đợi thấy mọi giá trị trung gian trên màn hình.

Ví dụ: khi bạn chọn một bộ lọc trong dropdown, bạn mong đợi nút bộ lọc phản hồi ngay lập tức khi bạn nhấp. Tuy nhiên, kết quả thực tế có thể chuyển đổi riêng biệt. Một chút trễ sẽ không đáng kể và thường được mong đợi. Và nếu bạn thay đổi bộ lọc một lần nữa trước khi kết quả xong render, bạn chỉ quan tâm đến việc thấy kết quả mới nhất.

Thông thường, để có trải nghiệm người dùng tốt nhất, một đầu vào người dùng duy nhất nên dẫn đến cả một cập nhật khẩn cấp và một cập nhật không khẩn cấp. Bạn có thể sử dụng API startTransition bên trong một sự kiện đầu vào để thông báo cho React biết cập nhật nào là khẩn cấp và cái nào là "transitions":


```js
import { startTransition } from 'react';

// Urgent: Show what was typed
setInputValue(input);

// Mark any state updates inside as transitions
startTransition(() => {
  // Transition: Show the results
  setSearchQuery(input);
});
```


Các cập nhật được bọc trong startTransition được xử lý là không khẩn cấp và sẽ bị ngắt nếu các cập nhật khẩn cấp hơn như nhấp chuột hoặc nhấn phím xuất hiện. Nếu một transition bị ngắt bởi người dùng (ví dụ: bằng cách gõ nhiều ký tự liên tiếp), React sẽ loại bỏ công việc render đã cũ chưa hoàn thành và chỉ render cập nhật mới nhất.


* `useTransition`: một Hook để bắt đầu các transitions, bao gồm một giá trị để theo dõi trạng thái pending.
* `startTransition`: một phương thức để bắt đầu các transitions khi Hook không thể được sử dụng.

Transitions sẽ opt in vào concurrent rendering, cho phép cập nhật bị ngắt. Nếu nội dung re-suspends, transitions cũng nói với React tiếp tục hiển thị nội dung hiện tại trong khi render nội dung transition trong nền (xem [Suspense RFC](https://github.com/reactjs/rfcs/blob/main/text/0213-suspense-in-react-18.md) để biết thêm thông tin).

[Xem tài liệu về transitions tại đây](/reference/react/useTransition).

### Các tính năng Suspense mới {/*new-suspense-features*/}

Suspense cho phép bạn khai báo trạng thái loading cho một phần của cây component nếu nó chưa sẵn sàng để hiển thị:

```js
<Suspense fallback={<Spinner />}>
  <Comments />
</Suspense>
```

Suspense biến "trạng thái loading UI" thành một khái niệm khai báo bậc một trong mô hình lập trình React. Điều này cho phép chúng tôi xây dựng các tính năng cấp cao hơn trên nó.

Chúng tôi đã giới thiệu một phiên bản giới hạn của Suspense vài năm trước. Tuy nhiên, trường hợp sử dụng duy nhất được hỗ trợ là code splitting với React.lazy, và nó hoàn toàn không được hỗ trợ khi render trên server.

Trong React 18, chúng tôi đã thêm hỗ trợ cho Suspense trên server và mở rộng khả năng của nó bằng cách sử dụng các tính năng concurrent rendering.

Suspense trong React 18 hoạt động tốt nhất khi kết hợp với API transition. Nếu bạn suspend trong một transition, React sẽ ngăn nội dung đã hiển thị bị thay thế bởi một fallback. Thay vào đó, React sẽ trễ render cho đến khi đủ dữ liệu đã được tải để ngăn trạng thái loading tệ.

Để biết thêm, hãy xem RFC cho [Suspense trong React 18](https://github.com/reactjs/rfcs/blob/main/text/0213-suspense-in-react-18.md).

### Các API Client và Server Rendering mới {/*new-client-and-server-rendering-apis*/}

Trong bản phát hành này, chúng tôi đã tận dụng cơ hội để thiết kế lại các API chúng tôi cung cấp để rendering trên client và server. Những thay đổi này cho phép người dùng tiếp tục sử dụng các API cũ ở chế độ React 17 trong khi họ nâng cấp lên các API mới trong React 18.

#### React DOM Client {/*react-dom-client*/}

Các API mới này bây giờ được xuất từ `react-dom/client`:

* `createRoot`: Phương thức mới để tạo root để `render` hoặc `unmount`. Sử dụng nó thay vì `ReactDOM.render`. Các tính năng mới trong React 18 không hoạt động mà không có nó.
* `hydrateRoot`: Phương thức mới để hydrate một ứng dụng được render trên server. Sử dụng nó thay vì `ReactDOM.hydrate` kết hợp với các API React DOM Server mới. Các tính năng mới trong React 18 không hoạt động mà không có nó.

Cả `createRoot` và `hydrateRoot` đều chấp nhận một tùy chọn mới gọi là `onRecoverableError` trong trường hợp bạn muốn được thông báo khi React phục hồi từ các lỗi trong quá trình rendering hoặc hydration để ghi nhật ký. Theo mặc định, React sẽ sử dụng [`reportError`](https://developer.mozilla.org/en-US/docs/Web/API/reportError), hoặc `console.error` trong các trình duyệt cũ hơn.

[Xem tài liệu cho React DOM Client tại đây](/reference/react-dom/client).

#### React DOM Server {/*react-dom-server*/}

Các API mới này bây giờ được xuất từ `react-dom/server` và có hỗ trợ đầy đủ cho streaming Suspense trên server:

* `renderToPipeableStream`: để streaming trong môi trường Node.
* `renderToReadableStream`: cho các môi trường edge runtime hiện đại, như Deno và Cloudflare workers.

Phương thức `renderToString` hiện tại tiếp tục hoạt động nhưng không được khuyến khích.

[Xem tài liệu cho React DOM Server tại đây](/reference/react-dom/server).

### Các hành vi Strict Mode mới {/*new-strict-mode-behaviors*/}

Trong tương lai, chúng tôi muốn thêm một tính năng cho phép React thêm và xóa các phần của UI trong khi bảo tồn state. Ví dụ: khi người dùng chuyển tab ra khỏi màn hình và quay lại, React nên có thể ngay lập tức hiển thị màn hình trước đó. Để làm điều này, React sẽ unmount và remount cây sử dụng cùng trạng thái component như trước.

Tính năng này sẽ cung cấp cho các ứng dụng React hiệu suất tốt hơn ngay từ đầu, nhưng yêu cầu các component phải linh hoạt trước các effect được mount và hủy bỏ nhiều lần. Hầu hết các effect sẽ hoạt động mà không cần thay đổi, nhưng một số effect giả định chúng chỉ được mount hoặc hủy bỏ một lần.

Để giúp phát hiện các vấn đề này, React 18 giới thiệu một kiểm tra chỉ dành cho development mới vào Strict Mode. Kiểm tra mới này sẽ tự động unmount và remount mọi component, bất cứ khi nào một component mount lần đầu tiên, khôi phục trạng thái trước đó ở lần mount thứ hai.

Trước thay đổi này, React sẽ mount component và tạo các effect:

```
* React mounts the component.
  * Layout effects are created.
  * Effects are created.
```


Với Strict Mode trong React 18, React sẽ mô phỏng unmounting và remounting component trong chế độ development:

```
* React mounts the component.
  * Layout effects are created.
  * Effects are created.
* React simulates unmounting the component.
  * Layout effects are destroyed.
  * Effects are destroyed.
* React simulates mounting the component with the previous state.
  * Layout effects are created.
  * Effects are created.
```

[Xem tài liệu về đảm bảo reusable state tại đây](/reference/react/StrictMode#fixing-bugs-found-by-re-running-effects-in-development).

### Các Hook mới {/*new-hooks*/}

#### useId {/*useid*/}

`useId` là một Hook mới để tạo các ID duy nhất trên cả client và server, trong khi tránh hydration mismatch. Nó chủ yếu hữu ích cho các thư viện component tích hợp với các API accessibility yêu cầu ID duy nhất. Điều này giải quyết một vấn đề đã tồn tại trong React 17 và thấp hơn, nhưng nó thậm chí còn quan trọng hơn trong React 18 vì cách trình render server streaming mới cung cấp HTML không theo thứ tự. [Xem tài liệu tại đây](/reference/react/useId).

> Lưu ý
>
> `useId` **không** dùng để tạo [keys trong một danh sách](/learn/rendering-lists#where-to-get-your-key). Keys nên được tạo từ dữ liệu của bạn.

#### useTransition {/*usetransition*/}

`useTransition` và `startTransition` cho phép bạn đánh dấu một số cập nhật state là không khẩn cấp. Các cập nhật state khác được coi là khẩn cấp theo mặc định. React sẽ cho phép các cập nhật state khẩn cấp (ví dụ: cập nhật một text input) ngắt các cập nhật state không khẩn cấp (ví dụ: render một danh sách kết quả tìm kiếm). [Xem tài liệu tại đây](/reference/react/useTransition).

#### useDeferredValue {/*usedeferredvalue*/}

`useDeferredValue` cho phép bạn trì hoãn re-render một phần không khẩn cấp của cây. Nó tương tự như debouncing, nhưng có một vài lợi thế so với nó. Không có độ trễ thời gian cố định, vì vậy React sẽ thử deferred render ngay sau khi render đầu tiên được phản ánh trên màn hình. Deferred render có thể bị ngắt và không chặn đầu vào người dùng. [Xem tài liệu tại đây](/reference/react/useDeferredValue).

#### useSyncExternalStore {/*usesyncexternalstore*/}

`useSyncExternalStore` là một Hook mới cho phép các external store hỗ trợ đọc concurrent bằng cách buộc các cập nhật vào store là đồng bộ. Nó loại bỏ nhu cầu sử dụng useEffect khi triển khai subscriptions đến các nguồn dữ liệu bên ngoài, và được khuyến nghị cho bất kỳ thư viện nào tích hợp với state bên ngoài React. [Xem tài liệu tại đây](/reference/react/useSyncExternalStore).

> Lưu ý
>
> `useSyncExternalStore` được thiết kế để sử dụng bởi các thư viện, không phải code ứng dụng.

#### useInsertionEffect {/*useinsertioneffect*/}

`useInsertionEffect` là một Hook mới cho phép các thư viện CSS-in-JS giải quyết các vấn đề hiệu suất khi inject styles trong render. Trừ khi bạn đã xây dựng một thư viện CSS-in-JS, chúng tôi không mong đợi bạn sẽ sử dụng điều này. Hook này sẽ chạy sau khi DOM bị đột biến, nhưng trước khi các layout effects đọc layout mới. Điều này giải quyết một vấn đề đã tồn tại trong React 17 và thấp hơn, nhưng thậm chí còn quan trọng hơn trong React 18 vì React nhường cho trình duyệt trong quá trình concurrent rendering, cho nó cơ hội để tính toán lại layout. [Xem tài liệu tại đây](/reference/react/useInsertionEffect).

> Lưu ý
>
> `useInsertionEffect` được thiết kế để sử dụng bởi các thư viện, không phải code ứng dụng.

## Cách nâng cấp {/*how-to-upgrade*/}

Xem [Cách nâng cấp lên React 18](/blog/2022/03/08/react-18-upgrade-guide) để có hướng dẫn từng bước và danh sách đầy đủ các breaking changes và notable changes.

## Changelog {/*changelog*/}

### React {/*react*/}

* Add `useTransition` and `useDeferredValue` to separate urgent updates from transitions. ([#10426](https://github.com/facebook/react/pull/10426), [#10715](https://github.com/facebook/react/pull/10715), [#15593](https://github.com/facebook/react/pull/15593), [#15272](https://github.com/facebook/react/pull/15272), [#15578](https://github.com/facebook/react/pull/15578), [#15769](https://github.com/facebook/react/pull/15769), [#17058](https://github.com/facebook/react/pull/17058), [#18796](https://github.com/facebook/react/pull/18796), [#19121](https://github.com/facebook/react/pull/19121), [#19703](https://github.com/facebook/react/pull/19703), [#19719](https://github.com/facebook/react/pull/19719), [#19724](https://github.com/facebook/react/pull/19724), [#20672](https://github.com/facebook/react/pull/20672), [#20976](https://github.com/facebook/react/pull/20976) by [@acdlite](https://github.com/acdlite), [@lunaruan](https://github.com/lunaruan), [@rickhanlonii](https://github.com/rickhanlonii), and [@sebmarkbage](https://github.com/sebmarkbage))
* Add `useId` for generating unique IDs. ([#17322](https://github.com/facebook/react/pull/17322), [#18576](https://github.com/facebook/react/pull/18576), [#22644](https://github.com/facebook/react/pull/22644), [#22672](https://github.com/facebook/react/pull/22672), [#21260](https://github.com/facebook/react/pull/21260) by [@acdlite](https://github.com/acdlite), [@lunaruan](https://github.com/lunaruan), and [@sebmarkbage](https://github.com/sebmarkbage))
* Add `useSyncExternalStore` to help external store libraries integrate with React. ([#15022](https://github.com/facebook/react/pull/15022), [#18000](https://github.com/facebook/react/pull/18000), [#18771](https://github.com/facebook/react/pull/18771), [#22211](https://github.com/facebook/react/pull/22211), [#22292](https://github.com/facebook/react/pull/22292), [#22239](https://github.com/facebook/react/pull/22239), [#22347](https://github.com/facebook/react/pull/22347), [#23150](https://github.com/facebook/react/pull/23150) by [@acdlite](https://github.com/acdlite), [@bvaughn](https://github.com/bvaughn), and [@drarmstr](https://github.com/drarmstr))
* Add `startTransition` as a version of `useTransition` without pending feedback. ([#19696](https://github.com/facebook/react/pull/19696)  by [@rickhanlonii](https://github.com/rickhanlonii))
* Add `useInsertionEffect` for CSS-in-JS libraries. ([#21913](https://github.com/facebook/react/pull/21913)  by [@rickhanlonii](https://github.com/rickhanlonii))
* Make Suspense remount layout effects when content reappears.  ([#19322](https://github.com/facebook/react/pull/19322), [#19374](https://github.com/facebook/react/pull/19374), [#19523](https://github.com/facebook/react/pull/19523), [#20625](https://github.com/facebook/react/pull/20625), [#21079](https://github.com/facebook/react/pull/21079) by [@acdlite](https://github.com/acdlite), [@bvaughn](https://github.com/bvaughn), and [@lunaruan](https://github.com/lunaruan))
* Make `<StrictMode>` re-run effects to check for restorable state. ([#19523](https://github.com/facebook/react/pull/19523) , [#21418](https://github.com/facebook/react/pull/21418)  by [@bvaughn](https://github.com/bvaughn) and [@lunaruan](https://github.com/lunaruan))
* Assume Symbols are always available. ([#23348](https://github.com/facebook/react/pull/23348)  by [@sebmarkbage](https://github.com/sebmarkbage))
* Remove `object-assign` polyfill. ([#23351](https://github.com/facebook/react/pull/23351)  by [@sebmarkbage](https://github.com/sebmarkbage))
* Remove unsupported `unstable_changedBits` API.  ([#20953](https://github.com/facebook/react/pull/20953)  by [@acdlite](https://github.com/acdlite))
* Allow components to render undefined. ([#21869](https://github.com/facebook/react/pull/21869)  by [@rickhanlonii](https://github.com/rickhanlonii))
* Flush `useEffect` resulting from discrete events like clicks synchronously. ([#21150](https://github.com/facebook/react/pull/21150)  by [@acdlite](https://github.com/acdlite))
* Suspense `fallback={undefined}` now behaves the same as `null` and isn't ignored. ([#21854](https://github.com/facebook/react/pull/21854)  by [@rickhanlonii](https://github.com/rickhanlonii))
* Consider all `lazy()` resolving to the same component equivalent. ([#20357](https://github.com/facebook/react/pull/20357)  by [@sebmarkbage](https://github.com/sebmarkbage))
* Don't patch console during first render. ([#22308](https://github.com/facebook/react/pull/22308)  by [@lunaruan](https://github.com/lunaruan))
* Improve memory usage. ([#21039](https://github.com/facebook/react/pull/21039)  by [@bgirard](https://github.com/bgirard))
* Improve messages if string coercion throws (Temporal.*, Symbol, etc.) ([#22064](https://github.com/facebook/react/pull/22064)  by [@justingrant](https://github.com/justingrant))
* Use `setImmediate` when available over `MessageChannel`. ([#20834](https://github.com/facebook/react/pull/20834)  by [@gaearon](https://github.com/gaearon))
* Fix context failing to propagate inside suspended trees. ([#23095](https://github.com/facebook/react/pull/23095)  by [@gaearon](https://github.com/gaearon))
* Fix `useReducer` observing incorrect props by removing the eager bailout mechanism. ([#22445](https://github.com/facebook/react/pull/22445)  by [@josephsavona](https://github.com/josephsavona))
* Fix `setState` being ignored in Safari when appending iframes. ([#23111](https://github.com/facebook/react/pull/23111)  by [@gaearon](https://github.com/gaearon))
* Fix a crash when rendering `ZonedDateTime` in the tree. ([#20617](https://github.com/facebook/react/pull/20617)  by [@dimaqq](https://github.com/dimaqq))
* Fix a crash when document is set to `null` in tests. ([#22695](https://github.com/facebook/react/pull/22695)  by [@SimenB](https://github.com/SimenB))
* Fix `onLoad` not triggering when concurrent features are on. ([#23316](https://github.com/facebook/react/pull/23316)  by [@gnoff](https://github.com/gnoff))
* Fix a warning when a selector returns `NaN`.  ([#23333](https://github.com/facebook/react/pull/23333)  by [@hachibeeDI](https://github.com/hachibeeDI))
* Fix a crash when document is set to `null` in tests. ([#22695](https://github.com/facebook/react/pull/22695) by [@SimenB](https://github.com/SimenB))
* Fix the generated license header. ([#23004](https://github.com/facebook/react/pull/23004)  by [@vitaliemiron](https://github.com/vitaliemiron))
* Add `package.json` as one of the entry points. ([#22954](https://github.com/facebook/react/pull/22954)  by [@Jack](https://github.com/Jack-Works))
* Allow suspending outside a Suspense boundary. ([#23267](https://github.com/facebook/react/pull/23267)  by [@acdlite](https://github.com/acdlite))
* Log a recoverable error whenever hydration fails. ([#23319](https://github.com/facebook/react/pull/23319)  by [@acdlite](https://github.com/acdlite))

### React DOM {/*react-dom*/}

* Add `createRoot` and `hydrateRoot`. ([#10239](https://github.com/facebook/react/pull/10239), [#11225](https://github.com/facebook/react/pull/11225), [#12117](https://github.com/facebook/react/pull/12117), [#13732](https://github.com/facebook/react/pull/13732), [#15502](https://github.com/facebook/react/pull/15502), [#15532](https://github.com/facebook/react/pull/15532), [#17035](https://github.com/facebook/react/pull/17035), [#17165](https://github.com/facebook/react/pull/17165), [#20669](https://github.com/facebook/react/pull/20669), [#20748](https://github.com/facebook/react/pull/20748), [#20888](https://github.com/facebook/react/pull/20888), [#21072](https://github.com/facebook/react/pull/21072), [#21417](https://github.com/facebook/react/pull/21417), [#21652](https://github.com/facebook/react/pull/21652), [#21687](https://github.com/facebook/react/pull/21687), [#23207](https://github.com/facebook/react/pull/23207), [#23385](https://github.com/facebook/react/pull/23385) by [@acdlite](https://github.com/acdlite), [@bvaughn](https://github.com/bvaughn), [@gaearon](https://github.com/gaearon), [@lunaruan](https://github.com/lunaruan), [@rickhanlonii](https://github.com/rickhanlonii), [@trueadm](https://github.com/trueadm), and [@sebmarkbage](https://github.com/sebmarkbage))
* Add selective hydration. ([#14717](https://github.com/facebook/react/pull/14717), [#14884](https://github.com/facebook/react/pull/14884), [#16725](https://github.com/facebook/react/pull/16725), [#16880](https://github.com/facebook/react/pull/16880), [#17004](https://github.com/facebook/react/pull/17004), [#22416](https://github.com/facebook/react/pull/22416), [#22629](https://github.com/facebook/react/pull/22629), [#22448](https://github.com/facebook/react/pull/22448), [#22856](https://github.com/facebook/react/pull/22856), [#23176](https://github.com/facebook/react/pull/23176) by [@acdlite](https://github.com/acdlite), [@gaearon](https://github.com/gaearon), [@salazarm](https://github.com/salazarm), and [@sebmarkbage](https://github.com/sebmarkbage))
* Add `aria-description` to the list of known ARIA attributes. ([#22142](https://github.com/facebook/react/pull/22142)  by [@mahyareb](https://github.com/mahyareb))
* Add `onResize` event to video elements. ([#21973](https://github.com/facebook/react/pull/21973)  by [@rileyjshaw](https://github.com/rileyjshaw))
* Add `imageSizes` and `imageSrcSet` to known props. ([#22550](https://github.com/facebook/react/pull/22550)  by [@eps1lon](https://github.com/eps1lon))
* Allow non-string `<option>` children if `value` is provided.  ([#21431](https://github.com/facebook/react/pull/21431)  by [@sebmarkbage](https://github.com/sebmarkbage))
* Fix `aspectRatio` style not being applied. ([#21100](https://github.com/facebook/react/pull/21100)  by [@gaearon](https://github.com/gaearon))
* Warn if `renderSubtreeIntoContainer` is called. ([#23355](https://github.com/facebook/react/pull/23355)  by [@acdlite](https://github.com/acdlite))

### React DOM Server {/*react-dom-server-1*/}

* Add the new streaming renderer. ([#14144](https://github.com/facebook/react/pull/14144), [#20970](https://github.com/facebook/react/pull/20970), [#21056](https://github.com/facebook/react/pull/21056), [#21255](https://github.com/facebook/react/pull/21255), [#21200](https://github.com/facebook/react/pull/21200), [#21257](https://github.com/facebook/react/pull/21257), [#21276](https://github.com/facebook/react/pull/21276), [#22443](https://github.com/facebook/react/pull/22443), [#22450](https://github.com/facebook/react/pull/22450), [#23247](https://github.com/facebook/react/pull/23247), [#24025](https://github.com/facebook/react/pull/24025), [#24030](https://github.com/facebook/react/pull/24030) by [@sebmarkbage](https://github.com/sebmarkbage))
* Fix context providers in SSR when handling multiple requests. ([#23171](https://github.com/facebook/react/pull/23171)  by [@frandiox](https://github.com/frandiox))
* Revert to client render on text mismatch. ([#23354](https://github.com/facebook/react/pull/23354)  by [@acdlite](https://github.com/acdlite))
* Deprecate `renderToNodeStream`. ([#23359](https://github.com/facebook/react/pull/23359)  by [@sebmarkbage](https://github.com/sebmarkbage))
* Fix a spurious error log in the new server renderer. ([#24043](https://github.com/facebook/react/pull/24043)  by [@eps1lon](https://github.com/eps1lon))
* Fix a bug in the new server renderer. ([#22617](https://github.com/facebook/react/pull/22617)  by [@shuding](https://github.com/shuding))
* Ignore function and symbol values inside custom elements on the server. ([#21157](https://github.com/facebook/react/pull/21157)  by [@sebmarkbage](https://github.com/sebmarkbage))

### React DOM Test Utils {/*react-dom-test-utils*/}

* Throw when `act` is used in production. ([#21686](https://github.com/facebook/react/pull/21686)  by [@acdlite](https://github.com/acdlite))
* Support disabling spurious act warnings with `global.IS_REACT_ACT_ENVIRONMENT`. ([#22561](https://github.com/facebook/react/pull/22561)  by [@acdlite](https://github.com/acdlite))
* Expand act warning to cover all APIs that might schedule React work. ([#22607](https://github.com/facebook/react/pull/22607)  by [@acdlite](https://github.com/acdlite))
* Make `act` batch updates. ([#21797](https://github.com/facebook/react/pull/21797)  by [@acdlite](https://github.com/acdlite))
* Remove warning for dangling passive effects. ([#22609](https://github.com/facebook/react/pull/22609)  by [@acdlite](https://github.com/acdlite))

### React Refresh {/*react-refresh*/}

* Track late-mounted roots in Fast Refresh. ([#22740](https://github.com/facebook/react/pull/22740)  by [@anc95](https://github.com/anc95))
* Add `exports` field to `package.json`. ([#23087](https://github.com/facebook/react/pull/23087)  by [@otakustay](https://github.com/otakustay))

### Server Components (Experimental) {/*server-components-experimental*/}

* Add Server Context support. ([#23244](https://github.com/facebook/react/pull/23244)  by [@salazarm](https://github.com/salazarm))
* Add `lazy` support. ([#24068](https://github.com/facebook/react/pull/24068)  by [@gnoff](https://github.com/gnoff))
* Update webpack plugin for webpack 5 ([#22739](https://github.com/facebook/react/pull/22739)  by [@michenly](https://github.com/michenly))
* Fix a mistake in the Node loader. ([#22537](https://github.com/facebook/react/pull/22537)  by [@btea](https://github.com/btea))
* Use `globalThis` instead of `window` for edge environments. ([#22777](https://github.com/facebook/react/pull/22777)  by [@huozhi](https://github.com/huozhi))
