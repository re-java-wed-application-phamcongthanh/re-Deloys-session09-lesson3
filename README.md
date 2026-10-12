# Bài 3: Viết Dockerfile Đóng Gói Trang Web HTML Tĩnh

## 🎯 1. Mục Tiêu
- Viết tệp `Dockerfile` ngắn gọn (2 dòng chỉ thị chính) để đóng gói một trang web HTML tĩnh vào Docker image.
- Build Docker Image với tên `my-html-app:v1`.
- Khởi chạy container trên cổng `8081` và kiểm tra phản hồi nội dung HTML qua lệnh `curl`.

---

## 📁 2. Cấu Trúc Thư Mục
```text
bai3/
├── Dockerfile        # Cấu hình đóng gói Nginx image
├── index.html        # Trang HTML tĩnh
└── README.md         # Tài liệu hướng dẫn & báo cáo kết quả
```

---

## 🛠️ 3. Mã Nguồn Cấu Hình

### 3.1. Tệp `index.html`
Nội dung trang web tĩnh:
```html
<h1>Hello Docker Session 09!</h1>
```

### 3.2. Tệp `Dockerfile`
```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
```

**Giải thích chỉ thị:**
- `FROM nginx:alpine`: Sử dụng base image Nginx chạy trên nền hệ điều hành Alpine siêu nhẹ, bảo mật và khởi động tức thì.
- `COPY index.html /usr/share/nginx/html/index.html`: Sao chép file `index.html` từ máy host vào đúng đường dẫn thư mục gốc mà Nginx phục vụ web theo mặc định (`/usr/share/nginx/html/`).

---

## 🚀 4. Quy Trình Build & Kiểm Thử

### Bước 1: Build Docker Image
```bash
docker build -t my-html-app:v1 .
```

**Kết quả build:**
```text
#1 [internal] load build definition from Dockerfile
#2 [internal] load metadata for docker.io/library/nginx:alpine
#3 [internal] load .dockerignore
#4 [internal] load build context
#5 [1/2] FROM docker.io/library/nginx:alpine
#6 [2/2] COPY index.html /usr/share/nginx/html/index.html
#7 naming to docker.io/library/my-html-app:v1 done
```

---

### Bước 2: Khởi chạy Container từ Image vừa build
Chạy container ở chế độ ngầm (`-d`), ánh xạ cổng `8081:80`, đặt tên là `html-app`:
```bash
docker run -d -p 8081:80 --name html-app my-html-app:v1
```

**Kết quả:**
```text
61df01375ed4581d9260d328b442a4aa70fd52040b5e2f32ee8b01b7ff2b1cc0
```

---

### Bước 3: Kiểm tra phản hồi qua `curl`
```bash
curl http://localhost:8081
```

**Kết quả mong đợi & thực tế:**
```html
<h1>Hello Docker Session 09!</h1>
```
> ✅ **Xác nhận**: Lệnh `curl` trả về chính xác nội dung của file `index.html`.

---

### Bước 4: Dọn dẹp tài nguyên
Sau khi hoàn tất kiểm tra, dừng và gỡ bỏ container:
```bash
docker stop html-app && docker rm html-app
```
*(Tùy chọn) Gỡ bỏ image thử nghiệm:*
```bash
docker rmi my-html-app:v1
```

---

## 💡 5. Điểm Cốt Lõi Rút Ra
1. **Tính bất biến (Immutability)**: Bằng cách COPY mã nguồn vào image tại thời điểm build, image chứa đầy đủ ứng dụng và môi trường chạy mà không phụ thuộc vào máy host.
2. **Kích thước tối ưu**: Nhờ base image `nginx:alpine`, image cuối cùng chỉ khoảng ~40MB, sẵn sàng để phân phối nhanh chóng trên CI/CD pipeline hoặc Kubernetes/Cloud.
