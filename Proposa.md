Đề xuất Dự án: Nền tảng Q&A và Polling Real-time với AWS
Đề tài #164: Serverless GraphQL API với AppSync và Lambda Resolvers

Mục lục
Tóm tắt Điều hành (Executive Summary)

1. Phát biểu Bài toán (Problem Statement)

1.1. Bối cảnh Hiện tại

1.2. Thách thức Cốt lõi

1.3. Tác động đến các Bên liên quan

1.4. Hậu quả Kinh doanh

2. Kiến trúc Giải pháp (Solution Architecture)

2.1. Tổng quan Kiến trúc

2.2. Lựa chọn Dịch vụ AWS và Lý do

2.3. Thiết kế Thành phần

2.4. Kiến trúc Bảo mật (Security Architecture)

2.5. Thiết kế Mở rộng & Hiệu năng

3. Kế hoạch Triển khai Kỹ thuật (Technical Implementation)

3.1. Các Giai đoạn Triển khai

3.2. Phương pháp Phát triển

3.3. Chiến lược Kiểm thử

4. Tiến độ & Cột mốc (Timeline & Milestones)

5. Ước tính Ngân sách (Budget Estimation)

5.1. Giả định về Lưu lượng

5.2. Bảng kê Chi phí Ước tính

5.3. Phân tích Lợi tức Đầu tư (ROI)

6. Đánh giá Rủi ro (Risk Assessment)

7. Kết quả Mong đợi (Expected Outcomes)

7.1. Chỉ số Thành công (Success Metrics)

7.2. Lợi ích Kinh doanh

7.3. Cải tiến Kỹ thuật

Phụ lục

A. GraphQL Schema (schema.graphql)

B. Chi tiết Tính toán Chi phí

C. Sơ đồ Kiến trúc

D. Nguồn tham khảo

Tóm tắt Điều hành (Executive Summary)
Trong kỷ nguyên số, các sự kiện trực tuyến (webinar, workshop, livestream) đã trở thành công cụ chiến lược không thể thiếu. Tuy nhiên, 81% các nhà tổ chức sự kiện vẫn xem việc duy trì tương tác của khán giả là thách thức lớn nhất. Các nền tảng chat truyền thống thường tạo ra "sự hỗn loạn thông tin", độ trễ cao và không có khả năng sàng lọc, dẫn đến trải nghiệm khán giả rời rạc, giảm hiệu quả truyền tải thông điệp và cuối cùng là làm xói mòn giá trị thương hiệu.

Dự án này đề xuất xây dựng một Nền tảng Hỏi-Đáp (Q&A) và Thăm dò ý kiến (Polling) Real-time sử dụng kiến trúc serverless 100% trên AWS để giải quyết triệt để bài toán trên. Giải pháp không chỉ cung cấp một kênh tương tác có cấu trúc, tức thời (<300ms độ trễ) mà còn biến tương tác thành dữ liệu kinh doanh có giá trị.

Kiến trúc giải pháp xoay quanh AWS AppSync làm trung tâm quản lý GraphQL API, kết hợp với các AWS Lambda Resolvers để xử lý logic nghiệp vụ linh hoạt. Dữ liệu được lưu trữ trên Amazon DynamoDB với thiết kế Single-Table để tối ưu hiệu năng và chi phí, trong khi Amazon Cognito đảm bảo xác thực và phân quyền an toàn. Toàn bộ hệ thống được phân phối toàn cầu qua Amazon CloudFront, đảm bảo trải nghiệm nhanh chóng cho người dùng ở mọi nơi.

Lợi ích kinh doanh và kết quả mong đợi là rất rõ ràng và có thể đo lường:

Tăng tỷ lệ tương tác của khán giả lên ít nhất 40% thông qua Q&A có sắp xếp theo lượt bình chọn (upvote) và polling trực quan.

Giảm độ trễ tương tác xuống dưới 300ms, mang lại trải nghiệm real-time thực sự.

Giảm chi phí vận hành trên 60% so với các giải pháp dựa trên máy chủ truyền thống nhờ mô hình "pay-as-you-go".

Cung cấp báo cáo phân tích sau sự kiện, biến các câu hỏi và kết quả bình chọn thành insight giá trị về mối quan tâm của khách hàng.

Nâng cao hình ảnh thương hiệu thông qua việc cung cấp một trải nghiệm sự kiện chuyên nghiệp, hiện đại và dựa trên dữ liệu.

Dự án được lên kế hoạch triển khai trong 15 ngày làm việc, với ngân sách vận hành ước tính dưới $15/tháng cho quy mô 500 người dùng/sự kiện, thể hiện hiệu quả chi phí vượt trội.

