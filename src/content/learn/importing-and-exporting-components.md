---
title: Import và Export các Component
---

<Intro>

Phép màu của các component nằm ở khả năng tái sử dụng: bạn có thể tạo các component được tạo thành từ các component khác. Nhưng khi bạn lồng ngày càng nhiều component, thường nên bắt đầu chia chúng thành các file khác nhau. Điều này cho phép bạn giữ các file dễ đọc và tái sử dụng các component ở nhiều nơi hơn.

</Intro>

<YouWillLearn>

* File component root là gì
* Cách import và export một component
* Khi nào nên sử dụng default và named imports và exports
* Cách import và export nhiều component từ một file
* Cách chia các component thành nhiều file

</YouWillLearn>

## File component root {/*the-root-component-file*/}

Trong [Component Đầu Tiên Của Bạn](/learn/your-first-component), bạn đã tạo component `Profile` và component `Gallery` render nó:

<Sandpack>

```js
function Profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/MK3eW3As.jpg"
      alt="Katherine Johnson"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

Hiện tại chúng đang nằm trong **file component root,** được đặt tên là `App.js` trong ví dụ này. Tùy thuộc vào thiết lập của bạn, component root của bạn có thể nằm trong file khác. Nếu bạn sử dụng framework với file-based routing, chẳng hạn như Next.js, component root của bạn sẽ khác nhau cho mỗi trang.

## Export và import một component {/*exporting-and-importing-a-component*/}

Điều gì sẽ xảy ra nếu bạn muốn thay đổi màn hình đầu trong tương lai và đặt một danh sách sách khoa học ở đó? Hoặc đặt tất cả các hồ sơ ở nơi khác? Việc di chuyển `Gallery` và `Profile` ra khỏi file component root là hợp lý. Điều này sẽ làm cho chúng có tính module hơn và có thể tái sử dụng trong các file khác. Bạn có thể di chuyển một component trong ba bước:

1. **Tạo** một file JS mới để đặt các component vào.
2. **Export** function component của bạn từ file đó (sử dụng [default](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/export#using_the_default_export) hoặc [named](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/export#using_named_exports) exports).
3. **Import** nó trong file nơi bạn sẽ sử dụng component (sử dụng kỹ thuật tương ứng để import [default](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/import#importing_defaults) hoặc [named](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/import#import_a_single_export_from_a_module) exports).

Ở đây cả `Profile` và `Gallery` đã được di chuyển ra khỏi `App.js` vào một file mới có tên `Gallery.js`. Bây giờ bạn có thể thay đổi `App.js` để import `Gallery` từ `Gallery.js`:

<Sandpack>

```js src/App.js
import Gallery from './Gallery.js';

export default function App() {
  return (
    <Gallery />
  );
}
```

```js src/Gallery.js
function Profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

Lưu ý cách ví dụ này hiện được chia thành hai file component:

1. `Gallery.js`:
     - Định nghĩa component `Profile` chỉ được sử dụng trong cùng file và không được export.
     - Exports component `Gallery` như một **default export.**
2. `App.js`:
     - Imports `Gallery` như một **default import** từ `Gallery.js`.
     - Exports component `App` root như một **default export.**


<Note>

Bạn có thể gặp các file bỏ qua phần mở rộng `.js` như sau:

```js
import Gallery from './Gallery';
```

Cả `'./Gallery.js'` và `'./Gallery'` đều sẽ hoạt động với React, mặc dù cái trước gần hơn với cách [native ES Modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) hoạt động.

</Note>

<DeepDive>

#### Default so với named exports {/*default-vs-named-exports*/}

Có hai cách chính để export giá trị với JavaScript: default exports và named exports. Cho đến nay, các ví dụ của chúng ta chỉ sử dụng default exports. Nhưng bạn có thể sử dụng một hoặc cả hai trong cùng một file. **Một file có thể có không quá một _default_ export, nhưng có thể có bao nhiêu _named_ exports tùy ý.**

![Default và named exports](/images/docs/illustrations/i_import-export.svg)

Cách bạn export component của mình quyết định cách bạn phải import nó. Bạn sẽ gặp lỗi nếu cố gắng import default export theo cách tương tự như named export! Bảng này có thể giúp bạn theo dõi:

| Cú pháp          | Câu lệnh export                            | Câu lệnh import                           |
| -----------      | -----------                                | -----------                               |
| Default  | `export default function Button() {}` | `import Button from './Button.js';`     |
| Named    | `export function Button() {}`         | `import { Button } from './Button.js';` |

Khi bạn viết _default_ import, bạn có thể đặt bất kỳ tên nào sau `import`. Ví dụ, bạn có thể viết `import Banana from './Button.js'` thay thế và nó vẫn cung cấp cho bạn cùng default export. Ngược lại, với named imports, tên phải khớp ở cả hai phía. Đó là lý do tại sao chúng được gọi là _named_ imports!

**Mọi người thường sử dụng default exports nếu file chỉ export một component, và sử dụng named exports nếu nó export nhiều component và giá trị.** Bất kể bạn thích phong cách code nào, hãy luôn đặt tên có ý nghĩa cho các hàm component và các file chứa chúng. Các component không có tên, như `export default () => {}`, được khuyến cáo không nên dùng vì chúng làm cho việc debug khó hơn.

