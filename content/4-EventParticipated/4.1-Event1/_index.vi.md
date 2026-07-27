---
title: "Event 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---
# Bài thu hoạch “Meetup 06/06/2026”

### Mục Đích Của Sự Kiện

- Chia sẻ các best practices trong thiết kế ứng dụng hiện đại và tối ưu hóa hạ tầng điện toán đám mây.
- Giới thiệu phương pháp thiết kế hướng tên miền (Domain-Driven Design - DDD) và kiến trúc hướng sự kiện (Event-Driven Architecture).
- Hướng dẫn lựa chọn các dịch vụ compute/containerization phù hợp với quy mô và bài toán của dự án.
- Giới thiệu các giải pháp bảo mật, giám sát mạng tự động và công cụ AI hỗ trợ quy trình phát triển phần mềm (SDLC).

### Danh Sách Diễn Giả

- **Huỳnh Bảo** - Junior Cloud Native Developer at Endava Vietnam / Founder & Head Lab - ITea Lab
- **Lê Hoàng Gia Đại** - AWS Cloud Engineer / Cyber Security Engineer (AWS G3)
- **Nguyễn Quốc Bảo** - Cloud & Game Developer
- **Trương Huy Phước** - Speaker / Technical Contributor
- **Việt Phát** - AI Specialist / Researcher (Swinburne University of Technology)
- **Trần Trung Vinh** - System Administrator at Central Retail Group

### Nội Dung Nổi Bật

#### Đưa ra các ảnh hưởng tiêu cực của kiến trúc ứng dụng cũ & Giải pháp Containerization

- **Hạn chế của VM & Monolith cũ:** Thời gian release lâu, ngốn tài nguyên hệ thống (CPU, RAM, Storage do mỗi VM phải chạy OS riêng), khó scale cho các ứng dụng nhỏ.
- **Đóng gói ứng dụng với Docker:** Tối ưu hóa tài nguyên, đảm bảo tính nhất quán giữa môi trường Dev và Production ("Build once, run anywhere"), cải thiện quy trình CI/CD và kiến trúc Microservices.

#### Ứng dụng Machine Learning vào Bảo mật Đám mây (AWS WAF + ML NIDS)

- **Giới hạn của WAF truyền thống:** Chỉ dựa vào rule cố định (Rule-based), dễ bỏ sót các cuộc tấn công dạng novel / zero-day hoặc kỹ thuật spoofing phức tạp.
- **Giải pháp WAF + ML NIDS:** Sử dụng tập dữ liệu CSE-CIC-IDS2018 và mô hình Machine Learning (như LightGBM) để phân tích hành vi mạng, phát hiện bất thường thời gian thực và kết nối với AWS WAF/Security Hub để chủ động ngăn chặn mối đe dọa.

#### Kết nối Real-time & Multiplayer trên AWS WebSockets

- **So sánh kiến trúc kết nối:** Đánh giá trade-offs giữa UDP/ENet (Game FPS/Racing), HTTP Polling (Stateless/Leaderboard) và WebSocket (Turn-based/Lobby/Chat).
- **Mô hình Serverless Multiplayer:** Sử dụng API Gateway WebSocket, AWS Lambda và DynamoDB để quản lý trạng thái kết nối, ghép trận (Matchmaking) và truyền nhận thông điệp hai chiều giữa client (Godot Engine) và server với chi phí tối ưu.

#### Hiện đại hóa Tra cứu Dữ liệu với GraphRAG & Amazon Neptune

- **Hạn chế của RAG truyền thống:** Tác vụ suy luận đa bước (Multi-hop reasoning) kém, thiếu ngữ cảnh về mối quan hệ giữa các đối thực thể.
- **Sức mạnh của GraphRAG:** Sử dụng Knowledge Graph kết hợp Amazon Bedrock và Amazon Neptune/Neptune Analytics giúp truy vết mối quan hệ giữa các entities, mang lại câu trả lời chính xác, giàu ngữ cảnh hơn cho LLM.

#### Kỹ năng làm việc nhóm & Lộ trình phát triển sự nghiệp IT

- **The Art of Effective Teamwork:** 4 quy tắc vàng gồm Mục tiêu rõ ràng (Clear Goals), Đặt đúng người đúng việc (Right Person, Right Place), Giao tiếp mở & Lắng nghe chủ động (Open Communication), và Trách nhiệm cá nhân (Personal Accountability).
- **Hành trình từ Helpdesk đến Senior Sysadmin/DevOps:** Tư duy chủ động tự học (Linux, Networking, IaC, Cloud Mindset), học đi đôi với hành thông qua hands-on lab thay vì chạy theo bằng cấp thuần túy.

### Những Gì Học Được

#### Tư Duy Thiết Kế & Hạ Tầng

- **Tư duy Cloud-Native:** Chuyển đổi từ hạ tầng On-Premise thủ công sang hạ tầng đám mây có khả năng tự động mở rộng, quản lý dưới dạng mã (Infrastructure as Code - IaC) và tự động hóa CI/CD.
- **Tiếp cận Bảo mật chủ động (Proactive Security):** Không chỉ dựa vào các quy tắc phòng thủ tĩnh mà cần kết hợp phân tích hành vi (Behavior-based) bằng AI/ML để ứng phó với hiểm họa zero-day.

#### Kiến Trúc Kỹ Thuật

