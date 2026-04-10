---
title: "Giới thiệu React Foundation"
author: Seth Webster, Matt Carroll, Joe Savona
date: 2025/10/07
description: Hôm nay, chúng tôi công bố kế hoạch thành lập React Foundation và cấu trúc quản trị kỹ thuật mới
---

October 7, 2025 by [Seth Webster](https://x.com/sethwebster), [Matt Carroll](https://x.com/mattcarrollcode), [Joe Savona](https://x.com/en_JS), [Sophie Alpert](https://x.com/sophiebits)

---


<div style={{display: 'flex', justifyContent: 'center', marginBottom: '1rem', marginLeft: '7rem', marginRight: '7rem' }}>
  <picture >
      <source srcset="/images/blog/react-foundation/react_foundation_logo.png" />
      <img className="w-full light-image" src="/images/blog/react-foundation/react_foundation_logo.webp" />
  </picture>
  <picture >
      <source srcset="/images/blog/react-foundation/react_foundation_logo_dark.png" />
      <img className="w-full dark-image" src="/images/blog/react-foundation/react_foundation_logo_dark.webp" />
  </picture>
</div>

<Intro>

Hôm nay, chúng tôi công bố kế hoạch thành lập React Foundation và cấu trúc quản trị kỹ thuật mới.

</Intro>

---

Chúng tôi đã mã nguồn mở React hơn một thập kỷ trước để giúp các nhà phát triển xây dựng trải nghiệm người dùng tuyệt vời. Từ những ngày đầu tiên, React đã nhận được những đóng góp đáng kể từ các contributor bên ngoài Meta. Theo thời gian, số lượng contributor và phạm vi đóng góp của họ đã tăng lên đáng kể. Từ một công cụ được phát triển cho Meta, React đã mở rộng thành một dự án trải rộng nhiều công ty với những đóng góp thường xuyên từ khắp hệ sinh thái. React đã vượt ra khỏi giới hạn của bất kỳ công ty đơn lẻ nào.

Để phục vụ cộng đồng React tốt hơn, chúng tôi công bố kế hoạch chuyển React và React Native từ Meta sang React Foundation mới. Là một phần của thay đổi này, chúng tôi cũng sẽ triển khai cấu trúc quản trị kỹ thuật độc lập mới. Chúng tôi tin rằng những thay đổi này sẽ cho phép chúng tôi cung cấp nhiều tài nguyên hơn cho các dự án trong hệ sinh thái React.

## React Foundation {/*the-react-foundation*/}

Chúng tôi sẽ biến React Foundation thành ngôi nhà mới cho React, React Native, và một số dự án hỗ trợ như JSX. Sứ mệnh của React Foundation sẽ là hỗ trợ cộng đồng và hệ sinh thái React. Sau khi triển khai, React Foundation sẽ

* Duy trì cơ sở hạ tầng của React như GitHub, CI, và thương hiệu
* Tổ chức React Conf
* Tạo các sáng kiến hỗ trợ hệ sinh thái React như hỗ trợ tài chính cho các dự án trong hệ sinh thái, cấp tài trợ, và tạo các chương trình

React Foundation sẽ được điều hành bởi hội đồng quản trị, với Seth Webster giữ vai trò giám đốc điều hành. Hội đồng này sẽ phân bổ quỹ và tài nguyên để hỗ trợ phát triển, cộng đồng, và hệ sinh thái React. Chúng tôi tin rằng đây là cấu trúc tốt nhất để đảm bảo React Foundation trung lập với nhà cung cấp và phản ánh lợi ích tốt nhất của cộng đồng.

Các thành viên doanh nghiệp sáng lập của React Foundation sẽ là Amazon, Callstack, Expo, Meta, Microsoft, Software Mansion, và Vercel. Những công ty này đã có tác động lớn đến hệ sinh thái React và React Native và chúng tôi biết ơn sự hỗ trợ của họ. Chúng tôi hào hứng chào đón thêm nhiều thành viên trong tương lai.

<div style={{display: 'flex', justifyContent: 'center', margin: '2rem'}}>
  <picture >
      <source srcset="/images/blog/react-foundation/react_foundation_member_logos.png" />
      <img className="w-full light-image" src="/images/blog/react-foundation/react_foundation_member_logos.webp" />
  </picture>
  <picture >
      <source srcset="/images/blog/react-foundation/react_foundation_member_logos_dark.png" />
      <img className="w-full dark-image" src="/images/blog/react-foundation/react_foundation_member_logos_dark.webp" />
  </picture>
</div>

## Quản trị kỹ thuật của React {/*reacts-technical-governance*/}

Chúng tôi tin rằng hướng đi kỹ thuật của React nên được quyết định bởi những người đóng góp và bảo trì React. Khi React chuyển sang foundation, điều quan trọng là không một công ty hay tổ chức đơn lẻ nào được đại diện quá mức. Để đạt được điều này, chúng tôi dự định xác định cấu trúc quản trị kỹ thuật mới cho React, độc lập với React Foundation.

Là một phần của việc tạo cấu trúc quản trị kỹ thuật mới cho React, chúng tôi sẽ tiếp cận cộng đồng để lấy phản hồi. Khi hoàn thiện, chúng tôi sẽ chia sẻ chi tiết trong một bài viết tương lai.

## Cảm ơn {/*thank-you*/}

Sự phát triển đáng kinh ngạc của React là nhờ hàng ngàn người, công ty, và dự án đã định hình React. Việc thành lập React Foundation là minh chứng cho sức mạnh và sự sôi động của cộng đồng React. Cùng nhau, React Foundation và cấu trúc quản trị kỹ thuật mới của React sẽ đảm bảo tương lai của React được bảo đảm trong nhiều năm tới.
