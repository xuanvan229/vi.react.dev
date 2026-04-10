---
title: <Profiler>
---

<Intro>

`<Profiler>` cho phép bạn đo hiệu suất render của một cây React theo chương trình.

```js
<Profiler id="App" onRender={onRender}>
  <App />
</Profiler>
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `<Profiler>` {/*profiler*/}

Bọc một cây component trong `<Profiler>` để đo hiệu suất render của nó.

```js
<Profiler id="App" onRender={onRender}>
  <App />
</Profiler>
```

#### Props {/*props*/}

* `id`: Một chuỗi xác định phần UI mà bạn đang đo.
* `onRender`: Một [`onRender` callback](#onrender-callback) mà React gọi mỗi khi các component trong cây được profiled cập nhật. Nó nhận thông tin về những gì đã được render và mất bao lâu.

#### Lưu ý {/*caveats*/}

* Profiling thêm một số overhead bổ sung, vì vậy **nó bị vô hiệu hóa trong bản build production theo mặc định.** Để bật profiling production, bạn cần kích hoạt một [bản build production đặc biệt có bật profiling.](/reference/dev-tools/react-performance-tracks#using-profiling-builds)

---

### `onRender` callback {/*onrender-callback*/}

React sẽ gọi `onRender` callback của bạn với thông tin về những gì đã được render.

```js
function onRender(id, phase, actualDuration, baseDuration, startTime, commitTime) {
  // Tổng hợp hoặc ghi log thời gian render...
}
```

#### Tham số {/*onrender-parameters*/}

* `id`: Prop `id` kiểu chuỗi của cây `<Profiler>` vừa commit. Điều này cho phép bạn xác định phần nào của cây đã được commit nếu bạn sử dụng nhiều profiler.
* `phase`: `"mount"`, `"update"` hoặc `"nested-update"`. Điều này cho bạn biết cây vừa được mount lần đầu hay re-render do thay đổi props, state, hoặc Hooks.
* `actualDuration`: Số mili giây dành cho việc render `<Profiler>` và các con của nó cho lần cập nhật hiện tại. Điều này cho biết cây con sử dụng memoization tốt đến mức nào (ví dụ [`memo`](/reference/react/memo) và [`useMemo`](/reference/react/useMemo)). Lý tưởng, giá trị này nên giảm đáng kể sau lần mount đầu tiên vì nhiều con chỉ cần re-render nếu props cụ thể của chúng thay đổi.
* `baseDuration`: Số mili giây ước tính thời gian cần để re-render toàn bộ cây con `<Profiler>` mà không có bất kỳ tối ưu nào. Nó được tính bằng cách cộng thời gian render gần nhất của mỗi component trong cây. Giá trị này ước tính chi phí render trong trường hợp xấu nhất (ví dụ lần mount đầu tiên hoặc cây không có memoization). So sánh `actualDuration` với nó để xem memoization có hoạt động không.
* `startTime`: Một timestamp số cho thời điểm React bắt đầu render lần cập nhật hiện tại.
* `commitTime`: Một timestamp số cho thời điểm React commit lần cập nhật hiện tại. Giá trị này được chia sẻ giữa tất cả các profiler trong một commit, cho phép nhóm chúng lại nếu cần.

---

## Cách sử dụng {/*usage*/}

### Đo hiệu suất render theo chương trình {/*measuring-rendering-performance-programmatically*/}

Bọc component `<Profiler>` quanh một cây React để đo hiệu suất render của nó.

```js {2,4}
<App>
  <Profiler id="Sidebar" onRender={onRender}>
    <Sidebar />
  </Profiler>
  <PageContent />
</App>
```

Nó yêu cầu hai props: một `id` (chuỗi) và một `onRender` callback (hàm) mà React gọi bất cứ khi nào một component trong cây "commit" một cập nhật.

<Pitfall>

Profiling thêm một số overhead bổ sung, vì vậy **nó bị vô hiệu hóa trong bản build production theo mặc định.** Để bật profiling production, bạn cần kích hoạt một [bản build production đặc biệt có bật profiling.](/reference/dev-tools/react-performance-tracks#using-profiling-builds)

</Pitfall>

<Note>

`<Profiler>` cho phép bạn thu thập các phép đo theo chương trình. Nếu bạn đang tìm một profiler tương tác, hãy thử tab Profiler trong [React Developer Tools](/learn/react-developer-tools). Nó cung cấp chức năng tương tự dưới dạng extension trình duyệt.

Các component được bọc trong `<Profiler>` cũng sẽ được đánh dấu trong [Component tracks](/reference/dev-tools/react-performance-tracks#components) của React Performance tracks ngay cả trong bản build profiling.
Trong bản build development, tất cả các component đều được đánh dấu trong Component tracks bất kể chúng có được bọc trong `<Profiler>` hay không.

</Note>

---

### Đo các phần khác nhau của ứng dụng {/*measuring-different-parts-of-the-application*/}

Bạn có thể sử dụng nhiều component `<Profiler>` để đo các phần khác nhau của ứng dụng:

```js {5,7}
<App>
  <Profiler id="Sidebar" onRender={onRender}>
    <Sidebar />
  </Profiler>
  <Profiler id="Content" onRender={onRender}>
    <Content />
  </Profiler>
</App>
```

Bạn cũng có thể lồng các component `<Profiler>`:

```js {5,7,9,12}
<App>
  <Profiler id="Sidebar" onRender={onRender}>
    <Sidebar />
  </Profiler>
  <Profiler id="Content" onRender={onRender}>
    <Content>
      <Profiler id="Editor" onRender={onRender}>
        <Editor />
      </Profiler>
      <Preview />
    </Content>
  </Profiler>
</App>
```

Mặc dù `<Profiler>` là một component nhẹ, nó chỉ nên được sử dụng khi cần thiết. Mỗi lần sử dụng thêm một chút overhead CPU và bộ nhớ cho ứng dụng.

---
