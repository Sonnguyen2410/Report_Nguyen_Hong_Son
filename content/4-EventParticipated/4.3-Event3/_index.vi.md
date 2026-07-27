---
title: "Event 3"
date: 2026-07-25
weight: 3
chapter: false
pre: " <b> 4.3. </b> "
---

# Bài thu hoạch “FCAJ - Agentic AI Build Week”

### Mục Đích Của Sự Kiện

- Tổng kết hành trình cuộc thi Hackathon Agentic AI Build Week (AABW) và chia sẻ trải nghiệm thực chiến xây dựng ứng dụng AI Agent.
- Giới thiệu các kiến trúc Agentic AI tiên tiến tích hợp công nghệ AWS Bedrock, AgentCore, SageMaker và các công cụ LLM hiện đại.
- Học hỏi phương pháp thiết kế ứng dụng AI đa kênh (Multi-channel AI Agent) và các mô hình giải quyết bài toán thực tế cho doanh nghiệp.
- Rút ra bài học kinh nghiệm về quản lý thời gian, làm việc nhóm dưới áp lực lớn và tối ưu hóa chi phí hạ tầng Cloud/AI.

### Danh Sách Diễn Giả / Các Đội Chia Sẻ

- **Team SignalScout** (Lê Tấn Lực, Đỗ Hoàng Hiếu, Triệu Quốc Hào, Nguyễn Văn Duy Khiêm, Nguyễn Công Minh, Nguyễn Trần Minh Quân)
- **Team Plan V** (Phạm Tiến Thuận Phát, Huỳnh Hoàng Long, Lê Minh Nghĩa, Trần Đại Vĩ, Nguyễn An) - Giải pháp Solution Architect Professional AI Native App
- **Team 3KA** (Huỳnh An Khương, Nguyễn Quốc Huy, Ngô Quang Khôi, Hoàng Lê Thành Đức, Đặng Nguyễn Phước Lộc, Đặng Trường Hưng) - Dự án S.H.E.P.H.E.R.D.
- **Team OneTeam** (Anh Duy, Trần Đông, Doãn Trung, Minh Việt, Anshul Roy) - Giải pháp KFC Bot Agent

### Nội Dung Nổi Bật

#### SignalScout: Hệ thống phát hiện tín hiệu thay đổi chiến lược doanh nghiệp

- **Thách thức:** Quản lý đa dịch vụ, quan sát (Observability) các dịch vụ phân tán, chi phí hạ tầng và phát hiện sớm các tín hiệu tái cấu trúc của doanh nghiệp.
- **Giải pháp & Kiến trúc:** Tích hợp AWS Bedrock, AgentCore Memory, LangFuse, WAF, DynamoDB và Amplify Hosting. Kết nối các tín hiệu rải rác thành câu chuyện rõ ràng có dẫn chứng cụ thể để hỗ trợ đội ngũ ra quyết định.
- **Tối ưu chi phí:** Phân tích bài toán chi phí chi tiết từ $17 - $130 (AWS) và đưa ra mô hình kiến trúc tối ưu hóa ngân sách vận hành.

#### Plan V: AI Native App hỗ trợ Solution Architect

- **Vấn đề thực tế:** Khách hàng yêu cầu thiết kế hệ thống AI khẩn cấp; Solution Architect phải trích xuất yêu cầu, vẽ sơ đồ, ước tính chi phí Cloud hoàn toàn thủ công và mất nhiều thời gian.
- **Giải pháp Plan V:** Xây dựng ứng dụng AI Native tự động phân tích câu lệnh tự nhiên (BRD/PRD), tạo sơ đồ Draw.io / AWS Architecture chuẩn, ước tính chi phí AWS cho vùng ap-southeast-1 và tự động sinh mã IaC.

#### S.H.E.P.H.E.R.D. (Team 3KA): Giám sát, phát hiện nguy cơ và điều phối đám đông thời gian thực

- **Giải pháp:** Xây dựng hệ thống phân tích luồng người dùng, mật độ đám đông và dự báo ùn tắc từ camera live stream.
- **Công nghệ ứng dụng:** Kết hợp Computer Vision (YOLO + ByteTrack), Amazon SageMaker, Amazon Bedrock AgentCore/Strands và React Dashboard.
- **Agentic Layer:** Bao gồm Autonomous Monitor (tự động cảnh báo khi ùn tắc) và Operator Copilot (hỗ trợ điều hành viên truy vấn bằng ngôn ngữ tự nhiên).

#### KFC Bot Agent (OneTeam): Đặt hàng đa kênh tự động bằng AI

