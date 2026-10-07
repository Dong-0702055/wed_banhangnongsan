# LIFEGIFT

LIFEGIFT là ứng dụng thương mại điện tử gồm giao diện React cho khách hàng và quản trị viên, REST API Spring Boot và cơ sở dữ liệu MySQL.

## Cấu trúc dự án

- `lifegift-frontend/` - React 19 và Vite; Docker dùng Nginx để phục vụ giao diện.
- `lifegift-backend/` - REST API Spring Boot 4.1, Java 17, Maven.
- `lifegift.sql` - Schema và dữ liệu mẫu MySQL. Docker chỉ import file này khi tạo database volume lần đầu.

## Chạy bằng Docker Compose

Yêu cầu: Docker Desktop hoặc Docker Engine có Compose plugin.

1. Tạo file môi trường riêng trên máy:

   ```powershell
   Copy-Item .env.example .env
   ```

2. Sửa `.env`: đặt mật khẩu database riêng và thay `JWT_SECRET` bằng chuỗi ngẫu nhiên dài ít nhất 32 ký tự. Có thể tạo chuỗi bằng `openssl rand -hex 32`.
3. Từ thư mục gốc dự án, chạy:

   ```powershell
   docker compose up --build
   ```

4. Mở trang web tại <http://localhost:8080>. Frontend chuyển tiếp request `/api` tới Spring Boot. API cũng có thể truy cập trực tiếp tại <http://localhost:8081>.

Dừng bằng `Ctrl+C` hoặc `docker compose down`. Database nằm trong named volume `lifegift-db-data` và được giữ lại qua các lần khởi động. SQL chỉ chạy khi volume được khởi tạo lần đầu, không ghi đè database đã tồn tại.

> **Trước khi đưa hệ thống ra ngoài môi trường local:** file SQL có dữ liệu mẫu. Hãy rà soát/thay dữ liệu này, đổi thông tin tài khoản mẫu và dùng secret riêng. Không dùng giá trị mẫu trong `.env.example` cho môi trường công khai.

## Chạy cục bộ, không dùng Docker

### Backend

Yêu cầu: Java 17, MySQL 8 và Maven wrapper.

1. Tạo database tên `lifegift` trong MySQL và import `lifegift.sql`.
2. Đặt thông tin kết nối và JWT secret trong terminal trước khi chạy backend. Ví dụ PowerShell:

   ```powershell
   $env:SPRING_DATASOURCE_URL = "jdbc:mysql://localhost:3306/lifegift?useSSL=false&serverTimezone=Asia/Ho_Chi_Minh&allowPublicKeyRetrieval=true"
   $env:SPRING_DATASOURCE_USERNAME = "root"
   $env:SPRING_DATASOURCE_PASSWORD = "<mat-khau-mysql-cua-ban>"
   $env:JWT_SECRET = "<chuoi-ngau-nhien-it-nhat-32-ky-tu>"
   ```

   Không commit thông tin đăng nhập hoặc JWT secret thật.
3. Khởi động API:

   ```powershell
   cd lifegift-backend
   .\mvnw.cmd spring-boot:run
   ```

API chạy tại cổng `8080`.

### Frontend

Yêu cầu: Node.js 22 và npm.

```powershell
cd lifegift-frontend
npm ci
npm run dev
```

Vite phục vụ giao diện tại <http://localhost:5173> và proxy request `/api` tới `http://localhost:8080`. Nếu backend chạy địa chỉ khác, đặt biến `VITE_API_PROXY_TARGET`.

## Kiểm tra

```powershell
cd lifegift-backend
.\mvnw.cmd test

cd ..\lifegift-frontend
npm run lint
npm run build
```

## Container và cổng mạng

| Service | Cổng máy host | Cổng container | Chức năng |
| --- | ---: | ---: | --- |
| `frontend` | `FRONTEND_PORT` (mặc định `8080`) | `8080` | React qua Nginx và proxy `/api` |
| `backend` | `BACKEND_PORT` (mặc định `8081`) | `8080` | Spring Boot API |
| `db` | `MYSQL_PORT` (mặc định `3307`) | `3306` | MySQL 8 |

Nếu cổng mặc định đã được sử dụng, thay các biến cổng tương ứng trong `.env`.
