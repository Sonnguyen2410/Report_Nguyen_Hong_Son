---
title: "Cấu hình Môi trường Backend trên EC2"
date: 2026-07-27
weight: 8
chapter: false
pre: " <b> 5.4.8. </b> "
---

Trong bước này, người thực hiện sẽ khởi tạo tệp biến môi trường Production trên máy chủ EC2 tại đường dẫn `/home/ec2-user/.env`, siết chặt phân quyền tệp và xác minh tính hợp lệ của IAM Instance Profile.

---

### 8.1. Khởi tạo tệp `.env` Production trên EC2

Kết nối vào EC2 qua **AWS SSM Session Manager** và khởi tạo tệp biến môi trường:

```bash
sudo touch /home/ec2-user/.env
sudo chmod 600 /home/ec2-user/.env
sudo vi /home/ec2-user/.env
```

---

### 8.2. Khai báo các Nhóm Biến Môi trường Production

Điền nội dung chi tiết vào tệp `/home/ec2-user/.env`:

```dotenv
PORT=5000
NODE_ENV=production
TRUST_PROXY=true

MONGODB_URI=mongodb+srv://learnsphere_prod:<password>@learnsphere-cluster.mongodb.net/learnsphere?retryWrites=true&w=majority
MONGODB_REQUIRE_TRANSACTIONS=true

JWT_SECRET=c84ac761c5224c53b96ad34fc94a8194c84ac761c5224c53b96ad34fc94a8194
FRONTEND_URL=https://d2onzy56n3iw1w.cloudfront.net

AWS_REGION=ap-southeast-1
AWS_S3_BUCKET=learnsphere-media-575620421319

AI_PROVIDER=bedrock
BEDROCK_REGION=ap-southeast-1
BEDROCK_MODEL_ID=apac.amazon.nova-lite-v1:0
GROQ_API_KEY=gsk_learnsphere_ai_inference_key_sample
EOF
```

> **Nguyên tắc bảo mật:** Tuyệt đối **không khai báo `AWS_ACCESS_KEY_ID` hoặc `AWS_SECRET_ACCESS_KEY`** trong tệp `.env` vì Backend Node.js tự động sử dụng quyền hạn từ `LearnSphereEc2Role` được gán vào Instance Profile của EC2.

---

### 8.3. Kiểm tra Tên biến Môi trường mà Không làm Lộ Giá trị

Chạy kịch bản `awk` để rà soát sự tồn tại của các biến mà không in giá trị bảo mật ra màn hình terminal:

```bash
sudo awk -F= '
  /^[A-Z0-9_]+=/ {
    if (length($2) > 0) print "OK: " $1;
    else print "THIEU: " $1
  }
' /home/ec2-user/.env
```

> **Kết quả mong đợi:** Tất cả các tên biến quan trọng đều hiển thị trạng thái `OK: TÊN_BIẾN`.

![Kiểm tra các biến môi trường production trên EC2](/images/5-Workshop/5.4/5.4.8.3.png)
<p align="center"><i>Hình 5.4.8.3 — Kiểm tra danh sách các biến môi trường Production trên EC2 bằng kịch bản awk.</i></p>

---

### 8.4. Xác minh IAM Instance Profile & S3 Access từ EC2

Chạy lệnh xác thực danh tính giả định (Assumed Role) từ dòng lệnh EC2:

```bash
aws sts get-caller-identity
aws s3api head-bucket --bucket learnsphere-media-575620421319
```

**Kết quả trả về:**
```text
{
    "UserId": "AROAXXXXXXXXXXXXX:i-008c48e6c120b2978",
    "Account": "575620421319",
    "Arn": "arn:aws:sts::575620421319:assumed-role/LearnSphereEc2Role/i-008c48e6c120b2978"
}
```

> Điều này chứng minh EC2 đang nhận temporary credentials an toàn từ IAM Role `LearnSphereEc2Role` thông qua IMDSv2.

![EC2 nhận temporary credentials từ IAM Role](/images/5-Workshop/5.4/5.4.8.4.png)
<p align="center"><i>Hình 5.4.8.4 — Xác minh EC2 nhận temporary credentials an toàn từ IAM Role qua IMDSv2.</i></p>
