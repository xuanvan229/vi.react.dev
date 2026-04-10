---
title: "<progress>"
---

<Intro>

Component [`<progress>` có sẵn trong trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/progress) cho phép bạn render một chỉ báo tiến trình.

```js
<progress value={0.5} />
```

</Intro>

<InlineToc />

---

## Tham chiếu {/*reference*/}

### `<progress>` {/*progress*/}

Để hiển thị một chỉ báo tiến trình, render component [`<progress>` có sẵn trong trình duyệt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/progress).

```js
<progress value={0.5} />
```

[Xem thêm các ví dụ bên dưới.](#usage)

#### Props {/*props*/}

`<progress>` hỗ trợ tất cả [common element props.](/reference/react-dom/components/common#common-props)

Ngoài ra, `<progress>` hỗ trợ các props này:

* [`max`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/progress#max): A number. Specifies the maximum `value`. Defaults to `1`.
* [`value`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/progress#value): A number between `0` and `max`, or `null` for indeterminate progress. Specifies how much was done.

---

## Cách sử dụng {/*usage*/}

### Kiểm soát chỉ báo tiến trình {/*controlling-a-progress-indicator*/}

Để hiển thị một chỉ báo tiến trình, render một component `<progress>`. Bạn có thể truyền một số `value` giữa `0` và giá trị `max` mà bạn chỉ định. Nếu bạn không truyền giá trị `max`, nó sẽ được giả định là `1` theo mặc định.

Nếu thao tác không đang diễn ra, hãy truyền `value={null}` để đặt chỉ báo tiến trình vào trạng thái không xác định.

<Sandpack>

```js
export default function App() {
  return (
    <>
      <progress value={0} />
      <progress value={0.5} />
      <progress value={0.7} />
      <progress value={75} max={100} />
      <progress value={1} />
      <progress value={null} />
    </>
  );
}
```

```css
progress { display: block; }
```

</Sandpack>
