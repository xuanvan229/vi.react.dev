---
title: Phản ứng với đầu vào bằng State
---

<Intro>

React cung cấp một cách khai báo để thao tác UI. Thay vì thao tác trực tiếp từng phần của UI, bạn mô tả các trạng thái khác nhau mà component có thể ở, và chuyển đổi giữa chúng để phản hồi với đầu vào của người dùng. Điều này tương tự với cách các nhà thiết kế nghĩ về UI.

</Intro>

<YouWillLearn>

* Lập trình UI khai báo khác với lập trình UI mệnh lệnh như thế nào
* Cách liệt kê các trạng thái trực quan khác nhau mà component có thể ở
* Cách kích hoạt các thay đổi giữa các trạng thái trực quan khác nhau từ code

</YouWillLearn>

## UI khai báo so với UI mệnh lệnh {/*how-declarative-ui-compares-to-imperative*/}

Khi bạn thiết kế các tương tác UI, bạn có lẽ nghĩ về cách UI *thay đổi* để phản hồi với hành động của người dùng. Hãy xem xét một form cho phép người dùng gửi câu trả lời:

* Khi bạn nhập gì đó vào form, nút "Submit" **trở nên enabled.**
* Khi bạn nhấn "Submit", cả form và nút **trở nên disabled,** và một spinner **xuất hiện.**
* Nếu yêu cầu mạng thành công, form **bị ẩn,** và thông báo "Thank you" **xuất hiện.**
* Nếu yêu cầu mạng thất bại, một thông báo lỗi **xuất hiện,** và form **trở nên enabled** trở lại.

Trong **lập trình mệnh lệnh,** phần trên tương ứng trực tiếp với cách bạn triển khai tương tác. Bạn phải viết các hướng dẫn chính xác để thao tác UI tùy thuộc vào những gì vừa xảy ra. Đây là một cách khác để nghĩ về điều này: hãy tưởng tượng ngồi cạnh ai đó trong xe hơi và chỉ từng bước cho họ đi đâu.

<Illustration src="/images/docs/illustrations/i_imperative-ui-programming.png"  alt="In a car driven by an anxious-looking person representing JavaScript, a passenger orders the driver to execute a sequence of complicated turn by turn navigations." />

Họ không biết bạn muốn đi đâu, họ chỉ làm theo lệnh của bạn. (Và nếu bạn đưa ra hướng dẫn sai, bạn sẽ đến nhầm nơi!) Nó được gọi là *mệnh lệnh* vì bạn phải "ra lệnh" cho từng phần tử, từ spinner đến nút, nói với máy tính *cách* cập nhật UI.

Trong ví dụ lập trình UI mệnh lệnh này, form được xây dựng *không có* React. Nó chỉ sử dụng trình duyệt [DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model):

<Sandpack>

```js src/index.js active
async function handleFormSubmit(e) {
  e.preventDefault();
  disable(textarea);
  disable(button);
  show(loadingMessage);
  hide(errorMessage);
  try {
    await submitForm(textarea.value);
    show(successMessage);
    hide(form);
  } catch (err) {
    show(errorMessage);
    errorMessage.textContent = err.message;
  } finally {
    hide(loadingMessage);
    enable(textarea);
    enable(button);
  }
}

function handleTextareaChange() {
  if (textarea.value.length === 0) {
    disable(button);
  } else {
    enable(button);
  }
}

function hide(el) {
  el.style.display = 'none';
}

function show(el) {
  el.style.display = '';
}

function enable(el) {
  el.disabled = false;
}

function disable(el) {
  el.disabled = true;
}

function submitForm(answer) {
  // Pretend it's hitting the network.
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (answer.toLowerCase() === 'istanbul') {
        resolve();
      } else {
        reject(new Error('Good guess but a wrong answer. Try again!'));
      }
    }, 1500);
  });
}

let form = document.getElementById('form');
let textarea = document.getElementById('textarea');
let button = document.getElementById('button');
let loadingMessage = document.getElementById('loading');
let errorMessage = document.getElementById('error');
let successMessage = document.getElementById('success');
form.onsubmit = handleFormSubmit;
textarea.oninput = handleTextareaChange;
```

```js sandbox.config.json hidden
{
  "hardReloadOnChange": true
}
```

```html public/index.html
<form id="form">
  <h2>City quiz</h2>
  <p>
    What city is located on two continents?
  </p>
  <textarea id="textarea"></textarea>
  <br />
  <button id="button" disabled>Submit</button>
  <p id="loading" style="display: none">Loading...</p>
  <p id="error" style="display: none; color: red;"></p>
</form>
<h1 id="success" style="display: none">That's right!</h1>

<style>
* { box-sizing: border-box; }
body { font-family: sans-serif; margin: 20px; padding: 0; }
</style>
```

</Sandpack>

Thao tác UI theo kiểu mệnh lệnh hoạt động tốt đủ cho các ví dụ đơn lẻ, nhưng nó ngày càng khó quản lý hơn trong các hệ thống phức tạp hơn. Hãy tưởng tượng cập nhật một trang đầy các form khác nhau như thế này. Thêm một phần tử UI mới hoặc một tương tác mới sẽ yêu cầu kiểm tra cẩn thận tất cả code hiện có để đảm bảo bạn không đã tạo ra bug (ví dụ: quên hiển thị hoặc ẩn điều gì đó).

