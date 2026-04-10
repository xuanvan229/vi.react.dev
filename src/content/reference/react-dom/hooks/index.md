---
title: "Các Hook React DOM tích hợp sẵn"
---

<Intro>

Package `react-dom` chứa các Hook chỉ được hỗ trợ cho các ứng dụng web (chạy trong môi trường DOM của trình duyệt). Các Hook này không được hỗ trợ trong các môi trường không phải trình duyệt như iOS, Android hoặc ứng dụng Windows. Nếu bạn đang tìm kiếm các Hook được hỗ trợ trong trình duyệt web *và các môi trường khác*, hãy xem [trang React Hooks](/reference/react/hooks). Trang này liệt kê tất cả các Hook trong package `react-dom`.

</Intro>

---

## Hook cho Form {/*form-hooks*/}

*Form* cho phép bạn tạo các điều khiển tương tác để gửi thông tin. Để quản lý form trong các component, hãy sử dụng một trong các Hook sau:

* [`useFormStatus`](/reference/react-dom/hooks/useFormStatus) cho phép bạn cập nhật UI dựa trên trạng thái của form.

```js
function Form({ action }) {
  async function increment(n) {
    return n + 1;
  }
  const [count, incrementFormAction] = useActionState(increment, 0);
  return (
    <form action={action}>
      <button formAction={incrementFormAction}>Count: {count}</button>
      <Button />
    </form>
  );
}

function Button() {
  const { pending } = useFormStatus();
  return (
    <button disabled={pending} type="submit">
      Submit
    </button>
  );
}
```