1. Phát biểu Bài toán (Problem Statement)
1.1. Bối cảnh Hiện tại
Sự bùng nổ của kinh tế số đã biến các sự kiện trực tuyến thành phương thức giao tiếp chính. Tuy nhiên, trong khi công nghệ streaming video đã phát triển vượt bậc, khía cạnh tương tác hai chiều vẫn còn ở giai đoạn sơ khai.

1.2. Thách thức Cốt lõi
Các kênh tương tác hiện tại đang tạo ra một "nợ trải nghiệm" (experience debt) đáng kể, thể hiện qua các vấn đề:

"Sự hỗn loạn thông tin" (Information Overload): Các câu hỏi quan trọng bị "trôi" đi trong biển thông tin nhiễu, khiến diễn giả bỏ lỡ những cơ hội tương tác vàng.

Thiếu cơ chế sàng lọc (Lack of Prioritization): Diễn giả không có cách nào để biết câu hỏi nào được nhiều người quan tâm nhất, dẫn đến việc trả lời các câu hỏi ít liên quan.

Độ trễ cao và trải nghiệm đứt gãy (High Latency & Disjointed Experience): Các hệ thống truyền thống không được tối ưu cho tương tác tức thời, phá vỡ luồng theo dõi tự nhiên của sự kiện.

1.3. Tác động đến các Bên liên quan
Đối với Khán giả: Trải nghiệm trở nên thụ động và đáng thất vọng. Họ cảm thấy tiếng nói của mình không được lắng nghe.

Đối với Diễn giả: Khó nắm bắt được sự tập trung và phản ứng của khán giả để điều chỉnh nội dung cho phù hợp.

Đối với Nhà tổ chức: Hình ảnh thương hiệu bị ảnh hưởng tiêu cực, để lại ấn tượng về sự thiếu chuyên nghiệp.

1.4. Hậu quả Kinh doanh
Giảm Tỷ lệ Chuyển đổi: Việc không trả lời được các câu hỏi then chốt của khách hàng tiềm năng đồng nghĩa với việc mất đi doanh thu.

Lãng phí Chi phí Marketing: ROI của việc thu hút người tham dự bị suy giảm nghiêm trọng nếu trải nghiệm sự kiện không đáp ứng kỳ vọng.

Mất Lợi thế Cạnh tranh: Trải nghiệm tương tác vượt trội chính là yếu tố khác biệt hóa.

2. Kiến trúc Giải pháp (Solution Architecture)
2.1. Tổng quan Kiến trúc
Giải pháp được thiết kế dựa trên kiến trúc serverless 100% trên AWS, lấy AWS AppSync làm trung tâm điều phối. Kiến trúc này đảm bảo khả năng mở rộng tự động, hiệu suất cao, bảo mật và tối ưu hóa chi phí.

Sơ đồ 1: Kiến trúc tổng thể của Nền tảng Q&A và Polling Real-time

Luồng dữ liệu chính (Data Flow):

Client (React App): Người dùng tương tác với ứng dụng web được host dưới dạng nội dung tĩnh trên Amazon S3.

Amazon CloudFront: Phân phối nội dung tĩnh và các yêu cầu API trên toàn cầu để giảm độ trễ.

Amazon Cognito: Người dùng xác thực qua Cognito và nhận về JSON Web Token (JWT).

AWS AppSync (GraphQL API): Client gửi các yêu cầu GraphQL (Queries, Mutations) đến AppSync, đính kèm JWT để xác thực.

AWS Lambda Resolvers: Các hàm Lambda chứa logic nghiệp vụ, được kích hoạt bởi AppSync.

Amazon DynamoDB: Cơ sở dữ liệu NoSQL lưu trữ toàn bộ dữ liệu ứng dụng.

Real-time Subscriptions: Khi có thay đổi dữ liệu, AppSync tự động đẩy cập nhật đến các client đang lắng nghe qua WebSocket.

Amazon CloudWatch: Giám sát toàn bộ hệ thống, tập trung Logs và metrics để theo dõi, cảnh báo.

2.2. Lựa chọn Dịch vụ AWS và Lý do
Dịch vụ

Mục đích

Lý do Lựa chọn (Justification)

AWS AppSync

Trung tâm GraphQL API

Cung cấp GraphQL endpoint được quản lý hoàn toàn, tích hợp sẵn real-time qua WebSockets, caching, và bảo mật. Giảm 90% công sức phát triển so với việc tự xây dựng.

AWS Lambda

Xử lý Logic Nghiệp vụ

