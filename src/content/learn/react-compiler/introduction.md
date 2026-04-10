---
title: Giới thiệu
---

<Intro>
React Compiler là một công cụ build-time mới tự động tối ưu hóa ứng dụng React của bạn. Nó hoạt động với JavaScript thuần, và hiểu [Quy tắc của React](/reference/rules), nên bạn không cần viết lại bất kỳ code nào để sử dụng nó.
</Intro>

<YouWillLearn>

* React Compiler làm gì
* Bắt đầu với compiler
* Chiến lược áp dụng dần dần
* Gỡ lỗi và xử lý sự cố khi gặp vấn đề
* Sử dụng compiler trên thư viện React của bạn

</YouWillLearn>

## React Compiler làm gì? {/*what-does-react-compiler-do*/}

React Compiler tự động tối ưu hóa ứng dụng React của bạn tại thời điểm build. React thường đủ nhanh mà không cần tối ưu hóa, nhưng đôi khi bạn cần thủ công memoize component và giá trị để giữ ứng dụng phản hồi nhanh. Việc memoization thủ công này nhàm chán, dễ sai, và thêm code cần bảo trì. React Compiler thực hiện tối ưu hóa này tự động cho bạn, giải phóng bạn khỏi gánh nặng tinh thần này để bạn có thể tập trung vào việc xây dựng tính năng.

### Trước React Compiler {/*before-react-compiler*/}

Không có compiler, bạn cần thủ công memoize component và giá trị để tối ưu hóa re-render:

```js
import { useMemo, useCallback, memo } from 'react';

const ExpensiveComponent = memo(function ExpensiveComponent({ data, onClick }) {
  const processedData = useMemo(() => {
    return expensiveProcessing(data);
  }, [data]);

  const handleClick = useCallback((item) => {
    onClick(item.id);
  }, [onClick]);

  return (
    <div>
      {processedData.map(item => (
        <Item key={item.id} onClick={() => handleClick(item)} />
      ))}
    </div>
  );
});
```


<Note>

Memoization thủ công này có một bug tinh vi làm hỏng memoization:

```js [[2, 1, "() => handleClick(item)"]]
<Item key={item.id} onClick={() => handleClick(item)} />
```

Mặc dù `handleClick` được bọc trong `useCallback`, arrow function `() => handleClick(item)` tạo một hàm mới mỗi khi component render. Điều này có nghĩa là `Item` sẽ luôn nhận prop `onClick` mới, làm hỏng memoization.

React Compiler có thể tối ưu hóa điều này đúng cách có hoặc không có arrow function, đảm bảo rằng `Item` chỉ re-render khi `props.onClick` thay đổi.

</Note>

### Sau React Compiler {/*after-react-compiler*/}

Với React Compiler, bạn viết cùng code mà không cần memoization thủ công:

```js
function ExpensiveComponent({ data, onClick }) {
  const processedData = expensiveProcessing(data);

  const handleClick = (item) => {
    onClick(item.id);
  };

  return (
    <div>
      {processedData.map(item => (
        <Item key={item.id} onClick={() => handleClick(item)} />
      ))}
    </div>
  );
}
```

