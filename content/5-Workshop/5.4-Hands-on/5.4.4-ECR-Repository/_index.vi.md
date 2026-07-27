---
title: "Tạo Kho lưu trữ Amazon ECR"
date: 2026-07-27
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

Trong bước này, người thực hiện sẽ khởi tạo kho chứa **Amazon ECR (Elastic Container Registry)** riêng tư để quản lý và lưu trữ các Docker Image phiên bản Backend Node.js của hệ thống LearnSphere.

---

### 4.1. Khởi tạo Private ECR Repository

1. Mở **AWS Management Console** -> dịch vụ **Amazon ECR** -> chọn **Private repositories**.
2. Bấm nút **Create repository**.
3. **Visibility settings:** Chọn **Private**.
4. **Repository name:** Đặt tên kho lưu trữ:

```text
learnsphere-be
```

5. **Image scan settings:** Bật công tắc **Scan on push = ON** (Tự động chạy bộ quét lỗ hổng an ninh dựa trên cơ sở dữ liệu CVE mỗi khi có Docker Image mới được đẩy lên).
6. Bấm **Create repository** để hoàn tất.

![Docker images của Backend được lưu trên Amazon ECR](/images/5-Workshop/5.4/5.4.4.png)
<p align="center"><i>Hình 5.4.4 — Private ECR Repository lưu trữ và quét lỗ hổng các Docker Image của Backend.</i></p>

---

### 4.2. Thiết lập Lifecycle Policy Tự động dọn dẹp Image cũ

Để tối ưu hóa chi phí lưu trữ kho ảnh Docker trên ECR, chúng ta thiết lập quy tắc tự động xóa các image cũ:

1. Mở Repository `learnsphere-be` -> chọn mục **Lifecycle policies** bên menu trái -> chọn **Create rule**.
2. **Rule priority:** `1`.
3. **Description:** `Giữ lại 10 Docker Image mới nhất`.
4. **Image status:** `Tagged`.
5. **Match criteria:** Select `Image count more than` -> Count: `10`.
6. Bấm **Save** để áp dụng.

---

### 4.3. Quy chuẩn Đặt tên Tag theo Git Commit SHA

Mỗi Docker Image đẩy lên ECR trong quy trình CI/CD sẽ được gắn thẻ (Tag) trùng khớp với mã băm Git Commit SHA:

```text
575620421319.dkr.ecr.ap-southeast-1.amazonaws.com/learnsphere-be:<GIT_SHA>
```

> **Lợi ích:** Đặt tag theo Git Commit SHA giúp xác định chính xác phiên bản mã nguồn đang chạy trên Production, đồng thời phục vụ cơ chế tự động khôi phục (Rollback) về phiên bản trước đó khi gặp sự cố.
