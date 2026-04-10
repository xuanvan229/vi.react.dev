---
title: eslint-plugin-react-hooks
version: rc
---

<Intro>

`eslint-plugin-react-hooks` cung cấp các quy tắc ESLint để thực thi [Các quy tắc của React](/reference/rules).

</Intro>

Plugin này giúp bạn phát hiện các vi phạm quy tắc của React tại thời điểm build, đảm bảo các component và hook của bạn tuân theo các quy tắc của React về tính đúng đắn và hiệu suất. Các lint bao gồm cả các pattern React cơ bản (exhaustive-deps và rules-of-hooks) và các vấn đề được React Compiler phát hiện. Các chuẩn đoán của React Compiler được tự động hiển thị bởi plugin ESLint này, và có thể được sử dụng ngay cả khi ứng dụng của bạn chưa áp dụng compiler.

<Note>
Khi compiler báo cáo một chuẩn đoán, điều đó có nghĩa là compiler đã có thể phát hiện tĩnh một pattern không được hỗ trợ hoặc vi phạm Các quy tắc của React. Khi phát hiện điều này, nó **tự động** bỏ qua các component và hook đó, trong khi vẫn giữ phần còn lại của ứng dụng được biên dịch. Điều này đảm bảo phạm vi tối ưu của các tối ưu hóa an toàn mà không làm hỏng ứng dụng của bạn.

Điều này có nghĩa đối với việc lint là bạn không cần phải sửa tất cả vi phạm ngay lập tức. Hãy xử lý chúng theo tốc độ của riêng bạn để dần dần tăng số lượng component được tối ưu hóa.
</Note>

## Các quy tắc được khuyến nghị {/*recommended*/}

Các quy tắc này được bao gồm trong preset `recommended` trong `eslint-plugin-react-hooks`:

* [`exhaustive-deps`](/reference/eslint-plugin-react-hooks/lints/exhaustive-deps) - Kiểm tra mảng dependency cho React hook chứa tất cả các dependency cần thiết
* [`rules-of-hooks`](/reference/eslint-plugin-react-hooks/lints/rules-of-hooks) - Kiểm tra component và hook tuân theo Quy tắc của Hook
* [`component-hook-factories`](/reference/eslint-plugin-react-hooks/lints/component-hook-factories) - Kiểm tra các hàm bậc cao định nghĩa component hoặc hook lồng nhau
* [`config`](/reference/eslint-plugin-react-hooks/lints/config) - Kiểm tra các tùy chọn cấu hình compiler
* [`error-boundaries`](/reference/eslint-plugin-react-hooks/lints/error-boundaries) - Kiểm tra việc sử dụng Error Boundary thay vì try/catch cho lỗi con
* [`gating`](/reference/eslint-plugin-react-hooks/lints/gating) - Kiểm tra cấu hình của chế độ gating
* [`globals`](/reference/eslint-plugin-react-hooks/lints/globals) - Kiểm tra việc gán/thay đổi biến toàn cục trong quá trình render
* [`immutability`](/reference/eslint-plugin-react-hooks/lints/immutability) - Kiểm tra việc thay đổi props, state và các giá trị bất biến khác
* [`incompatible-library`](/reference/eslint-plugin-react-hooks/lints/incompatible-library) - Kiểm tra việc sử dụng các thư viện không tương thích với memoization
* [`preserve-manual-memoization`](/reference/eslint-plugin-react-hooks/lints/preserve-manual-memoization) - Kiểm tra memoization thủ công hiện có được compiler bảo toàn
* [`purity`](/reference/eslint-plugin-react-hooks/lints/purity) - Kiểm tra component/hook là thuần túy bằng cách kiểm tra các hàm không thuần túy đã biết
* [`refs`](/reference/eslint-plugin-react-hooks/lints/refs) - Kiểm tra việc sử dụng ref đúng cách, không đọc/ghi trong quá trình render
* [`set-state-in-effect`](/reference/eslint-plugin-react-hooks/lints/set-state-in-effect) - Kiểm tra việc gọi setState đồng bộ trong effect
* [`set-state-in-render`](/reference/eslint-plugin-react-hooks/lints/set-state-in-render) - Kiểm tra việc set state trong quá trình render
* [`static-components`](/reference/eslint-plugin-react-hooks/lints/static-components) - Kiểm tra component là tĩnh, không được tạo lại mỗi lần render
* [`unsupported-syntax`](/reference/eslint-plugin-react-hooks/lints/unsupported-syntax) - Kiểm tra cú pháp mà React Compiler không hỗ trợ
* [`use-memo`](/reference/eslint-plugin-react-hooks/lints/use-memo) - Kiểm tra việc sử dụng hook `useMemo` mà không có giá trị trả về
