---
title: error-boundaries
---

<Intro>

Kiểm tra việc sử dụng Error Boundary thay vì try/catch cho lỗi trong các component con.

</Intro>

## Chi tiết quy tắc {/*rule-details*/}

Các khối try/catch không thể bắt lỗi xảy ra trong quá trình render của React. Lỗi được throw trong các phương thức render hoặc hook sẽ lan truyền lên trên cây component. Chỉ [Error Boundary](/reference/react/Component#catching-rendering-errors-with-an-error-boundary) mới có thể bắt các lỗi này.

### Không hợp lệ {/*invalid*/}

Ví dụ về code không đúng cho quy tắc này:

```js {expectedErrors: {'react-compiler': [4]}}
// ❌ Try/catch không bắt được lỗi render
function Parent() {
  try {
    return <ChildComponent />; // Nếu component này throw lỗi, catch không giúp được
  } catch (error) {
    return <div>Đã xảy ra lỗi</div>;
  }
}
```

### Hợp lệ {/*valid*/}

Ví dụ về code đúng cho quy tắc này:

```js
// ✅ Sử dụng error boundary
function Parent() {
  return (
    <ErrorBoundary>
      <ChildComponent />
    </ErrorBoundary>
  );
}
```

## Xử lý sự cố {/*troubleshooting*/}

### Tại sao linter bảo tôi không bọc `use` trong `try`/`catch`? {/*why-is-the-linter-telling-me-not-to-wrap-use-in-trycatch*/}

Hook `use` không throw lỗi theo cách truyền thống, nó tạm dừng việc thực thi component. Khi `use` gặp một promise đang chờ, nó tạm dừng component và để React hiển thị fallback. Chỉ Suspense và Error Boundary mới có thể xử lý các trường hợp này. Linter cảnh báo về việc dùng `try`/`catch` quanh `use` để tránh nhầm lẫn vì khối `catch` sẽ không bao giờ chạy.

```js {expectedErrors: {'react-compiler': [5]}}
// ❌ Try/catch quanh hook `use`
function Component({promise}) {
  try {
    const data = use(promise); // Không bắt được - `use` tạm dừng, không throw
    return <div>{data}</div>;
  } catch (error) {
    return <div>Tải thất bại</div>; // Không thể truy cập
  }
}

// ✅ Error boundary bắt lỗi của `use`
function App() {
  return (
    <ErrorBoundary fallback={<div>Tải thất bại</div>}>
      <Suspense fallback={<div>Đang tải...</div>}>
        <DataComponent promise={fetchData()} />
      </Suspense>
    </ErrorBoundary>
  );
}
```
