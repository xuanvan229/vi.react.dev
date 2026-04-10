---
title: Cài đặt
---

<Intro>
Hướng dẫn này sẽ giúp bạn cài đặt và cấu hình React Compiler trong ứng dụng React của bạn.
</Intro>

<YouWillLearn>

* Cách cài đặt React Compiler
* Cấu hình cơ bản cho các công cụ build khác nhau
* Cách xác minh cài đặt đang hoạt động

</YouWillLearn>

## Yêu cầu tiên quyết {/*prerequisites*/}

React Compiler được thiết kế để hoạt động tốt nhất với React 19, nhưng nó cũng hỗ trợ React 17 và 18. Tìm hiểu thêm về [tương thích phiên bản React](/reference/react-compiler/target).

## Cài đặt {/*installation*/}

Cài đặt React Compiler như một `devDependency`:

<TerminalBlock>
npm install -D babel-plugin-react-compiler@latest
</TerminalBlock>

Hoặc với Yarn:

<TerminalBlock>
yarn add -D babel-plugin-react-compiler@latest
</TerminalBlock>

Hoặc với pnpm:

<TerminalBlock>
pnpm install -D babel-plugin-react-compiler@latest
</TerminalBlock>

## Thiết lập cơ bản {/*basic-setup*/}

React Compiler được thiết kế để hoạt động mặc định mà không cần bất kỳ cấu hình nào. Tuy nhiên, nếu bạn cần cấu hình trong các trường hợp đặc biệt (ví dụ, để nhắm đến phiên bản React dưới 19), hãy tham khảo [tham chiếu tùy chọn compiler](/reference/react-compiler/configuration).

Quá trình thiết lập phụ thuộc vào công cụ build của bạn. React Compiler bao gồm một Babel plugin tích hợp với pipeline build của bạn.

<Pitfall>
React Compiler phải chạy **đầu tiên** trong pipeline Babel plugin của bạn. Compiler cần thông tin source gốc để phân tích đúng, vì vậy nó phải xử lý code của bạn trước các phép biến đổi khác.
</Pitfall>

### Babel {/*babel*/}

Tạo hoặc cập nhật `babel.config.js` của bạn:

```js {3}
module.exports = {
  plugins: [
    'babel-plugin-react-compiler', // phải chạy đầu tiên!
    // ... các plugin khác
  ],
  // ... cấu hình khác
};
```

### Vite {/*vite*/}

Nếu bạn sử dụng Vite, bạn có thể thêm plugin vào vite-plugin-react:

```js {3,9}
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [
    react({
      babel: {
        plugins: ['babel-plugin-react-compiler'],
      },
    }),
  ],
});
```

Ngoài ra, nếu bạn thích Babel plugin riêng cho Vite:

<TerminalBlock>
npm install -D vite-plugin-babel
</TerminalBlock>

```js {2,11}
// vite.config.js
import babel from 'vite-plugin-babel';
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [
    react(),
    babel({
      babelConfig: {
        plugins: ['babel-plugin-react-compiler'],
      },
    }),
  ],
});
```

### Next.js {/*usage-with-nextjs*/}

