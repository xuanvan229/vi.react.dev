---
title: Mô tả giao diện người dùng
---

<Intro>

React là một thư viện JavaScript để render giao diện người dùng (UI). UI được xây dựng từ các đơn vị nhỏ như nút bấm, văn bản và hình ảnh. React cho phép bạn kết hợp chúng thành các *component* có thể tái sử dụng và lồng vào nhau. Từ các website đến ứng dụng điện thoại, mọi thứ trên màn hình đều có thể được chia nhỏ thành các component. Trong chương này, bạn sẽ học cách tạo, tùy chỉnh và hiển thị có điều kiện các React component.

</Intro>

<YouWillLearn isChapter={true}>

* [Cách viết React component đầu tiên của bạn](/learn/your-first-component)
* [Khi nào và cách tạo các file đa component](/learn/importing-and-exporting-components)
* [Cách thêm markup vào JavaScript với JSX](/learn/writing-markup-with-jsx)
* [Cách sử dụng dấu ngoặc nhọn với JSX để truy cập chức năng JavaScript từ các component](/learn/javascript-in-jsx-with-curly-braces)
* [Cách cấu hình các component với props](/learn/passing-props-to-a-component)
* [Cách render component có điều kiện](/learn/conditional-rendering)
* [Cách render nhiều component cùng một lúc](/learn/rendering-lists)
* [Cách tránh các lỗi khó hiểu bằng cách giữ các component thuần túy](/learn/keeping-components-pure)
* [Tại sao việc hiểu UI của bạn như các cây lại hữu ích](/learn/understanding-your-ui-as-a-tree)

</YouWillLearn>

## Component đầu tiên của bạn {/*your-first-component*/}

Các ứng dụng React được xây dựng từ các phần UI độc lập gọi là *component*. Một React component là một hàm JavaScript mà bạn có thể thêm markup vào. Các component có thể nhỏ như một nút bấm, hoặc lớn như cả một trang. Đây là component `Gallery` render ba component `Profile`:

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

<LearnMore path="/learn/your-first-component">

Đọc **[Component Đầu Tiên Của Bạn](/learn/your-first-component)** để học cách khai báo và sử dụng các React component.

</LearnMore>

## Import và export các component {/*importing-and-exporting-components*/}

Bạn có thể khai báo nhiều component trong một file, nhưng các file lớn có thể khó điều hướng. Để giải quyết vấn đề này, bạn có thể *export* một component vào file riêng của nó, và sau đó *import* component đó từ file khác:


<Sandpack>

```js src/App.js hidden
import Gallery from './Gallery.js';

export default function App() {
  return (
    <Gallery />
  );
}
```

```js src/Gallery.js active
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
img { margin: 0 10px 10px 0; }
```

</Sandpack>

<LearnMore path="/learn/importing-and-exporting-components">

Đọc **[Import và Export các Component](/learn/importing-and-exporting-components)** để học cách chia các component vào file riêng của chúng.

</LearnMore>

## Viết markup với JSX {/*writing-markup-with-jsx*/}

Mỗi React component là một hàm JavaScript có thể chứa một số markup mà React render vào trình duyệt. Các React component sử dụng một phần mở rộng cú pháp gọi là JSX để biểu diễn markup đó. JSX trông rất giống HTML, nhưng nó nghiêm ngặt hơn một chút và có thể hiển thị thông tin động.

Nếu chúng ta dán HTML markup hiện có vào một React component, nó sẽ không luôn hoạt động:

<Sandpack>

```js
export default function TodoList() {
  return (
    // This doesn't quite work!
    <h1>Hedy Lamarr's Todos</h1>
    <img
      src="https://react.dev/images/docs/scientists/yXOvdOSs.jpg"
      alt="Hedy Lamarr"
      class="photo"
    >
    <ul>
      <li>Invent new traffic lights
      <li>Rehearse a movie scene
      <li>Improve spectrum technology
    </ul>
  );
}
```

```css
img { height: 90px; }
```

</Sandpack>