Nền tảng FaaS (Function-as-a-Service) cho phép chạy code không cần quản lý máy chủ, tự động co giãn và tối ưu chi phí triệt để.

Amazon DynamoDB

Cơ sở dữ liệu Chính

CSDL NoSQL có độ trễ ổn định ở mức mili giây đơn, mở rộng không giới hạn. Thiết kế Single-Table hiệu quả hơn 40% so với CSDL quan hệ cho workload này.

Amazon Cognito

Xác thực & Phân quyền

Cung cấp giải pháp quản lý người dùng hoàn chỉnh, an toàn, và dễ tích hợp. Tiết kiệm hàng tuần phát triển so với việc tự xây dựng.

Amazon CloudFront

Mạng phân phối nội dung (CDN)

Phân phối nội dung tĩnh và tăng tốc API. Giảm độ trễ cho người dùng toàn cầu lên đến 50%.

AWS SAM / CDK

Hạ tầng dưới dạng mã (IaC)

Cho phép định nghĩa và quản lý hạ tầng bằng code. Đảm bảo tính nhất quán 100% giữa các môi trường và cho phép CI/CD tự động.

2.3. Thiết kế Thành phần
GraphQL Schema: Thiết kế theo hướng "event-centric". eventId là tham số bắt buộc để đảm bảo client chỉ nhận dữ liệu liên quan.

Lambda Resolvers (Node.js/Python): Mỗi resolver là một hàm nhỏ, chuyên biệt.

upvoteQuestion: Sử dụng UpdateExpression với ConditionExpression trong DynamoDB để đảm bảo thao tác là nguyên tử (atomic).

getEventDetails: Một resolver duy nhất lấy tất cả thông tin sự kiện bằng một lệnh Query hiệu quả, tránh vấn đề N+1.

DynamoDB Single-Table Design:

Partition Key (PK): EVENT#{eventId}

Sort Key (SK): Sử dụng các tiền tố để phân loại dữ liệu: METADATA, QUESTION#{questionId}, POLL#{pollId}.

2.4. Kiến trúc Bảo mật (Security Architecture)
Xác thực (Authentication): Sử dụng Amazon Cognito User Pools làm phương thức xác thực mặc định. Mọi request đến AppSync phải đính kèm một JWT hợp lệ.

Phân quyền (Authorization): Áp dụng nhiều lớp:

AppSync Authorization Rules: Sử dụng chỉ thị @aws_auth(cognito_groups: ["admins"]) ngay trong schema để phân quyền ở cấp độ field.

Logic trong Lambda: Kiểm tra quyền chi tiết hơn (ví dụ: "chỉ tác giả của câu hỏi mới có quyền xóa").

IAM Roles (Principle of Least Privilege): Mỗi hàm Lambda có IAM Role riêng với quyền tối thiểu cần thiết.

Bảo vệ chống Tấn công: Tích hợp AWS WAF với AppSync để bảo vệ khỏi các cuộc tấn công phổ biến và giới hạn tần suất request (rate limiting).

2.5. Thiết kế Mở rộng & Hiệu năng
Khả năng mở rộng: Kiến trúc serverless vốn có khả năng tự động mở rộng để xử lý hàng trăm ngàn request đồng thời.

Tối ưu hiệu năng:

AppSync Caching: Kích hoạt caching ở cấp độ resolver cho các query ít thay đổi để giảm độ trễ.

Lambda Provisioned Concurrency: Cấu hình sẵn instance cho các hàm Lambda quan trọng để loại bỏ vấn đề "cold start".

CloudFront Caching: Cache nội dung tĩnh của ứng dụng React ở các Edge location để giảm thời gian tải trang.

3. Kế hoạch Triển khai Kỹ thuật (Technical Implementation)
3.1. Các Giai đoạn Triển khai
Dự án sẽ được chia thành 4 giai đoạn chính trong 15 ngày:

Phase 1: Nền tảng & Hạ tầng (3 ngày): Hoàn thiện thiết kế, chốt hạ GraphQL schema, thiết lập hạ tầng AWS cơ bản bằng AWS SAM/CDK.

Phase 2: Xây dựng Backend Logic (5 ngày): Viết code cho các Lambda resolver, cấu hình data source và kiểm thử tích hợp.

Phase 3: Phát triển Frontend & Tích hợp (5 ngày): Xây dựng giao diện React, tích hợp với backend qua AWS Amplify và kiểm thử End-to-End.

Phase 4: Hoàn thiện & Triển khai (2 ngày): Tối ưu hóa hiệu năng, bảo mật, hoàn thiện tài liệu và triển khai lên production.

