---
title: JavaScript trong JSX với Dấu Ngoặc Nhọn
---

<Intro>

JSX cho phép bạn viết markup dạng HTML bên trong file JavaScript, giữ logic render và nội dung ở cùng một nơi. Đôi khi bạn sẽ muốn thêm một chút logic JavaScript hoặc tham chiếu một thuộc tính động bên trong markup đó. Trong tình huống này, bạn có thể sử dụng dấu ngoặc nhọn trong JSX để mở cửa sổ sang JavaScript.

</Intro>

<YouWillLearn>

* Cách truyền chuỗi với dấu ngoặc kép
* Cách tham chiếu một biến JavaScript bên trong JSX với dấu ngoặc nhọn
* Cách gọi một hàm JavaScript bên trong JSX với dấu ngoặc nhọn
* Cách sử dụng một đối tượng JavaScript bên trong JSX với dấu ngoặc nhọn

</YouWillLearn>

## Truyền chuỗi với dấu ngoặc kép {/*passing-strings-with-quotes*/}

Khi bạn muốn truyền một thuộc tính chuỗi cho JSX, bạn đặt nó trong dấu ngoặc đơn hoặc kép:

<Sandpack>

```js
export default function Avatar() {
  return (
    <img
      className="avatar"
      src="https://react.dev/images/docs/scientists/7vQD0fPs.jpg"
      alt="Gregorio Y. Zara"
    />
  );
}
```

```css
.avatar { border-radius: 50%; height: 90px; }
```

</Sandpack>

Ở đây, `"https://react.dev/images/docs/scientists/7vQD0fPs.jpg"` và `"Gregorio Y. Zara"` đang được truyền như chuỗi.

Nhưng điều gì nếu bạn muốn chỉ định `src` hoặc văn bản `alt` một cách động? Bạn có thể **sử dụng một giá trị từ JavaScript bằng cách thay thế `"` và `"` với `{` và `}`**:

<Sandpack>

```js
export default function Avatar() {
  const avatar = 'https://react.dev/images/docs/scientists/7vQD0fPs.jpg';
  const description = 'Gregorio Y. Zara';
  return (
    <img
      className="avatar"
      src={avatar}
      alt={description}
    />
  );
}
```

```css
.avatar { border-radius: 50%; height: 90px; }
```

</Sandpack>

Lưu ý sự khác biệt giữa `className="avatar"`, chỉ định tên CSS class `"avatar"` làm cho hình ảnh tròn, và `src={avatar}` đọc giá trị của biến JavaScript có tên `avatar`. Đó là vì dấu ngoặc nhọn cho phép bạn làm việc với JavaScript ngay trong markup của bạn!

## Sử dụng dấu ngoặc nhọn: Cửa sổ vào thế giới JavaScript {/*using-curly-braces-a-window-into-the-javascript-world*/}

JSX là một cách đặc biệt để viết JavaScript. Điều đó có nghĩa là có thể sử dụng JavaScript bên trong nó -- với dấu ngoặc nhọn `{ }`. Ví dụ dưới đây đầu tiên khai báo tên cho nhà khoa học, `name`, sau đó nhúng nó với dấu ngoặc nhọn bên trong `<h1>`:

<Sandpack>

```js
export default function TodoList() {
  const name = 'Gregorio Y. Zara';
  return (
    <h1>{name}'s To Do List</h1>
  );
}
```

</Sandpack>

Hãy thử thay đổi giá trị `name` từ `'Gregorio Y. Zara'` thành `'Hedy Lamarr'`. Xem tiêu đề danh sách thay đổi như thế nào?

Bất kỳ biểu thức JavaScript nào sẽ hoạt động giữa các dấu ngoặc nhọn, bao gồm các lời gọi hàm như `formatDate()`:

<Sandpack>

```js
const today = new Date();

function formatDate(date) {
  return new Intl.DateTimeFormat(
    'en-US',
    { weekday: 'long' }
  ).format(date);
}

export default function TodoList() {
  return (
    <h1>To Do List for {formatDate(today)}</h1>
  );
}
```

</Sandpack>

### Nơi sử dụng dấu ngoặc nhọn {/*where-to-use-curly-braces*/}

Bạn chỉ có thể sử dụng dấu ngoặc nhọn theo hai cách bên trong JSX:

1. **Như văn bản** trực tiếp bên trong thẻ JSX: `<h1>{name}'s To Do List</h1>` hoạt động, nhưng `<{tag}>Gregorio Y. Zara's To Do List</{tag}>` sẽ không.
2. **Như thuộc tính** ngay sau dấu `=`: `src={avatar}` sẽ đọc biến `avatar`, nhưng `src="{avatar}"` sẽ truyền chuỗi `"{avatar}"`.

## Sử dụng "dấu ngoặc nhọn kép": CSS và các đối tượng khác trong JSX {/*using-double-curlies-css-and-other-objects-in-jsx*/}

