---
title: immutability
---

<Intro>

Kiểm tra việc thay đổi props, state và các giá trị [bất biến](/reference/rules/components-and-hooks-must-be-pure#props-and-state-are-immutable) khác.

</Intro>

## Chi tiết quy tắc {/*rule-details*/}

Props và state của component là các snapshot bất biến. Không bao giờ thay đổi chúng trực tiếp. Thay vào đó, hãy truyền props mới xuống, và sử dụng hàm setter từ `useState`.

## Các vi phạm phổ biến {/*common-violations*/}

### Không hợp lệ {/*invalid*/}

```js
// ❌ Thay đổi mảng bằng push
function Component() {
  const [items, setItems] = useState([1, 2, 3]);

  const addItem = () => {
    items.push(4); // Đang thay đổi!
    setItems(items); // Cùng tham chiếu, không re-render
  };
}

// ❌ Gán thuộc tính object
function Component() {
  const [user, setUser] = useState({name: 'Alice'});

  const updateName = () => {
    user.name = 'Bob'; // Đang thay đổi!
    setUser(user); // Cùng tham chiếu
  };
}

// ❌ Sort mà không spread
function Component() {
  const [items, setItems] = useState([3, 1, 2]);

  const sortItems = () => {
    setItems(items.sort()); // sort thay đổi mảng gốc!
  };
}
```

### Hợp lệ {/*valid*/}

```js
// ✅ Tạo mảng mới
function Component() {
  const [items, setItems] = useState([1, 2, 3]);

  const addItem = () => {
    setItems([...items, 4]); // Mảng mới
  };
}

// ✅ Tạo object mới
function Component() {
  const [user, setUser] = useState({name: 'Alice'});

  const updateName = () => {
    setUser({...user, name: 'Bob'}); // Object mới
  };
}
```

## Xử lý sự cố {/*troubleshooting*/}

### Tôi cần thêm phần tử vào mảng {/*add-items-array*/}

Thay đổi mảng bằng các phương thức như `push()` sẽ không kích hoạt re-render:

```js
// ❌ Sai: Thay đổi mảng
function TodoList() {
  const [todos, setTodos] = useState([]);

  const addTodo = (id, text) => {
    todos.push({id, text});
    setTodos(todos); // Cùng tham chiếu mảng!
  };

  return (
    <ul>
      {todos.map(todo => <li key={todo.id}>{todo.text}</li>)}
    </ul>
  );
}
```

Tạo một mảng mới thay vì:

```js
// ✅ Tốt hơn: Tạo mảng mới
function TodoList() {
  const [todos, setTodos] = useState([]);

  const addTodo = (id, text) => {
    setTodos([...todos, {id, text}]);
    // Hoặc: setTodos(todos => [...todos, {id: Date.now(), text}])
  };

  return (
    <ul>
      {todos.map(todo => <li key={todo.id}>{todo.text}</li>)}
    </ul>
  );
}
```

### Tôi cần cập nhật object lồng nhau {/*update-nested-objects*/}

Thay đổi thuộc tính lồng nhau không kích hoạt re-render:

```js
// ❌ Sai: Thay đổi object lồng nhau
function UserProfile() {
  const [user, setUser] = useState({
    name: 'Alice',
    settings: {
      theme: 'light',
      notifications: true
    }
  });

  const toggleTheme = () => {
    user.settings.theme = 'dark'; // Thay đổi trực tiếp!
    setUser(user); // Cùng tham chiếu object
  };
}
```

Spread ở mỗi cấp cần cập nhật:

```js
// ✅ Tốt hơn: Tạo object mới ở mỗi cấp
function UserProfile() {
  const [user, setUser] = useState({
    name: 'Alice',
    settings: {
      theme: 'light',
      notifications: true
    }
  });

  const toggleTheme = () => {
    setUser({
      ...user,
      settings: {
        ...user.settings,
        theme: 'dark'
      }
    });
  };
}
```
