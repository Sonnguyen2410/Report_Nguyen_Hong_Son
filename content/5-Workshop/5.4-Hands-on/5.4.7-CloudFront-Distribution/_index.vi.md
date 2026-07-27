---
title: "Cấu hình Amazon CloudFront"
date: 2026-07-27
weight: 7
chapter: false
pre: " <b> 5.4.7. </b> "
---

Trong bước này, người thực hiện sẽ khởi tạo **Amazon CloudFront Distribution** đóng vai trò là điểm truy cập HTTPS duy nhất cho toàn bộ ứng dụng LearnSphere, kết nối với S3 Frontend qua OAC và định tuyến API `/api/*` về máy chủ EC2.

---

### 7A. Cấu hình Frontend S3 & Origin Access Control (OAC)

#### 7A.1. Tạo CloudFront Distribution

1. Mở **AWS Management Console** -> dịch vụ **Amazon CloudFront** -> chọn **Create distribution**.
2. **Origin Domain (S3 FE):** Chọn Bucket `learnsphere-fe-575620421319.s3.ap-southeast-1.amazonaws.com`.
3. **Origin Access:** Select **Origin access control settings (recommended)** -> Create control setting (Bật Sign requests).
4. **Default Cache Behavior (`/*`):**
   - **Default root object:** `index.html`
   - **Viewer Protocol Policy:** `Redirect HTTP to HTTPS`
   - **Allowed HTTP Methods:** `GET, HEAD`
   - **Cache Policy:** `CachingOptimized`
5. Bấm **Create distribution**.

#### 7A.2. Cập nhật Bucket Policy cho S3 Frontend

Sau khi Distribution khởi tạo thành công, sao chép Bucket Policy và cập nhật tại S3 Bucket `learnsphere-fe-575620421319` (Tab **Permissions** -> **Bucket Policy**):

```json
{
  "Version": "2008-10-17",
  "Id": "PolicyForCloudFrontPrivateContent",
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
          "AWS:SourceArn": "arn:aws:cloudfront::575620421319:distribution/EQRDOBSCG5MC8"
        }
      }
    }
  ]
}
```

![Bucket policy được cập nhật sau khi tạo CloudFront OAC](/images/5-Workshop/5.4/5.4.7.A.png)
<p align="center"><i>Hình 5.4.7.A — Cập nhật S3 Bucket Policy cấp quyền đọc cho CloudFront OAC.</i></p>

---

### 7B. Kết nối Backend EC2 & Thêm Behavior API `/api/*`

#### 7B.1. Thêm EC2 Backend Origin

1. Mở CloudFront Distribution `EQRDOBSCG5MC8` -> tab **Origins** -> chọn **Create origin**.
2. **Origin Domain:** Nhập IPv4 Public DNS của EC2 Instance (ví dụ `ec2-xx-xx-xx-xx.ap-southeast-1.compute.amazonaws.com`).
3. **Protocol Policy:** `HTTP Only`, Port `5000`.

![Hai origin Frontend và Backend của CloudFront distribution](/images/5-Workshop/5.4/5.4.7.B.1.png)
<p align="center"><i>Hình 5.4.7.B.1 — Danh sách hai Origins (S3 Frontend và EC2 Backend) trên CloudFront Distribution.</i></p>

#### 7B.2. Tạo Behavior `/api/*`

1. Chuyển sang tab **Behaviors** -> chọn **Create behavior**:
   - **Path pattern:** `/api/*`
   - **Target Origin:** Chọn EC2 Backend Origin vừa tạo.
   - **Viewer Protocol Policy:** `Redirect HTTP to HTTPS`
   - **Allowed HTTP Methods:** `GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE`
   - **Cache Policy:** `CachingDisabled` (Tuyệt đối không lưu đệm dữ liệu API)
   - **Origin Request Policy:** `AllViewerExceptHostHeader` (Giữ nguyên JWT Headers)

> **Tác dụng:** Trình duyệt gọi `/api/*` tới CloudFront domain duy nhất, CloudFront chuyển tiếp về EC2 cổng 5000, **triệt tiêu hoàn toàn lỗi CORS và Mixed Content**.

![CloudFront định tuyến Frontend và Backend API](/images/5-Workshop/5.4/5.4.7.B.2.png)
<p align="center"><i>Hình 5.4.7.B.2 — Quy tắc CloudFront Behavior phân tách lưu lượng Frontend (`/*`) và Backend API (`/api/*`).</i></p>

#### 7B.3. Gắn CloudFront Function cho SPA Client-side Routing

Để tránh lỗi `404 Not Found` từ S3 khi người dùng ấn F5 tải lại trang trên các tuyến đường phụ như `/profile` hay `/courses`, chúng ta tạo một CloudFront Function:

```javascript
function handler(event) {
  var request = event.request;
  var uri = request.uri;

  // Nếu đường dẫn không chứa dấu chấm extension file, chuyển URI thành /index.html
  if (uri.endsWith("/") || !uri.split("/").pop().includes(".")) {
    request.uri = "/index.html";
  }

  return request;
}
```

> Gắn Function này vào sự kiện **Viewer Request** của Default Behavior `/*`.

![CloudFront Function hỗ trợ điều hướng SPA Router](/images/5-Workshop/5.4/5.4.7.B.3.png)
<p align="center"><i>Hình 5.4.7.B.3 — CloudFront Function chuyển hướng mọi tuyến đường con về /index.html hỗ trợ React Router.</i></p>

#### 7B.4. Ghi nhận Thông tin CloudFront Distribution

```text
Distribution ID: EQRDOBSCG5MC8
Domain Name: d2onzy56n3iw1w.cloudfront.net
```