React được xây dựng để giải quyết vấn đề này.

Trong React, bạn không trực tiếp thao tác UI--có nghĩa là bạn không enable, disable, hiển thị, hoặc ẩn các component trực tiếp. Thay vào đó, bạn **khai báo những gì bạn muốn hiển thị,** và React tìm cách cập nhật UI. Hãy nghĩ về việc lên taxi và nói với tài xế nơi bạn muốn đến thay vì nói chính xác họ phải rẽ đâu. Công việc của tài xế là đưa bạn đến đó, và họ thậm chí có thể biết một số lối tắt mà bạn chưa nghĩ đến!

<Illustration src="/images/docs/illustrations/i_declarative-ui-programming.png" alt="In a car driven by React, a passenger asks to be taken to a specific place on the map. React figures out how to do that." />

## Tư duy về UI theo kiểu khai báo {/*thinking-about-ui-declaratively*/}

Bạn đã thấy cách triển khai một form theo kiểu mệnh lệnh ở trên. Để hiểu rõ hơn về cách tư duy trong React, bạn sẽ đi qua việc triển khai lại UI này trong React bên dưới:

1. **Xác định** các trạng thái trực quan khác nhau của component
2. **Xác định** điều gì kích hoạt các thay đổi trạng thái đó
3. **Biểu diễn** state trong bộ nhớ bằng `useState`
4. **Loại bỏ** bất kỳ biến state không cần thiết nào
5. **Kết nối** các event handler để đặt state

### Bước 1: Xác định các trạng thái trực quan khác nhau của component {/*step-1-identify-your-components-different-visual-states*/}

