---
title: Cảnh báo ngừng hỗ trợ react-test-renderer
---

## Cảnh báo ReactTestRenderer.create() {/*reacttestrenderercreate-warning*/}

react-test-renderer đã bị ngừng hỗ trợ. Một cảnh báo sẽ xuất hiện mỗi khi gọi ReactTestRenderer.create() hoặc ReactShallowRender.render(). Package react-test-renderer vẫn sẽ có sẵn trên NPM nhưng sẽ không được bảo trì và có thể bị lỗi với các tính năng React mới hoặc các thay đổi bên trong React.

Đội ngũ React khuyến nghị chuyển đổi các bài test của bạn sang [@testing-library/react](https://testing-library.com/docs/react-testing-library/intro/) hoặc [@testing-library/react-native](https://callstack.github.io/react-native-testing-library/docs/start/intro) để có trải nghiệm testing hiện đại và được hỗ trợ tốt.


## Cảnh báo new ShallowRenderer() {/*new-shallowrenderer-warning*/}

Package react-test-renderer không còn export shallow renderer tại `react-test-renderer/shallow`. Đây chỉ đơn giản là việc đóng gói lại một package riêng đã được tách ra trước đó: `react-shallow-renderer`. Do đó bạn có thể tiếp tục sử dụng shallow renderer theo cách tương tự bằng cách cài đặt trực tiếp. Xem [Github](https://github.com/enzymejs/react-shallow-renderer) / [NPM](https://www.npmjs.com/package/react-shallow-renderer).
