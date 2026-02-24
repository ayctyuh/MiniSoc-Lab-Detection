Trong bài lab này, Shuffle SOAR được triển khai theo mô hình self-hosted thông qua Docker và Docker Compose.
1. Yêu cầu môi trường
```
Hệ điều hành: Ubuntu
Docker Engine
Docker Compose
Tối thiểu 4GB RAM (khuyến nghị 8GB do sử dụng OpenSearch)
Mạng nội bộ NAT cùng dải với Wazuh Server
```

2. cài đặt
2.1. Cấu hình kernel cho opensearch
Shuffle sử dụng OpenSearch làm backend lưu trữ dữ liệu, do đó cần điều chỉnh tham số vm.max_map_count để tránh lỗi khi khởi động container.
Thực hiện lệnh sau:
```
sudo sysctl -w vm.max_map_count=262144
```
Để cấu hình có hiệu lực sau khi reboot hệ thống:
```
echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf
```

2.2 Tải mã nguồn shuffle
Tải mã nguồn shuffle từ github chính thức
```
git clone https://github.com/Shuffle/Shuffle.git
```
Sau khi clone thành công, cấu trúc thư mục chính của Shuffle bao gồm:
```
backend/ – dịch vụ xử lý logic
frontend/ – giao diện người dùng
shuffle-database/ – thư mục lưu trữ dữ liệu
docker-compose.yml – file triển khai container
```

2.3. Cấu hình môi trường triển khai
Tạo file .env để cấu hình các biến môi trường cần thiết cho Shuffle:
```
OUTER_HOSTNAME=192.168.138.149
FRONTEND_PORT=3001
FRONTEND_PORT_HTTPS=3443
```
Trong đó:
- OUTER_HOSTNAME: địa chỉ IP của máy triển khai Shuffle
- FRONTEND_PORT: cổng truy cập giao diện web
- FRONTEND_PORT_HTTPS: cổng HTTPS (nếu sử dụng)

2.4. Khởi động bằng docker compose
Kéo các Docker image cần thiết:
```
docker compose pull
```
Khởi động toàn bộ hệ thống Shuffle ở chế độ background:
```
docker compose up -d
```
Sau khi khởi động, kiểm tra trạng thái các container:
```
docker compose ps
```
![alt text](diagram/shuffle-start.png)
3. Kiểm tra trạng thái
![alt text](02-environment-setup/diagram/shuffle.png)