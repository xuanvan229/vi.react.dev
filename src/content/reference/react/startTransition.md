---
title: startTransition
---

<Intro>

`startTransition` cho phép bạn render một phần của UI trong nền.

```js
startTransition(action)
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `startTransition(action)` {/*starttransition*/}

Hàm `startTransition` cho phép bạn đánh dấu một cập nhật state là một Transition.

```js {7,9}
import { startTransition } from 'react';

function TabContainer() {
  const [tab, setTab] = useState('about');

  function selectTab(nextTab) {
    startTransition(() => {
      setTab(nextTab);
    });
  }
  // ...
}
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `action`: Một hàm cập nhật một số state bằng cách gọi một hoặc nhiều [hàm `set`](/reference/react/useState#setstate). React gọi `action` ngay lập tức không có tham số và đánh dấu tất cả các cập nhật state được lên lịch đồng bộ trong lần gọi hàm `action` là Transition. Bất kỳ lệnh gọi async nào được await trong `action` sẽ được bao gồm trong transition, nhưng hiện tại yêu cầu bao gọi bất kỳ hàm `set` nào sau `await` trong một `startTransition` bổ sung (xem [Xử lý sự cố](/reference/react/useTransition#react-doesnt-treat-my-state-update-after-await-as-a-transition)). Các cập nhật state được đánh dấu là Transition sẽ [không chặn](#marking-a-state-update-as-a-non-blocking-transition) và [sẽ không hiển thị các chỉ báo tải không mong muốn.](/reference/react/useTransition#preventing-unwanted-loading-indicators).

#### Giá trị trả về {/*returns*/}

`startTransition` không trả về gì.

#### Lưu ý {/*caveats*/}

* `startTransition` không cung cấp cách theo dõi xem một Transition có đang chờ hay không. Để hiển thị chỉ báo đang chờ trong khi Transition đang diễn ra, bạn cần [`useTransition`](/reference/react/useTransition) thay thế.

* Bạn chỉ có thể bao gọi một cập nhật vào Transition nếu bạn có quyền truy cập vào hàm `set` của state đó. Nếu bạn muốn bắt đầu một Transition trong phản ứng với một số prop hoặc giá trị trả về của một Hook tùy chỉnh, hãy thử [`useDeferredValue`](/reference/react/useDeferredValue) thay thế.

* Hàm bạn truyền vào `startTransition` được gọi ngay lập tức, đánh dấu tất cả các cập nhật state xảy ra trong khi nó thực thi là Transition. Nếu bạn cố gắng thực hiện cập nhật state trong một `setTimeout`, chẳng hạn, chúng sẽ không được đánh dấu là Transition.

* Bạn phải bao gọi bất kỳ cập nhật state nào sau bất kỳ yêu cầu async nào trong một `startTransition` khác để đánh dấu chúng là Transition. Đây là một hạn chế đã biết mà chúng tôi sẽ sửa trong tương lai (xem [Xử lý sự cố](/reference/react/useTransition#react-doesnt-treat-my-state-update-after-await-as-a-transition)).

* Một cập nhật state được đánh dấu là Transition sẽ bị gián đoạn bởi các cập nhật state khác. Ví dụ, nếu bạn cập nhật một component biểu đồ bên trong một Transition, nhưng sau đó bắt đầu gõ vào một input trong khi biểu đồ đang trong quá trình re-render, React sẽ khởi động lại công việc render trên component biểu đồ sau khi xử lý cập nhật state của input.

* Các cập nhật Transition không thể được dùng để điều khiển các input văn bản.

* Nếu có nhiều Transition đang diễn ra, React hiện tại gộp chúng lại với nhau. Đây là một hạn chế có thể được loại bỏ trong một bản phát hành tương lai.

---

## Cách sử dụng {/*usage*/}

### Đánh dấu một cập nhật state là Transition không chặn {/*marking-a-state-update-as-a-non-blocking-transition*/}

Bạn có thể đánh dấu một cập nhật state là *Transition* bằng cách bao nó trong một lệnh gọi `startTransition`:

```js {7,9}
import { startTransition } from 'react';

function TabContainer() {
  const [tab, setTab] = useState('about');

  function selectTab(nextTab) {
    startTransition(() => {
      setTab(nextTab);
    });
  }
  // ...
}
```

Transition cho phép bạn giữ cho các cập nhật giao diện người dùng phản hồi nhanh ngay cả trên các thiết bị chậm.

Với một Transition, UI của bạn vẫn phản hồi nhanh trong khi re-render. Ví dụ, nếu người dùng click một tab nhưng sau đó đổi ý và click một tab khác, họ có thể làm điều đó mà không cần đợi lần re-render đầu tiên hoàn thành.

<Note>

`startTransition` rất giống với [`useTransition`](/reference/react/useTransition), ngoại trừ nó không cung cấp cờ `isPending` để theo dõi xem một Transition có đang diễn ra không. Bạn có thể gọi `startTransition` khi `useTransition` không có sẵn. Ví dụ, `startTransition` hoạt động bên ngoài các component, chẳng hạn như từ một thư viện dữ liệu.

[Tìm hiểu về Transition và xem các ví dụ trên trang `useTransition`.](/reference/react/useTransition)

</Note>
