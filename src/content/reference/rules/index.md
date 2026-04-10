---
title: Các quy tắc của React
---

<Intro>
Giống như các ngôn ngữ lập trình khác nhau có cách riêng để diễn đạt các khái niệm, React có các idiom riêng -- hay các quy tắc -- về cách diễn đạt các pattern theo cách dễ hiểu và mang lại các ứng dụng chất lượng cao.
</Intro>

<InlineToc />

---

<Note>
Để tìm hiểu thêm về cách diễn đạt UI với React, chúng tôi khuyến nghị đọc [Tư duy trong React](/learn/thinking-in-react).
</Note>

Phần này mô tả các quy tắc bạn cần tuân theo để viết code React theo đúng chuẩn. Viết code React theo đúng chuẩn có thể giúp bạn viết các ứng dụng được tổ chức tốt, an toàn và có khả năng kết hợp. Các thuộc tính này làm cho ứng dụng của bạn bền vững hơn với các thay đổi và giúp dễ dàng hơn khi làm việc với các nhà phát triển, thư viện và công cụ khác.

Các quy tắc này được gọi là **Các quy tắc của React**. Chúng là các quy tắc -- chứ không chỉ là hướng dẫn -- theo nghĩa là nếu chúng bị vi phạm, ứng dụng của bạn có khả năng có lỗi. Code của bạn cũng trở nên không theo chuẩn và khó hiểu, khó suy luận hơn.

Chúng tôi đặc biệt khuyến nghị sử dụng [Strict Mode](/reference/react/StrictMode) cùng với [ESLint plugin](https://www.npmjs.com/package/eslint-plugin-react-hooks) của React để giúp codebase của bạn tuân theo các Quy tắc của React. Bằng cách tuân theo các Quy tắc của React, bạn sẽ có thể tìm và xử lý các lỗi này và giữ cho ứng dụng của bạn dễ bảo trì.

---

## Components và Hooks phải thuần khiết {/*components-and-hooks-must-be-pure*/}

[Tính thuần khiết trong Components và Hooks](/reference/rules/components-and-hooks-must-be-pure) là một quy tắc quan trọng của React giúp ứng dụng của bạn có thể dự đoán được, dễ debug, và cho phép React tự động tối ưu hóa code của bạn.

* [Components phải có tính idempotent](/reference/rules/components-and-hooks-must-be-pure#components-and-hooks-must-be-idempotent) -- Các React component được giả định luôn trả về cùng một đầu ra với các đầu vào tương ứng -- props, state, và context.
* [Side effects phải chạy bên ngoài render](/reference/rules/components-and-hooks-must-be-pure#side-effects-must-run-outside-of-render) -- Side effects không nên chạy trong render, vì React có thể render components nhiều lần để tạo trải nghiệm người dùng tốt nhất.
* [Props và state là bất biến](/reference/rules/components-and-hooks-must-be-pure#props-and-state-are-immutable) -- Props và state của một component là các snapshot bất biến đối với một lần render. Không bao giờ thay đổi trực tiếp chúng.
* [Giá trị trả về và tham số của Hooks là bất biến](/reference/rules/components-and-hooks-must-be-pure#return-values-and-arguments-to-hooks-are-immutable) -- Một khi các giá trị được truyền cho Hook, bạn không nên sửa đổi chúng. Giống như props trong JSX, các giá trị trở nên bất biến khi được truyền cho Hook.
* [Giá trị là bất biến sau khi được truyền vào JSX](/reference/rules/components-and-hooks-must-be-pure#values-are-immutable-after-being-passed-to-jsx) -- Đừng thay đổi các giá trị sau khi chúng đã được sử dụng trong JSX. Di chuyển việc thay đổi trước khi JSX được tạo.

---

## React gọi Components và Hooks {/*react-calls-components-and-hooks*/}

[React chịu trách nhiệm render components và hooks khi cần thiết để tối ưu hóa trải nghiệm người dùng.](/reference/rules/react-calls-components-and-hooks) Nó mang tính khai báo: bạn nói cho React biết cần render gì trong logic của component, và React sẽ tìm ra cách tốt nhất để hiển thị cho người dùng.

* [Không bao giờ gọi trực tiếp hàm component](/reference/rules/react-calls-components-and-hooks#never-call-component-functions-directly) -- Components chỉ nên được sử dụng trong JSX. Không gọi chúng như các hàm thông thường.
* [Không bao giờ truyền hooks như giá trị thông thường](/reference/rules/react-calls-components-and-hooks#never-pass-around-hooks-as-regular-values) -- Hooks chỉ nên được gọi bên trong components. Không bao giờ truyền nó như một giá trị thông thường.

---

## Các quy tắc của Hooks {/*rules-of-hooks*/}

Hooks được định nghĩa bằng các hàm JavaScript, nhưng chúng đại diện cho một loại logic UI có thể tái sử dụng đặc biệt với các hạn chế về nơi chúng có thể được gọi. Bạn cần tuân theo [Các quy tắc của Hooks](/reference/rules/rules-of-hooks) khi sử dụng chúng.

* [Chỉ gọi Hooks ở cấp cao nhất](/reference/rules/rules-of-hooks#only-call-hooks-at-the-top-level) -- Không gọi Hooks bên trong vòng lặp, điều kiện, hoặc hàm lồng nhau. Thay vào đó, luôn sử dụng Hooks ở cấp cao nhất của hàm React, trước bất kỳ lệnh return sớm nào.
* [Chỉ gọi Hooks từ các hàm React](/reference/rules/rules-of-hooks#only-call-hooks-from-react-functions) -- Không gọi Hooks từ các hàm JavaScript thông thường.
