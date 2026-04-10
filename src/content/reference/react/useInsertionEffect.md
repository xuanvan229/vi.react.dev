---
title: useInsertionEffect
---

<Pitfall>

`useInsertionEffect` dành cho các tác giả thư viện CSS-in-JS. Trừ khi bạn đang làm việc trên một thư viện CSS-in-JS và cần nơi để chèn các styles, bạn có thể muốn dùng [`useEffect`](/reference/react/useEffect) hoặc [`useLayoutEffect`](/reference/react/useLayoutEffect) thay thế.

</Pitfall>

<Intro>

`useInsertionEffect` cho phép chèn các phần tử vào DOM trước khi bất kỳ layout Effect nào kích hoạt.

```js
useInsertionEffect(setup, dependencies?)
```

</Intro>

<InlineToc />

---

## Tham khảo {/*reference*/}

### `useInsertionEffect(setup, dependencies?)` {/*useinsertioneffect*/}

Gọi `useInsertionEffect` để chèn styles trước khi bất kỳ Effect nào kích hoạt có thể cần đọc layout:

```js
import { useInsertionEffect } from 'react';

// Bên trong thư viện CSS-in-JS của bạn
function useCSS(rule) {
  useInsertionEffect(() => {
    // ... chèn các thẻ <style> ở đây ...
  });
  return rule;
}
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Tham số {/*parameters*/}

* `setup`: Hàm chứa logic của Effect của bạn. Hàm setup của bạn cũng có thể tùy chọn trả về một hàm *cleanup*. Khi component của bạn được thêm vào DOM, nhưng trước khi bất kỳ layout Effect nào kích hoạt, React sẽ chạy hàm setup của bạn. Sau mỗi lần re-render với các dependency đã thay đổi, React sẽ trước tiên chạy hàm cleanup (nếu bạn đã cung cấp) với các giá trị cũ, và sau đó chạy hàm setup của bạn với các giá trị mới. Khi component của bạn bị xóa khỏi DOM, React sẽ chạy hàm cleanup của bạn.

* **tùy chọn** `dependencies`: Danh sách tất cả các giá trị reactive được tham chiếu bên trong code `setup`. Các giá trị reactive bao gồm props, state, và tất cả các biến và hàm được khai báo trực tiếp bên trong thân component của bạn. Nếu bộ linter của bạn được [cấu hình cho React](/learn/editor-setup#linting), nó sẽ xác minh rằng mọi giá trị reactive được chỉ định chính xác như một dependency. Danh sách dependency phải có số lượng mục cố định và được viết inline như `[dep1, dep2, dep3]`. React sẽ so sánh mỗi dependency với giá trị trước đó của nó bằng thuật toán so sánh [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is). Nếu bạn không chỉ định các dependency, Effect của bạn sẽ chạy lại sau mỗi lần re-render của component.

#### Giá trị trả về {/*returns*/}

`useInsertionEffect` trả về `undefined`.

#### Lưu ý {/*caveats*/}

* Các Effect chỉ chạy trên client. Chúng không chạy trong quá trình server rendering.
* Bạn không thể cập nhật state từ bên trong `useInsertionEffect`.
* Tại thời điểm `useInsertionEffect` chạy, các ref chưa được gắn.
* `useInsertionEffect` có thể chạy trước hoặc sau khi DOM đã được cập nhật. Bạn không nên dựa vào việc DOM được cập nhật tại bất kỳ thời điểm cụ thể nào.
* Không giống như các loại Effect khác, kích hoạt cleanup cho mọi Effect và sau đó setup cho mọi Effect, `useInsertionEffect` sẽ kích hoạt cả cleanup và setup từng component một. Điều này dẫn đến "xen kẽ" của các hàm cleanup và setup.
---

## Cách sử dụng {/*usage*/}

### Chèn dynamic styles từ các thư viện CSS-in-JS {/*injecting-dynamic-styles-from-css-in-js-libraries*/}

Theo truyền thống, bạn sẽ style các React component bằng CSS thuần túy.

```js
// Trong file JS của bạn:
<button className="success" />

// Trong file CSS của bạn:
.success { color: green; }
```

Một số team thích viết styles trực tiếp trong code JavaScript thay vì viết các file CSS. Điều này thường yêu cầu sử dụng thư viện CSS-in-JS hoặc một công cụ. Có ba cách tiếp cận phổ biến cho CSS-in-JS:

1. Trích xuất tĩnh sang file CSS bằng compiler
2. Inline styles, ví dụ: `<div style={{ opacity: 1 }}>`
3. Chèn runtime của các thẻ `<style>`

Nếu bạn sử dụng CSS-in-JS, chúng tôi khuyến nghị kết hợp hai cách tiếp cận đầu (file CSS cho static styles, inline styles cho dynamic styles). **Chúng tôi không khuyến nghị chèn runtime thẻ `<style>` vì hai lý do:**

1. Chèn runtime buộc trình duyệt phải tính toán lại styles thường xuyên hơn nhiều.
2. Chèn runtime có thể rất chậm nếu nó xảy ra vào thời điểm sai trong vòng đời React.

Vấn đề đầu tiên không thể giải quyết được, nhưng `useInsertionEffect` giúp bạn giải quyết vấn đề thứ hai.

Gọi `useInsertionEffect` để chèn styles trước khi bất kỳ layout Effect nào kích hoạt:

```js {4-11}
// Bên trong thư viện CSS-in-JS của bạn
let isInserted = new Set();
function useCSS(rule) {
  useInsertionEffect(() => {
    // Như đã giải thích trước đó, chúng tôi không khuyến nghị chèn runtime của các thẻ <style>.
    // Nhưng nếu bạn phải làm vậy, thì điều quan trọng là phải làm trong useInsertionEffect.
    if (!isInserted.has(rule)) {
      isInserted.add(rule);
      document.head.appendChild(getStyleForRule(rule));
    }
  });
  return rule;
}

function Button() {
  const className = useCSS('...');
  return <div className={className} />;
}
```

Tương tự như `useEffect`, `useInsertionEffect` không chạy trên server. Nếu bạn cần thu thập các quy tắc CSS nào đã được sử dụng trên server, bạn có thể làm điều đó trong quá trình rendering:

```js {1,4-6}
let collectedRulesSet = new Set();

function useCSS(rule) {
  if (typeof window === 'undefined') {
    collectedRulesSet.add(rule);
  }
  useInsertionEffect(() => {
    // ...
  });
  return rule;
}
```

[Đọc thêm về nâng cấp các thư viện CSS-in-JS với runtime injection sang `useInsertionEffect`.](https://github.com/reactwg/react-18/discussions/110)

<DeepDive>

#### Tại sao điều này tốt hơn so với chèn styles trong quá trình rendering hoặc useLayoutEffect? {/*how-is-this-better-than-injecting-styles-during-rendering-or-uselayouteffect*/}

Nếu bạn chèn styles trong quá trình rendering và React đang xử lý một [cập nhật không chặn,](/reference/react/useTransition#perform-non-blocking-updates-with-actions) trình duyệt sẽ tính toán lại styles trên mỗi frame trong khi render một cây component, điều này có thể **cực kỳ chậm.**

`useInsertionEffect` tốt hơn so với chèn styles trong [`useLayoutEffect`](/reference/react/useLayoutEffect) hoặc [`useEffect`](/reference/react/useEffect) vì nó đảm bảo rằng khi các Effect khác chạy trong các component của bạn, các thẻ `<style>` đã được chèn. Nếu không, các tính toán layout trong các Effect thông thường sẽ sai do styles lỗi thời.

</DeepDive>