- **Tối ưu hóa tài nguyên với Containers:** Hiểu sâu cơ chế làm việc của Docker images, Dockerfile layers, và cách ứng dụng container trong hệ thống thực tế.
- **Xử lý kết nối thời gian thực:** Thiết kế hệ thống xử lý sự kiện bất đồng bộ dựa trên API Gateway WebSocket và Serverless Lambda, hạn chế vấn đề Stale Connections và tối ưu chi phí truy xuất DynamoDB.
- **Biết cách ứng dụng GraphRAG:** Lựa chọn đường dẫn triển khai Fully Managed (Bedrock + Neptune Analytics) hoặc Custom (LlamaIndex + Neptune) tùy theo bài toán dữ liệu của doanh nghiệp.

#### Kỹ Năng Mềm & Định Hướng Sự Nghiệp

- **Tư duy Vận hành & Giải quyết vấn đề:** "Never test in production" – luôn tuân thủ quy trình kiểm thử, xây dựng hệ thống giám sát (Monitoring) trước khi sự cố xảy ra.
- **Làm việc nhóm hiệu quả:** Tận dụng các công cụ kỹ thuật số (Google Workspace, Slack, Discord, Trello) để tối ưu hóa hiệu suất giao tiếp trong team.

### Ứng Dụng Vào Công Việc

- **Áp dụng Docker vào dự án:** Đóng gói môi trường phát triển ứng dụng bằng Docker/Dockerfile giúp đồng bộ hóa môi trường làm việc giữa các thành viên trong nhóm.
- **Tích hợp WebSocket/Serverless:** Thử nghiệm xây dựng các tính năng tương tác real-time (như chat hoặc notification) bằng AWS API Gateway WebSocket và AWS Lambda.
- **Nâng cấp giải pháp RAG:** Nghiên cứu bổ sung Knowledge Graph (GraphRAG) vào các bài toán xử lý tài liệu và tra cứu kiến thức nội bộ để tăng độ chính xác.
- **Cải thiện quy trình phát triển cá nhân:** Xây dựng lộ trình học tập tập trung vào Linux, Networking và AWS Services; thực hành tạo bài lab thực tế để củng cố kỹ năng SysAdmin/DevOps.
- **Tăng cường hiệu quả làm việc nhóm:** Thiết lập mục tiêu rõ ràng, phân công nhiệm vụ phù hợp với thế mạnh của từng thành viên và tăng cường trao đổi trực tiếp trong dự án.

### Trải nghiệm trong event

Tham gia workshop **“GenAI-powered App-DB Modernization”** vào ngày 06/06/2026 là một trải nghiệm rất bổ ích, giúp tôi có cái nhìn toàn diện về cách hiện đại hóa ứng dụng, cơ sở dữ liệu cũng như định hướng phát triển sự nghiệp trong lĩnh vực công nghệ. Một số trải nghiệm nổi bật:

#### Học hỏi từ các diễn giả có chuyên môn cao
- Các diễn giả giàu kinh nghiệm thực chiến đã mang đến những chia sẻ sinh động từ lý thuyết đến demo trực tiếp, giúp tôi dễ dàng hình dung các mô hình kiến trúc complex trên AWS.
- Nhận được những lời khuyên chân thành và thực tế về hành trình phát triển bản thân trong ngành IT từ một cựu IT Helpdesk tiến lên Senior SysAdmin.

#### Trải nghiệm kỹ thuật thực tế
- Được quan sát demo trực quan về cách ghép trận thời gian thực (Matchmaking) bằng WebSocket & Lambda.
- Hiểu rõ cơ chế xây dựng Knowledge Graph bằng Amazon Neptune để phục vụ các ứng dụng GenAI phức tạp.
- Tiếp cận cách thức huấn luyện và đánh giá mô hình Machine Learning (LightGBM) trên tập dữ liệu sự cố mạng thực tế (CSE-CIC-IDS2018).

#### Tương tác và Kết nối
- Sự kiện tạo không gian cởi mở cho phép người tham dự thoải mái đặt câu hỏi, thảo luận trực tiếp với diễn giả về các vướng mắc kỹ thuật.
- Kết nối thêm với nhiều bạn học và đồng nghiệp có cùng đam mê về Cloud, DevOps, AI và Game Development.

#### Bài học rút ra
- Công nghệ thay đổi rất nhanh, việc nắm vững kiến thức cốt lõi (Core Fundamentals) về mạng, hệ điều hành và tư duy kiến trúc là chìa khóa để thích ứng.
- Hiện đại hóa hệ thống là một lộ trình từng bước (Phased approach), đòi hỏi sự kết hợp giữa công nghệ hiện đại (Docker, Serverless, GenAI) và quy trình vận hành bảo mật chuẩn chỉnh.

#### Một số hình ảnh khi tham gia sự kiện

![Hình ảnh tham gia sự kiện 1.1](/images/event1.2.JPG)

![Hình ảnh tham gia sự kiện 1.2](/images/event1.1.png)

![Hình ảnh tham gia sự kiện 1.3](/images/event1.3.png)

> Tổng thể, sự kiện không chỉ cung cấp kiến thức kỹ thuật chuyên sâu mà còn truyền cảm hứng mạnh mẽ, giúp tôi định hình rõ ràng hơn con đường phát triển chuyên môn và kỹ năng làm việc trong tương lai.
