
# Đề xuất Dự án: Nền tảng Q&A và Polling Real-time với AWS

**Đề tài #164: Serverless GraphQL API với AppSync và Lambda Resolvers**

---

## Mục lục

- [Tóm tắt Điều hành (Executive Summary)](#tóm-tắt-điều-hành-executive-summary)
- [1. Phát biểu Bài toán (Problem Statement)](#1-phát-biểu-bài-toán-problem-statement)
  - [1.1. Bối cảnh Hiện tại](#11-bối-cảnh-hiện-tại)
  - [1.2. Thách thức Cốt lõi](#12-thách-thức-cốt-lõi)
  - [1.3. Tác động đến các Bên liên quan](#13-tác-động-đến-các-bên-liên-quan)
  - [1.4. Hậu quả Kinh doanh](#14-hậu-quả-kinh-doanh)
- [2. Kiến trúc Giải pháp (Solution Architecture)](#2-kiến-trúc-giải-pháp-solution-architecture)
  - [2.1. Tổng quan Kiến trúc](#21-tổng-quan-kiến-trúc)
  - [2.2. Lựa chọn Dịch vụ AWS và Lý do](#22-lựa-chọn-dịch-vụ-aws-và-lý-do)
  - [2.3. Thiết kế Thành phần](#23-thiết-kế-thành-phần)
  - [2.4. Kiến trúc Bảo mật (Security Architecture)](#24-kiến-trúc-bảo-mật-security-architecture)
  - [2.5. Thiết kế Mở rộng & Hiệu năng](#25-thiết-kế-mở-rộng--hiệu-năng)
- [3. Kế hoạch Triển khai Kỹ thuật (Technical Implementation)](#3-kế-hoạch-triển-khai-kỹ-thuật-technical-implementation)
  - [3.1. Các Giai đoạn Triển khai](#31-các-giai-đoạn-triển-khai)
  - [3.2. Phương pháp Phát triển](#32-phương-pháp-phát-triển)
  - [3.3. Chiến lược Kiểm thử](#33-chiến-lược-kiểm-thử)
- [4. Tiến độ & Cột mốc (Timeline & Milestones)](#4-tiến-độ--cột-mốc-timeline--milestones)
- [5. Ước tính Ngân sách (Budget Estimation)](#5-ước-tính-ngân-sách-budget-estimation)
  - [5.1. Giả định về Lưu lượng](#51-giả-định-về-lưu-lượng)
  - [5.2. Bảng kê Chi phí Ước tính](#52-bảng-kê-chi-phí-ước-tính-aws-region-ap-southeast-1)
  - [5.3. Phân tích Lợi tức Đầu tư (ROI)](#53-phân-tích-lợi-tức-đầu-tư-roi)
- [6. Đánh giá Rủi ro (Risk Assessment)](#6-đánh-giá-rủi-ro-risk-assessment)
- [7. Kết quả Mong đợi (Expected Outcomes)](#7-kết-quả-mong-đợi-expected-outcomes)
  - [7.1. Chỉ số Thành công (Success Metrics)](#71-chỉ-số-thành-công-success-metrics)
  - [7.2. Lợi ích Kinh doanh](#72-lợi-ích-kinh-doanh)
  - [7.3. Cải tiến Kỹ thuật](#73-cải-tiến-kỹ-thuật)
- [Phụ lục](#phụ-lục)
  - [A. GraphQL Schema (schema.graphql)](#a-graphql-schema-schemagraphql)
  - [B. Chi tiết Tính toán Chi phí](#b-chi-tiết-tính-toán-chi-phí)
  - [C. Sơ đồ Kiến trúc](#c-sơ-đồ-kiến-trúc)
  - [D. Nguồn tham khảo](#d-nguồn-tham-khảo)

---

## Tóm tắt Điều hành (Executive Summary)

[cite_start]Trong kỷ nguyên số, các sự kiện trực tuyến (webinar, workshop, livestream) đã trở thành công cụ chiến lược không thể thiếu. [cite: 37] [cite_start]Tuy nhiên, 81% các nhà tổ chức sự kiện vẫn xem việc duy trì tương tác của khán giả là thách thức lớn nhất (Nguồn: Bizzabo, 2022). [cite: 38] [cite_start]Các nền tảng chat truyền thống thường tạo ra "sự hỗn loạn thông tin", độ trễ cao và không có khả năng sàng lọc, dẫn đến trải nghiệm khán giả rời rạc, giảm hiệu quả truyền tải thông điệp và cuối cùng là làm xói mòn giá trị thương hiệu. [cite: 39]

[cite_start]Dự án này đề xuất xây dựng một **Nền tảng Hỏi-Đáp (Q&A) và Thăm dò ý kiến (Polling) Real-time** sử dụng kiến trúc `serverless` 100% trên AWS để giải quyết triệt để bài toán trên. [cite: 40] [cite_start]Giải pháp không chỉ cung cấp một kênh tương tác có cấu trúc, tức thời (<300ms độ trễ) mà còn biến tương tác thành dữ liệu kinh doanh có giá trị. [cite: 41]

[cite_start]**Kiến trúc giải pháp** xoay quanh `AWS AppSync` làm trung tâm quản lý GraphQL API, kết hợp với các `AWS Lambda Resolvers` để xử lý logic nghiệp vụ linh hoạt. [cite: 42] [cite_start]Dữ liệu được lưu trữ trên `Amazon DynamoDB` với thiết kế `Single-Table` để tối ưu hiệu năng và chi phí, trong khi `Amazon Cognito` đảm bảo xác thực và phân quyền an toàn. [cite: 43] [cite_start]Toàn bộ hệ thống được phân phối toàn cầu qua `Amazon CloudFront`, đảm bảo trải nghiệm nhanh chóng cho người dùng ở mọi nơi. [cite: 44]

**Lợi ích kinh doanh và kết quả mong đợi** là rất rõ ràng và có thể đo lường:
* [cite_start]**Tăng tỷ lệ tương tác của khán giả lên ít nhất 40%** thông qua Q&A có sắp xếp theo lượt bình chọn (upvote) và polling trực quan. [cite: 46]
* [cite_start]**Giảm độ trễ tương tác xuống dưới 300ms**, mang lại trải nghiệm real-time thực sự. [cite: 47]
* [cite_start]**Giảm chi phí vận hành trên 60%** so với các giải pháp dựa trên máy chủ truyền thống nhờ mô hình "pay-as-you-go" và tối ưu hóa tài nguyên. [cite: 48]
* [cite_start]**Cung cấp báo cáo phân tích sau sự kiện**, biến các câu hỏi và kết quả bình chọn thành insight giá trị về mối quan tâm của khách hàng. [cite: 49]
* [cite_start]**Nâng cao hình ảnh thương hiệu** thông qua việc cung cấp một trải nghiệm sự kiện chuyên nghiệp, hiện đại và dựa trên dữ liệu. [cite: 50]

[cite_start]Dự án được lên kế hoạch triển khai trong **15 ngày làm việc**, với ngân sách vận hành ước tính dưới **$15/tháng** cho quy mô 500 người dùng/sự kiện, thể hiện hiệu quả chi phí vượt trội. [cite: 51] [cite_start]Tôi tin rằng giải pháp này không chỉ giải quyết một bài toán cấp thiết mà còn tạo ra lợi thế cạnh tranh bền vững và có tiềm năng phát triển thành một sản phẩm SaaS độc lập. [cite: 52]

---

## 1. Phát biểu Bài toán (Problem Statement)

### 1.1. Bối cảnh Hiện tại

[cite_start]Sự bùng nổ của kinh tế số đã biến các sự kiện trực tuyến thành phương thức giao tiếp chính. [cite: 55] [cite_start]Tuy nhiên, trong khi công nghệ streaming video đã phát triển vượt bậc, khía cạnh tương tác hai chiều vẫn còn ở giai đoạn sơ khai. [cite: 56] [cite_start]Hầu hết các sự kiện hiện nay đều dựa vào các khung chat văn bản đơn giản, một công cụ được thiết kế cho các cuộc trò chuyện 1-1, không phải cho các cuộc thảo luận quy mô lớn, có cấu trúc. [cite: 57]

### 1.2. Thách thức Cốt lõi

[cite_start]Các kênh tương tác hiện tại đang tạo ra một "nợ trải nghiệm" (`experience debt`) đáng kể, thể hiện qua các vấn đề: [cite: 59]
* [cite_start]**"Sự hỗn loạn thông tin" (Information Overload):** Trong các sự kiện lớn, khung chat nhanh chóng bị quá tải. [cite: 60] [cite_start]Các câu hỏi quan trọng bị "trôi" đi trong biển thông tin nhiễu. [cite: 61] [cite_start]Điều này không chỉ làm nản lòng người hỏi mà còn khiến diễn giả bỏ lỡ những cơ hội tương tác vàng. [cite: 62]
* [cite_start]**Thiếu cơ chế sàng lọc (Lack of Prioritization):** Diễn giả không có cách nào để biết câu hỏi nào được nhiều người quan tâm nhất. [cite: 63] [cite_start]Họ buộc phải đọc lướt một cách ngẫu nhiên, dẫn đến việc trả lời các câu hỏi ít liên quan trong khi bỏ qua các mối quan tâm cốt lõi của đám đông. [cite: 64]
* [cite_start]**Độ trễ cao và trải nghiệm đứt gãy (High Latency & Disjointed Experience):** Các hệ thống truyền thống không được tối ưu cho tương tác tức thời, tạo ra độ trễ khó chịu. [cite: 65] [cite_start]Khán giả phải làm mới trang để xem kết quả poll, phá vỡ luồng theo dõi tự nhiên của sự kiện. [cite: 66]

### 1.3. Tác động đến các Bên liên quan

* [cite_start]**Đối với Khán giả:** Trải nghiệm trở nên thụ động và đáng thất vọng. [cite: 68] [cite_start]Họ cảm thấy tiếng nói của mình không được lắng nghe. [cite: 69] [cite_start]Theo một khảo sát của Markletic, **49% các nhà tiếp thị cho rằng tương tác với khán giả là yếu tố lớn nhất tạo nên một sự kiện thành công**. [cite: 70] [cite_start]Sự thiếu tương tác làm giảm giá trị mà khán giả nhận được. [cite: 71]
* [cite_start]**Đối với Diễn giả:** Việc thiếu tương tác trực tiếp khiến diễn giả khó nắm bắt được sự tập trung và phản ứng của khán giả. [cite: 72] [cite_start]Do đó, họ có thể không điều chỉnh được nội dung bài nói để phù hợp với những gì người nghe thực sự muốn biết. [cite: 73]
* [cite_start]**Đối với Nhà tổ chức:** Hình ảnh thương hiệu bị ảnh hưởng tiêu cực. [cite: 74] [cite_start]Một sự kiện với luồng tương tác lộn xộn để lại ấn tượng về sự thiếu chuyên nghiệp, làm giảm khả năng giữ chân khán giả cho các sự kiện tương lai. [cite: 75]

### 1.4. Hậu quả Kinh doanh

[cite_start]Việc không giải quyết bài toán tương tác dẫn đến những hậu quả kinh doanh có thể định lượng được: [cite: 77]
* [cite_start]**Giảm Tỷ lệ Chuyển đổi (Lower Conversion Rates):** Đối với webinar bán hàng, việc không trả lời được các câu hỏi then chốt của khách hàng tiềm năng đồng nghĩa với việc mất đi doanh thu. [cite: 78]
* [cite_start]**Lãng phí Chi phí Marketing:** Các tổ chức đầu tư đáng kể vào việc thu hút người tham dự, nhưng ROI của khoản đầu tư này bị suy giảm nghiêm trọng nếu trải nghiệm sự kiện không đáp ứng được kỳ vọng. [cite: 79]
* [cite_start]**Mất Lợi thế Cạnh tranh:** Trong một thị trường bão hòa, trải nghiệm tương tác vượt trội chính là yếu tố khác biệt hóa. [cite: 80] [cite_start]Các tổ chức không đầu tư vào đây sẽ dần mất thị phần vào tay đối thủ. [cite: 81]

---

## 2. Kiến trúc Giải pháp (Solution Architecture)

### 2.1. Tổng quan Kiến trúc

[cite_start]Giải pháp được thiết kế dựa trên kiến trúc `serverless` 100% trên AWS, lấy `AWS AppSync` làm trung tâm điều phối. [cite: 84] [cite_start]Kiến trúc này đảm bảo khả năng mở rộng tự động, hiệu suất cao, bảo mật và tối ưu hóa chi phí. [cite: 85]

![Kiến trúc tổng thể](https://raw.githubusercontent.com/samuelbaker3193/mdfile/main/AppSync-LF-arch-diagram2-1.png)
[cite_start]*Sơ đồ 1: Kiến trúc tổng thể của Nền tảng Q&A và Polling Real-time* [cite: 86]

[cite_start]**Luồng dữ liệu chính (Data Flow):** [cite: 87]
1.  [cite_start]**Client (React App):** Người dùng tương tác với ứng dụng web được host dưới dạng nội dung tĩnh trên `Amazon S3`. [cite: 88]
2.  [cite_start]**Amazon CloudFront:** Phân phối nội dung tĩnh và các yêu cầu API trên toàn cầu để giảm độ trễ, tận dụng mạng lưới biên của AWS. [cite: 89, 90]
3.  **Amazon Cognito:** Người dùng xác thực qua `Cognito`. [cite_start]Sau khi thành công, `Cognito` trả về một `JSON Web Token (JWT)`. [cite: 91]
4.  [cite_start]**AWS AppSync (GraphQL API):** Client gửi các yêu cầu GraphQL (`Queries`, `Mutations`) đến `AppSync`, đính kèm `JWT` trong header `Authorization`. [cite: 92] [cite_start]`AppSync` xác thực token với `Cognito` và điều phối request đến resolver tương ứng. [cite: 92, 93]
5.  [cite_start]**AWS Lambda Resolvers:** Các hàm `Lambda` chứa logic nghiệp vụ, được kích hoạt bởi `AppSync` để xử lý dữ liệu và tương tác với `DynamoDB`. [cite: 94, 95]
6.  [cite_start]**Amazon DynamoDB:** Cơ sở dữ liệu NoSQL lưu trữ toàn bộ dữ liệu ứng dụng. [cite: 96]
7.  [cite_start]**Real-time Subscriptions:** Khi một `mutation` thành công, `AppSync` tự động đẩy dữ liệu cập nhật đến các client đang lắng nghe (`subscribed`) thông qua kết nối `WebSocket` an toàn. [cite: 97]
8.  **Amazon CloudWatch:** Toàn bộ hệ thống được giám sát. [cite_start]`Logs` từ `Lambda` và `metrics` từ các dịch vụ khác được tập trung tại `CloudWatch` để theo dõi và cảnh báo. [cite: 98, 99]

### 2.2. Lựa chọn Dịch vụ AWS và Lý do

| Dịch vụ             | Mục đích                         | Lý do Lựa chọn (Justification)                                                                                                                                                             |
| :------------------ | :------------------------------ | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`AWS AppSync`** | Trung tâm GraphQL API           | Cung cấp GraphQL endpoint được quản lý hoàn toàn, tích hợp sẵn real-time qua `WebSockets`, caching, và bảo mật. [cite_start]**Giảm 90% công sức phát triển** so với việc tự xây dựng. [cite: 101]      |
| **`AWS Lambda`** | Xử lý Logic Nghiệp vụ           | [cite_start]Nền tảng FaaS (`Function-as-a-Service`) cho phép chạy code không cần quản lý máy chủ, tự động co giãn và tối ưu chi phí triệt để. [cite: 101]                                        |
| **`Amazon DynamoDB`** | Cơ sở dữ liệu Chính             | CSDL NoSQL có độ trễ ổn định ở mức mili giây đơn, mở rộng không giới hạn. [cite_start]**Thiết kế `Single-Table`** hiệu quả hơn 40% so với CSDL quan hệ cho workload này. [cite: 101]               |
| **`Amazon Cognito`** | Xác thực & Phân quyền          | Cung cấp giải pháp quản lý người dùng hoàn chỉnh, an toàn, và dễ tích hợp. [cite_start]**Tiết kiệm hàng tuần phát triển** so với việc tự xây dựng. [cite: 101]                               |
| **`Amazon CloudFront`** | Mạng phân phối nội dung (CDN)  | Phân phối nội dung tĩnh và tăng tốc API. [cite_start]**Giảm độ trễ cho người dùng toàn cầu lên đến 50%** bằng cách phục vụ request từ các điểm biên (`Edge Location`). [cite: 101]               |
| **`AWS SAM / CDK`** | Hạ tầng dưới dạng mã (`IaC`) | Cho phép định nghĩa và quản lý hạ tầng bằng code. [cite_start]**Đảm bảo tính nhất quán 100%** giữa các môi trường và cho phép CI/CD tự động. [cite: 101]                                  |

### 2.3. Thiết kế Thành phần

* **GraphQL Schema:** Thiết kế theo hướng "event-centric". `eventId` là tham số bắt buộc để đảm bảo client chỉ nhận dữ liệu liên quan, tăng cường bảo mật và hiệu quả. [cite_start](Xem chi tiết trong **Phụ lục A**). [cite: 103, 104]
* [cite_start]**Lambda Resolvers (`Node.js`/`Python`):** Mỗi resolver là một hàm nhỏ, chuyên biệt. [cite: 105]
    * [cite_start]`upvoteQuestion`: Sử dụng `UpdateExpression` với `ConditionExpression` trong `DynamoDB` để đảm bảo thao tác là nguyên tử (`atomic`) và mỗi người dùng chỉ vote một lần. [cite: 106]
    * [cite_start]`getEventDetails`: Một resolver duy nhất lấy tất cả thông tin của sự kiện bằng một lệnh `Query` hiệu quả, tránh vấn đề `N+1`. [cite: 107]
* [cite_start]**DynamoDB Single-Table Design:** [cite: 108]
    * [cite_start]**Partition Key (PK):** `EVENT#{eventId}` [cite: 109]
    * [cite_start]**Sort Key (SK):** Sử dụng các tiền tố để phân loại dữ liệu: `METADATA`, `QUESTION#{questionId}`, `POLL#{pollId}`. [cite: 110]

### 2.4. Kiến trúc Bảo mật (Security Architecture)

* [cite_start]**Xác thực (Authentication):** Sử dụng `Amazon Cognito User Pools` làm phương thức xác thực mặc định. [cite: 113] [cite_start]Mọi request đến `AppSync` phải đính kèm một `JWT` hợp lệ. [cite: 114]
* [cite_start]**Phân quyền (Authorization):** Áp dụng nhiều lớp: [cite: 115]
    1.  [cite_start]**AppSync Authorization Rules:** Sử dụng các chỉ thị như `@aws_auth(cognito_groups: ["admins", "moderators"])` ngay trong GraphQL schema để phân quyền ở cấp độ field. [cite: 116]
    2.  [cite_start]**Logic trong Lambda:** Kiểm tra quyền chi tiết hơn, ví dụ: "chỉ tác giả của câu hỏi mới có quyền xóa". [cite: 117]
    3.  [cite_start]**IAM Roles (Principle of Least Privilege):** Mỗi hàm `Lambda` có `IAM Role` riêng với quyền tối thiểu cần thiết (ví dụ: `Lambda createQuestion` chỉ có quyền `dynamodb:PutItem`). [cite: 118]
* [cite_start]**Bảo vệ chống Tấn công:** Tích hợp `AWS WAF` với `AppSync` để bảo vệ khỏi các cuộc tấn công phổ biến và giới hạn tần suất request (`rate limiting`) để chống DDoS. [cite: 119]

### 2.5. Thiết kế Mở rộng & Hiệu năng

* **Khả năng mở rộng:** Kiến trúc `serverless` vốn có khả năng tự động mở rộng. [cite_start]`AppSync`, `Lambda`, và `DynamoDB` có thể xử lý hàng trăm ngàn request đồng thời. [cite: 121, 122]
* [cite_start]**Tối ưu hiệu năng:** [cite: 123]
    * [cite_start]**AppSync Caching:** Kích hoạt caching ở cấp độ resolver cho các query ít thay đổi để giảm độ trễ và chi phí. [cite: 124]
    * [cite_start]**Lambda Provisioned Concurrency:** Cấu hình sẵn instance cho các hàm `Lambda` quan trọng để loại bỏ vấn đề "cold start". [cite: 125]
    * [cite_start]**CloudFront Caching:** Cache nội dung tĩnh của ứng dụng React ở các `Edge location` để giảm thời gian tải trang. [cite: 126]

---

## 3. Kế hoạch Triển khai Kỹ thuật (Technical Implementation)

### 3.1. Các Giai đoạn Triển khai

[cite_start]Dự án sẽ được chia thành 4 giai đoạn chính trong 15 ngày: [cite: 129, 147]
* [cite_start]**Phase 1: Nền tảng & Hạ tầng (3 ngày):** Hoàn thiện thiết kế, chốt hạ GraphQL schema, thiết lập hạ tầng AWS cơ bản bằng `AWS SAM`/`CDK`. [cite: 130]
* [cite_start]**Phase 2: Xây dựng Backend Logic (5 ngày):** Viết code cho các `Lambda resolver`, cấu hình data source và thực hiện kiểm thử tích hợp. [cite: 131]
* [cite_start]**Phase 3: Phát triển Frontend & Tích hợp (5 ngày):** Xây dựng giao diện `React`, tích hợp với backend qua `AWS Amplify` và thực hiện kiểm thử `End-to-End`. [cite: 132]
* [cite_start]**Phase 4: Hoàn thiện & Triển khai (2 ngày):** Tối ưu hóa hiệu năng, bảo mật, hoàn thiện tài liệu và triển khai lên môi trường `production`. [cite: 133]

### 3.2. Phương pháp Phát triển

* [cite_start]**Infrastructure as Code (IaC):** Toàn bộ hạ tầng AWS sẽ được định nghĩa bằng `AWS SAM`, cho phép phiên bản hóa, tái sử dụng và tự động hóa việc triển khai. [cite: 135, 136]
* [cite_start]**API-Driven Development:** `GraphQL schema` là "hợp đồng" giữa frontend và backend, cho phép hai đội làm việc song song. [cite: 137, 138]
* [cite_start]**CI/CD Pipeline:** Sử dụng `GitHub Actions` để xây dựng một pipeline tự động hóa hoàn chỉnh: [cite: 139]
    * [cite_start]**Khi `push` lên nhánh `develop`:** Tự động chạy `Unit Test` và triển khai lên môi trường `staging`. [cite: 140]
    * [cite_start]**Khi tạo `Pull Request` vào `main`:** Chạy `Unit Test`, `Integration Test` và yêu cầu `review`. [cite: 141, 142]
    * [cite_start]**Khi `merge` vào `main`:** Tự động triển khai lên `production` sử dụng chiến lược `Canary Deployment` cho `Lambda` để giảm thiểu rủi ro. [cite: 143]

### 3.3. Chiến lược Kiểm thử

| Loại Kiểm thử          | Công cụ                  | Mục tiêu                                                                                                                                                    |
| :---------------------- | :----------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Unit Test** | `Jest` (cho `Node.js`)   | [cite_start]Kiểm thử logic bên trong từng hàm `Lambda`, mock các lời gọi AWS SDK. [cite: 145] [cite_start]Đảm bảo độ bao phủ code > 80%. [cite: 145]                                           |
| **Integration Test** | `AppSync Console`        | [cite_start]Kiểm thử sự tương tác giữa `AppSync` và các `Lambda resolver`, xác nhận dữ liệu và logic phân quyền hoạt động chính xác. [cite: 145]                                   |
| **End-to-End (E2E) Test** | `Cypress` / `Playwright` | [cite_start]Tự động hóa kịch bản sử dụng thực tế trên giao diện, ví dụ: "người dùng A đăng nhập, đặt câu hỏi; người dùng B thấy câu hỏi real-time và upvote". [cite: 145] |
| **Performance Test** | `Artillery.io`           | [cite_start]Mô phỏng hàng trăm người dùng đồng thời để xác định điểm nghẽn và đảm bảo hệ thống đáp ứng KPI về độ trễ. [cite: 145]                                           |

---

## 4. Tiến độ & Cột mốc (Timeline & Milestones)

[cite_start]Dự án được thiết kế để hoàn thành trong 3 tuần (15 ngày làm việc). [cite: 147]

| Tuần | Ngày   | Nhiệm vụ chính                                                                                 | Deliverable / Cột mốc                                                                                             |
| :--- | :----- | :---------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------- |
| **1** | 1-3    | **Phase 1:** Hoàn thiện thiết kế, chốt schema, setup repo, triển khai `IaC` cơ bản.             | [cite_start]Sơ đồ kiến trúc cuối cùng, `schema.graphql`, repo GitHub, hạ tầng được triển khai trên `staging`. [cite: 148]            |
|      | 4-5    | **Phase 2 (part 1):** Xây dựng resolver cho Q&A (`create`, `upvote`).                           | [cite_start]**Cột mốc 1:** Các `mutation` và `subscription` cho Q&A hoạt động qua AppSync Console. [cite: 148]                  |
| **2** | 6-8    | **Phase 2 (part 2):** Hoàn thiện các resolver còn lại (Polling, User).                          | [cite_start]Tất cả các resolver backend hoàn thành và vượt qua `integration test`. [cite: 148]                                    |
|      | 9-10   | **Phase 3 (part 1):** Xây dựng giao diện đăng nhập, hiển thị Q&A real-time.                     | [cite_start]**Cột mốc 2:** Giao diện người dùng có thể đăng nhập, gửi câu hỏi và nhận cập nhật real-time. [cite: 148]            |
| **3** | 11-13  | **Phase 3 (part 2):** Hoàn thiện giao diện Polling, viết kịch bản E2E.                          | Giao diện hoàn chỉnh. [cite_start]Các kịch bản test `E2E` tự động được thiết lập và chạy thành công. [cite: 148]                 |
|      | 14     | **Phase 4 (part 1):** Tối ưu hiệu năng (`caching`, `provisioned concurrency`), kiểm thử bảo mật.  | [cite_start]Báo cáo kết quả kiểm thử, cấu hình `AppSync Caching` & `WAF`. [cite: 148]                                            |
|      | 15     | **Phase 4 (part 2):** Triển khai `Production`, bàn giao.                                        | **Cột mốc cuối:** Hệ thống hoạt động trên môi trường `production`. [cite_start]Buổi demo và bàn giao dự án. [cite: 148]            |

---

## 5. Ước tính Ngân sách (Budget Estimation)

### 5.1. Giả định về Lưu lượng

[cite_start]Tính toán dựa trên một tháng hoạt động tại khu vực `ap-southeast-1` (Singapore): [cite: 151]
* [cite_start]**Số sự kiện:** 10 sự kiện/tháng [cite: 152]
* [cite_start]**Người dùng/sự kiện:** 500 người [cite: 153]
* [cite_start]**Thời lượng sự kiện:** 2 giờ [cite: 154]
* [cite_start]**Tương tác/người dùng:** 10 (đặt câu hỏi, vote, poll) [cite: 155]
* [cite_start]**=> Tổng request:** 50,000 requests/tháng [cite: 156]
* [cite_start]**=> Tổng kết nối real-time:** 10,000 giờ-kết-nối/tháng [cite: 157]
* [cite_start]**=> Tổng người dùng hoạt động (MAUs):** 5,000 MAUs/tháng [cite: 158]

### 5.2. Bảng kê Chi phí Ước tính (AWS Region: `ap-southeast-1`)

| Dịch vụ             | Hạng mục Miễn phí (Free Tier)     | Mức sử dụng Ước tính          | Chi phí Ước tính (USD) |
| :------------------ | :-------------------------------- | :--------------------------- | :-------------------- |
| **`AWS AppSync`** | 1M requests, 200k giờ-kết-nối    | 50k requests, 10k giờ-kết-nối | $0.00                 |
| **`AWS Lambda`** | 1M requests, 400k GB-giây         | 50k requests (128MB, 200ms)  | $0.00                 |
| **`Amazon DynamoDB`** | 25 GB, 25 WCU, 25 RCU             | ~1 GB, <1 WCU/RCU (On-Demand)  | ~$1.25                |
| **`Amazon Cognito`** | 50,000 MAUs                       | 5,000 MAUs                   | $0.00                 |
| **`Amazon CloudFront`** | 1TB data, 10M requests          | ~20 GB data, 100k requests   | ~$1.80                |
| **`Amazon CloudWatch`** | 10 custom metrics, 5GB logs       | Mặc định (trong free tier)   | $0.00                 |
| **`AWS WAF`** | -                                 | 1 Web ACL, 1 Rule, 50k requests | ~$6.20                |
| **Tổng cộng** |                                   |                              | **~ $9.25 / tháng** |
[cite_start][cite: 160]

### 5.3. Phân tích Lợi tức Đầu tư (ROI)

* [cite_start]**Chi phí Giải pháp Serverless:** ~$9.25/tháng, tăng tuyến tính theo mức độ sử dụng. [cite: 162]
* **Chi phí Giải pháp Truyền thống:**
    * [cite_start]2 x `EC2 t4g.small` instances (cho HA): ~$25/tháng [cite: 164]
    * [cite_start]1 x `Application Load Balancer`: ~$24/tháng [cite: 165]
    * [cite_start]Quản lý: ~10 giờ làm việc của kỹ sư/tháng. [cite: 166]
    * [cite_start]**Tổng chi phí truyền thống:** > $49/tháng + chi phí nhân sự. [cite: 167]
* [cite_start]**Kết luận ROI:** Giải pháp `serverless` không chỉ **giảm chi phí trực tiếp trên 80%** mà còn **loại bỏ gần như hoàn toàn gánh nặng quản trị hạ tầng**, giúp đội ngũ tập trung 100% vào việc xây dựng tính năng. [cite: 168]

---

## 6. Đánh giá Rủi ro (Risk Assessment)

| Rủi ro                                                                  | Ảnh hưởng | Khả năng  | Chiến lược Giảm thiểu (Mitigation)                                                                                                                                  | Kế hoạch Dự phòng (Contingency)                                                                                             |
| :---------------------------------------------------------------------- | :-------- | :-------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------- |
| **Kỹ thuật:** `Lambda cold start` làm tăng độ trễ cho request đầu tiên.    | Trung bình  | Cao       | - [cite_start]Sử dụng `Provisioned Concurrency`.<br>- Tối ưu kích thước gói triển khai của Lambda. [cite: 170]                                                                   | [cite_start]Hiển thị chỉ báo tải (loading indicator) trên giao diện người dùng. [cite: 170]                                                |
| **Chi phí:** Tăng đột biến do bị tấn công DDoS hoặc lỗi logic.         | Cao       | Thấp      | - [cite_start]Sử dụng `AWS WAF` để rate-limit.<br>- Thiết lập `AWS Budgets` để cảnh báo. [cite: 170]                                                                                | Kích hoạt quy tắc WAF khẩn cấp. [cite_start]Tắt `Lambda` function gây lỗi để điều tra. [cite: 170]                                            |
| **Bảo mật:** Lộ thông tin nhạy cảm do cấu hình sai quyền.             | Rất cao  | Thấp      | - [cite_start]Tuân thủ nguyên tắc `quyền tối thiểu (Least Privilege)`.<br>- Tự động hóa kiểm tra `IAM`.<br>- Yêu cầu `peer review` cho thay đổi bảo mật. [cite: 170] | Thu hồi phiên làm việc, xoay vòng khóa truy cập. [cite_start]Phân tích `CloudTrail` để xác định phạm vi. [cite: 170]                        |
| **Vận hành:** Phụ thuộc vào một nhà cung cấp duy nhất (`AWS Vendor Lock-in`). | Thấp      | Rất cao   | - [cite_start]Thiết kế theo tiêu chuẩn mở (`GraphQL`).<br>- Sử dụng `IaC` giúp việc di chuyển khả thi hơn. [cite: 170]                                                           | Chấp nhận rủi ro. [cite_start]Lợi ích từ hệ sinh thái AWS vượt trội hơn rủi ro. [cite: 170]                                                    |

---

## 7. Kết quả Mong đợi (Expected Outcomes)

### 7.1. Chỉ số Thành công (Success Metrics)

* [cite_start]**Tỷ lệ tương tác:** (Số câu hỏi + vote + poll) / tổng số người tham dự > 40%. [cite: 173]
* [cite_start]**Độ trễ API (P99):** Thời gian phản hồi của 99% request API < 300ms. [cite: 174]
* [cite_start]**Tỷ lệ lỗi API:** Số request trả về lỗi 5xx < 0.01%. [cite: 175]
* [cite_start]**Chi phí mỗi người dùng tương tác:** Tổng chi phí / tổng người dùng hoạt động < $0.01. [cite: 176]
* [cite_start]**Mức độ hài lòng (CSAT):** Điểm khảo sát sau sự kiện > 4.5/5. [cite: 177]

### 7.2. Lợi ích Kinh doanh

* [cite_start]**Ngắn hạn (0-6 tháng):** Cải thiện ngay lập tức chất lượng tương tác và mức độ hài lòng của khán giả. [cite: 179, 180]
* [cite_start]**Trung hạn (6-18 tháng):** Xây dựng danh tiếng công nghệ cho nhà tổ chức. [cite: 182] [cite_start]Dữ liệu thu thập được trở thành nguồn insight quý giá cho chiến lược kinh doanh. [cite: 183]
* [cite_start]**Dài hạn (18+ tháng):** Nền tảng có thể phát triển thành sản phẩm **Software-as-a-Service (SaaS)** thương mại, mở ra nguồn doanh thu mới. [cite: 184]

### 7.3. Cải tiến Kỹ thuật

* [cite_start]**Hiện đại hóa:** Chuyển đổi sang kiến trúc real-time, `event-driven` hiện đại. [cite: 186]
* [cite_start]**Tăng tốc độ phát triển:** Kiến trúc `serverless` và `IaC` cho phép triển khai tính năng mới nhanh và an toàn hơn. [cite: 187]
* [cite_start]**Nâng cao độ tin cậy:** Hệ thống được quản lý bởi AWS sẽ có độ sẵn sàng (`availability`) 99.99%. [cite: 188]

---

## Phụ lục

### A. GraphQL Schema (`schema.graphql`)

```graphql
# Chỉ cho phép người dùng đã xác thực qua Cognito truy cập API
# Chỉ người dùng trong nhóm 'admins' mới có quyền thực hiện các thao tác quản trị
schema @aws_api_key @aws_cognito_user_pools {
  # Định nghĩa loại dữ liệu cho một sự kiện
  type Event {
    id: ID!
    name: String!
    description: String
    questions: [Question!]
    polls: [Poll!]
    createdAt: AWSDateTime!
  }

  # Định nghĩa loại dữ liệu cho một câu hỏi
  type Question {
    id: ID!
    eventId: ID!
    content: String!
    author: String # Lấy từ context của Cognito
    upvotes: Int!
    isAnswered: Boolean
    createdAt: AWSDateTime!
  }

  # Định nghĩa cho một cuộc bình chọn
  type Poll {
    id: ID!
    eventId: ID!
    questionText: String!
    options: [PollOption!]!
    isActive: Boolean!
    totalVotes: Int!
  }

  type PollOption {
    text: String!
    votes: Int!
  }

  # Định nghĩa các hành động ĐỌC dữ liệu
  type Query {
    # Lấy thông tin chi tiết của một sự kiện
    getEvent(id: ID!): Event
  }

  # Định nghĩa các hành động THAY ĐỔI dữ liệu
  type Mutation {
    # Tạo một sự kiện mới (chỉ admin)
    createEvent(name: String!, description: String): Event
      @aws_auth(cognito_groups: ["admins"])

    # Tạo một câu hỏi mới
    createQuestion(eventId: ID!, content: String!): Question

    # Upvote một câu hỏi
    upvoteQuestion(questionId: ID!): Question

    # Đánh dấu câu hỏi đã được trả lời (chỉ admin/moderator)
    markQuestionAsAnswered(questionId: ID!, isAnswered: Boolean!): Question
      @aws_auth(cognito_groups: ["admins", "moderators"])

    # Tạo một cuộc bình chọn mới (chỉ admin/moderator)
    createPoll(eventId: ID!, questionText: String!, options: [String!]!): Poll
      @aws_auth(cognito_groups: ["admins", "moderators"])

    # Gửi một phiếu bầu
    submitVote(pollId: ID!, optionText: String!): Poll
  }

  # Định nghĩa các kênh LẮNG NGHE sự kiện real-time
  type Subscription {
    # Lắng nghe câu hỏi mới hoặc được upvote trong một sự kiện
    onQuestionUpdated(eventId: ID!): Question
      @aws_subscribe(mutations: ["createQuestion", "upvoteQuestion", "markQuestionAsAnswered"])

    # Lắng nghe khi kết quả poll thay đổi trong một sự kiện
    onPollUpdated(eventId: ID!): Poll
      @aws_subscribe(mutations: ["submitVote", "createPoll"])
  }
}
```
B. Chi tiết Tính toán Chi phí
Bảng kê chi phí trong 

Phần 5.2 được ước tính bằng AWS Pricing Calculator vào ngày 7 tháng 7 năm 2025 cho khu vực ap-southeast-1. 

C. Sơ đồ Kiến trúc
Sơ đồ kiến trúc chi tiết được trình bày trong 

Phần 2.1. 

D. Nguồn tham khảo
Bizzabo (2022). 

Event Marketing Benchmarks and Trends Report. 

Markletic (2023). 

Virtual Event Statistics for 2023. 

AWS Pricing Calculator. Retrieved July 7, 2025, from https://calculator.aws/ 
