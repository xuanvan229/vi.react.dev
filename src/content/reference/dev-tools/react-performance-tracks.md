---
title: React Performance tracks
---

<Intro>

React Performance tracks là các mục custom chuyên biệt xuất hiện trên timeline của Performance panel trong developer tools của trình duyệt.

</Intro>

Các track này được thiết kế để cung cấp cho developer cái nhìn toàn diện về hiệu suất ứng dụng React của họ bằng cách trực quan hóa các sự kiện và metric dành riêng cho React cùng với các nguồn dữ liệu quan trọng khác như network request, thực thi JavaScript, và hoạt động event loop, tất cả được đồng bộ hóa trên một timeline thống nhất trong Performance panel để hiểu đầy đủ về hành vi ứng dụng.

<div style={{display: 'flex', justifyContent: 'center', marginBottom: '1rem'}}>
  <img className="w-full light-image" src="/images/docs/performance-tracks/overview.png" alt="React Performance Tracks" />
  <img className="w-full dark-image" src="/images/docs/performance-tracks/overview.dark.png" alt="React Performance Tracks" />
</div>

<InlineToc />

---

## Cách sử dụng {/*usage*/}

React Performance tracks chỉ khả dụng trong các bản build development và profiling của React:

- **Development**: được bật theo mặc định.
- **Profiling**: Chỉ có Scheduler tracks được bật theo mặc định. Components track chỉ liệt kê các Components nằm trong các cây con được bọc bởi [`<Profiler>`](/reference/react/Profiler). Nếu bạn đã bật [tiện ích mở rộng React Developer Tools](/learn/react-developer-tools), tất cả Components đều được bao gồm trong Components track ngay cả khi chúng không được bọc trong `<Profiler>`. Server tracks không khả dụng trong các bản build profiling.