- **Triết lý thiết kế:** "Design Once | Deploy Everywhere" - Xây dựng một nền tảng Agent duy nhất có khả năng tích hợp linh hoạt với nhiều kênh nhắn tin (Zalo, WhatsApp, Telegram, Instagram) qua các Channel Adapters.
- **Hiệu quả vượt trội:** Giảm chi phí xuống còn $0.006/đơn hàng, độ trễ xử lý phản hồi chỉ 3–5 giây, và giảm 60% lượng mã nguồn hạ tầng nhờ sử dụng AWS Bedrock AgentCore.

### Những Gì Học Được

#### Tư Duy Kỹ Thuật & Kiến Trúc AI

- **Thiết kế linh hoạt (Decoupled Architecture):** Tách biệt lớp tiếp nhận kênh (Ingestion Layer), lớp xử lý công cụ (Tool Layer) và lớp trí tuệ AI (Agent Core) giúp hệ thống dễ mở rộng mà không phải viết lại mã nguồn.
- **Kết hợp Computer Vision & LLM:** Học được cách tích hợp luồng xử lý video thời gian thực (YOLO) với các Agentic AI để ra quyết định cảnh báo tự động.
- **Tối ưu hóa chi phí (Cost Efficiency):** Hiểu rõ cách tính toán chi phí token Bedrock, dịch vụ nhớ (AgentCore Memory) và serverless để kiểm soát ngân sách sản phẩm.

#### Kỹ Năng Thực Chiến & Hackathon

- **Hành trình 24h Hackathon:** Hiểu được thực tế khắc nghiệt khi tham gia cuộc thi (áp lực thời gian, không sleep, xử lý lỗi code phút chót) và cách vượt qua sự thiếu hụt kiến thức ban đầu bằng làm việc nhóm.
- **Nguyên tắc sản phẩm nhỏ (Scope it tiny):** Thà hoàn thành một tính năng nhỏ gọn, chạy mượt mà còn hơn theo đuổi một ý tưởng quá lớn nhưng bị lỗi.
- **Kỹ năng Pitching & Demo:** Cách truyền tải câu chuyện sản phẩm trong 3 phút ngắn gọn, đánh trúng vào nỗi đau của khách hàng.

### Ứng Dụng Vào Công Việc

- **Áp dụng mô hình Agentic AI:** Tích hợp AWS Bedrock AgentCore vào các dự án chatbot nội bộ để tăng khả năng tự động xử lý tác vụ thay vì chỉ trả lời văn bản đơn thuần.
- **Tối ưu hóa kiến trúc ứng dụng:** Sử dụng các Adapter Pattern khi thiết kế hệ thống có nhiều kênh đầu vào (Multi-channel).
- **Thực hành kiểm soát chi phí Cloud:** Tính toán chi phí dự kiến (Cost Estimation) trước khi triển khai bất kỳ dịch vụ AWS nào lên mảng sản xuất.
- **Chuẩn bị cho các đợt Hackathon tiếp theo:** Chuẩn bị sẵn starter templates, phân công vai trò rõ ràng trong team và tập trung xây dựng MVP hoàn chỉnh.

### Trải nghiệm trong event

Buổi chia sẻ bài thu hoạch và tổng kết Agentic AI Build Week đã mang lại cho tôi những góc nhìn vô cùng thực tế và truyền cảm hứng mạnh mẽ về việc đưa AI từ ý tưởng ra sản phẩm thực tế. Một số trải nghiệm nổi bật:

#### Cảm hứng từ những câu chuyện thực chiến
- Ấn tượng với tinh thần chiến đấu 24 giờ liên tục của các team. Câu chuyện vượt qua rào cản "không có background AI", lần đầu làm quen với AWS nhưng vẫn tạo ra sản phẩm đoạt giải đã tiếp thêm rất nhiều động lực cho tôi.

#### Ấn tượng về tính ứng dụng của các dự án
- Các dự án như Plan V (tự động hóa công việc của SA) hay KFC Bot Agent (đặt hàng tự động $0.006/đơn) cho thấy tiềm năng kinh tế khổng lồ của AI khi được giải đúng bài toán thực tế của doanh nghiệp.

#### Bài học rút ra
- "Showing up is already half the battle" - Sự dũng cảm bắt đầu và tinh thần học hỏi quan trọng hơn việc chờ đợi bản thân hoàn hảo rồi mới làm.
- Giá trị lớn nhất từ các sự kiện không chỉ là giải thưởng, mà là những kỹ năng thực chiến tích lũy được và những người đồng đội tuyệt vời tìm thấy trên hành trình.

#### Một số hình ảnh khi tham gia sự kiện

![Hình ảnh tham gia sự kiện 3.1](/images/event3.1.jpg)

![Hình ảnh tham gia sự kiện 3.2](/images/event3.2.jpg)


> Tổng thể, sự kiện giúp tôi mở rộng tư duy về xây dựng ứng dụng AI Native, làm chủ các dịch vụ Serverless/AI trên AWS và tiếp thêm ngọn lửa đam mê theo đuổi con đường công nghệ.
