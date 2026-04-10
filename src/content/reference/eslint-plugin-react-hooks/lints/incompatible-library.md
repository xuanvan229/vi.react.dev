---
title: incompatible-library
---

<Intro>

Kiểm tra việc sử dụng các thư viện không tương thích với memoization (thủ công hoặc tự động).

</Intro>

<Note>

Các thư viện này được thiết kế trước khi các quy tắc memoization của React được ghi tài liệu đầy đủ. Chúng đã đưa ra các lựa chọn đúng đắn vào thời điểm đó để tối ưu hóa các cách tiện lợi để giữ component phản ứng vừa đủ khi state ứng dụng thay đổi. Mặc dù các pattern cũ này hoạt động, chúng tôi đã phát hiện rằng chúng không tương thích với mô hình lập trình của React. Chúng tôi sẽ tiếp tục làm việc với các tác giả thư viện để chuyển đổi các thư viện này sang sử dụng các pattern tuân theo Các quy tắc của React.

</Note>

## Chi tiết quy tắc {/*rule-details*/}

Một số thư viện sử dụng các pattern không được React hỗ trợ. Khi linter phát hiện việc sử dụng các API này từ [danh sách đã biết](https://github.com/facebook/react/blob/main/compiler/packages/babel-plugin-react-compiler/src/HIR/DefaultModuleTypeProvider.ts), nó sẽ đánh dấu chúng theo quy tắc này. Điều này có nghĩa là React Compiler có thể tự động bỏ qua các component sử dụng các API không tương thích này, để tránh làm hỏng ứng dụng của bạn.

```js
// Ví dụ về cách memoization bị hỏng với các thư viện này
function Form() {
  const { watch } = useForm();

  // ❌ Giá trị này sẽ không bao giờ cập nhật, ngay cả khi trường 'name' thay đổi
  const name = useMemo(() => watch('name'), [watch]);

  return <div>Name: {name}</div>; // UI dường như "đóng băng"
}
```

React Compiler tự động memoize giá trị theo Các quy tắc của React. Nếu một thứ gì đó hỏng với `useMemo` thủ công, nó cũng sẽ làm hỏng tối ưu hóa tự động của compiler. Quy tắc này giúp xác định các pattern có vấn đề này.

<DeepDive>

#### Thiết kế API tuân theo Các quy tắc của React {/*designing-apis-that-follow-the-rules-of-react*/}

Một câu hỏi cần suy nghĩ khi thiết kế API thư viện hoặc hook là liệu việc gọi API có thể được memoize an toàn với `useMemo` hay không. Nếu không, cả memoization thủ công và của React Compiler sẽ làm hỏng code của người dùng.

Ví dụ, một pattern không tương thích là "interior mutability". Interior mutability là khi một object hoặc function giữ state ẩn riêng của nó thay đổi theo thời gian, mặc dù tham chiếu đến nó vẫn giữ nguyên. Hãy nghĩ về nó như một cái hộp trông giống nhau bên ngoài nhưng bí mật sắp xếp lại nội dung bên trong. React không thể biết có gì thay đổi vì nó chỉ kiểm tra xem bạn có đưa cho nó một hộp khác không, chứ không phải những gì bên trong. Điều này phá vỡ memoization, vì React dựa vào việc object (hoặc function) bên ngoài thay đổi nếu một phần giá trị của nó đã thay đổi.

Theo nguyên tắc chung, khi thiết kế React API, hãy nghĩ xem liệu `useMemo` có làm hỏng nó không:

```js
function Component() {
  const { someFunction } = useLibrary();
  // luôn nên an toàn khi memoize hàm như thế này
  const result = useMemo(() => someFunction(), [someFunction]);
}
```

Thay vào đó, thiết kế API trả về state bất biến và sử dụng các hàm cập nhật rõ ràng:

```js
// ✅ Tốt: Trả về state bất biến thay đổi tham chiếu khi cập nhật
function Component() {
  const { field, updateField } = useLibrary();
  // luôn an toàn khi memo
  const greeting = useMemo(() => `Hello, ${field.name}!`, [field.name]);

  return (
    <div>
      <input
        value={field.name}
        onChange={(e) => updateField('name', e.target.value)}
      />
      <p>{greeting}</p>
    </div>
  );
}
```

</DeepDive>

### Không hợp lệ {/*invalid*/}

Ví dụ về code không đúng cho quy tắc này:

```js
// ❌ react-hook-form `watch`
function Component() {
  const {watch} = useForm();
  const value = watch('field'); // Interior mutability
  return <div>{value}</div>;
}

// ❌ TanStack Table `useReactTable`
function Component({data}) {
  const table = useReactTable({
    data,
    columns,
    getCoreRowModel: getCoreRowModel(),
  });
  // table instance sử dụng interior mutability
  return <Table table={table} />;
}
```

<Pitfall>

#### MobX {/*mobx*/}

Các pattern MobX như `observer` cũng phá vỡ các giả định về memoization, nhưng linter chưa phát hiện được chúng. Nếu bạn dựa vào MobX và thấy ứng dụng không hoạt động với React Compiler, bạn có thể cần sử dụng directive `"use no memo"`.

```js
// ❌ MobX `observer`
const Component = observer(() => {
  const [timer] = useState(() => new Timer());
  return <span>Seconds passed: {timer.secondsPassed}</span>;
});
```

</Pitfall>

### Hợp lệ {/*valid*/}

Ví dụ về code đúng cho quy tắc này:

```js
// ✅ Với react-hook-form, dùng `useWatch`:
function Component() {
  const {register, control} = useForm();
  const watchedValue = useWatch({
    control,
    name: 'field'
  });

  return (
    <>
      <input {...register('field')} />
      <div>Giá trị hiện tại: {watchedValue}</div>
    </>
  );
}
```

Một số thư viện khác chưa có API thay thế tương thích với mô hình memoization của React. Nếu linter không tự động bỏ qua các component hoặc hook gọi các API này, vui lòng [tạo issue](https://github.com/facebook/react/issues) để chúng tôi có thể thêm vào linter.