3.2. Phương pháp Phát triển
Infrastructure as Code (IaC): Toàn bộ hạ tầng AWS sẽ được định nghĩa bằng AWS SAM, cho phép phiên bản hóa, tái sử dụng và tự động hóa.

API-Driven Development: GraphQL schema là "hợp đồng" giữa frontend và backend, cho phép hai đội làm việc song song.

CI/CD Pipeline: Sử dụng GitHub Actions để xây dựng một pipeline tự động hóa hoàn chỉnh:

Khi push lên nhánh develop: Tự động chạy Unit Test và triển khai lên staging.

Khi tạo Pull Request vào main: Chạy Unit Test, Integration Test và yêu cầu review.

Khi merge vào main: Tự động triển khai lên production sử dụng chiến lược Canary Deployment.

3.3. Chiến lược Kiểm thử
Loại Kiểm thử

Công cụ

Mục tiêu

Unit Test

Jest (cho Node.js)

Kiểm thử logic bên trong từng hàm Lambda, mock các lời gọi AWS SDK. Đảm bảo độ bao phủ code > 80%.

Integration Test

AppSync Console

Kiểm thử sự tương tác giữa AppSync và các Lambda resolver, xác nhận dữ liệu và logic phân quyền.

End-to-End (E2E) Test

Cypress / Playwright

Tự động hóa kịch bản sử dụng thực tế trên giao diện, ví dụ: "người dùng A đăng nhập, đặt câu hỏi; người dùng B thấy câu hỏi real-time và upvote".

Performance Test

Artillery.io

Mô phỏng hàng trăm người dùng đồng thời để xác định điểm nghẽn và đảm bảo hệ thống đáp ứng KPI về độ trễ.

4. Tiến độ & Cột mốc (Timeline & Milestones)
Dự án được thiết kế để hoàn thành trong 3 tuần (15 ngày làm việc).

Tuần

Ngày

Nhiệm vụ chính

Deliverable / Cột mốc

1

1-3

Phase 1: Hoàn thiện thiết kế, chốt schema, setup repo, triển khai IaC cơ bản.

Sơ đồ kiến trúc cuối cùng, schema.graphql, repo GitHub, hạ tầng trên staging.



4-5

Phase 2 (part 1): Xây dựng resolver cho Q&A (create, upvote).

Cột mốc 1: Các mutation và subscription cho Q&A hoạt động qua AppSync Console.

2

6-8

Phase 2 (part 2): Hoàn thiện các resolver còn lại (Polling, User).

Tất cả các resolver backend hoàn thành và vượt qua integration test.



9-10

Phase 3 (part 1): Xây dựng giao diện đăng nhập, hiển thị Q&A real-time.

Cột mốc 2: Giao diện người dùng có thể đăng nhập, gửi câu hỏi và nhận cập nhật real-time.

3

11-13

Phase 3 (part 2): Hoàn thiện giao diện Polling, viết kịch bản E2E.

Giao diện hoàn chỉnh. Các kịch bản test E2E tự động chạy thành công.



14

Phase 4 (part 1): Tối ưu hiệu năng, kiểm thử bảo mật.

Báo cáo kết quả kiểm thử, cấu hình AppSync Caching & WAF.



15

Phase 4 (part 2): Triển khai Production, bàn giao.

Cột mốc cuối: Hệ thống hoạt động trên production. Buổi demo và bàn giao.

5. Ước tính Ngân sách (Budget Estimation)
5.1. Giả định về Lưu lượng
Tính toán dựa trên một tháng hoạt động tại khu vực ap-southeast-1 (Singapore):

Số sự kiện: 10 sự kiện/tháng

Người dùng/sự kiện: 500 người

Tổng request: 50,000 requests/tháng

Tổng kết nối real-time: 10,000 giờ-kết-nối/tháng

Tổng người dùng hoạt động (MAUs): 5,000 MAUs/tháng

5.2. Bảng kê Chi phí Ước tính (AWS Region: ap-southeast-1)
Dịch vụ

Hạng mục Miễn phí (Free Tier)

Mức sử dụng Ước tính

Chi phí Ước tính (USD)

AWS AppSync

1M requests, 200k giờ-kết-nối

50k requests, 10k giờ-kết-nối

$0.00

AWS Lambda

1M requests, 400k GB-giây

50k requests (128MB, 200ms)

$0.00

Amazon DynamoDB

25 GB, 25 WCU, 25 RCU

~1 GB, <1 WCU/RCU (On-Demand)

~$1.25

Amazon Cognito

50,000 MAUs

5,000 MAUs

$0.00

Amazon CloudFront

1TB data, 10M requests

~20 GB data, 100k requests

