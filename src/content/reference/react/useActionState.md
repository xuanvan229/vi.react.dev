---
title: useActionState
---

<Intro>

`useActionState` la mot React Hook cho phep ban cap nhat state voi cac tac dung phu su dung [Action](/reference/react/useTransition#functions-called-in-starttransition-are-called-actions).

```js
const [state, dispatchAction, isPending] = useActionState(reducerAction, initialState, permalink?);
```

</Intro>

<InlineToc />

---

## Tham khao {/*reference*/}

### `useActionState(reducerAction, initialState, permalink?)` {/*useactionstate*/}

Goi `useActionState` o cap cao nhat cua component de tao state cho ket qua cua mot Action.

```js
import { useActionState } from 'react';

function reducerAction(previousState, actionPayload) {
  // ...
}

function MyCart({initialState}) {
  const [state, dispatchAction, isPending] = useActionState(reducerAction, initialState);
  // ...
}
```

[Xem them cac vi du ben duoi.](#usage)

#### Tham so {/*parameters*/}

* `reducerAction`: Ham duoc goi khi Action duoc kich hoat. Khi duoc goi, no nhan state truoc do (ban dau la `initialState` ban da cung cap, sau do la gia tri tra ve truoc do) lam doi so dau tien, tiep theo la `actionPayload` duoc truyen vao `dispatchAction`.
* `initialState`: Gia tri ban muon state ban dau la. React se bo qua doi so nay sau khi `dispatchAction` duoc goi lan dau tien.
* **tuy chon** `permalink`: Mot chuoi chua URL trang duy nhat ma form nay thay doi.
  * De su dung tren cac trang voi [React Server Components](/reference/rsc/server-components) voi progressive enhancement.
  * Neu `reducerAction` la mot [Server Function](/reference/rsc/server-functions) va form duoc gui truoc khi JavaScript bundle duoc tai, trinh duyet se dieu huong den URL permalink da chi dinh thay vi URL cua trang hien tai.

#### Gia tri tra ve {/*returns*/}

`useActionState` tra ve mot mang voi dung ba gia tri:

1. State hien tai. Trong lan render dau tien, no se khop voi `initialState` ban da truyen. Sau khi `dispatchAction` duoc goi, no se khop voi gia tri duoc tra ve boi `reducerAction`.
2. Mot ham `dispatchAction` ma ban goi ben trong [Action](/reference/react/useTransition#functions-called-in-starttransition-are-called-actions).
3. Co `isPending` cho ban biet lieu co bat ky Action nao da dispatch cho Hook nay dang cho xu ly hay khong.

#### Luu y {/*caveats*/}

* `useActionState` la mot Hook, vi vay ban chi co the goi no **o cap cao nhat cua component** hoac cac Hook cua rieng ban. Ban khong the goi no ben trong vong lap hoac dieu kien. Neu ban can dieu do, hay trich xuat mot component moi va chuyen state vao do.
* React xep hang doi va thuc thi nhieu loi goi den `dispatchAction` tuan tu. Moi loi goi den `reducerAction` nhan ket qua cua loi goi truoc do.
* Ham `dispatchAction` co identity on dinh, vi vay ban se thuong thay no bi bo qua khoi cac dependency cua Effect, nhung bao gom no se khong gay ra Effect chay lai. Neu linter cho phep ban bo qua mot dependency ma khong co loi, thi viec do la an toan. [Tim hieu them ve viec loai bo cac dependency cua Effect.](/learn/removing-effect-dependencies#move-dynamic-objects-and-functions-inside-your-effect)
* Khi su dung tuy chon `permalink`, dam bao cung component form duoc render tren trang dich (bao gom cung `reducerAction` va `permalink`) de React biet cach truyen state. Khi trang tro nen tuong tac, tham so nay khong con tac dung.
* Khi su dung Server Functions, `initialState` can phai [serializable](/reference/rsc/use-server#serializable-parameters-and-return-values) (cac gia tri nhu object don gian, mang, chuoi va so).
* Neu `dispatchAction` nem loi, React huy tat ca cac action da xep hang doi va hien thi [Error Boundary](/reference/react/Component#catching-rendering-errors-with-an-error-boundary) gan nhat.
* Neu co nhieu Action dang dien ra, React gop chung lai voi nhau. Day la mot han che co the duoc go bo trong ban phat hanh tuong lai.

<Note>

`dispatchAction` phai duoc goi tu mot Action.

Ban co the boc no trong [`startTransition`](/reference/react/startTransition), hoac truyen no cho mot [Action prop](/reference/react/useTransition#exposing-action-props-from-components). Cac loi goi ben ngoai pham vi do se khong duoc xu ly nhu mot phan cua Transition va [ghi log loi](#async-function-outside-transition) o che do development.

</Note>

---

### Ham `reducerAction` {/*reduceraction*/}

Ham `reducerAction` duoc truyen vao `useActionState` nhan state truoc do va tra ve state moi.

Khong giong nhu reducer trong `useReducer`, `reducerAction` co the la async va thuc hien cac tac dung phu:

```js
async function reducerAction(previousState, actionPayload) {
  const newState = await post(actionPayload);
  return newState;
}
```

Moi khi ban goi `dispatchAction`, React goi `reducerAction` voi `actionPayload`. Reducer se thuc hien cac tac dung phu nhu gui du lieu, va tra ve state moi. Neu `dispatchAction` duoc goi nhieu lan, React xep hang doi va thuc thi chung theo thu tu de ket qua cua loi goi truoc do duoc truyen lam `previousState` cho loi goi hien tai.

#### Tham so {/*reduceraction-parameters*/}

* `previousState`: State cuoi cung. Ban dau no bang voi `initialState`. Sau loi goi dau tien den `dispatchAction`, no bang voi state cuoi cung duoc tra ve.

* **tuy chon** `actionPayload`: Doi so duoc truyen vao `dispatchAction`. No co the la gia tri cua bat ky kieu nao. Tuong tu nhu quy uoc cua `useReducer`, no thuong la mot object voi thuoc tinh `type` de xac dinh no va, tuy chon, cac thuoc tinh khac voi thong tin bo sung.

#### Gia tri tra ve {/*reduceraction-returns*/}

`reducerAction` tra ve state moi, va kich hoat mot Transition de render lai voi state do.

#### Luu y {/*reduceraction-caveats*/}

* `reducerAction` co the la dong bo hoac bat dong bo. No co the thuc hien cac hanh dong dong bo nhu hien thi thong bao, hoac cac hanh dong bat dong bo nhu gui cap nhat len server.
* `reducerAction` khong duoc goi hai lan trong `<StrictMode>` vi `reducerAction` duoc thiet ke de cho phep cac tac dung phu.
* Kieu tra ve cua `reducerAction` phai khop voi kieu cua `initialState`. Neu TypeScript suy luan ra su khong khop, ban co the can chu thich ro rang kieu state cua ban.
* Neu ban set state sau `await` trong `reducerAction`, hien tai ban can boc cap nhat state trong mot `startTransition` bo sung. Xem tai lieu [startTransition](/reference/react/useTransition#react-doesnt-treat-my-state-update-after-await-as-a-transition) de biet them.
* Khi su dung Server Functions, `actionPayload` can phai [serializable](/reference/rsc/use-server#serializable-parameters-and-return-values) (cac gia tri nhu object don gian, mang, chuoi va so).

<DeepDive>

#### Tai sao no duoc goi la `reducerAction`? {/*why-is-it-called-reduceraction*/}

Ham duoc truyen vao `useActionState` duoc goi la *reducer action* vi:

- No *reduce* state truoc do thanh state moi, giong nhu `useReducer`.
- No la mot *Action* vi no duoc goi ben trong mot Transition va co the thuc hien cac tac dung phu.

Ve mat khai niem, `useActionState` giong nhu `useReducer`, nhung ban co the thuc hien cac tac dung phu trong reducer.

</DeepDive>

---

## Cach su dung {/*usage*/}

### Them state vao mot Action {/*adding-state-to-an-action*/}

Goi `useActionState` o cap cao nhat cua component de tao state cho ket qua cua mot Action.

```js [[1, 7, "count"], [2, 7, "dispatchAction"], [3, 7, "isPending"]]
import { useActionState } from 'react';

async function addToCartAction(prevCount) {
  // ...
}
function Counter() {
  const [count, dispatchAction, isPending] = useActionState(addToCartAction, 0);

  // ...
}
```

`useActionState` tra ve mot mang voi dung ba muc:

1. <CodeStep step={1}>State hien tai</CodeStep>, ban dau duoc dat thanh state ban dau ban da cung cap.
2. <CodeStep step={2}>Action dispatcher</CodeStep> cho phep ban kich hoat `reducerAction`.
3. Mot <CodeStep step={3}>trang thai cho</CodeStep> cho ban biet lieu Action co dang duoc thuc hien hay khong.

De goi `addToCartAction`, goi <CodeStep step={2}>action dispatcher</CodeStep>. React se xep hang doi cac loi goi den `addToCartAction` voi so luong truoc do.

<Sandpack>

```js src/App.js
import { useActionState, startTransition } from 'react';
import { addToCart } from './api';
import Total from './Total';

export default function Checkout() {
  const [count, dispatchAction, isPending] = useActionState(async (prevCount) => {
    return await addToCart(prevCount)
  }, 0);

  function handleClick() {
    startTransition(() => {
      dispatchAction();
    });
  }

  return (
    <div className="checkout">
      <h2>Checkout</h2>
      <div className="row">
        <span>Eras Tour Tickets</span>
        <span>Qty: {count}</span>
      </div>
      <div className="row">
        <button onClick={handleClick}>Add Ticket{isPending ? ' 🌀' : '  '}</button>
      </div>
      <hr />
      <Total quantity={count} />
    </div>
  );
}
```

```js src/Total.js
const formatter = new Intl.NumberFormat('en-US', {
  style: 'currency',
  currency: 'USD',
  minimumFractionDigits: 0,
});

export default function Total({quantity}) {
  return (
    <div className="row total">
      <span>Total</span>
      <span>{formatter.format(quantity * 9999)}</span>
    </div>
  );
}
```

```js src/api.js
export async function addToCart(count) {
  await new Promise(resolve => setTimeout(resolve, 1000));
  return count + 1;
}

export async function removeFromCart(count) {
  await new Promise(resolve => setTimeout(resolve, 1000));
  return Math.max(0, count - 1);
}
```

```css
.checkout {
  display: flex;
  flex-direction: column;
  gap: 12px;
  padding: 16px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-family: system-ui;
}

.checkout h2 {
  margin: 0 0 8px 0;
}

.row {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.row button {
  margin-left: auto;
  min-width: 150px;
}

.total {
  font-weight: bold;
}

hr {
  width: 100%;
  border: none;
  border-top: 1px solid #ccc;
  margin: 4px 0;
}

button {
  padding: 8px 16px;
  cursor: pointer;
}
```

</Sandpack>

Moi khi ban nhan "Add Ticket," React xep hang doi mot loi goi den `addToCartAction`. React hien thi trang thai cho cho den khi tat ca cac ve duoc them, va sau do render lai voi state cuoi cung.

<DeepDive>

#### Cach xep hang doi cua `useActionState` hoat dong {/*how-useactionstate-queuing-works*/}

Thu nhan "Add Ticket" nhieu lan. Moi khi ban nhan, mot `addToCartAction` moi duoc xep hang doi. Vi co do tre nhan tao 1 giay, dieu do co nghia la 4 lan nhan se mat ~4 giay de hoan thanh.

**Day la co chu dich trong thiet ke cua `useActionState`.**

Chung ta phai doi ket qua truoc do cua `addToCartAction` de truyen `prevCount` cho loi goi tiep theo den `addToCartAction`. Dieu do co nghia la React phai doi Action truoc do hoan thanh truoc khi goi Action tiep theo.

Ban thuong co the giai quyet dieu nay bang cach [su dung voi useOptimistic](/reference/react/useActionState#using-with-useoptimistic) nhung doi voi cac truong hop phuc tap hon ban co the muon xem xet [huy cac action da xep hang doi](#cancelling-queued-actions) hoac khong su dung `useActionState`.

</DeepDive>

---

### Su dung nhieu kieu Action {/*using-multiple-action-types*/}

De xu ly nhieu kieu, ban co the truyen mot doi so cho `dispatchAction`.

Theo quy uoc, thuong viet no nhu mot cau lenh switch. Voi moi truong hop trong switch, tinh toan va tra ve mot state tiep theo nao do. Doi so co the co bat ky hinh dang nao, nhung thuong truyen cac object voi thuoc tinh `type` de xac dinh action.

<Sandpack>

```js src/App.js
import { useActionState, startTransition } from 'react';
import { addToCart, removeFromCart } from './api';
import Total from './Total';

export default function Checkout() {
  const [count, dispatchAction, isPending] = useActionState(updateCartAction, 0);

  function handleAdd() {
    startTransition(() => {
      dispatchAction({ type: 'ADD' });
    });
  }

  function handleRemove() {
    startTransition(() => {
      dispatchAction({ type: 'REMOVE' });
    });
  }

  return (
    <div className="checkout">
      <h2>Checkout</h2>
      <div className="row">
        <span>Eras Tour Tickets</span>
        <span className="stepper">
          <span className="qty">{isPending ? '🌀' : count}</span>
          <span className="buttons">
            <button onClick={handleAdd}>▲</button>
            <button onClick={handleRemove}>▼</button>
          </span>
        </span>
      </div>
      <hr />
      <Total quantity={count} isPending={isPending}/>
    </div>
  );
}

async function updateCartAction(prevCount, actionPayload) {
  switch (actionPayload.type) {
    case 'ADD': {
      return await addToCart(prevCount);
    }
    case 'REMOVE': {
      return await removeFromCart(prevCount);
    }
  }
  return prevCount;
}
```

```js src/Total.js
const formatter = new Intl.NumberFormat('en-US', {
  style: 'currency',
  currency: 'USD',
  minimumFractionDigits: 0,
});

export default function Total({quantity, isPending}) {
  return (
    <div className="row total">
      <span>Total</span>
      {isPending ? '🌀 Updating...' : formatter.format(quantity * 9999)}
    </div>
  );
}
```

```js src/api.js hidden
export async function addToCart(count) {
  await new Promise(resolve => setTimeout(resolve, 1000));
  return count + 1;
}

export async function removeFromCart(count) {
  await new Promise(resolve => setTimeout(resolve, 1000));
  return Math.max(0, count - 1);
}
```

```css
.checkout {
  display: flex;
  flex-direction: column;
  gap: 12px;
  padding: 16px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-family: system-ui;
}

.checkout h2 {
  margin: 0 0 8px 0;
}

.row {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.stepper {
  display: flex;
  align-items: center;
  gap: 8px;
}

.qty {
  min-width: 20px;
  text-align: center;
}

.buttons {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.buttons button {
  padding: 0 8px;
  font-size: 10px;
  line-height: 1.2;
  cursor: pointer;
}

.pending {
  width: 20px;
  text-align: center;
}

.total {
  font-weight: bold;
}

hr {
  width: 100%;
  border: none;
  border-top: 1px solid #ccc;
  margin: 4px 0;
}
```

</Sandpack>

Khi ban nhan de tang hoac giam so luong, mot `"ADD"` hoac `"REMOVE"` duoc dispatch. Trong `reducerAction`, cac API khac nhau duoc goi de cap nhat so luong.

Trong vi du nay, chung ta su dung trang thai cho cua cac Action de thay the ca so luong va tong. Neu ban muon cung cap phan hoi ngay lap tuc, chang han nhu cap nhat so luong ngay lap tuc, ban co the su dung `useOptimistic`.

<DeepDive>

#### `useActionState` khac `useReducer` nhu the nao? {/*useactionstate-vs-usereducer*/}

Ban co the nhan thay vi du nay trang giong `useReducer`, nhung chung phuc vu cac muc dich khac nhau:

- **Su dung `useReducer`** de quan ly state cua UI. Reducer phai la pure.

- **Su dung `useActionState`** de quan ly state cua cac Action. Reducer co the thuc hien cac tac dung phu.

Ban co the nghi ve `useActionState` nhu `useReducer` cho cac tac dung phu tu cac Action cua nguoi dung. Vi no tinh toan Action tiep theo dua tren Action truoc do, no phai [sap xep cac loi goi tuan tu](/reference/react/useActionState#how-useactionstate-queuing-works). Neu ban muon thuc hien cac Action song song, hay su dung `useState` va `useTransition` truc tiep.

</DeepDive>

---

### Su dung voi `useOptimistic` {/*using-with-useoptimistic*/}

Ban co the ket hop `useActionState` voi [`useOptimistic`](/reference/react/useOptimistic) de hien thi phan hoi UI ngay lap tuc:


<Sandpack>

```js src/App.js
import { useActionState, startTransition, useOptimistic } from 'react';
import { addToCart, removeFromCart } from './api';
import Total from './Total';

export default function Checkout() {
  const [count, dispatchAction, isPending] = useActionState(updateCartAction, 0);
  const [optimisticCount, setOptimisticCount] = useOptimistic(count);

  function handleAdd() {
    startTransition(() => {
      setOptimisticCount(c => c + 1);
      dispatchAction({ type: 'ADD' });
    });
  }

  function handleRemove() {
    startTransition(() => {
      setOptimisticCount(c => c - 1);
      dispatchAction({ type: 'REMOVE' });
    });
  }

  return (
    <div className="checkout">
      <h2>Checkout</h2>
      <div className="row">
        <span>Eras Tour Tickets</span>
        <span className="stepper">
          <span className="pending">{isPending && '🌀'}</span>
          <span className="qty">{optimisticCount}</span>
          <span className="buttons">
            <button onClick={handleAdd}>▲</button>
            <button onClick={handleRemove}>▼</button>
          </span>
        </span>
      </div>
      <hr />
      <Total quantity={optimisticCount} isPending={isPending}/>
    </div>
  );
}

async function updateCartAction(prevCount, actionPayload) {
  switch (actionPayload.type) {
    case 'ADD': {
      return await addToCart(prevCount);
    }
    case 'REMOVE': {
      return await removeFromCart(prevCount);
    }
  }
  return prevCount;
}
```

```js src/Total.js
const formatter = new Intl.NumberFormat('en-US', {
  style: 'currency',
  currency: 'USD',
  minimumFractionDigits: 0,
});

export default function Total({quantity, isPending}) {
  return (
    <div className="row total">
      <span>Total</span>
      <span>{isPending ? '🌀 Updating...' : formatter.format(quantity * 9999)}</span>
    </div>
  );
}
```

```js src/api.js hidden
export async function addToCart(count) {
  await new Promise(resolve => setTimeout(resolve, 1000));
  return count + 1;
}

export async function removeFromCart(count) {
  await new Promise(resolve => setTimeout(resolve, 1000));
  return Math.max(0, count - 1);
}
```

```css
.checkout {
  display: flex;
  flex-direction: column;
  gap: 12px;
  padding: 16px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-family: system-ui;
}

.checkout h2 {
  margin: 0 0 8px 0;
}

.row {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.stepper {
  display: flex;
  align-items: center;
  gap: 8px;
}

.qty {
  min-width: 20px;
  text-align: center;
}

.buttons {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.buttons button {
  padding: 0 8px;
  font-size: 10px;
  line-height: 1.2;
  cursor: pointer;
}

.pending {
  width: 20px;
  text-align: center;
}

.total {
  font-weight: bold;
}

hr {
  width: 100%;
  border: none;
  border-top: 1px solid #ccc;
  margin: 4px 0;
}
```

</Sandpack>


`setOptimisticCount` cap nhat so luong ngay lap tuc, va `dispatchAction()` xep hang doi `updateCartAction`. Mot chi bao cho xuat hien tren ca so luong va tong de phan hoi cho nguoi dung rang cap nhat cua ho van dang duoc ap dung.

---


### Su dung voi Action props {/*using-with-action-props*/}

Khi ban truyen ham `dispatchAction` cho mot component hien thi mot [Action prop](/reference/react/useTransition#exposing-action-props-from-components), ban khong can goi `startTransition` hoac `useOptimistic` ban than.

Vi du nay cho thay viec su dung cac prop `increaseAction` va `decreaseAction` cua component QuantityStepper:

<Sandpack>

```js src/App.js
import { useActionState } from 'react';
import { addToCart, removeFromCart } from './api';
import QuantityStepper from './QuantityStepper';
import Total from './Total';

export default function Checkout() {
  const [count, dispatchAction, isPending] = useActionState(updateCartAction, 0);

  function addAction() {
    dispatchAction({type: 'ADD'});
  }

  function removeAction() {
    dispatchAction({type: 'REMOVE'});
  }

  return (
    <div className="checkout">
      <h2>Checkout</h2>
      <div className="row">
        <span>Eras Tour Tickets</span>
        <QuantityStepper
          value={count}
          increaseAction={addAction}
          decreaseAction={removeAction}
        />
      </div>
      <hr />
      <Total quantity={count} isPending={isPending} />
    </div>
  );
}

async function updateCartAction(prevCount, actionPayload) {
  switch (actionPayload.type) {
    case 'ADD': {
      return await addToCart(prevCount);
    }
    case 'REMOVE': {
      return await removeFromCart(prevCount);
    }
  }
  return prevCount;
}
```

```js src/QuantityStepper.js
import { startTransition, useOptimistic } from 'react';

export default function QuantityStepper({value, increaseAction, decreaseAction}) {
  const [optimisticValue, setOptimisticValue] = useOptimistic(value);
  const isPending = value !== optimisticValue;
  function handleIncrease() {
    startTransition(async () => {
      setOptimisticValue(c => c + 1);
      await increaseAction();
    });
  }

  function handleDecrease() {
    startTransition(async () => {
      setOptimisticValue(c => Math.max(0, c - 1));
      await decreaseAction();
    });
  }

  return (
    <span className="stepper">
      <span className="pending">{isPending && '🌀'}</span>
      <span className="qty">{optimisticValue}</span>
      <span className="buttons">
        <button onClick={handleIncrease}>▲</button>
        <button onClick={handleDecrease}>▼</button>
      </span>
    </span>
  );
}
```

```js src/Total.js
const formatter = new Intl.NumberFormat('en-US', {
  style: 'currency',
  currency: 'USD',
  minimumFractionDigits: 0,
});

export default function Total({quantity, isPending}) {
  return (
    <div className="row total">
      <span>Total</span>
      {isPending ? '🌀 Updating...' : formatter.format(quantity * 9999)}
    </div>
  );
}
```

```js src/api.js hidden
export async function addToCart(count) {
  await new Promise(resolve => setTimeout(resolve, 1000));
  return count + 1;
}

export async function removeFromCart(count) {
  await new Promise(resolve => setTimeout(resolve, 1000));
  return Math.max(0, count - 1);
}
```

```css
.checkout {
  display: flex;
  flex-direction: column;
  gap: 12px;
  padding: 16px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-family: system-ui;
}

.checkout h2 {
  margin: 0 0 8px 0;
}

.row {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.stepper {
  display: flex;
  align-items: center;
  gap: 8px;
}

.qty {
  min-width: 20px;
  text-align: center;
}

.buttons {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.buttons button {
  padding: 0 8px;
  font-size: 10px;
  line-height: 1.2;
  cursor: pointer;
}

.pending {
  width: 20px;
  text-align: center;
}

.total {
  font-weight: bold;
}

hr {
  width: 100%;
  border: none;
  border-top: 1px solid #ccc;
  margin: 4px 0;
}
```

</Sandpack>

Vi `<QuantityStepper>` co ho tro san cho transition, trang thai cho, va cap nhat optimistic so luong, ban chi can cho Action biet _can thay doi gi_, va _cach thay doi_ duoc xu ly cho ban.

---

### Huy cac Action da xep hang doi {/*cancelling-queued-actions*/}

Ban co the su dung `AbortController` de huy cac Action dang cho:

<Sandpack>

```js src/App.js
import { useActionState, useRef } from 'react';
import { addToCart, removeFromCart } from './api';
import QuantityStepper from './QuantityStepper';
import Total from './Total';

export default function Checkout() {
  const abortRef = useRef(null);
  const [count, dispatchAction, isPending] = useActionState(updateCartAction, 0);

  async function addAction() {
    if (abortRef.current) {
      abortRef.current.abort();
    }
    abortRef.current = new AbortController();
    await dispatchAction({ type: 'ADD', signal: abortRef.current.signal });
  }

  async function removeAction() {
    if (abortRef.current) {
      abortRef.current.abort();
    }
    abortRef.current = new AbortController();
    await dispatchAction({ type: 'REMOVE', signal: abortRef.current.signal });
  }

  return (
    <div className="checkout">
      <h2>Checkout</h2>
      <div className="row">
        <span>Eras Tour Tickets</span>
        <QuantityStepper
          value={count}
          increaseAction={addAction}
          decreaseAction={removeAction}
        />
      </div>
      <hr />
      <Total quantity={count} isPending={isPending} />
    </div>
  );
}

async function updateCartAction(prevCount, actionPayload) {
  switch (actionPayload.type) {
    case 'ADD': {
      try {
        return await addToCart(prevCount, { signal: actionPayload.signal });
      } catch (e) {
        return prevCount + 1;
      }
    }
    case 'REMOVE': {
      try {
        return await removeFromCart(prevCount, { signal: actionPayload.signal });
      } catch (e) {
        return Math.max(0, prevCount - 1);
      }
    }
  }
  return prevCount;
}
```

```js src/QuantityStepper.js
import { startTransition, useOptimistic } from 'react';

export default function QuantityStepper({value, increaseAction, decreaseAction}) {
  const [optimisticValue, setOptimisticValue] = useOptimistic(value);
  const isPending = value !== optimisticValue;
  function handleIncrease() {
    startTransition(async () => {
      setOptimisticValue(c => c + 1);
      await increaseAction();
    });
  }

  function handleDecrease() {
    startTransition(async () => {
      setOptimisticValue(c => Math.max(0, c - 1));
      await decreaseAction();
    });
  }

  return (
          <span className="stepper">
      <span className="pending">{isPending && '🌀'}</span>
      <span className="qty">{optimisticValue}</span>
      <span className="buttons">
        <button onClick={handleIncrease}>▲</button>
        <button onClick={handleDecrease}>▼</button>
      </span>
    </span>
  );
}
```

```js src/Total.js
const formatter = new Intl.NumberFormat('en-US', {
  style: 'currency',
  currency: 'USD',
  minimumFractionDigits: 0,
});

export default function Total({quantity, isPending}) {
  return (
    <div className="row total">
      <span>Total</span>
      {isPending ? '🌀 Updating...' : formatter.format(quantity * 9999)}
    </div>
  );
}
```

```js src/api.js hidden
class AbortError extends Error {
  name = 'AbortError';
  constructor(message = 'The operation was aborted') {
    super(message);
  }
}

function sleep(ms, signal) {
  if (!signal) return new Promise((resolve) => setTimeout(resolve, ms));
  if (signal.aborted) return Promise.reject(new AbortError());

  return new Promise((resolve, reject) => {
    const id = setTimeout(() => {
      signal.removeEventListener('abort', onAbort);
      resolve();
    }, ms);

    const onAbort = () => {
      clearTimeout(id);
      reject(new AbortError());
    };

    signal.addEventListener('abort', onAbort, { once: true });
  });
}
export async function addToCart(count, opts) {
  await sleep(1000, opts?.signal);
  return count + 1;
}

export async function removeFromCart(count, opts) {
  await sleep(1000, opts?.signal);
  return Math.max(0, count - 1);
}
```

```css
.checkout {
  display: flex;
  flex-direction: column;
  gap: 12px;
  padding: 16px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-family: system-ui;
}

.checkout h2 {
  margin: 0 0 8px 0;
}

.row {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.stepper {
  display: flex;
  align-items: center;
  gap: 8px;
}

.qty {
  min-width: 20px;
  text-align: center;
}

.buttons {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.buttons button {
  padding: 0 8px;
  font-size: 10px;
  line-height: 1.2;
  cursor: pointer;
}

.pending {
  width: 20px;
  text-align: center;
}

.total {
  font-weight: bold;
}

hr {
  width: 100%;
  border: none;
  border-top: 1px solid #ccc;
  margin: 4px 0;
}
```

</Sandpack>

Thu nhan tang hoac giam nhieu lan, va nhan thay tong cap nhat trong vong 1 giay bat ke ban nhan bao nhieu lan. Dieu nay hoat dong vi no su dung `AbortController` de "hoan thanh" Action truoc do de Action tiep theo co the tien hanh.

<Pitfall>

Huy mot Action khong phai luc nao cung an toan.

Vi du, neu Action thuc hien mot mutation (nhu ghi vao co so du lieu), huy yeu cau mang khong hoan tac thay doi phia server. Day la ly do `useActionState` khong huy mac dinh. Chi an toan khi ban biet tac dung phu co the duoc bo qua hoac thu lai mot cach an toan.

</Pitfall>

---

### Su dung voi Action props cua `<form>` {/*use-with-a-form*/}

Ban co the truyen ham `dispatchAction` lam prop `action` cho `<form>`.

Khi su dung theo cach nay, React tu dong boc viec gui trong mot Transition, vi vay ban khong can goi `startTransition` ban than. `reducerAction` nhan state truoc do va `FormData` da gui:

<Sandpack>

```js src/App.js
import { useActionState, useOptimistic } from 'react';
import { addToCart, removeFromCart } from './api';
import Total from './Total';

export default function Checkout() {
  const [count, dispatchAction, isPending] = useActionState(updateCartAction, 0);
  const [optimisticCount, setOptimisticCount] = useOptimistic(count);

  async function formAction(formData) {
    const type = formData.get('type');
    if (type === 'ADD') {
      setOptimisticCount(c => c + 1);
    } else {
      setOptimisticCount(c => Math.max(0, c - 1));
    }
    return dispatchAction(formData);
  }

  return (
    <form action={formAction} className="checkout">
      <h2>Checkout</h2>
      <div className="row">
        <span>Eras Tour Tickets</span>
        <span className="stepper">
          <span className="pending">{isPending && '🌀'}</span>
          <span className="qty">{optimisticCount}</span>
          <span className="buttons">
            <button type="submit" name="type" value="ADD">▲</button>
            <button type="submit" name="type" value="REMOVE">▼</button>
          </span>
        </span>
      </div>
      <hr />
      <Total quantity={count} isPending={isPending} />
    </form>
  );
}

async function updateCartAction(prevCount, formData) {
  const type = formData.get('type');
  switch (type) {
    case 'ADD': {
      return await addToCart(prevCount);
    }
    case 'REMOVE': {
      return await removeFromCart(prevCount);
    }
  }
  return prevCount;
}
```

```js src/Total.js
const formatter = new Intl.NumberFormat('en-US', {
  style: 'currency',
  currency: 'USD',
  minimumFractionDigits: 0,
});

export default function Total({quantity, isPending}) {
  return (
    <div className="row total">
      <span>Total</span>
      {isPending ? '🌀 Updating...' : formatter.format(quantity * 9999)}
    </div>
  );
}
```

```js src/api.js hidden
export async function addToCart(count) {
  await new Promise(resolve => setTimeout(resolve, 1000));
  return count + 1;
}

export async function removeFromCart(count) {
  await new Promise(resolve => setTimeout(resolve, 1000));
  return Math.max(0, count - 1);
}
```

```css
.checkout {
  display: flex;
  flex-direction: column;
  gap: 12px;
  padding: 16px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-family: system-ui;
}

.checkout h2 {
  margin: 0 0 8px 0;
}

.row {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.stepper {
  display: flex;
  align-items: center;
  gap: 8px;
}

.qty {
  min-width: 20px;
  text-align: center;
}

.buttons {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.buttons button {
  padding: 0 8px;
  font-size: 10px;
  line-height: 1.2;
  cursor: pointer;
}

.pending {
  width: 20px;
  text-align: center;
}

.total {
  font-weight: bold;
}

hr {
  width: 100%;
  border: none;
  border-top: 1px solid #ccc;
  margin: 4px 0;
}
```

</Sandpack>

Trong vi du nay, khi nguoi dung nhan cac mui ten stepper, nut gui form va `useActionState` goi `updateCartAction` voi du lieu form. Vi du su dung `useOptimistic` de hien thi so luong moi ngay lap tuc trong khi server xac nhan cap nhat.

<RSC>

Khi su dung voi mot [Server Function](/reference/rsc/server-functions), `useActionState` cho phep phan hoi cua server duoc hien thi truoc khi hydration (khi React gan vao HTML duoc render phia server) hoan thanh. Ban cung co the su dung tham so `permalink` tuy chon cho progressive enhancement (cho phep form hoat dong truoc khi JavaScript tai) tren cac trang co noi dung dong. Dieu nay thuong duoc framework xu ly cho ban.

</RSC>

Xem tai lieu [`<form>`](/reference/react-dom/components/form#handle-form-submission-with-a-server-function) de biet them ve viec su dung Action voi form.

---

### Xu ly loi {/*handling-errors*/}

Co hai cach xu ly loi voi `useActionState`.

Doi voi cac loi da biet, chang han nhu loi xac thuc "so luong khong kha dung" tu backend cua ban, ban co the tra ve no nhu mot phan cua state `reducerAction` va hien thi no trong UI.

Doi voi cac loi khong biet, chang han nhu `undefined is not a function`, ban co the nem loi. React se huy tat ca cac Action da xep hang doi va hien thi [Error Boundary](/reference/react/Component#catching-rendering-errors-with-an-error-boundary) gan nhat bang cach nem lai loi tu hook `useActionState`.

<Sandpack>

```js src/App.js
import {useActionState, startTransition} from 'react';
import {ErrorBoundary} from 'react-error-boundary';
import {addToCart} from './api';
import Total from './Total';

function Checkout() {
  const [state, dispatchAction, isPending] = useActionState(
    async (prevState, quantity) => {
      const result = await addToCart(prevState.count, quantity);
      if (result.error) {
        // Tra ve loi tu API nhu state
        return {...prevState, error: `Could not add quanitiy ${quantity}: ${result.error}`};
      }

      if (!isPending) {
        // Xoa trang thai loi cho dispatch dau tien.
        return {count: result.count, error: null};
      }

      // Tra ve so luong moi, va bat ky loi nao da xay ra.
      return {count: result.count, error: prevState.error};


    },
    {
      count: 0,
      error: null,
    }
  );

  function handleAdd(quantity) {
    startTransition(() => {
      dispatchAction(quantity);
    });
  }

  return (
    <div className="checkout">
      <h2>Checkout</h2>
      <div className="row">
        <span>Eras Tour Tickets</span>
        <span>
          {isPending && '🌀 '}Qty: {state.count}
        </span>
      </div>
      <div className="buttons">
        <button onClick={() => handleAdd(1)}>Add 1</button>
        <button onClick={() => handleAdd(10)}>Add 10</button>
        <button onClick={() => handleAdd(NaN)}>Add NaN</button>
      </div>
      {state.error && <div className="error">{state.error}</div>}
      <hr />
      <Total quantity={state.count} isPending={isPending} />
    </div>
  );
}



export default function App() {
  return (
    <ErrorBoundary
      fallbackRender={({resetErrorBoundary}) => (
        <div className="checkout">
          <h2>Something went wrong</h2>
          <p>The action could not be completed.</p>
          <button onClick={resetErrorBoundary}>Try again</button>
        </div>
      )}>
      <Checkout />
    </ErrorBoundary>
  );
}
```

```js src/Total.js
const formatter = new Intl.NumberFormat('en-US', {
  style: 'currency',
  currency: 'USD',
  minimumFractionDigits: 0,
});

export default function Total({quantity, isPending}) {
  return (
    <div className="row total">
      <span>Total</span>
      <span>
        {isPending ? '🌀 Updating...' : formatter.format(quantity * 9999)}
      </span>
    </div>
  );
}
```

```js src/api.js hidden
export async function addToCart(count, quantity) {
  await new Promise((resolve) => setTimeout(resolve, 1000));
  if (quantity > 5) {
    return {error: 'Quantity not available'};
  } else if (isNaN(quantity)) {
    throw new Error('Quantity must be a number');
  }
  return {count: count + quantity};
}
```

```css
.checkout {
  display: flex;
  flex-direction: column;
  gap: 12px;
  padding: 16px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-family: system-ui;
}

.checkout h2 {
  margin: 0 0 8px 0;
}

.row {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.total {
  font-weight: bold;
}

hr {
  width: 100%;
  border: none;
  border-top: 1px solid #ccc;
  margin: 4px 0;
}

button {
  padding: 8px 16px;
  cursor: pointer;
}

.buttons {
  display: flex;
  gap: 8px;
}

.error {
  color: red;
  font-size: 14px;
}
```

```json package.json hidden
{
  "dependencies": {
    "react": "19.0.0",
    "react-dom": "19.0.0",
    "react-scripts": "^5.0.0",
    "react-error-boundary": "4.0.3"
  },
  "main": "/index.js"
}
```

</Sandpack>

Trong vi du nay, "Add 10" mo phong mot API tra ve loi xac thuc, ma `updateCartAction` luu trong state va hien thi inline. "Add NaN" dan den so luong khong hop le, vi vay `updateCartAction` nem loi, truyen qua `useActionState` den `ErrorBoundary` va hien thi UI dat lai.


---

## Xu ly su co {/*troubleshooting*/}

### Co `isPending` cua toi khong cap nhat {/*ispending-not-updating*/}

Neu ban dang goi `dispatchAction` thu cong (khong thong qua Action prop), hay dam bao ban boc loi goi trong [`startTransition`](/reference/react/startTransition):

```js
import { useActionState, startTransition } from 'react';

function MyComponent() {
  const [state, dispatchAction, isPending] = useActionState(myAction, null);

  function handleClick() {
    // ✅ Dung: boc trong startTransition
    startTransition(() => {
      dispatchAction();
    });
  }

  // ...
}
```

Khi `dispatchAction` duoc truyen cho mot Action prop, React tu dong boc no trong mot Transition.

---

### Action cua toi khong the doc du lieu form {/*action-cannot-read-form-data*/}

Khi ban su dung `useActionState`, `reducerAction` nhan mot doi so bo sung lam doi so dau tien: state truoc do hoac state ban dau. Du lieu form da gui do do la doi so thu hai thay vi doi so dau tien.

```js {2,7}
// Khong co useActionState
function action(formData) {
  const name = formData.get('name');
}

// Voi useActionState
function action(prevState, formData) {
  const name = formData.get('name');
}
```

---

### Cac action cua toi bi bo qua {/*actions-skipped*/}

Neu ban goi `dispatchAction` nhieu lan va mot so trong do khong chay, co the la vi mot loi goi `dispatchAction` truoc do da nem loi.

Khi mot `reducerAction` nem loi, React bo qua tat ca cac loi goi `dispatchAction` da xep hang doi sau do.

De xu ly dieu nay, bat loi ben trong `reducerAction` va tra ve state loi thay vi nem:

```js
async function myReducerAction(prevState, data) {
  try {
    const result = await submitData(data);
    return { success: true, data: result };
  } catch (error) {
    // ✅ Tra ve state loi thay vi nem
    return { success: false, error: error.message };
  }
}
```

---

### State cua toi khong dat lai {/*reset-state*/}

`useActionState` khong cung cap ham dat lai san co. De dat lai state, ban co the thiet ke `reducerAction` de xu ly tin hieu dat lai:

```js
const initialState = { name: '', error: null };

async function formAction(prevState, payload) {
  // Xu ly dat lai
  if (payload === null) {
    return initialState;
  }
  // Logic action binh thuong
  const result = await submitData(payload);
  return result;
}

function MyComponent() {
  const [state, dispatchAction, isPending] = useActionState(formAction, initialState);

  function handleReset() {
    startTransition(() => {
      dispatchAction(null); // Truyen null de kich hoat dat lai
    });
  }

  // ...
}
```

Ngoai ra, ban co the them prop `key` cho component su dung `useActionState` de buoc no mount lai voi state moi, hoac prop `action` cua `<form>`, tu dong dat lai sau khi gui.

---

### Toi gap loi: "An async function with useActionState was called outside of a transition." {/*async-function-outside-transition*/}

Mot loi thuong gap la quen goi `dispatchAction` tu ben trong mot Transition:

<ConsoleBlockMulti>
<ConsoleLogLine level="error">

An async function with useActionState was called outside of a transition. This is likely not what you intended (for example, isPending will not update correctly). Either call the returned function inside startTransition, or pass it to an `action` or `formAction` prop.

</ConsoleLogLine>
</ConsoleBlockMulti>


Loi nay xay ra vi `dispatchAction` phai chay ben trong mot Transition:

```js
function MyComponent() {
  const [state, dispatchAction, isPending] = useActionState(myAsyncAction, null);

  function handleClick() {
    // ❌ Sai: goi dispatchAction ben ngoai Transition
    dispatchAction();
  }

  // ...
}
```

De sua, hoac boc loi goi trong [`startTransition`](/reference/react/startTransition):

```js
import { useActionState, startTransition } from 'react';

function MyComponent() {
  const [state, dispatchAction, isPending] = useActionState(myAsyncAction, null);

  function handleClick() {
    // ✅ Dung: boc trong startTransition
    startTransition(() => {
      dispatchAction();
    });
  }

  // ...
}
```

Hoac truyen `dispatchAction` cho mot Action prop, duoc goi trong mot Transition:

```js
function MyComponent() {
  const [state, dispatchAction, isPending] = useActionState(myAsyncAction, null);

  // ✅ Dung: action prop boc trong Transition cho ban
  return <Button action={dispatchAction}>...</Button>;
}
```

---

### Toi gap loi: "Cannot update action state while rendering" {/*cannot-update-during-render*/}

Ban khong the goi `dispatchAction` trong khi render:

<ConsoleBlock level="error">

Cannot update action state while rendering.

</ConsoleBlock>

Dieu nay gay ra vong lap vo han vi goi `dispatchAction` len lich cap nhat state, kich hoat render lai, goi `dispatchAction` lai.

```js
function MyComponent() {
  const [state, dispatchAction, isPending] = useActionState(myAction, null);

  // ❌ Sai: goi dispatchAction trong khi render
  dispatchAction();

  // ...
}
```

De sua, chi goi `dispatchAction` de phan hoi cac su kien cua nguoi dung (nhu gui form hoac nhan nut).