Ngoài chuỗi, số, và các biểu thức JavaScript khác, bạn thậm chí có thể truyền đối tượng trong JSX. Các đối tượng cũng được biểu thị bằng dấu ngoặc nhọn, như `{ name: "Hedy Lamarr", inventions: 5 }`. Vì vậy, để truyền một đối tượng JS trong JSX, bạn phải bọc đối tượng trong một cặp dấu ngoặc nhọn khác: `person={{ name: "Hedy Lamarr", inventions: 5 }}`.

Bạn có thể thấy điều này với inline CSS style trong JSX. React không yêu cầu bạn sử dụng inline style (CSS class hoạt động tốt cho hầu hết các trường hợp). Nhưng khi bạn cần inline style, bạn truyền một đối tượng cho thuộc tính `style`:

<Sandpack>

```js
export default function TodoList() {
  return (
    <ul style={{
      backgroundColor: 'black',
      color: 'pink'
    }}>
      <li>Improve the videophone</li>
      <li>Prepare aeronautics lectures</li>
      <li>Work on the alcohol-fuelled engine</li>
    </ul>
  );
}
```

```css
body { padding: 0; margin: 0 }
ul { padding: 20px 20px 20px 40px; margin: 0; }
```

</Sandpack>

Hãy thử thay đổi giá trị của `backgroundColor` và `color`.

Bạn thực sự có thể thấy đối tượng JavaScript bên trong dấu ngoặc nhọn khi bạn viết nó như thế này:

```js {2-5}
<ul style={
  {
    backgroundColor: 'black',
    color: 'pink'
  }
}>
```

Lần tới khi bạn thấy `{{` và `}}` trong JSX, hãy biết rằng đó không phải gì hơn là một đối tượng bên trong dấu ngoặc nhọn JSX!

<Pitfall>

Các thuộc tính `style` inline được viết theo camelCase. Ví dụ, HTML `<ul style="background-color: black">` sẽ được viết là `<ul style={{ backgroundColor: 'black' }}>` trong component của bạn.

</Pitfall>

## Thêm vui với đối tượng JavaScript và dấu ngoặc nhọn {/*more-fun-with-javascript-objects-and-curly-braces*/}

Bạn có thể di chuyển nhiều biểu thức vào một đối tượng, và tham chiếu chúng trong JSX bên trong dấu ngoặc nhọn:

<Sandpack>

```js
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src="https://react.dev/images/docs/scientists/7vQD0fPs.jpg"
        alt="Gregorio Y. Zara"
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

```css
body { padding: 0; margin: 0 }
body > div > div { padding: 20px; }
.avatar { border-radius: 50%; height: 90px; }
```

</Sandpack>

Trong ví dụ này, đối tượng JavaScript `person` chứa chuỗi `name` và đối tượng `theme`:

```js
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};
```

Component có thể sử dụng các giá trị này từ `person` như sau:

```js
<div style={person.theme}>
  <h1>{person.name}'s Todos</h1>
```

JSX rất tối giản như một ngôn ngữ template vì nó cho phép bạn tổ chức dữ liệu và logic bằng JavaScript.

<Recap>

Bây giờ bạn biết gần như mọi thứ về JSX:

* Các thuộc tính JSX trong dấu ngoặc kép được truyền như chuỗi.
* Dấu ngoặc nhọn cho phép bạn đưa logic JavaScript và biến vào markup của bạn.
* Chúng hoạt động bên trong nội dung thẻ JSX hoặc ngay sau `=` trong thuộc tính.
* `{{` và `}}` không phải là cú pháp đặc biệt: đó là một đối tượng JavaScript được đặt bên trong dấu ngoặc nhọn JSX.

</Recap>

<Challenges>

#### Sửa lỗi {/*fix-the-mistake*/}

Code này bị lỗi với thông báo `Objects are not valid as a React child`:

<Sandpack>

```js
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person}'s Todos</h1>
      <img
        className="avatar"
        src="https://react.dev/images/docs/scientists/7vQD0fPs.jpg"
        alt="Gregorio Y. Zara"
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

```css
body { padding: 0; margin: 0 }
body > div > div { padding: 20px; }
.avatar { border-radius: 50%; height: 90px; }
```

</Sandpack>

Bạn có thể tìm ra vấn đề không?

<Hint>Hãy tìm những gì bên trong dấu ngoặc nhọn. Chúng ta có đặt đúng thứ vào đó không?</Hint>

<Solution>

Điều này xảy ra vì ví dụ này render *chính đối tượng* vào markup thay vì chuỗi: `<h1>{person}'s Todos</h1>` đang cố gắng render toàn bộ đối tượng `person`! Bao gồm các đối tượng thô như nội dung văn bản gây ra lỗi vì React không biết bạn muốn hiển thị chúng như thế nào.

Để sửa, hãy thay thế `<h1>{person}'s Todos</h1>` bằng `<h1>{person.name}'s Todos</h1>`:

