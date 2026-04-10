---
title: Render danh sách
---

<Intro>

Bạn thường sẽ muốn hiển thị nhiều component tương tự từ một tập hợp dữ liệu. Bạn có thể sử dụng [các phương thức mảng JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array#) để thao tác với một mảng dữ liệu. Trên trang này, bạn sẽ sử dụng [`filter()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) và [`map()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array/map) với React để lọc và chuyển đổi mảng dữ liệu của bạn thành một mảng các component.

</Intro>

<YouWillLearn>

* Cách render các component từ một mảng bằng cách sử dụng `map()` của JavaScript
* Cách chỉ render các component cụ thể bằng cách sử dụng `filter()` của JavaScript
* Khi nào và tại sao sử dụng React key

</YouWillLearn>

## Render dữ liệu từ mảng {/*rendering-data-from-arrays*/}

Giả sử bạn có một danh sách nội dung.

```js
<ul>
  <li>Creola Katherine Johnson: mathematician</li>
  <li>Mario José Molina-Pasquel Henríquez: chemist</li>
  <li>Mohammad Abdus Salam: physicist</li>
  <li>Percy Lavon Julian: chemist</li>
  <li>Subrahmanyan Chandrasekhar: astrophysicist</li>
</ul>
```

Sự khác biệt duy nhất giữa các mục trong danh sách đó là nội dung, dữ liệu của chúng. Bạn thường cần hiển thị nhiều instance của cùng một component bằng cách sử dụng dữ liệu khác nhau khi xây dựng giao diện: từ danh sách bình luận đến thư viện ảnh hồ sơ. Trong các tình huống này, bạn có thể lưu trữ dữ liệu đó trong các object và mảng JavaScript và sử dụng các phương thức như [`map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) và [`filter()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) để render danh sách các component từ chúng.

Đây là một ví dụ ngắn về cách tạo danh sách các mục từ một mảng:

1. **Chuyển** dữ liệu vào một mảng:

```js
const people = [
  'Creola Katherine Johnson: mathematician',
  'Mario José Molina-Pasquel Henríquez: chemist',
  'Mohammad Abdus Salam: physicist',
  'Percy Lavon Julian: chemist',
  'Subrahmanyan Chandrasekhar: astrophysicist'
];
```

2. **Map** các thành viên `people` thành một mảng mới các node JSX, `listItems`:

```js
const listItems = people.map(person => <li>{person}</li>);
```

3. **Trả về** `listItems` từ component của bạn được bọc trong `<ul>`:

```js
return <ul>{listItems}</ul>;
```

Đây là kết quả:

<Sandpack>

```js
const people = [
  'Creola Katherine Johnson: mathematician',
  'Mario José Molina-Pasquel Henríquez: chemist',
  'Mohammad Abdus Salam: physicist',
  'Percy Lavon Julian: chemist',
  'Subrahmanyan Chandrasekhar: astrophysicist'
];

export default function List() {
  const listItems = people.map(person =>
    <li>{person}</li>
  );
  return <ul>{listItems}</ul>;
}
```

```css
li { margin-bottom: 10px; }
```

</Sandpack>

Lưu ý sandbox trên hiển thị lỗi trong console:

<ConsoleBlock level="error">

Warning: Each child in a list should have a unique "key" prop.

</ConsoleBlock>

Bạn sẽ học cách sửa lỗi này ở phần sau trong trang này. Trước khi đến đó, hãy thêm một số cấu trúc cho dữ liệu của bạn.

## Lọc mảng các mục {/*filtering-arrays-of-items*/}

Dữ liệu này có thể được cấu trúc hơn nữa.

```js
const people = [{
  id: 0,
  name: 'Creola Katherine Johnson',
  profession: 'mathematician',
}, {
  id: 1,
  name: 'Mario José Molina-Pasquel Henríquez',
  profession: 'chemist',
}, {
  id: 2,
  name: 'Mohammad Abdus Salam',
  profession: 'physicist',
}, {
  id: 3,
  name: 'Percy Lavon Julian',
  profession: 'chemist',
}, {
  id: 4,
  name: 'Subrahmanyan Chandrasekhar',
  profession: 'astrophysicist',
}];
```

Giả sử bạn muốn một cách để chỉ hiển thị những người có nghề nghiệp là `'chemist'`. Bạn có thể sử dụng phương thức `filter()` của JavaScript để chỉ trả về những người đó. Phương thức này nhận một mảng các mục, truyền chúng qua một "bài kiểm tra" (một hàm trả về `true` hoặc `false`), và trả về một mảng mới chỉ gồm những mục đã qua bài kiểm tra (trả về `true`).

Bạn chỉ muốn các mục có `profession` là `'chemist'`. Hàm "kiểm tra" cho điều này trông như `(person) => person.profession === 'chemist'`. Đây là cách ghép lại:

1. **Tạo** một mảng mới chỉ gồm những người "chemist", `chemists`, bằng cách gọi `filter()` trên `people` lọc theo `person.profession === 'chemist'`:

```js
const chemists = people.filter(person =>
  person.profession === 'chemist'
);
```

2. Bây giờ **map** qua `chemists`:

```js {1,13}
const listItems = chemists.map(person =>
  <li>
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
```

3. Cuối cùng, **trả về** `listItems` từ component của bạn:

```js
return <ul>{listItems}</ul>;
```

<Sandpack>

```js src/App.js
import { people } from './data.js';
import { getImageUrl } from './utils.js';

export default function List() {
  const chemists = people.filter(person =>
    person.profession === 'chemist'
  );
  const listItems = chemists.map(person =>
    <li>
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
  return <ul>{listItems}</ul>;
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
  grid-template-columns: auto 1fr;
  gap: 20px;
  align-items: center;
}
img { width: 100px; height: 100px; border-radius: 50%; }
```

</Sandpack>

<Pitfall>

Arrow function ngầm trả về biểu thức ngay sau `=>`, vì vậy bạn không cần câu lệnh `return`:

```js
const listItems = chemists.map(person =>
  <li>...</li> // Ngầm trả về!
);
```

Tuy nhiên, **bạn phải viết `return` rõ ràng nếu `=>` của bạn được theo sau bởi `{` dấu ngoặc nhọn!**

```js
const listItems = chemists.map(person => { // Dấu ngoặc nhọn
  return <li>...</li>;
});
```

Arrow function chứa `=> {` được gọi là có ["block body".](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#function_body) Chúng cho phép bạn viết nhiều hơn một dòng code, nhưng bạn *phải* tự viết câu lệnh `return`. Nếu bạn quên nó, sẽ không có gì được trả về!

</Pitfall>

## Giữ các mục danh sách theo thứ tự với `key` {/*keeping-list-items-in-order-with-key*/}

Lưu ý rằng tất cả các sandbox trên hiển thị lỗi trong console:

<ConsoleBlock level="error">

Warning: Each child in a list should have a unique "key" prop.

</ConsoleBlock>

Bạn cần cung cấp cho mỗi mục mảng một `key` -- một chuỗi hoặc số nhận dạng duy nhất nó giữa các mục khác trong mảng đó:

```js
<li key={person.id}>...</li>
```

<Note>

Các element JSX trực tiếp bên trong lời gọi `map()` luôn cần key!

</Note>

Key nói với React mục mảng nào tương ứng với mỗi component, để nó có thể khớp chúng lại sau này. Điều này trở nên quan trọng nếu các mục mảng của bạn có thể di chuyển (ví dụ: do sắp xếp), được chèn vào hoặc bị xóa. Một `key` được chọn tốt giúp React suy ra chính xác điều gì đã xảy ra và thực hiện các cập nhật đúng cho cây DOM.

Thay vì tạo key trực tiếp, bạn nên đưa chúng vào trong dữ liệu của mình:

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
        <b>{person.name}</b>
          {' ' + person.profession + ' '}
          known for {person.accomplishment}
      </p>
    </li>
  );
  return <ul>{listItems}</ul>;
}
```

```js src/data.js active
export const people = [{
  id: 0, // Used in JSX as a key
  name: 'Creola Katherine Johnson',
  profession: 'mathematician',
  accomplishment: 'spaceflight calculations',
  imageId: 'MK3eW3A'
}, {
  id: 1, // Used in JSX as a key
  name: 'Mario José Molina-Pasquel Henríquez',
  profession: 'chemist',
  accomplishment: 'discovery of Arctic ozone hole',
  imageId: 'mynHUSa'
}, {
  id: 2, // Used in JSX as a key
  name: 'Mohammad Abdus Salam',
  profession: 'physicist',
  accomplishment: 'electromagnetism theory',
  imageId: 'bE7W1ji'
}, {
  id: 3, // Used in JSX as a key
  name: 'Percy Lavon Julian',
  profession: 'chemist',
  accomplishment: 'pioneering cortisone drugs, steroids and birth control pills',
  imageId: 'IOjWm71'
}, {
  id: 4, // Used in JSX as a key
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
  grid-template-columns: auto 1fr;
  gap: 20px;
  align-items: center;
}
img { width: 100px; height: 100px; border-radius: 50%; }
```

</Sandpack>

<DeepDive>

#### Hiển thị nhiều node DOM cho mỗi mục danh sách {/*displaying-several-dom-nodes-for-each-list-item*/}

Bạn làm gì khi mỗi mục cần render không phải một, mà là nhiều node DOM?

Cú pháp [`<>...</>` Fragment](/reference/react/Fragment) ngắn gọn sẽ không cho phép bạn truyền key, vì vậy bạn cần nhóm chúng vào một `<div>` duy nhất, hoặc sử dụng cú pháp [`<Fragment>` dài hơn và rõ ràng hơn:](/reference/react/Fragment#rendering-a-list-of-fragments)

```js
import { Fragment } from 'react';

// ...

const listItems = people.map(person =>
  <Fragment key={person.id}>
    <h1>{person.name}</h1>
    <p>{person.bio}</p>
  </Fragment>
);
```

Fragment biến mất khỏi DOM, vì vậy điều này sẽ tạo ra một danh sách phẳng gồm `<h1>`, `<p>`, `<h1>`, `<p>`, v.v.

</DeepDive>

### Lấy `key` ở đâu {/*where-to-get-your-key*/}

Các nguồn dữ liệu khác nhau cung cấp các nguồn key khác nhau:

* **Dữ liệu từ cơ sở dữ liệu:** Nếu dữ liệu của bạn đến từ cơ sở dữ liệu, bạn có thể sử dụng các key/ID cơ sở dữ liệu, vốn là duy nhất theo bản chất.
* **Dữ liệu được tạo cục bộ:** Nếu dữ liệu của bạn được tạo và lưu trữ cục bộ (ví dụ: ghi chú trong ứng dụng ghi chú), hãy sử dụng bộ đếm tăng dần, [`crypto.randomUUID()`](https://developer.mozilla.org/en-US/docs/Web/API/Crypto/randomUUID) hoặc một gói như [`uuid`](https://www.npmjs.com/package/uuid) khi tạo các mục.

### Quy tắc của key {/*rules-of-keys*/}

* **Key phải là duy nhất giữa các anh chị em.** Tuy nhiên, bạn có thể sử dụng cùng key cho các node JSX trong các mảng _khác nhau_.
* **Key không được thay đổi** vì điều đó sẽ làm mất tác dụng của chúng! Đừng tạo chúng trong khi render.

### Tại sao React cần key? {/*why-does-react-need-keys*/}

Hãy tưởng tượng các file trên màn hình của bạn không có tên. Thay vào đó, bạn sẽ tham chiếu chúng theo thứ tự -- file đầu tiên, file thứ hai, v.v. Bạn có thể quen với điều đó, nhưng một khi bạn xóa một file, nó sẽ trở nên khó hiểu. File thứ hai sẽ trở thành file đầu tiên, file thứ ba sẽ trở thành file thứ hai, v.v.

Tên file trong thư mục và key JSX trong mảng phục vụ mục đích tương tự. Chúng cho phép chúng ta nhận dạng duy nhất một mục giữa các anh chị em của nó. Một key được chọn tốt cung cấp thêm thông tin hơn vị trí trong mảng. Ngay cả khi _vị trí_ thay đổi do sắp xếp lại, `key` cho phép React nhận dạng mục đó trong suốt vòng đời của nó.

<Pitfall>

Bạn có thể bị cám dỗ sử dụng index của một mục trong mảng làm key của nó. Thực ra, đó là những gì React sẽ sử dụng nếu bạn không chỉ định `key` nào cả. Nhưng thứ tự mà bạn render các mục sẽ thay đổi theo thời gian nếu một mục được chèn vào, bị xóa, hoặc nếu mảng được sắp xếp lại. Index làm key thường dẫn đến các bug tinh tế và khó hiểu.

Tương tự, đừng tạo key ngay lập tức, ví dụ với `key={Math.random()}`. Điều này sẽ khiến key không bao giờ khớp giữa các lần render, dẫn đến tất cả các component và DOM được tạo lại mỗi lần. Không chỉ chậm, mà còn mất bất kỳ input của người dùng bên trong các mục danh sách. Thay vào đó, hãy sử dụng ID ổn định dựa trên dữ liệu.

Lưu ý rằng các component của bạn sẽ không nhận `key` như một prop. Nó chỉ được React sử dụng như một gợi ý. Nếu component của bạn cần ID, bạn phải truyền nó như một prop riêng biệt: `<Profile key={id} userId={id} />`.

</Pitfall>

<Recap>

Trên trang này bạn đã học:

* Cách chuyển dữ liệu ra khỏi các component và vào các cấu trúc dữ liệu như mảng và object.
* Cách tạo tập hợp các component tương tự với `map()` của JavaScript.
* Cách tạo mảng các mục đã lọc với `filter()` của JavaScript.
* Tại sao và cách đặt `key` trên mỗi component trong một tập hợp để React có thể theo dõi từng cái ngay cả khi vị trí hoặc dữ liệu của chúng thay đổi.

</Recap>



<Challenges>

#### Chia danh sách thành hai {/*splitting-a-list-in-two*/}

Ví dụ này hiển thị danh sách tất cả mọi người.

Thay đổi nó để hiển thị hai danh sách riêng biệt lần lượt: **Chemists** và **Everyone Else.** Như trước đây, bạn có thể xác định một người có phải là chemist hay không bằng cách kiểm tra `person.profession === 'chemist'`.

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
  grid-template-columns: auto 1fr;
  gap: 20px;
  align-items: center;
}
img { width: 100px; height: 100px; border-radius: 50%; }
```

</Sandpack>

<Solution>

Bạn có thể sử dụng `filter()` hai lần, tạo hai mảng riêng biệt, và sau đó `map` qua cả hai:

<Sandpack>

```js src/App.js
import { people } from './data.js';
import { getImageUrl } from './utils.js';

export default function List() {
  const chemists = people.filter(person =>
    person.profession === 'chemist'
  );
  const everyoneElse = people.filter(person =>
    person.profession !== 'chemist'
  );
  return (
    <article>
      <h1>Scientists</h1>
      <h2>Chemists</h2>
      <ul>
        {chemists.map(person =>
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
        )}
      </ul>
      <h2>Everyone Else</h2>
      <ul>
        {everyoneElse.map(person =>
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
        )}
      </ul>
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
  grid-template-columns: auto 1fr;
  gap: 20px;
  align-items: center;
}
img { width: 100px; height: 100px; border-radius: 50%; }
```

</Sandpack>

Trong giải pháp này, các lời gọi `map` được đặt trực tiếp nội tuyến vào các phần tử `<ul>` cha, nhưng bạn có thể giới thiệu các biến cho chúng nếu bạn thấy điều đó dễ đọc hơn.

Vẫn còn một chút trùng lặp giữa các danh sách được render. Bạn có thể đi xa hơn và trích xuất các phần lặp lại vào một component `<ListSection>`:

<Sandpack>

```js src/App.js
import { people } from './data.js';
import { getImageUrl } from './utils.js';

function ListSection({ title, people }) {
  return (
    <>
      <h2>{title}</h2>
      <ul>
        {people.map(person =>
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
        )}
      </ul>
    </>
  );
}

export default function List() {
  const chemists = people.filter(person =>
    person.profession === 'chemist'
  );
  const everyoneElse = people.filter(person =>
    person.profession !== 'chemist'
  );
  return (
    <article>
      <h1>Scientists</h1>
      <ListSection
        title="Chemists"
        people={chemists}
      />
      <ListSection
        title="Everyone Else"
        people={everyoneElse}
      />
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
  grid-template-columns: auto 1fr;
  gap: 20px;
  align-items: center;
}
img { width: 100px; height: 100px; border-radius: 50%; }
```

</Sandpack>

Một người đọc rất chú ý có thể nhận thấy rằng với hai lần gọi `filter`, chúng ta kiểm tra nghề nghiệp của mỗi người hai lần. Kiểm tra một thuộc tính rất nhanh, vì vậy trong ví dụ này vẫn ổn. Nếu logic của bạn tốn kém hơn thế, bạn có thể thay thế các lời gọi `filter` bằng một vòng lặp thủ công xây dựng các mảng và kiểm tra mỗi người một lần.

Thực ra, nếu `people` không bao giờ thay đổi, bạn có thể chuyển code này ra khỏi component của mình. Từ góc nhìn của React, tất cả những gì quan trọng là bạn cung cấp cho nó một mảng các node JSX ở cuối. Nó không quan tâm bạn tạo ra mảng đó như thế nào:

<Sandpack>

```js src/App.js
import { people } from './data.js';
import { getImageUrl } from './utils.js';

let chemists = [];
let everyoneElse = [];
people.forEach(person => {
  if (person.profession === 'chemist') {
    chemists.push(person);
  } else {
    everyoneElse.push(person);
  }
});

function ListSection({ title, people }) {
  return (
    <>
      <h2>{title}</h2>
      <ul>
        {people.map(person =>
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
        )}
      </ul>
    </>
  );
}

export default function List() {
  return (
    <article>
      <h1>Scientists</h1>
      <ListSection
        title="Chemists"
        people={chemists}
      />
      <ListSection
        title="Everyone Else"
        people={everyoneElse}
      />
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
  grid-template-columns: auto 1fr;
  gap: 20px;
  align-items: center;
}
img { width: 100px; height: 100px; border-radius: 50%; }
```

</Sandpack>

</Solution>

#### Danh sách lồng nhau trong một component {/*nested-lists-in-one-component*/}

Tạo danh sách công thức từ mảng này! Đối với mỗi công thức trong mảng, hiển thị tên của nó như `<h2>` và liệt kê nguyên liệu trong `<ul>`.

<Hint>

Điều này sẽ yêu cầu lồng hai lời gọi `map` khác nhau.

</Hint>

<Sandpack>

```js src/App.js
import { recipes } from './data.js';

export default function RecipeList() {
  return (
    <div>
      <h1>Recipes</h1>
    </div>
  );
}
```

```js src/data.js
export const recipes = [{
  id: 'greek-salad',
  name: 'Greek Salad',
  ingredients: ['tomatoes', 'cucumber', 'onion', 'olives', 'feta']
}, {
  id: 'hawaiian-pizza',
  name: 'Hawaiian Pizza',
  ingredients: ['pizza crust', 'pizza sauce', 'mozzarella', 'ham', 'pineapple']
}, {
  id: 'hummus',
  name: 'Hummus',
  ingredients: ['chickpeas', 'olive oil', 'garlic cloves', 'lemon', 'tahini']
}];
```

</Sandpack>

<Solution>

Đây là một cách bạn có thể thực hiện:

<Sandpack>

```js src/App.js
import { recipes } from './data.js';

export default function RecipeList() {
  return (
    <div>
      <h1>Recipes</h1>
      {recipes.map(recipe =>
        <div key={recipe.id}>
          <h2>{recipe.name}</h2>
          <ul>
            {recipe.ingredients.map(ingredient =>
              <li key={ingredient}>
                {ingredient}
              </li>
            )}
          </ul>
        </div>
      )}
    </div>
  );
}
```

```js src/data.js
export const recipes = [{
  id: 'greek-salad',
  name: 'Greek Salad',
  ingredients: ['tomatoes', 'cucumber', 'onion', 'olives', 'feta']
}, {
  id: 'hawaiian-pizza',
  name: 'Hawaiian Pizza',
  ingredients: ['pizza crust', 'pizza sauce', 'mozzarella', 'ham', 'pineapple']
}, {
  id: 'hummus',
  name: 'Hummus',
  ingredients: ['chickpeas', 'olive oil', 'garlic cloves', 'lemon', 'tahini']
}];
```

</Sandpack>

Mỗi `recipes` đã bao gồm trường `id`, vì vậy đó là những gì vòng lặp ngoài sử dụng làm `key`. Không có ID nào bạn có thể sử dụng để duyệt qua nguyên liệu. Tuy nhiên, có thể giả định rằng cùng một nguyên liệu sẽ không được liệt kê hai lần trong cùng một công thức, vì vậy tên của nó có thể đóng vai trò là `key`. Ngoài ra, bạn có thể thay đổi cấu trúc dữ liệu để thêm ID, hoặc sử dụng index làm `key` (với lưu ý rằng bạn không thể sắp xếp lại các nguyên liệu một cách an toàn).

</Solution>

#### Trích xuất một component mục danh sách {/*extracting-a-list-item-component*/}

Component `RecipeList` này chứa hai lời gọi `map` lồng nhau. Để đơn giản hóa nó, hãy trích xuất component `Recipe` từ nó sẽ nhận các prop `id`, `name`, và `ingredients`. Bạn đặt `key` bên ngoài ở đâu và tại sao?

<Sandpack>

```js src/App.js
import { recipes } from './data.js';

export default function RecipeList() {
  return (
    <div>
      <h1>Recipes</h1>
      {recipes.map(recipe =>
        <div key={recipe.id}>
          <h2>{recipe.name}</h2>
          <ul>
            {recipe.ingredients.map(ingredient =>
              <li key={ingredient}>
                {ingredient}
              </li>
            )}
          </ul>
        </div>
      )}
    </div>
  );
}
```

```js src/data.js
export const recipes = [{
  id: 'greek-salad',
  name: 'Greek Salad',
  ingredients: ['tomatoes', 'cucumber', 'onion', 'olives', 'feta']
}, {
  id: 'hawaiian-pizza',
  name: 'Hawaiian Pizza',
  ingredients: ['pizza crust', 'pizza sauce', 'mozzarella', 'ham', 'pineapple']
}, {
  id: 'hummus',
  name: 'Hummus',
  ingredients: ['chickpeas', 'olive oil', 'garlic cloves', 'lemon', 'tahini']
}];
```

</Sandpack>

<Solution>

Bạn có thể copy-paste JSX từ `map` bên ngoài vào component `Recipe` mới và trả về JSX đó. Sau đó bạn có thể thay đổi `recipe.name` thành `name`, `recipe.id` thành `id`, v.v., và truyền chúng như props cho `Recipe`:

<Sandpack>

```js
import { recipes } from './data.js';

function Recipe({ id, name, ingredients }) {
  return (
    <div>
      <h2>{name}</h2>
      <ul>
        {ingredients.map(ingredient =>
          <li key={ingredient}>
            {ingredient}
          </li>
        )}
      </ul>
    </div>
  );
}

export default function RecipeList() {
  return (
    <div>
      <h1>Recipes</h1>
      {recipes.map(recipe =>
        <Recipe {...recipe} key={recipe.id} />
      )}
    </div>
  );
}
```

```js src/data.js
export const recipes = [{
  id: 'greek-salad',
  name: 'Greek Salad',
  ingredients: ['tomatoes', 'cucumber', 'onion', 'olives', 'feta']
}, {
  id: 'hawaiian-pizza',
  name: 'Hawaiian Pizza',
  ingredients: ['pizza crust', 'pizza sauce', 'mozzarella', 'ham', 'pineapple']
}, {
  id: 'hummus',
  name: 'Hummus',
  ingredients: ['chickpeas', 'olive oil', 'garlic cloves', 'lemon', 'tahini']
}];
```

</Sandpack>

Ở đây, `<Recipe {...recipe} key={recipe.id} />` là cú pháp tắt có nghĩa là "truyền tất cả thuộc tính của object `recipe` như props cho component `Recipe`". Bạn cũng có thể viết từng prop rõ ràng: `<Recipe id={recipe.id} name={recipe.name} ingredients={recipe.ingredients} key={recipe.id} />`.

**Lưu ý rằng `key` được chỉ định trên chính `<Recipe>` thay vì trên `<div>` gốc được trả về từ `Recipe`.** Điều này là vì `key` này cần thiết trực tiếp trong ngữ cảnh của mảng xung quanh. Trước đây, bạn có một mảng `<div>` vì vậy mỗi cái cần một `key`, nhưng bây giờ bạn có một mảng `<Recipe>`. Nói cách khác, khi bạn trích xuất một component, đừng quên để `key` bên ngoài JSX bạn copy và paste.

</Solution>

#### Danh sách với dấu phân cách {/*list-with-a-separator*/}

Ví dụ này render một bài haiku nổi tiếng của Tachibana Hokushi, với mỗi dòng được bọc trong thẻ `<p>`. Công việc của bạn là chèn dấu phân cách `<hr />` giữa mỗi đoạn văn. Cấu trúc kết quả của bạn sẽ trông như thế này:

```js
<article>
  <p>I write, erase, rewrite</p>
  <hr />
  <p>Erase again, and then</p>
  <hr />
  <p>A poppy blooms.</p>
</article>
```

Một bài haiku chỉ chứa ba dòng, nhưng giải pháp của bạn nên hoạt động với bất kỳ số dòng nào. Lưu ý rằng các phần tử `<hr />` chỉ xuất hiện *giữa* các phần tử `<p>`, không phải ở đầu hoặc cuối!

<Sandpack>

```js
const poem = {
  lines: [
    'I write, erase, rewrite',
    'Erase again, and then',
    'A poppy blooms.'
  ]
};

export default function Poem() {
  return (
    <article>
      {poem.lines.map((line, index) =>
        <p key={index}>
          {line}
        </p>
      )}
    </article>
  );
}
```

```css
body {
  text-align: center;
}
p {
  font-family: Georgia, serif;
  font-size: 20px;
  font-style: italic;
}
hr {
  margin: 0 120px 0 120px;
  border: 1px dashed #45c3d8;
}
```

</Sandpack>

(Đây là trường hợp hiếm khi index làm key là chấp nhận được vì các dòng thơ sẽ không bao giờ được sắp xếp lại.)

<Hint>

Bạn sẽ cần chuyển `map` thành vòng lặp thủ công, hoặc sử dụng Fragment.

</Hint>

<Solution>

Bạn có thể viết vòng lặp thủ công, chèn `<hr />` và `<p>...</p>` vào mảng đầu ra khi bạn đi:

<Sandpack>

```js
const poem = {
  lines: [
    'I write, erase, rewrite',
    'Erase again, and then',
    'A poppy blooms.'
  ]
};

export default function Poem() {
  let output = [];

  // Fill the output array
  poem.lines.forEach((line, i) => {
    output.push(
      <hr key={i + '-separator'} />
    );
    output.push(
      <p key={i + '-text'}>
        {line}
      </p>
    );
  });
  // Remove the first <hr />
  output.shift();

  return (
    <article>
      {output}
    </article>
  );
}
```

```css
body {
  text-align: center;
}
p {
  font-family: Georgia, serif;
  font-size: 20px;
  font-style: italic;
}
hr {
  margin: 0 120px 0 120px;
  border: 1px dashed #45c3d8;
}
```

</Sandpack>

Sử dụng index dòng gốc làm `key` không còn hoạt động nữa vì mỗi dấu phân cách và đoạn văn bây giờ ở trong cùng một mảng. Tuy nhiên, bạn có thể cung cấp cho mỗi cái một key khác biệt bằng cách sử dụng hậu tố, ví dụ `key={i + '-text'}`.

Ngoài ra, bạn có thể render một tập hợp Fragment chứa `<hr />` và `<p>...</p>`. Tuy nhiên, cú pháp tắt `<>...</>` không hỗ trợ truyền key, vì vậy bạn phải viết `<Fragment>` rõ ràng:

<Sandpack>

```js
import { Fragment } from 'react';

const poem = {
  lines: [
    'I write, erase, rewrite',
    'Erase again, and then',
    'A poppy blooms.'
  ]
};

export default function Poem() {
  return (
    <article>
      {poem.lines.map((line, i) =>
        <Fragment key={i}>
          {i > 0 && <hr />}
          <p>{line}</p>
        </Fragment>
      )}
    </article>
  );
}
```

```css
body {
  text-align: center;
}
p {
  font-family: Georgia, serif;
  font-size: 20px;
  font-style: italic;
}
hr {
  margin: 0 120px 0 120px;
  border: 1px dashed #45c3d8;
}
```

</Sandpack>

Hãy nhớ, Fragment (thường được viết là `<> </>`) cho phép bạn nhóm các node JSX mà không thêm `<div>` thêm vào!

</Solution>

</Challenges>