_[Xem ví dụ này trong React Compiler Playground](https://playground.react.dev/#N4Igzg9grgTgxgUxALhAMygOzgFwJYSYAEAogB4AOCmYeAbggMIQC2Fh1OAFMEQCYBDHAIA0RQowA2eOAGsiAXwCURYAB1iROITA4iFGBERgwCPgBEhAogF4iCStVoMACoeO1MAcy6DhSgG4NDSItHT0ACwFMPkkmaTlbIi48HAQWFRsAPlUQ0PFMKRlZFLSWADo8PkC8hSDMPJgEHFhiLjzQgB4+eiyO-OADIwQTM0thcpYBClL02xz2zXz8zoBJMqJZBABPG2BU9Mq+BQKiuT2uTJyomLizkoOMk4B6PqX8pSUFfs7nnro3qEapgFCAFEA)_

React Compiler tự động áp dụng memoization tối ưu, đảm bảo ứng dụng của bạn chỉ re-render khi cần thiết.

<DeepDive>
#### React Compiler thêm loại memoization nào? {/*what-kind-of-memoization-does-react-compiler-add*/}

Memoization tự động của React Compiler chủ yếu tập trung vào **cải thiện hiệu suất cập nhật** (re-render các component hiện có), nên nó tập trung vào hai trường hợp sử dụng:

1. **Bỏ qua re-render lan truyền của component**
    * Re-render `<Parent />` gây ra nhiều component trong cây component re-render, mặc dù chỉ `<Parent />` thay đổi
1. **Bỏ qua các phép tính tốn kém bên ngoài React**
    * Ví dụ, gọi `expensivelyProcessAReallyLargeArrayOfObjects()` bên trong component hoặc hook cần dữ liệu đó

#### Tối ưu hóa re-render {/*optimizing-re-renders*/}

React cho phép bạn biểu diễn UI như một hàm của state hiện tại (cụ thể hơn: props, state, và context). Trong triển khai hiện tại, khi state của một component thay đổi, React sẽ re-render component đó _và tất cả con của nó_ -- trừ khi bạn đã áp dụng memoization thủ công với `useMemo()`, `useCallback()`, hoặc `React.memo()`. Ví dụ, trong ví dụ sau, `<MessageButton>` sẽ re-render mỗi khi state của `<FriendList>` thay đổi:

```javascript
function FriendList({ friends }) {
  const onlineCount = useFriendOnlineCount();
  if (friends.length === 0) {
    return <NoFriends />;
  }
  return (
    <div>
      <span>{onlineCount} online</span>
      {friends.map((friend) => (
        <FriendListCard key={friend.id} friend={friend} />
      ))}
      <MessageButton />
    </div>
  );
}
```
[_Xem ví dụ này trong React Compiler Playground_](https://playground.react.dev/#N4Igzg9grgTgxgUxALhAMygOzgFwJYSYAEAYjHgpgCYAyeYOAFMEWuZVWEQL4CURwADrEicQgyKEANnkwIAwtEw4iAXiJQwCMhWoB5TDLmKsTXgG5hRInjRFGbXZwB0UygHMcACzWr1ABn4hEWsYBBxYYgAeADkIHQ4uAHoAPksRbisiMIiYYkYs6yiqPAA3FMLrIiiwAAcAQ0wU4GlZBSUcbklDNqikusaKkKrgR0TnAFt62sYHdmp+VRT7SqrqhOo6Bnl6mCoiAGsEAE9VUfmqZzwqLrHqM7ubolTVol5eTOGigFkEMDB6u4EAAhKA4HCEZ5DNZ9ErlLIWYTcEDcIA)

React Compiler tự động áp dụng tương đương memoization thủ công, đảm bảo chỉ các phần liên quan của ứng dụng re-render khi state thay đổi, đôi khi được gọi là "fine-grained reactivity". Trong ví dụ trên, React Compiler xác định rằng giá trị trả về của `<FriendListCard />` có thể được tái sử dụng ngay cả khi `friends` thay đổi, và có thể tránh tạo lại JSX này _và_ tránh re-render `<MessageButton>` khi count thay đổi.

#### Các phép tính tốn kém cũng được memoize {/*expensive-calculations-also-get-memoized*/}

React Compiler cũng có thể tự động memoize các phép tính tốn kém được sử dụng trong rendering:

```js
// **Không** được memoize bởi React Compiler, vì đây không phải component hay hook
function expensivelyProcessAReallyLargeArrayOfObjects() { /* ... */ }

// Được memoize bởi React Compiler vì đây là component
function TableContainer({ items }) {
  // Lời gọi hàm này sẽ được memoize:
  const data = expensivelyProcessAReallyLargeArrayOfObjects(items);
  // ...
}
```
[_Xem ví dụ này trong React Compiler Playground_](https://playground.react.dev/#N4Igzg9grgTgxgUxALhAejQAgFTYHIQAuumAtgqRAJYBeCAJpgEYCemASggIZyGYDCEUgAcqAGwQwANJjBUAdokyEAFlTCZ1meUUxdMcIcIjyE8vhBiYVECAGsAOvIBmURYSonMCAB7CzcgBuCGIsAAowEIhgYACCnFxioQAyXDAA5gixMDBcLADyzvlMAFYIvGAAFACUmMCYaNiYAHStOFgAvk5OGJgAshTUdIysHNy8AkbikrIKSqpaWvqGIiZmhE6u7p7ymAAqXEwSguZcCpKV9VSEFBodtcBOmAYmYHz0XIT6ALzefgFUYKhCJRBAxeLcJIsVIZLI5PKFYplCqVa63aoAbm6u0wMAQhFguwAPPRAQA+YAfL4dIloUmBMlODogDpAA)

Tuy nhiên, nếu `expensivelyProcessAReallyLargeArrayOfObjects` thực sự là một hàm tốn kém, bạn có thể muốn cân nhắc triển khai memoization riêng bên ngoài React, vì:

- React Compiler chỉ memoize component và hook React, không phải mọi hàm
- Memoization của React Compiler không được chia sẻ giữa nhiều component hoặc hook

Vì vậy nếu `expensivelyProcessAReallyLargeArrayOfObjects` được sử dụng trong nhiều component khác nhau, ngay cả khi cùng items được truyền xuống, phép tính tốn kém đó sẽ được chạy lại nhiều lần. Chúng tôi khuyên bạn nên [profiling](reference/react/useMemo#how-to-tell-if-a-calculation-is-expensive) trước để xem nó có thực sự tốn kém không trước khi làm code phức tạp hơn.
</DeepDive>

## Tôi có nên thử compiler không? {/*should-i-try-out-the-compiler*/}

Chúng tôi khuyến khích mọi người bắt đầu sử dụng React Compiler. Mặc dù compiler hiện vẫn là phần bổ sung tùy chọn cho React, trong tương lai một số tính năng có thể yêu cầu compiler để hoạt động đầy đủ.

### Có an toàn để sử dụng không? {/*is-it-safe-to-use*/}

React Compiler hiện đã ổn định và đã được kiểm tra rộng rãi trong production. Mặc dù nó đã được sử dụng trong production tại các công ty như Meta, việc triển khai compiler cho production cho ứng dụng của bạn sẽ phụ thuộc vào sức khỏe của codebase và mức độ bạn đã tuân theo [Quy tắc của React](/reference/rules).

## Công cụ build nào được hỗ trợ? {/*what-build-tools-are-supported*/}

React Compiler có thể được cài đặt qua [nhiều công cụ build](/learn/react-compiler/installation) như Babel, Vite, Metro, và Rsbuild.

React Compiler chủ yếu là một Babel plugin wrapper nhẹ xung quanh compiler lõi, được thiết kế để tách biệt khỏi Babel. Mặc dù phiên bản ổn định ban đầu của compiler sẽ vẫn chủ yếu là Babel plugin, chúng tôi đang làm việc với các team swc và [oxc](https://github.com/oxc-project/oxc/issues/10048) để xây dựng hỗ trợ hạng nhất cho React Compiler để bạn không phải thêm Babel trở lại pipeline build trong tương lai.

Người dùng Next.js có thể bật React Compiler được gọi qua swc bằng cách sử dụng [v15.3.1](https://github.com/vercel/next.js/releases/tag/v15.3.1) trở lên.

## Tôi nên làm gì với useMemo, useCallback, và React.memo? {/*what-should-i-do-about-usememo-usecallback-and-reactmemo*/}

Theo mặc định, React Compiler sẽ memoize code của bạn dựa trên phân tích và heuristic. Trong hầu hết trường hợp, memoization này sẽ chính xác, hoặc hơn, so với những gì bạn có thể viết.

Tuy nhiên, trong một số trường hợp, developer có thể cần kiểm soát nhiều hơn đối với memoization. Hook `useMemo` và `useCallback` có thể tiếp tục được sử dụng với React Compiler như một cửa thoát để cung cấp quyền kiểm soát giá trị nào được memoize. Một trường hợp sử dụng phổ biến là nếu giá trị được memoize được sử dụng làm dependency của effect, để đảm bảo effect không chạy lại nhiều lần ngay cả khi dependency không thay đổi có ý nghĩa.

Đối với code mới, chúng tôi khuyên bạn nên dựa vào compiler cho memoization và sử dụng `useMemo`/`useCallback` khi cần để đạt được kiểm soát chính xác.

Đối với code hiện có, chúng tôi khuyên bạn nên giữ memoization hiện có (xóa nó có thể thay đổi output biên dịch) hoặc kiểm tra cẩn thận trước khi xóa memoization.

## Thử React Compiler {/*try-react-compiler*/}

Phần này sẽ giúp bạn bắt đầu với React Compiler và hiểu cách sử dụng nó hiệu quả trong dự án.

* **[Cài đặt](/learn/react-compiler/installation)** - Cài đặt React Compiler và cấu hình cho công cụ build của bạn
* **[Tương thích phiên bản React](/reference/react-compiler/target)** - Hỗ trợ React 17, 18, và 19
* **[Cấu hình](/reference/react-compiler/configuration)** - Tùy chỉnh compiler cho nhu cầu cụ thể của bạn
* **[Áp dụng dần dần](/learn/react-compiler/incremental-adoption)** - Chiến lược triển khai dần dần compiler trong codebase hiện có
* **[Gỡ lỗi và xử lý sự cố](/learn/react-compiler/debugging)** - Xác định và sửa lỗi khi sử dụng compiler
* **[Biên dịch thư viện](/reference/react-compiler/compiling-libraries)** - Thực hành tốt nhất cho việc phân phối code đã biên dịch
* **[Tham chiếu API](/reference/react-compiler/configuration)** - Tài liệu chi tiết của tất cả tùy chọn cấu hình

## Tài nguyên bổ sung {/*additional-resources*/}

Ngoài tài liệu này, chúng tôi khuyên bạn nên xem [React Compiler Working Group](https://github.com/reactwg/react-compiler) để biết thêm thông tin và thảo luận về compiler.