<Sandpack>

```js
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src="https://react.dev/images/docs/scientists/7vQD0fPs.jpg"
        alt="Gregorio Y. Zara"
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

```css
body { padding: 0; margin: 0 }
body > div > div { padding: 20px; }
.avatar { border-radius: 50%; height: 90px; }
```

</Sandpack>

</Solution>

#### Trích xuất thông tin vào một đối tượng {/*extract-information-into-an-object*/}

Trích xuất URL hình ảnh vào đối tượng `person`.

<Sandpack>

```js
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src="https://react.dev/images/docs/scientists/7vQD0fPs.jpg"
        alt="Gregorio Y. Zara"
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

```css
body { padding: 0; margin: 0 }
body > div > div { padding: 20px; }
.avatar { border-radius: 50%; height: 90px; }
```

</Sandpack>

<Solution>

Di chuyển URL hình ảnh vào thuộc tính có tên `person.imageUrl` và đọc nó từ thẻ `<img>` bằng dấu ngoặc nhọn:

<Sandpack>

```js
const person = {
  name: 'Gregorio Y. Zara',
  imageUrl: "https://react.dev/images/docs/scientists/7vQD0fPs.jpg",
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src={person.imageUrl}
        alt="Gregorio Y. Zara"
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

```css
body { padding: 0; margin: 0 }
body > div > div { padding: 20px; }
.avatar { border-radius: 50%; height: 90px; }
```

</Sandpack>

</Solution>

#### Viết một biểu thức bên trong dấu ngoặc nhọn JSX {/*write-an-expression-inside-jsx-curly-braces*/}

Trong đối tượng bên dưới, URL hình ảnh đầy đủ được chia thành bốn phần: base URL, `imageId`, `imageSize`, và phần mở rộng file.

Chúng ta muốn URL hình ảnh kết hợp các thuộc tính này lại với nhau: base URL (luôn là `'https://react.dev/images/docs/scientists/'`), `imageId` (`'7vQD0fP'`), `imageSize` (`'s'`), và phần mở rộng file (luôn là `'.jpg'`). Tuy nhiên, có điều gì đó sai với cách thẻ `<img>` chỉ định `src`.

Bạn có thể sửa nó không?

<Sandpack>

```js

const baseUrl = 'https://react.dev/images/docs/scientists/';
const person = {
  name: 'Gregorio Y. Zara',
  imageId: '7vQD0fP',
  imageSize: 's',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src="{baseUrl}{person.imageId}{person.imageSize}.jpg"
        alt={person.name}
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

```css
body { padding: 0; margin: 0 }
body > div > div { padding: 20px; }
.avatar { border-radius: 50%; }
```

</Sandpack>

Để kiểm tra xem bản sửa lỗi của bạn có hoạt động không, hãy thử thay đổi giá trị của `imageSize` thành `'b'`. Hình ảnh sẽ thay đổi kích thước sau khi bạn chỉnh sửa.

<Solution>

Bạn có thể viết nó như `src={baseUrl + person.imageId + person.imageSize + '.jpg'}`.

1. `{` mở biểu thức JavaScript
2. `baseUrl + person.imageId + person.imageSize + '.jpg'` tạo ra chuỗi URL đúng
3. `}` đóng biểu thức JavaScript

<Sandpack>

```js
const baseUrl = 'https://react.dev/images/docs/scientists/';
const person = {
  name: 'Gregorio Y. Zara',
  imageId: '7vQD0fP',
  imageSize: 's',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src={baseUrl + person.imageId + person.imageSize + '.jpg'}
        alt={person.name}
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

```css
body { padding: 0; margin: 0 }
body > div > div { padding: 20px; }
.avatar { border-radius: 50%; }
```

</Sandpack>

Bạn cũng có thể di chuyển biểu thức này vào một hàm riêng biệt như `getImageUrl` bên dưới:

<Sandpack>

```js src/App.js
import { getImageUrl } from './utils.js'

const person = {
  name: 'Gregorio Y. Zara',
  imageId: '7vQD0fP',
  imageSize: 's',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
  return (
    <div style={person.theme}>
      <h1>{person.name}'s Todos</h1>
      <img
        className="avatar"
        src={getImageUrl(person)}
        alt={person.name}
      />
      <ul>
        <li>Improve the videophone</li>
        <li>Prepare aeronautics lectures</li>
        <li>Work on the alcohol-fuelled engine</li>
      </ul>
    </div>
  );
}
```

```js src/utils.js
export function getImageUrl(person) {
  return (
    'https://react.dev/images/docs/scientists/' +
    person.imageId +
    person.imageSize +
    '.jpg'
  );
}
```

```css
body { padding: 0; margin: 0 }
body > div > div { padding: 20px; }
.avatar { border-radius: 50%; }
```

</Sandpack>

Các biến và hàm có thể giúp bạn giữ markup đơn giản!

</Solution>

</Challenges>