Vui lòng tham khảo [tài liệu Next.js](https://nextjs.org/docs/app/api-reference/next-config-js/reactCompiler) để biết thêm thông tin.

### React Router {/*usage-with-react-router*/}
Cài đặt `vite-plugin-babel`, và thêm Babel plugin của compiler vào đó:

<TerminalBlock>
npm install vite-plugin-babel
</TerminalBlock>

```js {3-4,16}
// vite.config.js
import { defineConfig } from "vite";
import babel from "vite-plugin-babel";
import { reactRouter } from "@react-router/dev/vite";

const ReactCompilerConfig = { /* ... */ };

export default defineConfig({
  plugins: [
    reactRouter(),
    babel({
      filter: /\.[jt]sx?$/,
      babelConfig: {
        presets: ["@babel/preset-typescript"], // nếu bạn sử dụng TypeScript
        plugins: [
          ["babel-plugin-react-compiler", ReactCompilerConfig],
        ],
      },
    }),
  ],
});
```

### Webpack {/*usage-with-webpack*/}

Một webpack loader cộng đồng [hiện có sẵn tại đây](https://github.com/SukkaW/react-compiler-webpack).

### Expo {/*usage-with-expo*/}

Vui lòng tham khảo [tài liệu Expo](https://docs.expo.dev/guides/react-compiler/) để bật và sử dụng React Compiler trong ứng dụng Expo.

### Metro (React Native) {/*usage-with-react-native-metro*/}

React Native sử dụng Babel qua Metro, vì vậy hãy tham khảo phần [Sử dụng với Babel](#babel) để biết hướng dẫn cài đặt.

### Rspack {/*usage-with-rspack*/}

Vui lòng tham khảo [tài liệu Rspack](https://rspack.dev/guide/tech/react#react-compiler) để bật và sử dụng React Compiler trong ứng dụng Rspack.

### Rsbuild {/*usage-with-rsbuild*/}

Vui lòng tham khảo [tài liệu Rsbuild](https://rsbuild.dev/guide/framework/react#react-compiler) để bật và sử dụng React Compiler trong ứng dụng Rsbuild.


## Tích hợp ESLint {/*eslint-integration*/}

React Compiler bao gồm một ESLint rule giúp xác định code không thể được tối ưu hóa. Khi ESLint rule báo lỗi, điều đó có nghĩa là compiler sẽ bỏ qua tối ưu hóa cho component hoặc hook cụ thể đó. Điều này là an toàn: compiler sẽ tiếp tục tối ưu hóa các phần khác của codebase. Bạn không cần sửa tất cả vi phạm ngay lập tức. Xử lý chúng theo tốc độ của bạn để dần dần tăng số lượng component được tối ưu hóa.

Cài đặt ESLint plugin:

<TerminalBlock>
npm install -D eslint-plugin-react-hooks@latest
</TerminalBlock>

Nếu bạn chưa cấu hình eslint-plugin-react-hooks, hãy làm theo [hướng dẫn cài đặt trong readme](https://github.com/facebook/react/blob/main/packages/eslint-plugin-react-hooks/README.md#installation). Các rule compiler có sẵn trong preset `recommended-latest`.

ESLint rule sẽ:
- Xác định vi phạm [Quy tắc của React](/reference/rules)
- Hiển thị component nào không thể được tối ưu hóa
- Cung cấp thông báo lỗi hữu ích để sửa vấn đề

## Xác minh cài đặt {/*verify-your-setup*/}

Sau khi cài đặt, xác minh rằng React Compiler đang hoạt động đúng.

### Kiểm tra React DevTools {/*check-react-devtools*/}

Các component được tối ưu hóa bởi React Compiler sẽ hiển thị badge "Memo ✨" trong React DevTools:

1. Cài đặt extension trình duyệt [React Developer Tools](/learn/react-developer-tools)
2. Mở ứng dụng trong chế độ development
3. Mở React DevTools
4. Tìm emoji ✨ bên cạnh tên component

Nếu compiler đang hoạt động:
- Component sẽ hiển thị badge "Memo ✨" trong React DevTools
- Các phép tính tốn kém sẽ được tự động memoize
- Không cần `useMemo` thủ công

### Kiểm tra Build Output {/*check-build-output*/}

Bạn cũng có thể xác minh compiler đang chạy bằng cách kiểm tra build output. Code đã biên dịch sẽ bao gồm logic memoization tự động mà compiler thêm vào.

```js
import { c as _c } from "react/compiler-runtime";
export default function MyApp() {
  const $ = _c(1);
  let t0;
  if ($[0] === Symbol.for("react.memo_cache_sentinel")) {
    t0 = <div>Hello World</div>;
    $[0] = t0;
  } else {
    t0 = $[0];
  }
  return t0;
}

```

## Xử lý sự cố {/*troubleshooting*/}

### Loại trừ component cụ thể {/*opting-out-specific-components*/}

Nếu một component gây ra vấn đề sau khi biên dịch, bạn có thể tạm thời loại trừ nó bằng directive `"use no memo"`:

```js
function ProblematicComponent() {
  "use no memo";
  // Code component ở đây
}
```

Điều này cho compiler biết bỏ qua tối ưu hóa cho component cụ thể này. Bạn nên sửa vấn đề gốc và xóa directive sau khi đã giải quyết.

Để biết thêm trợ giúp xử lý sự cố, xem [hướng dẫn gỡ lỗi](/learn/react-compiler/debugging).

## Bước tiếp theo {/*next-steps*/}

Bây giờ bạn đã cài đặt React Compiler, tìm hiểu thêm về:

- [Tương thích phiên bản React](/reference/react-compiler/target) cho React 17 và 18
- [Tùy chọn cấu hình](/reference/react-compiler/configuration) để tùy chỉnh compiler
- [Chiến lược áp dụng dần dần](/learn/react-compiler/incremental-adoption) cho codebase hiện có
- [Kỹ thuật gỡ lỗi](/learn/react-compiler/debugging) để xử lý sự cố
- [Hướng dẫn biên dịch thư viện](/reference/react-compiler/compiling-libraries) để biên dịch thư viện React của bạn
