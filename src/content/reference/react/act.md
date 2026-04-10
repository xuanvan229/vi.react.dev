---
title: act
---

<Intro>

`act` là một test helper để áp dụng các cập nhật React đang chờ trước khi đưa ra các assertion.

```js
await act(async actFn)
```

</Intro>

Để chuẩn bị một component cho các assertion, bao gọi code render nó và thực hiện các cập nhật bên trong một lệnh gọi `await act()`. Điều này làm cho bài kiểm thử của bạn chạy gần hơn với cách React hoạt động trong trình duyệt.

<Note>
Bạn có thể thấy việc sử dụng `act()` trực tiếp hơi quá dài dòng. Để tránh một số đoạn code lặp lại, bạn có thể sử dụng thư viện như [React Testing Library](https://testing-library.com/docs/react-testing-library/intro), có các helper được bao bọc với `act()`.
</Note>


<InlineToc />

---

## Tham chiếu {/*reference*/}

### `await act(async actFn)` {/*await-act-async-actfn*/}

Khi viết UI test, các tác vụ như render, sự kiện người dùng hoặc tải dữ liệu có thể được coi là các "đơn vị" tương tác với giao diện người dùng. React cung cấp một helper gọi là `act()` đảm bảo tất cả các cập nhật liên quan đến các "đơn vị" này đã được xử lý và áp dụng vào DOM trước khi bạn đưa ra bất kỳ assertion nào.

Tên `act` xuất phát từ mẫu [Arrange-Act-Assert](https://wiki.c2.com/?ArrangeActAssert).

```js {2,4}
it ('renders with button disabled', async () => {
  await act(async () => {
    root.render(<TestComponent />)
  });
  expect(container.querySelector('button')).toBeDisabled();
});
```

<Note>

Chúng tôi khuyến nghị sử dụng `act` với `await` và một hàm `async`. Mặc dù phiên bản đồng bộ hoạt động trong nhiều trường hợp, nó không hoạt động trong tất cả các trường hợp và do cách React lên lịch các cập nhật nội bộ, rất khó dự đoán khi nào bạn có thể sử dụng phiên bản đồng bộ.

Chúng tôi sẽ không dùng và loại bỏ phiên bản đồng bộ trong tương lai.

</Note>

#### Tham số {/*parameters*/}

* `async actFn`: Một hàm async bao gọi các render hoặc tương tác cho các component đang được kiểm thử. Bất kỳ cập nhật nào được kích hoạt trong `actFn` đều được thêm vào hàng đợi act nội bộ, sau đó được flush cùng nhau để xử lý và áp dụng bất kỳ thay đổi nào vào DOM. Vì nó là async, React cũng sẽ chạy bất kỳ code nào vượt qua ranh giới async, và flush bất kỳ cập nhật nào được lên lịch.

#### Giá trị trả về {/*returns*/}

`act` không trả về gì.

## Cách sử dụng {/*usage*/}

Khi kiểm thử một component, bạn có thể sử dụng `act` để đưa ra các assertion về đầu ra của nó.

Ví dụ, giả sử chúng ta có component `Counter` này, các ví dụ sử dụng bên dưới cho thấy cách kiểm thử nó:

```js
function Counter() {
  const [count, setCount] = useState(0);
  const handleClick = () => {
    setCount(prev => prev + 1);
  }

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  }, [count]);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={handleClick}>
        Click me
      </button>
    </div>
  )
}
```

### Render các component trong test {/*rendering-components-in-tests*/}

Để kiểm thử đầu ra render của một component, bao gọi render bên trong `act()`:

```js  {10,12}
import {act} from 'react';
import ReactDOMClient from 'react-dom/client';
import Counter from './Counter';

it('can render and update a counter', async () => {
  container = document.createElement('div');
  document.body.appendChild(container);

  // ✅ Render the component inside act().
  await act(() => {
    ReactDOMClient.createRoot(container).render(<Counter />);
  });

  const button = container.querySelector('button');
  const label = container.querySelector('p');
  expect(label.textContent).toBe('You clicked 0 times');
  expect(document.title).toBe('You clicked 0 times');
});
```

Ở đây, chúng ta tạo một container, thêm nó vào document, và render component `Counter` bên trong `act()`. Điều này đảm bảo rằng component được render và các effect của nó được áp dụng trước khi đưa ra các assertion.

Sử dụng `act` đảm bảo rằng tất cả các cập nhật đã được áp dụng trước khi chúng ta đưa ra các assertion.

### Dispatch event trong test {/*dispatching-events-in-tests*/}

Để kiểm thử các event, bao gọi dispatch event bên trong `act()`:

```js {14,16}
import {act} from 'react';
import ReactDOMClient from 'react-dom/client';
import Counter from './Counter';

it.only('can render and update a counter', async () => {
  const container = document.createElement('div');
  document.body.appendChild(container);

  await act( async () => {
    ReactDOMClient.createRoot(container).render(<Counter />);
  });

  // ✅ Dispatch the event inside act().
  await act(async () => {
    button.dispatchEvent(new MouseEvent('click', { bubbles: true }));
  });

  const button = container.querySelector('button');
  const label = container.querySelector('p');
  expect(label.textContent).toBe('You clicked 1 times');
  expect(document.title).toBe('You clicked 1 times');
});
```

Ở đây, chúng ta render component với `act`, sau đó dispatch event bên trong một `act()` khác. Điều này đảm bảo rằng tất cả các cập nhật từ event được áp dụng trước khi đưa ra các assertion.

<Pitfall>

Đừng quên rằng dispatch các sự kiện DOM chỉ hoạt động khi container DOM được thêm vào document. Bạn có thể sử dụng thư viện như [React Testing Library](https://testing-library.com/docs/react-testing-library/intro) để giảm đoạn code lặp lại.

</Pitfall>

## Xử lý sự cố {/*troubleshooting*/}

### Tôi gặp lỗi: "The current testing environment is not configured to support act(...)" {/*error-the-current-testing-environment-is-not-configured-to-support-act*/}

Sử dụng `act` yêu cầu thiết lập `global.IS_REACT_ACT_ENVIRONMENT=true` trong môi trường kiểm thử của bạn. Điều này để đảm bảo rằng `act` chỉ được sử dụng trong môi trường đúng.

Nếu bạn không thiết lập global, bạn sẽ thấy lỗi như thế này:

<ConsoleBlock level="error">

Warning: The current testing environment is not configured to support act(...)

</ConsoleBlock>

Để sửa, thêm điều này vào tệp thiết lập global của bạn cho các React test:

```js
global.IS_REACT_ACT_ENVIRONMENT=true
```

<Note>

Trong các framework kiểm thử như [React Testing Library](https://testing-library.com/docs/react-testing-library/intro), `IS_REACT_ACT_ENVIRONMENT` đã được thiết lập sẵn cho bạn.

</Note>