</DeepDive>

## Export và import nhiều component từ cùng một file {/*exporting-and-importing-multiple-components-from-the-same-file*/}

Điều gì nếu bạn muốn hiển thị chỉ một `Profile` thay vì một gallery? Bạn cũng có thể export component `Profile`. Nhưng `Gallery.js` đã có một *default* export, và bạn không thể có _hai_ default exports. Bạn có thể tạo một file mới với default export, hoặc bạn có thể thêm *named* export cho `Profile`. **Một file chỉ có thể có một default export, nhưng có thể có nhiều named exports!**

<Note>

Để giảm bớt sự nhầm lẫn tiềm ẩn giữa default và named exports, một số team chọn chỉ theo một phong cách (default hoặc named), hoặc tránh pha trộn chúng trong một file duy nhất. Hãy làm những gì phù hợp nhất với bạn!

</Note>

Đầu tiên, **export** `Profile` từ `Gallery.js` sử dụng named export (không có từ khóa `default`):

```js
export function Profile() {
  // ...
}
```

Sau đó, **import** `Profile` từ `Gallery.js` vào `App.js` sử dụng named import (với dấu ngoặc nhọn):

```js
import { Profile } from './Gallery.js';
```

Cuối cùng, **render** `<Profile />` từ component `App`:

```js
export default function App() {
  return <Profile />;
}
```

Bây giờ `Gallery.js` chứa hai exports: default `Gallery` export, và named `Profile` export. `App.js` imports cả hai. Hãy thử chỉnh sửa `<Profile />` thành `<Gallery />` và ngược lại trong ví dụ này:

<Sandpack>

```js src/App.js
import Gallery from './Gallery.js';
import { Profile } from './Gallery.js';

export default function App() {
  return (
    <Profile />
  );
}
```

```js src/Gallery.js
export function Profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

Bây giờ bạn đang sử dụng kết hợp default và named exports:

* `Gallery.js`:
  - Exports component `Profile` như một **named export có tên `Profile`.**
  - Exports component `Gallery` như một **default export.**
* `App.js`:
  - Imports `Profile` như một **named import có tên `Profile`** từ `Gallery.js`.
  - Imports `Gallery` như một **default import** từ `Gallery.js`.
  - Exports component `App` root như một **default export.**

<Recap>

Trên trang này bạn đã học:

* File component root là gì
* Cách import và export một component
* Khi nào và cách sử dụng default và named imports và exports
* Cách export nhiều component từ cùng một file

</Recap>



<Challenges>

#### Chia các component thêm {/*split-the-components-further*/}

Hiện tại, `Gallery.js` export cả `Profile` và `Gallery`, điều này hơi khó hiểu.

Di chuyển component `Profile` vào file `Profile.js` riêng của nó, và sau đó thay đổi component `App` để render cả `<Profile />` và `<Gallery />` lần lượt.

Bạn có thể sử dụng default hoặc named export cho `Profile`, nhưng hãy đảm bảo rằng bạn sử dụng cú pháp import tương ứng trong cả `App.js` và `Gallery.js`! Bạn có thể tham khảo bảng từ deep dive ở trên:

| Cú pháp          | Câu lệnh export                            | Câu lệnh import                           |
| -----------      | -----------                                | -----------                               |
| Default  | `export default function Button() {}` | `import Button from './Button.js';`     |
| Named    | `export function Button() {}`         | `import { Button } from './Button.js';` |

<Hint>

Đừng quên import các component ở nơi chúng được gọi. `Gallery` có dùng `Profile` không?

</Hint>

<Sandpack>

```js src/App.js
import Gallery from './Gallery.js';
import { Profile } from './Gallery.js';

export default function App() {
  return (
    <div>
      <Profile />
    </div>
  );
}
```

```js src/Gallery.js active
// Di chuyển tôi vào Profile.js!
export function Profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```js src/Profile.js
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

Sau khi bạn làm nó hoạt động với một loại export, hãy làm nó hoạt động với loại còn lại.

<Solution>

Đây là giải pháp với named exports:

<Sandpack>

```js src/App.js
import Gallery from './Gallery.js';
import { Profile } from './Profile.js';

export default function App() {
  return (
    <div>
      <Profile />
      <Gallery />
    </div>
  );
}
```

```js src/Gallery.js
import { Profile } from './Profile.js';

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```js src/Profile.js
export function Profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

Đây là giải pháp với default exports:

<Sandpack>

```js src/App.js
import Gallery from './Gallery.js';
import Profile from './Profile.js';

export default function App() {
  return (
    <div>
      <Profile />
      <Gallery />
    </div>
  );
}
```

```js src/Gallery.js
import Profile from './Profile.js';

export default function Gallery() {
  return (
    <section>
      <h1>Amazing scientists</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```

```js src/Profile.js
export default function Profile() {
  return (
    <img
      src="https://react.dev/images/docs/scientists/QIrZWGIs.jpg"
      alt="Alan L. Hart"
    />
  );
}
```

```css
img { margin: 0 10px 10px 0; height: 90px; }
```

</Sandpack>

</Solution>

</Challenges>