~$1.80

Amazon CloudWatch

10 custom metrics, 5GB logs

Mặc định (trong free tier)

$0.00

AWS WAF

-

1 Web ACL, 1 Rule, 50k requests

~$6.20

Tổng cộng





~ $9.25 / tháng

5.3. Phân tích Lợi tức Đầu tư (ROI)
Chi phí Giải pháp Serverless: ~$9.25/tháng, tăng tuyến tính theo mức độ sử dụng.

Chi phí Giải pháp Truyền thống: > $49/tháng + chi phí nhân sự.

Kết luận ROI: Giải pháp serverless không chỉ giảm chi phí trực tiếp trên 80% mà còn loại bỏ gần như hoàn toàn gánh nặng quản trị hạ tầng.

6. Đánh giá Rủi ro (Risk Assessment)
Rủi ro

Ảnh hưởng

Khả năng

Chiến lược Giảm thiểu (Mitigation)

Kế hoạch Dự phòng (Contingency)

Kỹ thuật: Lambda cold start làm tăng độ trễ.

Trung bình

Cao

- Sử dụng Provisioned Concurrency.<br>- Tối ưu kích thước gói triển khai của Lambda.

Hiển thị chỉ báo tải (loading indicator).

Chi phí: Tăng đột biến do tấn công DDoS hoặc lỗi logic.

Cao

Thấp

- Sử dụng AWS WAF để rate-limit.<br>- Thiết lập AWS Budgets để cảnh báo.

Kích hoạt quy tắc WAF khẩn cấp. Tắt Lambda function gây lỗi để điều tra.

Bảo mật: Lộ thông tin nhạy cảm do cấu hình sai quyền.

Rất cao

Thấp

- Tuân thủ nguyên tắc quyền tối thiểu.<br>- Tự động hóa kiểm tra IAM.<br>- Yêu cầu peer review cho thay đổi bảo mật.

Thu hồi phiên làm việc, xoay vòng khóa truy cập. Phân tích CloudTrail để xác định phạm vi.

Vận hành: Phụ thuộc vào nhà cung cấp duy nhất (AWS Vendor Lock-in).

Thấp

Rất cao

- Thiết kế theo tiêu chuẩn mở (GraphQL).<br>- Sử dụng IaC giúp việc di chuyển khả thi hơn.

Chấp nhận rủi ro vì lợi ích từ hệ sinh thái AWS vượt trội hơn.

7. Kết quả Mong đợi (Expected Outcomes)
7.1. Chỉ số Thành công (Success Metrics)
Tỷ lệ tương tác: (Số câu hỏi + vote + poll) / tổng số người tham dự > 40%.

Độ trễ API (P99): Thời gian phản hồi của 99% request API < 300ms.

Tỷ lệ lỗi API: Số request trả về lỗi 5xx < 0.01%.

Chi phí mỗi người dùng tương tác: Tổng chi phí / tổng người dùng hoạt động < $0.01.

Mức độ hài lòng (CSAT): Điểm khảo sát sau sự kiện > 4.5/5.

7.2. Lợi ích Kinh doanh
Ngắn hạn (0-6 tháng): Cải thiện ngay lập tức chất lượng tương tác và mức độ hài lòng của khán giả.

Trung hạn (6-18 tháng): Xây dựng danh tiếng công nghệ. Dữ liệu thu thập được trở thành nguồn insight quý giá.

Dài hạn (18+ tháng): Nền tảng có thể phát triển thành sản phẩm Software-as-a-Service (SaaS) thương mại.

7.3. Cải tiến Kỹ thuật
Hiện đại hóa: Chuyển đổi sang kiến trúc real-time, event-driven hiện đại.

Tăng tốc độ phát triển: Kiến trúc serverless và IaC cho phép triển khai tính năng mới nhanh và an toàn hơn.

Nâng cao độ tin cậy: Hệ thống được quản lý bởi AWS sẽ có độ sẵn sàng (availability) 99.99%.

Phụ lục
A. GraphQL Schema (schema.graphql)
```
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
Bảng kê chi phí trong Phần 5.2 được ước tính bằng AWS Pricing Calculator vào ngày 7 tháng 7 năm 2025 cho khu vực ap-southeast-1.

C. Sơ đồ Kiến trúc
Sơ đồ kiến trúc chi tiết được trình bày trong Phần 2.1.

D. Nguồn tham khảo
Bizzabo (2022). Event Marketing Benchmarks and Trends Report.

Markletic (2023). Virtual Event Statistics for 2023.

AWS Pricing Calculator. Retrieved July 7, 2025, from https://calculator.aws/
