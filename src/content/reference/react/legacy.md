---
title: "Các API React Kế thừa"
---

<Intro>

Các API này được export từ package `react`, nhưng không được khuyến nghị sử dụng trong code viết mới. Xem các trang API riêng lẻ được liên kết để biết các thay thế được đề xuất.

</Intro>

---

## API Kế thừa {/*legacy-apis*/}

* [`Children`](/reference/react/Children) cho phép bạn thao tác và chuyển đổi JSX nhận được qua prop `children`. [Xem các thay thế.](/reference/react/Children#alternatives)
* [`cloneElement`](/reference/react/cloneElement) cho phép bạn tạo một React element sử dụng một element khác làm điểm khởi đầu. [Xem các thay thế.](/reference/react/cloneElement#alternatives)
* [`Component`](/reference/react/Component) cho phép bạn định nghĩa một component React dưới dạng class JavaScript. [Xem các thay thế.](/reference/react/Component#alternatives)
* [`createElement`](/reference/react/createElement) cho phép bạn tạo một React element. Thông thường, bạn sẽ sử dụng JSX thay thế.
* [`createRef`](/reference/react/createRef) tạo một đối tượng ref có thể chứa giá trị tùy ý. [Xem các thay thế.](/reference/react/createRef#alternatives)
* [`forwardRef`](/reference/react/forwardRef) cho phép component của bạn expose một node DOM cho component cha với một [ref.](/learn/manipulating-the-dom-with-refs)
* [`isValidElement`](/reference/react/isValidElement) kiểm tra xem một giá trị có phải là React element hay không. Thường được sử dụng với [`cloneElement`.](/reference/react/cloneElement)
* [`PureComponent`](/reference/react/PureComponent) tương tự như [`Component`,](/reference/react/Component) nhưng bỏ qua re-render khi props giống nhau. [Xem các thay thế.](/reference/react/PureComponent#alternatives)

---

## API đã bị loại bỏ {/*removed-apis*/}

Các API này đã bị loại bỏ trong React 19:

* [`createFactory`](https://18.react.dev/reference/react/createFactory): sử dụng JSX thay thế.
* Class Components: [`static contextTypes`](https://18.react.dev//reference/react/Component#static-contexttypes): sử dụng [`static contextType`](#static-contexttype) thay thế.
* Class Components: [`static childContextTypes`](https://18.react.dev//reference/react/Component#static-childcontexttypes): sử dụng [`static contextType`](#static-contexttype) thay thế.
* Class Components: [`static getChildContext`](https://18.react.dev//reference/react/Component#getchildcontext): sử dụng [`Context`](/reference/react/createContext#provider) thay thế.
* Class Components: [`static propTypes`](https://18.react.dev//reference/react/Component#static-proptypes): sử dụng hệ thống type như [TypeScript](https://www.typescriptlang.org/) thay thế.
* Class Components: [`this.refs`](https://18.react.dev//reference/react/Component#refs): sử dụng [`createRef`](/reference/react/createRef) thay thế.
