---
title: Thiết lập trình soạn thảo
---

<Intro>

Một trình soạn thảo được cấu hình đúng có thể giúp code rõ ràng hơn và viết nhanh hơn. Nó thậm chí có thể giúp bạn phát hiện lỗi ngay khi viết! Nếu đây là lần đầu tiên bạn thiết lập trình soạn thảo hoặc bạn muốn điều chỉnh trình soạn thảo hiện tại, chúng tôi có một vài gợi ý.

</Intro>

<YouWillLearn>

* Các trình soạn thảo phổ biến nhất là gì
* Cách tự động định dạng code

</YouWillLearn>

## Trình soạn thảo của bạn {/*your-editor*/}

[VS Code](https://code.visualstudio.com/) là một trong những trình soạn thảo phổ biến nhất hiện nay. Nó có một kho extension lớn và tích hợp tốt với các dịch vụ phổ biến như GitHub. Hầu hết các tính năng được liệt kê dưới đây cũng có thể được thêm vào VS Code dưới dạng extension, khiến nó có khả năng tùy chỉnh cao!

Các trình soạn thảo văn bản phổ biến khác được sử dụng trong cộng đồng React bao gồm:

* [WebStorm](https://www.jetbrains.com/webstorm/) là một môi trường phát triển tích hợp được thiết kế đặc biệt cho JavaScript.
* [Sublime Text](https://www.sublimetext.com/) có hỗ trợ JSX và TypeScript, [tô sáng cú pháp](https://stackoverflow.com/a/70960574/458193) và tự động hoàn thành tích hợp sẵn.
* [Vim](https://www.vim.org/) là một trình soạn thảo văn bản có khả năng tùy chỉnh cao được xây dựng để tạo và thay đổi bất kỳ loại văn bản nào một cách hiệu quả. Nó được bao gồm dưới dạng "vi" với hầu hết các hệ thống UNIX và Apple OS X.

## Các tính năng trình soạn thảo được khuyến nghị {/*recommended-text-editor-features*/}

Một số trình soạn thảo có các tính năng này tích hợp sẵn, nhưng những trình khác có thể yêu cầu thêm extension. Hãy kiểm tra xem trình soạn thảo bạn chọn hỗ trợ những gì!

### Linting {/*linting*/}

Code linter tìm các vấn đề trong code khi bạn viết, giúp bạn sửa chúng sớm. [ESLint](https://eslint.org/) là một linter mã nguồn mở phổ biến cho JavaScript.

* [Cài đặt ESLint với cấu hình được khuyến nghị cho React](https://www.npmjs.com/package/eslint-config-react-app) (hãy chắc chắn bạn đã [cài đặt Node!](https://nodejs.org/en/download/current/))
* [Tích hợp ESLint trong VSCode với extension chính thức](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

**Hãy chắc chắn rằng bạn đã bật tất cả các quy tắc [`eslint-plugin-react-hooks`](https://www.npmjs.com/package/eslint-plugin-react-hooks) cho dự án.** Chúng rất quan trọng và phát hiện các lỗi nghiêm trọng nhất sớm. Preset [`eslint-config-react-app`](https://www.npmjs.com/package/eslint-config-react-app) được khuyến nghị đã bao gồm chúng.

### Định dạng {/*formatting*/}

Điều cuối cùng bạn muốn khi chia sẻ code với người đóng góp khác là tranh luận về [tab hay space](https://www.google.com/search?q=tabs+vs+spaces)! May mắn thay, [Prettier](https://prettier.io/) sẽ dọn dẹp code bằng cách định dạng lại để tuân theo các quy tắc được cấu hình sẵn. Chạy Prettier, và tất cả tab sẽ được chuyển thành space -- và thụt lề, dấu ngoặc kép, v.v. cũng sẽ được thay đổi để tuân theo cấu hình. Trong thiết lập lý tưởng, Prettier sẽ chạy khi bạn lưu file, nhanh chóng thực hiện các chỉnh sửa cho bạn.

Bạn có thể cài đặt [extension Prettier trong VSCode](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) bằng các bước sau:

1. Mở VS Code
2. Sử dụng Quick Open (nhấn Ctrl/Cmd+P)
3. Dán `ext install esbenp.prettier-vscode`
4. Nhấn Enter

#### Định dạng khi lưu {/*formatting-on-save*/}

Lý tưởng nhất, bạn nên định dạng code mỗi lần lưu. VS Code có cài đặt cho điều này!

1. Trong VS Code, nhấn `CTRL/CMD + SHIFT + P`.
2. Gõ "settings"
3. Nhấn Enter
4. Trong thanh tìm kiếm, gõ "format on save"
5. Đảm bảo tùy chọn "format on save" được đánh dấu!

> Nếu preset ESLint của bạn có các quy tắc định dạng, chúng có thể xung đột với Prettier. Chúng tôi khuyến nghị vô hiệu hóa tất cả quy tắc định dạng trong preset ESLint sử dụng [`eslint-config-prettier`](https://github.com/prettier/eslint-config-prettier) để ESLint *chỉ* được sử dụng để phát hiện lỗi logic. Nếu bạn muốn đảm bảo các file được định dạng trước khi merge pull request, hãy sử dụng [`prettier --check`](https://prettier.io/docs/en/cli.html#--check) cho continuous integration.