Trong khoa học máy tính, bạn có thể nghe về ["state machine"](https://en.wikipedia.org/wiki/Finite-state_machine) ở một trong nhiều "trạng thái". Nếu bạn làm việc với một nhà thiết kế, bạn có thể đã thấy các mockup cho các "trạng thái trực quan" khác nhau. React đứng ở giao điểm của thiết kế và khoa học máy tính, vì vậy cả hai ý tưởng này đều là nguồn cảm hứng.

Đầu tiên, bạn cần trực quan hóa tất cả các "trạng thái" khác nhau của UI mà người dùng có thể thấy:

* **Empty**: Form có nút "Submit" bị disabled.
* **Typing**: Form có nút "Submit" được enabled.
* **Submitting**: Form bị disabled hoàn toàn. Spinner được hiển thị.
* **Success**: Thông báo "Thank you" được hiển thị thay cho form.
* **Error**: Giống như trạng thái Typing, nhưng có thêm thông báo lỗi.

Giống như một nhà thiết kế, bạn sẽ muốn "mock up" hoặc tạo "mock" cho các trạng thái khác nhau trước khi thêm logic. Ví dụ, đây là một mock chỉ cho phần trực quan của form. Mock này được kiểm soát bởi một prop gọi là `status` với giá trị mặc định là `'empty'`:

<Sandpack>

```js
export default function Form({
  status = 'empty'
}) {
  if (status === 'success') {
    return <h1>That's right!</h1>
  }
  return (
    <>
      <h2>City quiz</h2>
      <p>
        In which city is there a billboard that turns air into drinkable water?
      </p>
      <form>
        <textarea />
        <br />
        <button>
          Submit
        </button>
      </form>
    </>
  )
}
```

</Sandpack>

Bạn có thể đặt tên prop đó theo bất kỳ tên gì bạn thích, tên không quan trọng. Hãy thử chỉnh sửa `status = 'empty'` thành `status = 'success'` để xem thông báo thành công xuất hiện. Mocking cho phép bạn nhanh chóng lặp lại trên UI trước khi bạn kết nối bất kỳ logic nào. Đây là một prototype phong phú hơn của cùng một component, vẫn "được kiểm soát" bởi prop `status`:

<Sandpack>

```js
export default function Form({
  // Try 'submitting', 'error', 'success':
  status = 'empty'
}) {
  if (status === 'success') {
    return <h1>That's right!</h1>
  }
  return (
    <>
      <h2>City quiz</h2>
      <p>
        In which city is there a billboard that turns air into drinkable water?
      </p>
      <form>
        <textarea disabled={
          status === 'submitting'
        } />
        <br />
        <button disabled={
          status === 'empty' ||
          status === 'submitting'
        }>
          Submit
        </button>
        {status === 'error' &&
          <p className="Error">
            Good guess but a wrong answer. Try again!
          </p>
        }
      </form>
      </>
  );
}
```

```css
.Error { color: red; }
```

</Sandpack>

<DeepDive>

#### Hiển thị nhiều trạng thái trực quan cùng một lúc {/*displaying-many-visual-states-at-once*/}

Nếu một component có nhiều trạng thái trực quan, sẽ thuận tiện khi hiển thị tất cả chúng trên một trang:

<Sandpack>

```js src/App.js active
import Form from './Form.js';

let statuses = [
  'empty',
  'typing',
  'submitting',
  'success',
  'error',
];

export default function App() {
  return (
    <>
      {statuses.map(status => (
        <section key={status}>
          <h4>Form ({status}):</h4>
          <Form status={status} />
        </section>
      ))}
    </>
  );
}
```

```js src/Form.js
export default function Form({ status }) {
  if (status === 'success') {
    return <h1>That's right!</h1>
  }
  return (
    <form>
      <textarea disabled={
        status === 'submitting'
      } />
      <br />
      <button disabled={
        status === 'empty' ||
        status === 'submitting'
      }>
        Submit
      </button>
      {status === 'error' &&
        <p className="Error">
          Good guess but a wrong answer. Try again!
        </p>
      }
    </form>
  );
}
```

```css
section { border-bottom: 1px solid #aaa; padding: 20px; }
h4 { color: #222; }
body { margin: 0; }
.Error { color: red; }
```

</Sandpack>

Các trang như thế này thường được gọi là "living styleguides" hoặc "storybooks".

</DeepDive>

### Bước 2: Xác định điều gì kích hoạt các thay đổi trạng thái {/*step-2-determine-what-triggers-those-state-changes*/}

Bạn có thể kích hoạt cập nhật state để phản hồi với hai loại đầu vào:

* **Đầu vào từ người dùng,** như nhấp vào nút, nhập vào trường, điều hướng một liên kết.
* **Đầu vào từ máy tính,** như nhận phản hồi mạng, hoàn thành timeout, tải hình ảnh.

<IllustrationBlock>
  <Illustration caption="Human inputs" alt="A finger." src="/images/docs/illustrations/i_inputs1.png" />
  <Illustration caption="Computer inputs" alt="Ones and zeroes." src="/images/docs/illustrations/i_inputs2.png" />
</IllustrationBlock>

Trong cả hai trường hợp, **bạn phải đặt [biến state](/learn/state-a-components-memory#anatomy-of-usestate) để cập nhật UI.** Đối với form bạn đang phát triển, bạn sẽ cần thay đổi state để phản hồi với một vài đầu vào khác nhau:

* **Thay đổi text input** (người dùng) nên chuyển nó từ trạng thái *Empty* sang trạng thái *Typing* hoặc ngược lại, tùy thuộc vào việc text box có trống hay không.
* **Nhấp vào nút Submit** (người dùng) nên chuyển nó sang trạng thái *Submitting*.
* **Phản hồi mạng thành công** (máy tính) nên chuyển nó sang trạng thái *Success*.
* **Phản hồi mạng thất bại** (máy tính) nên chuyển nó sang trạng thái *Error* với thông báo lỗi tương ứng.

<Note>

Lưu ý rằng đầu vào từ người dùng thường yêu cầu [event handler](/learn/responding-to-events)!

</Note>

Để giúp trực quan hóa luồng này, hãy thử vẽ mỗi trạng thái trên giấy như một vòng tròn có nhãn, và mỗi thay đổi giữa hai trạng thái như một mũi tên. Bạn có thể phác thảo nhiều luồng theo cách này và giải quyết bug trước khi triển khai.

<DiagramGroup>

<Diagram name="responding_to_input_flow" height={350} width={688} alt="Flow chart moving left to right with 5 nodes. The first node labeled 'empty' has one edge labeled 'start typing' connected to a node labeled 'typing'. That node has one edge labeled 'press submit' connected to a node labeled 'submitting', which has two edges. The left edge is labeled 'network error' connecting to a node labeled 'error'. The right edge is labeled 'network success' connecting to a node labeled 'success'.">

Form states

</Diagram>

</DiagramGroup>

### Bước 3: Biểu diễn state trong bộ nhớ bằng `useState` {/*step-3-represent-the-state-in-memory-with-usestate*/}

Tiếp theo bạn sẽ cần biểu diễn các trạng thái trực quan của component trong bộ nhớ bằng [`useState`.](/reference/react/useState) Sự đơn giản là chìa khóa: mỗi phần state là một "mảnh di chuyển", và **bạn muốn càng ít "mảnh di chuyển" càng tốt.** Càng phức tạp dẫn đến càng nhiều bug!

Bắt đầu với state *nhất thiết phải* có ở đó. Ví dụ, bạn sẽ cần lưu trữ `answer` cho input, và `error` (nếu tồn tại) để lưu lỗi cuối cùng:

```js
const [answer, setAnswer] = useState('');
const [error, setError] = useState(null);
```

Sau đó, bạn sẽ cần một biến state biểu diễn trạng thái trực quan nào bạn muốn hiển thị. Thường có nhiều hơn một cách để biểu diễn điều đó trong bộ nhớ, vì vậy bạn sẽ cần thử nghiệm với nó.

Nếu bạn khó nghĩ ra cách tốt nhất ngay lập tức, hãy bắt đầu bằng cách thêm đủ state mà bạn *chắc chắn* tất cả các trạng thái trực quan có thể có đều được bao gồm:

```js
const [isEmpty, setIsEmpty] = useState(true);
const [isTyping, setIsTyping] = useState(false);
const [isSubmitting, setIsSubmitting] = useState(false);
const [isSuccess, setIsSuccess] = useState(false);
const [isError, setIsError] = useState(false);
```

Ý tưởng đầu tiên của bạn có lẽ không phải là tốt nhất, nhưng không sao--tái cấu trúc state là một phần của quá trình!

### Bước 4: Loại bỏ bất kỳ biến state không cần thiết nào {/*step-4-remove-any-non-essential-state-variables*/}

Bạn muốn tránh trùng lặp trong nội dung state để bạn chỉ theo dõi những gì cần thiết. Dành một chút thời gian để tái cấu trúc cấu trúc state của bạn sẽ làm cho các component dễ hiểu hơn, giảm trùng lặp và tránh ý nghĩa không mong muốn. Mục tiêu của bạn là **ngăn các trường hợp mà state trong bộ nhớ không đại diện cho bất kỳ UI hợp lệ nào mà bạn muốn người dùng thấy.** (Ví dụ, bạn không bao giờ muốn hiển thị thông báo lỗi và disable input cùng một lúc, hoặc người dùng sẽ không thể sửa lỗi!)

Dưới đây là một số câu hỏi bạn có thể hỏi về các biến state của mình:

* **State này có gây ra mâu thuẫn không?** Ví dụ, `isTyping` và `isSubmitting` không thể đồng thời là `true`. Một mâu thuẫn thường có nghĩa là state không đủ bị hạn chế. Có bốn kết hợp có thể của hai boolean, nhưng chỉ ba tương ứng với các trạng thái hợp lệ. Để loại bỏ trạng thái "không thể", bạn có thể kết hợp chúng thành một `status` phải là một trong ba giá trị: `'typing'`, `'submitting'`, hoặc `'success'`.
* **Cùng thông tin có sẵn trong một biến state khác không?** Một mâu thuẫn khác: `isEmpty` và `isTyping` không thể đồng thời là `true`. Bằng cách tạo chúng thành các biến state riêng biệt, bạn có nguy cơ chúng bị mất đồng bộ và gây ra bug. May mắn thay, bạn có thể loại bỏ `isEmpty` và thay vào đó kiểm tra `answer.length === 0`.
* **Bạn có thể lấy cùng thông tin từ nghịch đảo của một biến state khác không?** `isError` không cần thiết vì bạn có thể kiểm tra `error !== null` thay thế.

Sau khi dọn dẹp này, bạn còn lại 3 (giảm từ 7!) biến state *cần thiết*:

```js
const [answer, setAnswer] = useState('');
const [error, setError] = useState(null);
const [status, setStatus] = useState('typing'); // 'typing', 'submitting', or 'success'
```

Bạn biết chúng là cần thiết, vì bạn không thể loại bỏ bất kỳ cái nào mà không phá vỡ chức năng.

<DeepDive>

#### Loại bỏ các trạng thái "không thể" bằng reducer {/*eliminating-impossible-states-with-a-reducer*/}

Ba biến này là biểu diễn đủ tốt của state của form này. Tuy nhiên, vẫn còn một số trạng thái trung gian không hoàn toàn có nghĩa. Ví dụ, một `error` không null không có nghĩa khi `status` là `'success'`. Để mô hình hóa state chính xác hơn, bạn có thể [trích xuất nó vào một reducer.](/learn/extracting-state-logic-into-a-reducer) Reducer cho phép bạn hợp nhất nhiều biến state thành một object duy nhất và gom tất cả các logic liên quan!

</DeepDive>

### Bước 5: Kết nối các event handler để đặt state {/*step-5-connect-the-event-handlers-to-set-state*/}

Cuối cùng, tạo các event handler cập nhật state. Dưới đây là form cuối cùng, với tất cả các event handler được kết nối:

<Sandpack>

```js
import { useState } from 'react';

export default function Form() {
  const [answer, setAnswer] = useState('');
  const [error, setError] = useState(null);
  const [status, setStatus] = useState('typing');

  if (status === 'success') {
    return <h1>That's right!</h1>
  }

  async function handleSubmit(e) {
    e.preventDefault();
    setStatus('submitting');
    try {
      await submitForm(answer);
      setStatus('success');
    } catch (err) {
      setStatus('typing');
      setError(err);
    }
  }

  function handleTextareaChange(e) {
    setAnswer(e.target.value);
  }

  return (
    <>
      <h2>City quiz</h2>
      <p>
        In which city is there a billboard that turns air into drinkable water?
      </p>
      <form onSubmit={handleSubmit}>
        <textarea
          value={answer}
          onChange={handleTextareaChange}
          disabled={status === 'submitting'}
        />
        <br />
        <button disabled={
          answer.length === 0 ||
          status === 'submitting'
        }>
          Submit
        </button>
        {error !== null &&
          <p className="Error">
            {error.message}
          </p>
        }
      </form>
    </>
  );
}

function submitForm(answer) {
  // Pretend it's hitting the network.
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      let shouldError = answer.toLowerCase() !== 'lima'
      if (shouldError) {
        reject(new Error('Good guess but a wrong answer. Try again!'));
      } else {
        resolve();
      }
    }, 1500);
  });
}
```

```css
.Error { color: red; }
```

</Sandpack>

Mặc dù code này dài hơn ví dụ mệnh lệnh ban đầu, nhưng nó ít dễ vỡ hơn nhiều. Biểu diễn tất cả các tương tác dưới dạng thay đổi state cho phép bạn sau đó giới thiệu các trạng thái trực quan mới mà không phá vỡ các trạng thái hiện có. Nó cũng cho phép bạn thay đổi những gì nên được hiển thị trong mỗi trạng thái mà không thay đổi logic của chính tương tác.

<Recap>

* Lập trình khai báo có nghĩa là mô tả UI cho mỗi trạng thái trực quan thay vì quản lý vi mô UI (mệnh lệnh).
* Khi phát triển một component:
  1. Xác định tất cả các trạng thái trực quan của nó.
  2. Xác định các trigger từ người dùng và máy tính cho thay đổi state.
  3. Mô hình hóa state bằng `useState`.
  4. Loại bỏ state không cần thiết để tránh bug và mâu thuẫn.
  5. Kết nối các event handler để đặt state.

</Recap>



<Challenges>

#### Thêm và xóa một CSS class {/*add-and-remove-a-css-class*/}

Làm cho việc nhấp vào hình ảnh *xóa* CSS class `background--active` khỏi `<div>` bên ngoài, nhưng *thêm* class `picture--active` vào `<img>`. Nhấp lại vào nền nên khôi phục các CSS class ban đầu.

Về mặt trực quan, bạn nên mong đợi rằng nhấp vào hình ảnh xóa nền tím và làm nổi bật đường viền hình ảnh. Nhấp ra ngoài hình ảnh làm nổi bật nền, nhưng xóa điểm nổi bật đường viền hình ảnh.

<Sandpack>

```js
export default function Picture() {
  return (
    <div className="background background--active">
      <img
        className="picture"
        alt="Rainbow houses in Kampung Pelangi, Indonesia"
        src="https://react.dev/images/docs/scientists/5qwVYb1.jpeg"
      />
    </div>
  );
}
```

```css
body { margin: 0; padding: 0; height: 250px; }

.background {
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #eee;
}

.background--active {
  background: #a6b5ff;
}

.picture {
  width: 200px;
  height: 200px;
  border-radius: 10px;
  border: 5px solid transparent;
}

.picture--active {
  border: 5px solid #a6b5ff;
}
```

</Sandpack>

<Solution>

Component này có hai trạng thái trực quan: khi hình ảnh đang active, và khi hình ảnh không active:

* Khi hình ảnh đang active, các CSS class là `background` và `picture picture--active`.
* Khi hình ảnh không active, các CSS class là `background background--active` và `picture`.

Một biến state boolean duy nhất là đủ để ghi nhớ liệu hình ảnh có đang active không. Nhiệm vụ ban đầu là xóa hoặc thêm CSS class. Tuy nhiên, trong React bạn cần *mô tả* những gì bạn muốn thấy thay vì *thao tác* các phần tử UI. Vì vậy bạn cần tính toán cả hai CSS class dựa trên state hiện tại. Bạn cũng cần [dừng lan truyền](/learn/responding-to-events#stopping-propagation) để nhấp vào hình ảnh không đăng ký như một nhấp chuột vào nền.

Xác minh rằng phiên bản này hoạt động bằng cách nhấp vào hình ảnh và sau đó ra ngoài:

<Sandpack>

```js
import { useState } from 'react';

export default function Picture() {
  const [isActive, setIsActive] = useState(false);

  let backgroundClassName = 'background';
  let pictureClassName = 'picture';
  if (isActive) {
    pictureClassName += ' picture--active';
  } else {
    backgroundClassName += ' background--active';
  }

  return (
    <div
      className={backgroundClassName}
      onClick={() => setIsActive(false)}
    >
      <img
        onClick={e => {
          e.stopPropagation();
          setIsActive(true);
        }}
        className={pictureClassName}
        alt="Rainbow houses in Kampung Pelangi, Indonesia"
        src="https://react.dev/images/docs/scientists/5qwVYb1.jpeg"
      />
    </div>
  );
}
```

```css
body { margin: 0; padding: 0; height: 250px; }

.background {
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #eee;
}

.background--active {
  background: #a6b5ff;
}

.picture {
  width: 200px;
  height: 200px;
  border-radius: 10px;
  border: 5px solid transparent;
}

.picture--active {
  border: 5px solid #a6b5ff;
}
```

</Sandpack>

Ngoài ra, bạn có thể trả về hai đoạn JSX riêng biệt:

<Sandpack>

```js
import { useState } from 'react';

export default function Picture() {
  const [isActive, setIsActive] = useState(false);
  if (isActive) {
    return (
      <div
        className="background"
        onClick={() => setIsActive(false)}
      >
        <img
          className="picture picture--active"
          alt="Rainbow houses in Kampung Pelangi, Indonesia"
          src="https://react.dev/images/docs/scientists/5qwVYb1.jpeg"
          onClick={e => e.stopPropagation()}
        />
      </div>
    );
  }
  return (
    <div className="background background--active">
      <img
        className="picture"
        alt="Rainbow houses in Kampung Pelangi, Indonesia"
        src="https://react.dev/images/docs/scientists/5qwVYb1.jpeg"
        onClick={() => setIsActive(true)}
      />
    </div>
  );
}
```

```css
body { margin: 0; padding: 0; height: 250px; }

.background {
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #eee;
}

.background--active {
  background: #a6b5ff;
}

.picture {
  width: 200px;
  height: 200px;
  border-radius: 10px;
  border: 5px solid transparent;
}

.picture--active {
  border: 5px solid #a6b5ff;
}
```

</Sandpack>

Hãy nhớ rằng nếu hai đoạn JSX khác nhau mô tả cùng một cây, cấu trúc lồng nhau của chúng (phần tử `<div>` đầu tiên → `<img>` đầu tiên) phải thẳng hàng. Nếu không, việc chuyển đổi `isActive` sẽ tạo lại toàn bộ cây bên dưới và [reset state của nó.](/learn/preserving-and-resetting-state) Đây là lý do tại sao, nếu một cây JSX tương tự được trả về trong cả hai trường hợp, tốt hơn là viết chúng như một đoạn JSX duy nhất.

</Solution>

#### Trình chỉnh sửa hồ sơ {/*profile-editor*/}

Đây là một form nhỏ được triển khai bằng JavaScript và DOM thuần túy. Hãy thử nó để hiểu hành vi của nó:

<Sandpack>

```js src/index.js active
function handleFormSubmit(e) {
  e.preventDefault();
  if (editButton.textContent === 'Edit Profile') {
    editButton.textContent = 'Save Profile';
    hide(firstNameText);
    hide(lastNameText);
    show(firstNameInput);
    show(lastNameInput);
  } else {
    editButton.textContent = 'Edit Profile';
    hide(firstNameInput);
    hide(lastNameInput);
    show(firstNameText);
    show(lastNameText);
  }
}

function handleFirstNameChange() {
  firstNameText.textContent = firstNameInput.value;
  helloText.textContent = (
    'Hello ' +
    firstNameInput.value + ' ' +
    lastNameInput.value + '!'
  );
}

function handleLastNameChange() {
  lastNameText.textContent = lastNameInput.value;
  helloText.textContent = (
    'Hello ' +
    firstNameInput.value + ' ' +
    lastNameInput.value + '!'
  );
}

function hide(el) {
  el.style.display = 'none';
}

function show(el) {
  el.style.display = '';
}

let form = document.getElementById('form');
let editButton = document.getElementById('editButton');
let firstNameInput = document.getElementById('firstNameInput');
let firstNameText = document.getElementById('firstNameText');
let lastNameInput = document.getElementById('lastNameInput');
let lastNameText = document.getElementById('lastNameText');
let helloText = document.getElementById('helloText');
form.onsubmit = handleFormSubmit;
firstNameInput.oninput = handleFirstNameChange;
lastNameInput.oninput = handleLastNameChange;
```

```js sandbox.config.json hidden
{
  "hardReloadOnChange": true
}
```

```html public/index.html
<form id="form">
  <label>
    First name:
    <b id="firstNameText">Jane</b>
    <input
      id="firstNameInput"
      value="Jane"
      style="display: none">
  </label>
  <label>
    Last name:
    <b id="lastNameText">Jacobs</b>
    <input
      id="lastNameInput"
      value="Jacobs"
      style="display: none">
  </label>
  <button type="submit" id="editButton">Edit Profile</button>
  <p><i id="helloText">Hello, Jane Jacobs!</i></p>
</form>

<style>
* { box-sizing: border-box; }
body { font-family: sans-serif; margin: 20px; padding: 0; }
label { display: block; margin-bottom: 20px; }
</style>
```

</Sandpack>

Form này chuyển đổi giữa hai chế độ: ở chế độ chỉnh sửa, bạn thấy các input, và ở chế độ xem, bạn chỉ thấy kết quả. Nhãn nút thay đổi giữa "Edit" và "Save" tùy thuộc vào chế độ bạn đang ở. Khi bạn thay đổi các input, thông báo chào đón ở dưới cùng cập nhật theo thời gian thực.

Nhiệm vụ của bạn là triển khai lại nó trong React trong sandbox bên dưới. Để thuận tiện cho bạn, markup đã được chuyển đổi thành JSX, nhưng bạn sẽ cần làm cho nó hiển thị và ẩn các input như bản gốc.

Đảm bảo rằng nó cũng cập nhật văn bản ở dưới cùng!

<Sandpack>

```js
export default function EditProfile() {
  return (
    <form>
      <label>
        First name:{' '}
        <b>Jane</b>
        <input />
      </label>
      <label>
        Last name:{' '}
        <b>Jacobs</b>
        <input />
      </label>
      <button type="submit">
        Edit Profile
      </button>
      <p><i>Hello, Jane Jacobs!</i></p>
    </form>
  );
}
```

```css
label { display: block; margin-bottom: 20px; }
```

</Sandpack>

<Solution>

Bạn sẽ cần hai biến state để giữ các giá trị input: `firstName` và `lastName`. Bạn cũng sẽ cần một biến state `isEditing` giữ thông tin về việc có hiển thị các input hay không. Bạn _không_ cần biến `fullName` vì tên đầy đủ luôn có thể được tính từ `firstName` và `lastName`.

Cuối cùng, bạn nên sử dụng [conditional rendering](/learn/conditional-rendering) để hiển thị hoặc ẩn các input tùy thuộc vào `isEditing`.

<Sandpack>

```js
import { useState } from 'react';

export default function EditProfile() {
  const [isEditing, setIsEditing] = useState(false);
  const [firstName, setFirstName] = useState('Jane');
  const [lastName, setLastName] = useState('Jacobs');

  return (
    <form onSubmit={e => {
      e.preventDefault();
      setIsEditing(!isEditing);
    }}>
      <label>
        First name:{' '}
        {isEditing ? (
          <input
            value={firstName}
            onChange={e => {
              setFirstName(e.target.value)
            }}
          />
        ) : (
          <b>{firstName}</b>
        )}
      </label>
      <label>
        Last name:{' '}
        {isEditing ? (
          <input
            value={lastName}
            onChange={e => {
              setLastName(e.target.value)
            }}
          />
        ) : (
          <b>{lastName}</b>
        )}
      </label>
      <button type="submit">
        {isEditing ? 'Save' : 'Edit'} Profile
      </button>
      <p><i>Hello, {firstName} {lastName}!</i></p>
    </form>
  );
}
```

```css
label { display: block; margin-bottom: 20px; }
```

</Sandpack>

So sánh giải pháp này với code mệnh lệnh ban đầu. Chúng khác nhau như thế nào?

</Solution>

#### Tái cấu trúc giải pháp mệnh lệnh không có React {/*refactor-the-imperative-solution-without-react*/}

Đây là sandbox ban đầu từ thách thức trước, được viết theo kiểu mệnh lệnh không có React:

<Sandpack>

```js src/index.js active
function handleFormSubmit(e) {
  e.preventDefault();
  if (editButton.textContent === 'Edit Profile') {
    editButton.textContent = 'Save Profile';
    hide(firstNameText);
    hide(lastNameText);
    show(firstNameInput);
    show(lastNameInput);
  } else {
    editButton.textContent = 'Edit Profile';
    hide(firstNameInput);
    hide(lastNameInput);
    show(firstNameText);
    show(lastNameText);
  }
}

function handleFirstNameChange() {
  firstNameText.textContent = firstNameInput.value;
  helloText.textContent = (
    'Hello ' +
    firstNameInput.value + ' ' +
    lastNameInput.value + '!'
  );
}

function handleLastNameChange() {
  lastNameText.textContent = lastNameInput.value;
  helloText.textContent = (
    'Hello ' +
    firstNameInput.value + ' ' +
    lastNameInput.value + '!'
  );
}

function hide(el) {
  el.style.display = 'none';
}

function show(el) {
  el.style.display = '';
}

let form = document.getElementById('form');
let editButton = document.getElementById('editButton');
let firstNameInput = document.getElementById('firstNameInput');
let firstNameText = document.getElementById('firstNameText');
let lastNameInput = document.getElementById('lastNameInput');
let lastNameText = document.getElementById('lastNameText');
let helloText = document.getElementById('helloText');
form.onsubmit = handleFormSubmit;
firstNameInput.oninput = handleFirstNameChange;
lastNameInput.oninput = handleLastNameChange;
```

```js sandbox.config.json hidden
{
  "hardReloadOnChange": true
}
```

```html public/index.html
<form id="form">
  <label>
    First name:
    <b id="firstNameText">Jane</b>
    <input
      id="firstNameInput"
      value="Jane"
      style="display: none">
  </label>
  <label>
    Last name:
    <b id="lastNameText">Jacobs</b>
    <input
      id="lastNameInput"
      value="Jacobs"
      style="display: none">
  </label>
  <button type="submit" id="editButton">Edit Profile</button>
  <p><i id="helloText">Hello, Jane Jacobs!</i></p>
</form>

<style>
* { box-sizing: border-box; }
body { font-family: sans-serif; margin: 20px; padding: 0; }
label { display: block; margin-bottom: 20px; }
</style>
```

</Sandpack>

Hãy tưởng tượng React không tồn tại. Bạn có thể tái cấu trúc code này theo cách làm cho logic ít dễ vỡ hơn và giống với phiên bản React hơn không? Nó sẽ trông như thế nào nếu state là rõ ràng, như trong React?

Nếu bạn đang gặp khó khăn khi nghĩ bắt đầu từ đâu, stub bên dưới đã có hầu hết cấu trúc tại chỗ. Nếu bạn bắt đầu ở đây, hãy điền vào logic còn thiếu trong hàm `updateDOM`. (Tham khảo code gốc khi cần.)

<Sandpack>

```js src/index.js active
let firstName = 'Jane';
let lastName = 'Jacobs';
let isEditing = false;

function handleFormSubmit(e) {
  e.preventDefault();
  setIsEditing(!isEditing);
}

function handleFirstNameChange(e) {
  setFirstName(e.target.value);
}

function handleLastNameChange(e) {
  setLastName(e.target.value);
}

function setFirstName(value) {
  firstName = value;
  updateDOM();
}

function setLastName(value) {
  lastName = value;
  updateDOM();
}

function setIsEditing(value) {
  isEditing = value;
  updateDOM();
}

function updateDOM() {
  if (isEditing) {
    editButton.textContent = 'Save Profile';
    // TODO: show inputs, hide content
  } else {
    editButton.textContent = 'Edit Profile';
    // TODO: hide inputs, show content
  }
  // TODO: update text labels
}

function hide(el) {
  el.style.display = 'none';
}

function show(el) {
  el.style.display = '';
}

let form = document.getElementById('form');
let editButton = document.getElementById('editButton');
let firstNameInput = document.getElementById('firstNameInput');
let firstNameText = document.getElementById('firstNameText');
let lastNameInput = document.getElementById('lastNameInput');
let lastNameText = document.getElementById('lastNameText');
let helloText = document.getElementById('helloText');
form.onsubmit = handleFormSubmit;
firstNameInput.oninput = handleFirstNameChange;
lastNameInput.oninput = handleLastNameChange;
```

```js sandbox.config.json hidden
{
  "hardReloadOnChange": true
}
```

```html public/index.html
<form id="form">
  <label>
    First name:
    <b id="firstNameText">Jane</b>
    <input
      id="firstNameInput"
      value="Jane"
      style="display: none">
  </label>
  <label>
    Last name:
    <b id="lastNameText">Jacobs</b>
    <input
      id="lastNameInput"
      value="Jacobs"
      style="display: none">
  </label>
  <button type="submit" id="editButton">Edit Profile</button>
  <p><i id="helloText">Hello, Jane Jacobs!</i></p>
</form>

<style>
* { box-sizing: border-box; }
body { font-family: sans-serif; margin: 20px; padding: 0; }
label { display: block; margin-bottom: 20px; }
</style>
```

</Sandpack>

<Solution>

Logic còn thiếu bao gồm việc chuyển đổi hiển thị của các input và nội dung, và cập nhật các nhãn:

<Sandpack>

```js src/index.js active
let firstName = 'Jane';
let lastName = 'Jacobs';
let isEditing = false;

function handleFormSubmit(e) {
  e.preventDefault();
  setIsEditing(!isEditing);
}

function handleFirstNameChange(e) {
  setFirstName(e.target.value);
}

function handleLastNameChange(e) {
  setLastName(e.target.value);
}

function setFirstName(value) {
  firstName = value;
  updateDOM();
}

function setLastName(value) {
  lastName = value;
  updateDOM();
}

function setIsEditing(value) {
  isEditing = value;
  updateDOM();
}

function updateDOM() {
  if (isEditing) {
    editButton.textContent = 'Save Profile';
    hide(firstNameText);
    hide(lastNameText);
    show(firstNameInput);
    show(lastNameInput);
  } else {
    editButton.textContent = 'Edit Profile';
    hide(firstNameInput);
    hide(lastNameInput);
    show(firstNameText);
    show(lastNameText);
  }
  firstNameText.textContent = firstName;
  lastNameText.textContent = lastName;
  helloText.textContent = (
    'Hello ' +
    firstName + ' ' +
    lastName + '!'
  );
}

function hide(el) {
  el.style.display = 'none';
}

function show(el) {
  el.style.display = '';
}

let form = document.getElementById('form');
let editButton = document.getElementById('editButton');
let firstNameInput = document.getElementById('firstNameInput');
let firstNameText = document.getElementById('firstNameText');
let lastNameInput = document.getElementById('lastNameInput');
let lastNameText = document.getElementById('lastNameText');
let helloText = document.getElementById('helloText');
form.onsubmit = handleFormSubmit;
firstNameInput.oninput = handleFirstNameChange;
lastNameInput.oninput = handleLastNameChange;
```

```js sandbox.config.json hidden
{
  "hardReloadOnChange": true
}
```

```html public/index.html
<form id="form">
  <label>
    First name:
    <b id="firstNameText">Jane</b>
    <input
      id="firstNameInput"
      value="Jane"
      style="display: none">
  </label>
  <label>
    Last name:
    <b id="lastNameText">Jacobs</b>
    <input
      id="lastNameInput"
      value="Jacobs"
      style="display: none">
  </label>
  <button type="submit" id="editButton">Edit Profile</button>
  <p><i id="helloText">Hello, Jane Jacobs!</i></p>
</form>

<style>
* { box-sizing: border-box; }
body { font-family: sans-serif; margin: 20px; padding: 0; }
label { display: block; margin-bottom: 20px; }
</style>
```

</Sandpack>

Hàm `updateDOM` bạn viết cho thấy những gì React thực hiện bên dưới khi bạn đặt state. (Tuy nhiên, React cũng tránh chạm vào DOM cho các thuộc tính chưa thay đổi kể từ lần cuối chúng được đặt.)

</Solution>

</Challenges>