Nếu được bật, các track sẽ tự động xuất hiện trong các trace bạn ghi lại với Performance panel của các trình duyệt cung cấp [extensibility APIs](https://developer.chrome.com/docs/devtools/performance/extension).

<Pitfall>

Công cụ đo lường profiling cho phép React Performance tracks hoạt động có thêm một số overhead, vì vậy nó bị tắt trong các bản build production theo mặc định.
Các track Server Components và Server Requests chỉ khả dụng trong các bản build development.

</Pitfall>

### Sử dụng profiling builds {/*using-profiling-builds*/}

Ngoài các bản build production và development, React còn bao gồm một bản build profiling đặc biệt.
Để sử dụng profiling builds, bạn phải dùng `react-dom/profiling` thay vì `react-dom/client`.
Chúng tôi khuyến nghị bạn alias `react-dom/client` thành `react-dom/profiling` tại thời điểm build thông qua bundler aliases thay vì cập nhật từng import `react-dom/client` thủ công.
Framework của bạn có thể có hỗ trợ tích hợp để bật profiling build của React.

---

## Các Track {/*tracks*/}

### Scheduler {/*scheduler*/}

Scheduler là một khái niệm nội bộ của React được sử dụng để quản lý các task với các mức độ ưu tiên khác nhau. Track này bao gồm 4 subtrack, mỗi subtrack đại diện cho công việc có mức độ ưu tiên cụ thể:

- **Blocking** - Các cập nhật đồng bộ, có thể được khởi tạo bởi tương tác người dùng.
- **Transition** - Công việc không chặn xảy ra trong nền, thường được khởi tạo thông qua [`startTransition`](/reference/react/startTransition).
- **Suspense** - Công việc liên quan đến Suspense boundaries, chẳng hạn như hiển thị fallbacks hoặc tiết lộ nội dung.
- **Idle** - Công việc có mức độ ưu tiên thấp nhất được thực hiện khi không có task nào khác có mức độ ưu tiên cao hơn.

<div style={{display: 'flex', justifyContent: 'center', marginBottom: '1rem'}}>
  <img className="w-full light-image" src="/images/docs/performance-tracks/scheduler.png" alt="Scheduler track" />
  <img className="w-full dark-image" src="/images/docs/performance-tracks/scheduler.dark.png" alt="Scheduler track" />
</div>

#### Renders {/*renders*/}

Mỗi render pass bao gồm nhiều phase mà bạn có thể thấy trên timeline:

- **Update** - đây là nguyên nhân gây ra render pass mới.
- **Render** - React render cây con đã cập nhật bằng cách gọi các hàm render của các component. Bạn có thể thấy cây con component đã render trên [Components track](#components), sử dụng cùng color scheme.
- **Commit** - Sau khi render các component, React sẽ gửi các thay đổi vào DOM và chạy layout effects, như [`useLayoutEffect`](/reference/react/useLayoutEffect).
- **Remaining Effects** - React chạy passive effects của cây con đã render. Điều này thường xảy ra sau khi paint, và đây là lúc React chạy các hook như [`useEffect`](/reference/react/useEffect). Một ngoại lệ đã biết là tương tác người dùng, như click, hoặc các discrete event khác. Trong tình huống này, phase này có thể chạy trước khi paint.

<div style={{display: 'flex', justifyContent: 'center', marginBottom: '1rem'}}>
  <img className="w-full light-image" src="/images/docs/performance-tracks/scheduler-update.png" alt="Scheduler track: updates" />
  <img className="w-full dark-image" src="/images/docs/performance-tracks/scheduler-update.dark.png" alt="Scheduler track: updates" />
</div>

[Tìm hiểu thêm về renders và commits](/learn/render-and-commit).

#### Cascading updates {/*cascading-updates*/}

Cascading updates là một trong những pattern gây ra hiệu suất bị giảm. Nếu một cập nhật được lên lịch trong quá trình render pass, React có thể loại bỏ công việc đã hoàn thành và bắt đầu một pass mới.

Trong các bản build development, React có thể cho bạn biết Component nào đã lên lịch một cập nhật mới. Điều này bao gồm cả các cập nhật thông thường và các cascading update. Bạn có thể xem stack trace nâng cao bằng cách click vào mục "Cascading update", mục đó cũng sẽ hiển thị tên của method đã lên lịch cập nhật.

<div style={{display: 'flex', justifyContent: 'center', marginBottom: '1rem'}}>
  <img className="w-full light-image" src="/images/docs/performance-tracks/scheduler-cascading-update.png" alt="Scheduler track: cascading updates" />
  <img className="w-full dark-image" src="/images/docs/performance-tracks/scheduler-cascading-update.dark.png" alt="Scheduler track: cascading updates" />
</div>

[Tìm hiểu thêm về Effects](/learn/you-might-not-need-an-effect).

### Components {/*components*/}

Components track trực quan hóa thời lượng của các React component. Chúng được hiển thị dưới dạng flamegraph, trong đó mỗi mục đại diện cho thời lượng của render component tương ứng và tất cả các component con kế thừa của nó.

<div style={{display: 'flex', justifyContent: 'center', marginBottom: '1rem'}}>
  <img className="w-full light-image" src="/images/docs/performance-tracks/components-render.png" alt="Components track: render durations" />
  <img className="w-full dark-image" src="/images/docs/performance-tracks/components-render.dark.png" alt="Components track: render durations" />
</div>

Tương tự như thời lượng render, thời lượng effect cũng được biểu diễn dưới dạng flamegraph, nhưng với color scheme khác phù hợp với phase tương ứng trên Scheduler track.

<div style={{display: 'flex', justifyContent: 'center', marginBottom: '1rem'}}>
  <img className="w-full light-image" src="/images/docs/performance-tracks/components-effects.png" alt="Components track: effects durations" />
  <img className="w-full dark-image" src="/images/docs/performance-tracks/components-effects.dark.png" alt="Components track: effects durations" />
</div>

<Note>

Không giống như renders, không phải tất cả effect đều được hiển thị trên Components track theo mặc định.

Để duy trì hiệu suất và ngăn UI bị lộn xộn, React chỉ hiển thị những effect có thời lượng từ 0.05ms trở lên, hoặc đã kích hoạt một cập nhật.

</Note>

Các sự kiện bổ sung có thể được hiển thị trong các phase render và effects:

- <span style={{padding: '0.125rem 0.25rem', backgroundColor: '#facc15', color: '#1f1f1fff'}}>Mount</span> - Cây con component tương ứng của renders hoặc effects đã được mount.
- <span style={{padding: '0.125rem 0.25rem', backgroundColor: '#facc15', color: '#1f1f1fff'}}>Unmount</span> - Cây con component tương ứng của renders hoặc effects đã bị unmount.
- <span style={{padding: '0.125rem 0.25rem', backgroundColor: '#facc15', color: '#1f1f1fff'}}>Reconnect</span> - Tương tự như Mount, nhưng giới hạn cho các trường hợp khi [`<Activity>`](/reference/react/Activity) được sử dụng.
- <span style={{padding: '0.125rem 0.25rem', backgroundColor: '#facc15', color: '#1f1f1fff'}}>Disconnect</span> - Tương tự như Unmount, nhưng giới hạn cho các trường hợp khi [`<Activity>`](/reference/react/Activity) được sử dụng.

#### Changed props {/*changed-props*/}

Trong các bản build development, khi bạn click vào một mục render component, bạn có thể kiểm tra các thay đổi tiềm năng trong props. Bạn có thể sử dụng thông tin này để xác định các render không cần thiết.

<div style={{display: 'flex', justifyContent: 'center', marginBottom: '1rem'}}>
  <img className="w-full light-image" src="/images/docs/performance-tracks/changed-props.png" alt="Components track: changed props" />
  <img className="w-full dark-image" src="/images/docs/performance-tracks/changed-props.dark.png" alt="Components track: changed props" />
</div>

### Server {/*server*/}

<div style={{display: 'flex', justifyContent: 'center', marginBottom: '1rem'}}>
  <img className="w-full light-image" src="/images/docs/performance-tracks/server-overview.png" alt="React Server Performance Tracks" />
  <img className="w-full dark-image" src="/images/docs/performance-tracks/server-overview.dark.png" alt="React Server Performance Tracks" />
</div>

#### Server Requests {/*server-requests*/}

Server Requests track trực quan hóa tất cả các Promise cuối cùng kết thúc trong React Server Component. Điều này bao gồm bất kỳ thao tác `async` nào như gọi `fetch` hoặc các thao tác file Node.js async.

React sẽ cố gắng kết hợp các Promise được bắt đầu từ bên trong code bên thứ ba thành một span duy nhất đại diện cho thời lượng của toàn bộ thao tác chặn code bên thứ nhất.
Ví dụ, một phương thức thư viện bên thứ ba được gọi là `getUser` gọi `fetch` nội bộ nhiều lần sẽ được biểu diễn như một span duy nhất tên `getUser`, thay vì hiển thị nhiều span `fetch`.

Clicking vào các span sẽ cho bạn thấy stack trace của nơi Promise được tạo cũng như view của giá trị mà Promise đã resolve thành, nếu có.

Các Promise bị reject được hiển thị màu đỏ cùng với giá trị bị reject của chúng.

#### Server Components {/*server-components*/}

Server Components track trực quan hóa thời lượng của các React Server Components và các Promise mà chúng đã await. Thời gian được hiển thị dưới dạng flamegraph, trong đó mỗi mục đại diện cho thời lượng của render component tương ứng và tất cả các component con kế thừa của nó.

Nếu bạn await một Promise, React sẽ hiển thị thời lượng của Promise đó. Để xem tất cả các thao tác I/O, hãy sử dụng Server Requests track.

Các màu sắc khác nhau được sử dụng để chỉ thời lượng của render component. Màu càng tối, thời lượng càng dài.

Nhóm Server Components track sẽ luôn chứa track "Primary". Nếu React có thể render Server Components đồng thời, nó sẽ hiển thị các track "Parallel" bổ sung.
Nếu có nhiều hơn 8 Server Components được render đồng thời, React sẽ liên kết chúng với track "Parallel" cuối cùng thay vì thêm nhiều track hơn.
