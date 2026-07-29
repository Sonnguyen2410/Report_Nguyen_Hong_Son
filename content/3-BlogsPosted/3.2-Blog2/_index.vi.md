---
title: "Blog 2"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---

# Tại sao nhóm mình không dùng Amazon Bedrock Knowledge Bases dù đang làm chatbot RAG?

Chào mọi người,

Trong quá trình làm dự án chatbot hỏi đáp tài liệu (RAG), nhóm mình sử dụng Amazon Bedrock để gọi Claude trả lời câu hỏi dựa trên các tài liệu người dùng upload. Khi tìm hiểu cách xây dựng RAG trên AWS, mình thấy hầu như tài liệu nào cũng nhắc đến Knowledge Bases for Amazon Bedrock.

Điều này cũng dễ hiểu. Knowledge Bases gần như làm thay toàn bộ pipeline RAG:
* đọc tài liệu
* chunk
* tạo embedding
* lưu vector
* retrieval

Nghe rất hấp dẫn. Ban đầu mình cũng nghĩ: "Vậy cần gì phải tự xây nữa?" Nhưng sau khi đọc AWS Blog và đối chiếu với kiến trúc của nhóm, tụi mình lại quyết định không sử dụng Knowledge Bases mà vẫn tự xây pipeline với FAISS.

## Knowledge Bases giúp làm gì?

AWS mô tả Knowledge Bases là một dịch vụ Fully Managed RAG.

Chỉ cần chỉ định nơi lưu tài liệu (ví dụ Amazon S3), Bedrock sẽ tự động:
* đọc tài liệu
* chia nhỏ nội dung
* tạo embedding
* lưu vector database
* truy xuất context
* gửi context cho Foundation Model

Nói cách khác, rất nhiều bước mà developer thường phải tự viết đã được AWS tự động hóa.

## Ban đầu mình định dùng luôn

Lúc mới đọc tài liệu mình nghĩ đây gần như là lựa chọn hoàn hảo.

* Không cần FAISS.
* Không cần viết pipeline ingest.
* Không cần quản lý vector database.

Mọi thứ đều có sẵn.

## Nhưng khi nhìn lại dự án...

Đây mới là điều khiến nhóm mình thay đổi quyết định. Chatbot của nhóm không chỉ upload PDF. Người dùng còn upload:
* PDF scan
* DOCX
* tài liệu có bảng
* tài liệu có ảnh

Có những file phải OCR trước, file phải xử lý riêng, file cần chunk theo heading, file cần chunk theo section. Nếu dùng Knowledge Bases thì phần ingest sẽ được AWS quản lý. Trong khi nhóm mình lại muốn kiểm soát toàn bộ pipeline.

## Đây là lý do nhóm mình chọn FAISS

FAISS khiến nhóm phải tự làm nhiều việc hơn. Nhưng đổi lại nhóm có thể chủ động:
* tự OCR bằng Textract khi cần
* tự quyết định cách chunk
* tự thêm metadata theo user
* tự xử lý multi-tenant
* tự cập nhật vector khi user xóa tài liệu

Đây đều là những thứ nhóm mình cần trong dự án.

## Điều mình học được

Sau khi đọc AWS Blog mình nhận ra một điều khá thú vị. Knowledge Bases không phải là "phiên bản tốt hơn" của FAISS. Nó chỉ là một cách khác để xây dựng RAG.

Nếu muốn triển khai nhanh, Knowledge Bases gần như là lựa chọn lý tưởng. Nhưng nếu muốn kiểm soát toàn bộ pipeline ingest và retrieval, việc tự xây vẫn có nhiều lợi thế hơn. Theo mình, không có lựa chọn nào đúng tuyệt đối. Quan trọng là kiến trúc nào phù hợp với yêu cầu của dự án.

## Kết luận

Ban đầu mình nghĩ: AWS đã có Knowledge Bases thì chắc không cần tự xây RAG nữa. Sau khi tìm hiểu kỹ hơn, mình mới nhận ra Knowledge Bases giải quyết rất tốt bài toán triển khai nhanh. Trong khi đó, những dự án cần tùy biến sâu về xử lý tài liệu, chunking, metadata hay retrieval vẫn có lý do để xây dựng pipeline riêng. Với chatbot của nhóm mình, FAISS không phải vì "tốt hơn" Knowledge Bases, mà đơn giản là phù hợp hơn với cách hệ thống đang được thiết kế.

---

🔗 **LINK BLOG THAM KHẢO**
AWS News Blog – Knowledge Bases now delivers fully managed RAG experience in Amazon Bedrock:
[https://aws.amazon.com/vi/blogs/aws/knowledge-bases-now-delivers-fully-managed-rag-experience-in-amazon-bedrock/](https://aws.amazon.com/vi/blogs/aws/knowledge-bases-now-delivers-fully-managed-rag-experience-in-amazon-bedrock/)

🔗 **Nguồn bài viết trên Facebook:** [AWS Study Group Post](https://www.facebook.com/groups/awsstudygroupfcj/posts/2226903721407921?notif_id=1785325801641354&notif_t=tagged_with_story&ref=notif)