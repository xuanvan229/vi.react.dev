---
title: Cảnh báo ARIA Prop không hợp lệ
---

Cảnh báo này sẽ xuất hiện nếu bạn cố render một phần tử DOM với một `aria-*` prop không tồn tại trong [đặc tả](https://www.w3.org/TR/wai-aria-1.1/#states_and_properties) Web Accessibility Initiative (WAI) Accessible Rich Internet Application (ARIA).

1. Nếu bạn cho rằng mình đang sử dụng một prop hợp lệ, hãy kiểm tra chính tả cẩn thận. `aria-labelledby` và `aria-activedescendant` thường hay bị viết sai chính tả.

2. Nếu bạn viết `aria-role`, có thể bạn muốn dùng `role`.

3. Nếu không, nếu bạn đang sử dụng phiên bản mới nhất của React DOM và đã xác minh rằng bạn đang dùng một tên thuộc tính hợp lệ được liệt kê trong đặc tả ARIA, vui lòng [báo lỗi](https://github.com/facebook/react/issues/new/choose).
