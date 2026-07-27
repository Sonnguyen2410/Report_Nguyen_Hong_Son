---
title: "Cấu hình Amazon CloudFront"
date: 2026-07-27
weight: 7
chapter: false
pre: " <b> 5.4.7. </b> "
---

# 5.4.7. Cấu hình Amazon CloudFront (CDN, Origins, Behaviors & SPA Function)

Trong bước này, người thực hiện sẽ khởi tạo **Amazon CloudFront Distribution** đóng vai trò là điểm truy cập HTTPS duy nhất cho toàn bộ ứng dụng LearnSphere, định tuyến giao diện tĩnh về S3 và chuyển tiếp API về máy chủ EC2.

---

### 1. Tạo CloudFront Distribution

1. Truy cập dịch vụ **Amazon CloudFront** $\rightarrow$ chọn **Create distribution**.
2. **Origin Domain 1 (S3 Frontend):** Chọn Bucket `learnsphere-fe-575620421319.s3.ap-southeast-1.amazonaws.com`.
3. **Origin Access:** Chọn **Origin access control settings (recommended)** $\rightarrow$ chọn **Create control setting** (Bật Sign requests).
4. **Default Cache Behavior (`/*`):**
   - **Viewer Protocol Policy:** `Redirect HTTP to HTTPS`.
   - **Allowed HTTP Methods:** `GET, HEAD`.
   - **Cache Policy:** `CachingOptimized`.
5. Bấm **Create distribution**.

---

### 2. Cập nhật Bucket Policy cho S3 Frontend

1. Sau khi tạo Distribution, CloudFront hiển thị thông báo vàng yêu cầu cập nhật S3 Bucket Policy.
2. Chọn **Copy policy**.
3. Mở S3 Bucket `learnsphere-fe-575620421319` $\rightarrow$ tab **Permissions** $\rightarrow$ mục **Bucket policy** bấm **Edit** và dán policy để cấp quyền OAC:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCloudFrontServicePrincipal",
      "Effect": "Allow",
      "Principal": {
        "Service": "cloudfront.amazonaws.com"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::learnsphere-fe-575620421319/*",
      "Condition": {
        "ArnLike": {
          "AWS:SourceArn": "arn:aws:cloudfront::575620421319:distribution/*"
        }
      }
    }
  ]
}
```

---

### 3. Thêm Origin 2 (EC2 Backend) & Behavior API (`/api/*`)

1. Tại CloudFront Distribution vừa tạo $\rightarrow$ tab **Origins** $\rightarrow$ chọn **Create origin**.
2. **Origin Domain:** Nhập IPv4 Public DNS của EC2 Instance (ví dụ `ec2-xx-xx-xx-xx.ap-southeast-1.compute.amazonaws.com`).
3. **Protocol Policy:** `HTTP Only`, Port `5000`.
4. Chuyển sang tab **Behaviors** $\rightarrow$ chọn **Create behavior**:
   - **Path pattern:** `/api/*`
   - **Target Origin:** Chọn EC2 Backend Origin vừa tạo.
   - **Viewer Protocol Policy:** `Redirect HTTP to HTTPS`.
   - **Allowed HTTP Methods:** `GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE`.
   - **Cache Policy:** `CachingDisabled`.
   - **Origin Request Policy:** `AllViewerExceptHostHeader`.

---

### 4. Gắn CloudFront Function xử lý Client-Side SPA Routing

1. Tại menu CloudFront bên trái $\rightarrow$ chọn **Functions** $\rightarrow$ bấm **Create function**.
2. **Name:** `LearnSphereSPARouting`.
3. Dán đoạn mã kịch bản điều hướng SPA Router:

```javascript
function handler(event) {
    var request = event.request;
    var uri = request.uri;
    
    // Nếu URL không chứa dấu chấm extension (file), điều hướng về /index.html
    if (!uri.includes('.')) {
        request.uri = '/index.html';
    }
    
    return request;
}
```

4. Bấm **Save changes** $\rightarrow$ tab **Publish** $\rightarrow$ chọn **Publish function**.
5. Trong mục **Associated distributions**, đính kèm Function này vào Distribution tại sự kiện **Viewer Request** của Behavior mặc định `/*`.
