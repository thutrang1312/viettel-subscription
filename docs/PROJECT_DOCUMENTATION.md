# Tài liệu dự án Viettel Subscription

## 1. Tổng quan

Dự án là một website thương mại điện tử / nền tảng đăng ký gói cước di động cho thuê bao Viettel. Ứng dụng cho phép khách hàng xem danh sách gói cước, lọc theo loại gói, lựa chọn SIM và thực hiện đăng ký. Ngoài ra, hệ thống còn có phần quản trị để quản lý gói cước, xem báo cáo thống kê và xử lý đăng ký của khách hàng.

Dự án được xây dựng bằng Laravel 12 với giao diện Blade + Vite, sử dụng SQLite làm cơ sở dữ liệu mặc định để triển khai nhanh ở môi trường local.

## 2. Mục tiêu nghiệp vụ

- Hiển thị các gói cước theo danh mục: trả trước, trả sau, data, combo, data zone.
- Tìm kiếm và lọc gói cước theo mã gói, loại, chu kỳ, mạng 4G/5G.
- Cho phép khách hàng đăng ký gói cước qua form với thông tin cá nhân.
- Hỗ trợ chọn SIM cho các gói có liên quan đến SIM.
- Gửi OTP qua email trong quy trình đăng ký tài khoản.
- Phân quyền người dùng: user / admin.
- Admin có dashboard thống kê doanh thu, số lượng đăng ký, hồ sơ khách hàng và quản lý gói cước.

## 3. Công nghệ sử dụng

- PHP 8.2
- Laravel Framework 12
- SQLite (mặc định trong `.env.example`)
- MySQL có thể được cấu hình thay thế nếu cần
- Laravel Blade Templates
- Vite + Tailwind cho frontend build
- Composer + npm để quản lý dependency

## 4. Cấu trúc thư mục chính

```text
app/
  Http/Controllers/      # Controller xử lý route và logic nghiệp vụ
  Models/               # Eloquent models
config/                 # Cấu hình Laravel
database/
  migrations/            # Các migration
  seeders/              # Seeder dữ liệu
public/                 # File công khai, asset build, index.php
resources/
  views/                # Blade templates
  css/                  # CSS
  js/                   # JS frontend
routes/
  web.php               # Định nghĩa route chính
storage/
  app/
  framework/
  logs/
tests/                  # Test
```

## 5. Các model chính

- `User`: tài khoản người dùng, có trường `role` để phân quyền.
- `GoiCuoc`: thông tin gói cước, bao gồm mã gói, tên, loại gói, danh mục, chi phí, chu kỳ, mạng, ưu điểm.
- `UserPackage`: đơn đăng ký gói cước của khách hàng.
- `Sim`: thông tin SIM dùng cho các gói liên quan đến SIM.
- `GoiComboChiTiet`, `GoiTraSau`, `VungZone`: mô hình phụ hỗ trợ dữ liệu gói cước.

## 6. Luồng nghiệp vụ chính

### Người dùng

1. Người dùng truy cập trang chủ hoặc danh mục gói cước.
2. Chọn gói theo loại (data, combo, trả trước, trả sau).
3. Điền thông tin đăng ký và gửi form.
4. Hệ thống tạo bản ghi trong `user_package` với trạng thái `Chờ xử lý` hoặc `Đang hoạt động` tùy quy trình.
5. Nếu có các gói yêu cầu SIM, người dùng chọn SIM còn trống.

### Đăng ký tài khoản

1. Người dùng nhập thông tin name, phone, email, password.
2. Hệ thống gửi OTP qua email.
3. Người dùng xác nhận OTP.
4. Tài khoản được tạo và người dùng đăng nhập.

### Quản trị

1. Admin đăng nhập bằng tài khoản có `role = admin`.
2. Truy cập dashboard quản trị.
3. Xem thống kê doanh thu, số lượng đăng ký, số người dùng mới, số gói đang có.
4. Quản lý danh sách gói cước, thêm/sửa/xóa gói.
5. Xem và xử lý trạng thái đơn đăng ký.

## 7. Route quan trọng

Một số route chính có trong `routes/web.php`:

- `/login` - form đăng nhập
- `/register` - form đăng ký
- `/otp` - xác nhận OTP
- `/packages` - lọc và hiển thị gói cước
- `/goi-tra-truoc` - gói trả trước
- `/goi-tra-sau` - gói trả sau
- `/combo` - gói combo
- `/onlydata` - gói data
- `/tim-goi` - tìm kiếm gói cước
- `/goi_cuoc` - danh sách gói cước quản trị
- `/them_goi_cuoc` - thêm gói 
- `/dashboard/export` - xuất báo cáo thống kê
- `/adminIndex` - dashboard admin

## 8. Cài đặt môi trường local

### Yêu cầu

- PHP >= 8.2
- Composer
- Node.js >= 18
- Git

### Bước 1: Clone project

```bash
git clone <repo-url>
cd viettel-subscription
```

### Bước 2: Cài đặt dependency PHP

```bash
composer install
```

### Bước 3: Cài đặt dependency frontend

```bash
npm install
```

### Bước 4: Tạo file môi trường

```bash
cp .env.example .env
php artisan key:generate
```

### Bước 5: Cấu hình database

Dự án mặc định dùng SQLite:

```bash
touch database/database.sqlite
```

Nếu muốn dùng MySQL, chỉnh sửa các biến trong `.env`:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=viettel_subscription
DB_USERNAME=root
DB_PASSWORD=
```

### Bước 6: Chạy migration

```bash
php artisan migrate
```

Nếu muốn seed dữ liệu mẫu:

```bash
php artisan db:seed
```

### Bước 7: Chạy ứng dụng

```bash
npm run dev
php artisan serve
```

Truy cập: http://localhost:8000

## 9. Build production

```bash
npm run build
php artisan optimize
php artisan config:cache
php artisan route:cache
php artisan view:cache
```

## 10. Triển khai

File `render.yaml` đã được cấu hình cho việc deploy lên Render. Ngoài ra, `Dockerfile` cũng hỗ trợ build container nếu cần triển khai theo Docker.

### Render

- Dùng `render.yaml` như file cấu hình service.
- Cần khai báo `APP_KEY` và các biến môi trường `DB_*`, `MAIL_*` trong dashboard của Render.

### Docker

```bash
docker build -t viettel-subscription .
docker run -p 8000:8000 viettel-subscription
```

## 11. Lưu ý khi phát triển

- Trong môi trường local, mặc định `APP_URL` là `http://localhost` và mail đang được cấu hình theo `MAIL_MAILER=log`, nên OTP sẽ được ghi log thay vì gửi thật trong môi trường dev.
- Bản thân dự án có các controller và view còn đang trong quá trình hoàn thiện, vì vậy cần kiểm tra kỹ trước khi phát hành production.
- Nếu cần truy cập dashboard admin, hãy đảm bảo tài khoản khởi tạo có `role = admin`.

## 12. Kết luận

Viettel Subscription là một dự án Laravel phục vụ cho mô hình website bán / đăng ký gói cước di động, tập trung vào trải nghiệm người dùng, quản lý danh mục gói và thống kê doanh thu cho admin. Đây là nền tảng phù hợp để phát triển thêm module thanh toán, quản lý SIM, báo cáo doanh nghiệp, hoặc tích hợp API Viettel.
