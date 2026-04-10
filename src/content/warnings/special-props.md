---
title: Cảnh báo Props đặc biệt
---

Hầu hết các props trên một phần tử JSX đều được truyền đến component, tuy nhiên, có hai props đặc biệt (`ref` và `key`) được React sử dụng, và do đó không được chuyển tiếp đến component.

Ví dụ, bạn không thể đọc `props.key` từ một component. Nếu bạn cần truy cập cùng một giá trị trong component con, bạn nên truyền nó dưới dạng một prop khác (ví dụ: `<ListItemWrapper key={result.id} id={result.id} />` và đọc `props.id`). Mặc dù điều này có vẻ thừa, nhưng việc tách biệt logic ứng dụng khỏi các gợi ý cho React là rất quan trọng.