Nếu bạn có HTML như thế này, bạn có thể sửa nó bằng cách sử dụng [công cụ chuyển đổi](https://transform.tools/html-to-jsx):

<Sandpack>

```js
export default function TodoList() {
  return (
    <>
      <h1>Hedy Lamarr's Todos</h1>
      <img
        src="https://react.dev/images/docs/scientists/yXOvdOSs.jpg"
        alt="Hedy Lamarr"
        className="photo"
      />
      <ul>
        <li>Invent new traffic lights</li>
        <li>Rehearse a movie scene</li>
        <li>Improve spectrum technology</li>
      </ul>
    </>
  );
}
```

```css
img { height: 90px; }
```

</Sandpack>

<LearnMore path="/learn/writing-markup-with-jsx">

Đọc **[Viết Markup với JSX](/learn/writing-markup-with-jsx)** để học cách viết JSX hợp lệ.

</LearnMore>

## JavaScript trong JSX với dấu ngoặc nhọn {/*javascript-in-jsx-with-curly-braces*/}

JSX cho phép bạn viết markup dạng HTML bên trong file JavaScript, giữ logic render và nội dung ở cùng một nơi. Đôi khi bạn sẽ muốn thêm một chút logic JavaScript hoặc tham chiếu một thuộc tính động bên trong markup đó. Trong tình huống này, bạn có thể sử dụng dấu ngoặc nhọn trong JSX để "mở cửa sổ" sang JavaScript:

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

<LearnMore path="/learn/javascript-in-jsx-with-curly-braces">

Đọc **[JavaScript trong JSX với Dấu Ngoặc Nhọn](/learn/javascript-in-jsx-with-curly-braces)** để học cách truy cập dữ liệu JavaScript từ JSX.

</LearnMore>

## Truyền props cho một component {/*passing-props-to-a-component*/}

Các React component sử dụng *props* để giao tiếp với nhau. Mỗi component cha có thể truyền một số thông tin cho các component con bằng cách cung cấp cho chúng props. Props có thể nhắc bạn đến các thuộc tính HTML, nhưng bạn có thể truyền bất kỳ giá trị JavaScript nào qua chúng, bao gồm đối tượng, mảng, hàm, và thậm chí cả JSX!

<Sandpack>

```js
import { getImageUrl } from './utils.js'

export default function Profile() {
  return (
    <Card>
      <Avatar
        size={100}
        person={{
          name: 'Katsuko Saruhashi',
          imageId: 'YfeOqp2'
        }}
      />
    </Card>
  );
}

function Avatar({ person, size }) {
  return (
    <img
      className="avatar"
      src={getImageUrl(person)}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}

function Card({ children }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}

```

```js src/utils.js
export function getImageUrl(person, size = 's') {
  return (
    'https://react.dev/images/docs/scientists/' +
    person.imageId +
    size +
    '.jpg'
  );
}
```

```css
.card {
  width: fit-content;
  margin: 5px;
  padding: 5px;
  font-size: 20px;
  text-align: center;
  border: 1px solid #aaa;
  border-radius: 20px;
  background: #fff;
}
.avatar {
  margin: 20px;
  border-radius: 50%;
}
```

</Sandpack>

<LearnMore path="/learn/passing-props-to-a-component">

Đọc **[Truyền Props cho một Component](/learn/passing-props-to-a-component)** để học cách truyền và đọc props.

</LearnMore>

## Render có điều kiện {/*conditional-rendering*/}

Các component của bạn thường cần hiển thị những thứ khác nhau tùy thuộc vào các điều kiện khác nhau. Trong React, bạn có thể render JSX có điều kiện bằng cách sử dụng cú pháp JavaScript như câu lệnh `if`, `&&`, và toán tử `? :`.

Trong ví dụ này, toán tử `&&` của JavaScript được sử dụng để render có điều kiện một dấu kiểm:

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {name} {isPacked && '✅'}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item
          isPacked={true}
          name="Space suit"
        />
        <Item
          isPacked={true}
          name="Helmet with a golden leaf"
        />
        <Item
          isPacked={false}
          name="Photo of Tam"
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

<LearnMore path="/learn/conditional-rendering">

Đọc **[Render Có Điều Kiện](/learn/conditional-rendering)** để học các cách khác nhau để render nội dung có điều kiện.

</LearnMore>

## Render danh sách {/*rendering-lists*/}

Bạn thường sẽ muốn hiển thị nhiều component tương tự từ một tập hợp dữ liệu. Bạn có thể sử dụng `filter()` và `map()` của JavaScript với React để lọc và chuyển đổi mảng dữ liệu thành mảng các component.

Đối với mỗi phần tử mảng, bạn sẽ cần chỉ định một `key`. Thường bạn sẽ muốn sử dụng ID từ cơ sở dữ liệu làm `key`. Key cho phép React theo dõi vị trí của mỗi phần tử trong danh sách ngay cả khi danh sách thay đổi.

<Sandpack>

```js src/App.js
import { people } from './data.js';
import { getImageUrl } from './utils.js';

export default function List() {
  const listItems = people.map(person =>
    <li key={person.id}>
      <img
        src={getImageUrl(person)}
        alt={person.name}
      />
      <p>
        <b>{person.name}:</b>
        {' ' + person.profession + ' '}
        known for {person.accomplishment}
      </p>
    </li>
  );
  return (
    <article>
      <h1>Scientists</h1>
      <ul>{listItems}</ul>
    </article>
  );
}
```

```js src/data.js
export const people = [{
  id: 0,
  name: 'Creola Katherine Johnson',
  profession: 'mathematician',
  accomplishment: 'spaceflight calculations',
  imageId: 'MK3eW3A'
}, {
  id: 1,
  name: 'Mario José Molina-Pasquel Henríquez',
  profession: 'chemist',
  accomplishment: 'discovery of Arctic ozone hole',
  imageId: 'mynHUSa'
}, {
  id: 2,
  name: 'Mohammad Abdus Salam',
  profession: 'physicist',
  accomplishment: 'electromagnetism theory',
  imageId: 'bE7W1ji'
}, {
  id: 3,
  name: 'Percy Lavon Julian',
  profession: 'chemist',
  accomplishment: 'pioneering cortisone drugs, steroids and birth control pills',
  imageId: 'IOjWm71'
}, {
  id: 4,
  name: 'Subrahmanyan Chandrasekhar',
  profession: 'astrophysicist',
  accomplishment: 'white dwarf star mass calculations',
  imageId: 'lrWQx8l'
}];
```

```js src/utils.js
export function getImageUrl(person) {
  return (
    'https://react.dev/images/docs/scientists/' +
    person.imageId +
    's.jpg'
  );
}
```

```css
ul { list-style-type: none; padding: 0px 10px; }
li {
  margin-bottom: 10px;
  display: grid;
  grid-template-columns: 1fr 1fr;
  align-items: center;
}
img { width: 100px; height: 100px; border-radius: 50%; }
h1 { font-size: 22px; }
h2 { font-size: 20px; }
```

</Sandpack>

<LearnMore path="/learn/rendering-lists">

Đọc **[Render Danh Sách](/learn/rendering-lists)** để học cách render một danh sách các component, và cách chọn key.

</LearnMore>

## Giữ các component thuần túy {/*keeping-components-pure*/}

Một số hàm JavaScript là *thuần túy.* Một hàm thuần túy:

* **Chỉ làm việc của mình.** Nó không thay đổi bất kỳ đối tượng hoặc biến nào tồn tại trước khi nó được gọi.
* **Đầu vào giống nhau, đầu ra giống nhau.** Cho cùng đầu vào, một hàm thuần túy luôn nên trả về cùng kết quả.

Bằng cách chỉ viết các component như các hàm thuần túy, bạn có thể tránh toàn bộ một lớp lỗi khó hiểu và hành vi không thể đoán trước khi codebase của bạn phát triển. Đây là ví dụ về một component không thuần túy:

<Sandpack>

```js {expectedErrors: {'react-compiler': [5]}}
let guest = 0;

function Cup() {
  // Bad: changing a preexisting variable!
  guest = guest + 1;
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaSet() {
  return (
    <>
      <Cup />
      <Cup />
      <Cup />
    </>
  );
}
```

</Sandpack>

Bạn có thể làm cho component này thuần túy bằng cách truyền một prop thay vì sửa đổi biến đã tồn tại:

<Sandpack>

```js
function Cup({ guest }) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaSet() {
  return (
    <>
      <Cup guest={1} />
      <Cup guest={2} />
      <Cup guest={3} />
    </>
  );
}
```

</Sandpack>

<LearnMore path="/learn/keeping-components-pure">

Đọc **[Giữ Các Component Thuần Túy](/learn/keeping-components-pure)** để học cách viết các component như các hàm thuần túy, có thể đoán trước.

</LearnMore>

## UI của bạn như một cây {/*your-ui-as-a-tree*/}

React sử dụng cây để mô hình hóa các mối quan hệ giữa các component và module.

Một React render tree là biểu diễn của mối quan hệ cha-con giữa các component.

<Diagram name="generic_render_tree" height={250} width={500} alt="Một đồ thị cây với năm nút, mỗi nút đại diện cho một component. Nút gốc nằm ở trên cùng của đồ thị cây và được gắn nhãn 'Root Component'. Nó có hai mũi tên kéo xuống hai nút được gắn nhãn 'Component A' và 'Component C'. Mỗi mũi tên được gắn nhãn với 'renders'. 'Component A' có một mũi tên 'renders' duy nhất đến nút được gắn nhãn 'Component B'. 'Component C' có một mũi tên 'renders' duy nhất đến nút được gắn nhãn 'Component D'.">

Một ví dụ về React render tree.

</Diagram>

Các component gần đỉnh cây, gần component gốc, được coi là các component cấp cao nhất. Các component không có component con là leaf component. Việc phân loại component này hữu ích để hiểu luồng dữ liệu và hiệu suất render.

Mô hình hóa mối quan hệ giữa các module JavaScript là một cách hữu ích khác để hiểu ứng dụng của bạn. Chúng tôi gọi đó là module dependency tree.

<Diagram name="generic_dependency_tree" height={250} width={500} alt="Một đồ thị cây với năm nút. Mỗi nút đại diện cho một module JavaScript. Nút cao nhất được gắn nhãn 'RootModule.js'. Nó có ba mũi tên kéo đến các nút: 'ModuleA.js', 'ModuleB.js', và 'ModuleC.js'. Mỗi mũi tên được gắn nhãn là 'imports'. Nút 'ModuleC.js' có một mũi tên 'imports' duy nhất trỏ đến nút được gắn nhãn 'ModuleD.js'.">

Một ví dụ về module dependency tree.

</Diagram>

Dependency tree thường được các công cụ build sử dụng để đóng gói tất cả code JavaScript liên quan cho client tải xuống và render. Kích thước bundle lớn làm giảm trải nghiệm người dùng cho các ứng dụng React. Hiểu module dependency tree rất hữu ích để debug các vấn đề như vậy.

<LearnMore path="/learn/understanding-your-ui-as-a-tree">

Đọc **[UI Của Bạn Như Một Cây](/learn/understanding-your-ui-as-a-tree)** để học cách tạo render tree và module dependency tree cho một ứng dụng React và cách chúng là các mô hình tư duy hữu ích để cải thiện trải nghiệm người dùng và hiệu suất.

</LearnMore>


## Tiếp theo là gì? {/*whats-next*/}

Hãy đến [Component Đầu Tiên Của Bạn](/learn/your-first-component) để bắt đầu đọc chương này từng trang một!

Hoặc, nếu bạn đã quen thuộc với những chủ đề này, tại sao không đọc về [Thêm Tương Tác](/learn/adding-interactivity)?
