---
title: addTransitionType
version: canary
---

<Canary>

**API `addTransitionType` hiện chỉ có sẵn trong các kênh Canary và Experimental của React.**

[Tìm hiểu thêm về các kênh phát hành của React tại đây.](/community/versioning-policy#all-release-channels)

</Canary>

<Intro>

`addTransitionType` cho phép bạn chỉ định nguyên nhân của một transition.


```js
startTransition(() => {
  addTransitionType('my-transition-type');
  setState(newState);
});
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `addTransitionType` {/*addtransitiontype*/}

#### Tham số {/*parameters*/}

- `type`: Loại transition cần thêm. Đây có thể là bất kỳ chuỗi nào.

#### Giá trị trả về {/*returns*/}

`addTransitionType` không trả về gì.

#### Lưu ý {/*caveats*/}

- Nếu nhiều transition được kết hợp lại, tất cả các Loại Transition đều được thu thập. Bạn cũng có thể thêm nhiều hơn một loại vào một Transition.
- Các Loại Transition được đặt lại sau mỗi lần commit. Điều này có nghĩa là một fallback `<Suspense>` sẽ liên kết các loại sau một `startTransition`, nhưng việc hiển thị nội dung thì không.

---

## Cách sử dụng {/*usage*/}

### Thêm nguyên nhân của một transition {/*adding-the-cause-of-a-transition*/}

Gọi `addTransitionType` bên trong `startTransition` để chỉ định nguyên nhân của một transition:

``` [[1, 6, "addTransitionType"], [2, 5, "startTransition", [3, 6, "'submit-click'"]]
import { startTransition, addTransitionType } from 'react';

function Submit({action) {
  function handleClick() {
    startTransition(() => {
      addTransitionType('submit-click');
      action();
    });
  }

  return <button onClick={handleClick}>Click me</button>;
}

```

Khi bạn gọi <CodeStep step={1}>addTransitionType</CodeStep> bên trong phạm vi của <CodeStep step={2}>startTransition</CodeStep>, React sẽ liên kết <CodeStep step={3}>submit-click</CodeStep> như một trong những nguyên nhân của Transition.

Hiện tại, các Loại Transition có thể được sử dụng để tùy chỉnh các hoạt ảnh khác nhau dựa trên nguyên nhân gây ra Transition. Bạn có ba cách khác nhau để lựa chọn:

- [Tùy chỉnh hoạt ảnh sử dụng các loại view transition của trình duyệt](#customize-animations-using-browser-view-transition-types)
- [Tùy chỉnh hoạt ảnh sử dụng `View Transition` Class](#customize-animations-using-view-transition-class)
- [Tùy chỉnh hoạt ảnh sử dụng các sự kiện `ViewTransition`](#customize-animations-using-viewtransition-events)

Trong tương lai, chúng tôi dự định hỗ trợ thêm nhiều trường hợp sử dụng cho việc dùng nguyên nhân của một transition.

---
### Tùy chỉnh hoạt ảnh sử dụng các loại view transition của trình duyệt {/*customize-animations-using-browser-view-transition-types*/}

Khi một [`ViewTransition`](/reference/react/ViewTransition) được kích hoạt từ một transition, React thêm tất cả các Loại Transition như [các loại view transition](https://www.w3.org/TR/css-view-transitions-2/#active-view-transition-pseudo-examples) của trình duyệt vào element.

Điều này cho phép bạn tùy chỉnh các hoạt ảnh khác nhau dựa trên các phạm vi CSS:

```js [11]
function Component() {
  return (
    <ViewTransition>
      <div>Hello</div>
    </ViewTransition>
  );
}

startTransition(() => {
  addTransitionType('my-transition-type');
  setShow(true);
});
```

```css
:root:active-view-transition-type(my-transition-type) {
  &::view-transition-...(...) {
    ...
  }
}
```

---

### Tùy chỉnh hoạt ảnh sử dụng `View Transition` Class {/*customize-animations-using-view-transition-class*/}

Bạn có thể tùy chỉnh hoạt ảnh cho một `ViewTransition` được kích hoạt dựa trên loại bằng cách truyền một object vào View Transition Class:

```js
function Component() {
  return (
    <ViewTransition enter={{
      'my-transition-type': 'my-transition-class',
    }}>
      <div>Hello</div>
    </ViewTransition>
  );
}

// ...
startTransition(() => {
  addTransitionType('my-transition-type');
  setState(newState);
});
```

Nếu nhiều loại khớp, chúng được nối lại với nhau. Nếu không có loại nào khớp thì mục "default" đặc biệt được sử dụng thay thế. Nếu bất kỳ loại nào có giá trị "none" thì loại đó thắng và ViewTransition bị vô hiệu hóa (không được gán tên).

Chúng có thể được kết hợp với các prop enter/exit/update/layout/share để khớp dựa trên loại kích hoạt và Loại Transition.

```js
<ViewTransition enter={{
  'navigation-back': 'enter-right',
  'navigation-forward': 'enter-left',
}}
exit={{
  'navigation-back': 'exit-right',
  'navigation-forward': 'exit-left',
}}>
```

---

### Tùy chỉnh hoạt ảnh sử dụng các sự kiện `ViewTransition` {/*customize-animations-using-viewtransition-events*/}

Bạn có thể tùy chỉnh hoạt ảnh theo cách mệnh lệnh cho một `ViewTransition` được kích hoạt dựa trên loại bằng cách sử dụng các sự kiện View Transition:

```
<ViewTransition onUpdate={(inst, types) => {
  if (types.includes('navigation-back')) {
    ...
  } else if (types.includes('navigation-forward')) {
    ...
  } else {
    ...
  }
}}>
```

Điều này cho phép bạn chọn các hoạt ảnh mệnh lệnh khác nhau dựa trên nguyên nhân.
