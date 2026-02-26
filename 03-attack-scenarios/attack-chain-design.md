1. Mục tiêu
```
Bài lab mô phỏng một chuỗi tấn công hoàn chỉnh theo vòng đời tấn công thực tế trong môi trường doanh nghiệp, bao gồm các giai đoạn:
Phishing → Initial Access → Brute Force → Credential Compromise → Lateral Movement → Detection → Automation
Thông qua kịch bản này, hệ thống được kiểm tra khả năng:
- Thu thập log từ nhiều máy trạm trong cùng mạng nội bộ
- Phát hiện hành vi bất thường dựa trên rule có sẵn và tùy chỉnh
- Tương quan sự kiện theo timeline để tái hiện chuỗi tấn công
- Tự động hóa xử lý cảnh báo thông qua SOAR (Shuffle)
```

2. Kịch bản tấn công
```
Một nhân viên trong công ty nhận được email giả mạo (phishing) từ địa chỉ trông có vẻ hợp lệ. Nội dung email thông báo về một tài liệu quan
 trọng cần xem ngay, kèm theo file đính kèm hoặc đường link tải về. Nhân viên mở file và thực thi mà không nghi ngờ.

File độc hại chứa một reverse shell payload: khi chạy, máy nạn nhân tự động mở kết nối ra ngoài đến máy chủ của kẻ tấn công. Vì đây là kết nối 

đi ra (outbound) chứ không phải kết nối vào (inbound), firewall thông thường không chặn được. Qua kết nối này, kẻ tấn công có thể điều khiển máy 

nạn nhân từ xa và từ đó hoạt động bên trong mạng nội bộ như một thiết bị hợp lệ — đây là lý do kẻ tấn công được cấp phát IP trong dải 192.168.138.0/24.

Lưu ý: Trong bài lab này, bước Initial Access được giả định đã thành công. Kali Linux đóng vai trò máy của kẻ tấn công đã có mặt trong mạng nội bộ và bắt đầu thực hiện các bước tiếp theo của chuỗi tấn công.
```

3. Chuỗi tấn công

Bước 0 — Initial Access (Phishing + Reverse Shell)
```
Mô tả: Kẻ tấn công gửi email phishing đến nhân viên công ty, đính kèm file độc hại (ví dụ: file .exe giả dạng PDF hoặc file Office có macro). Khi nhân viên thực thi file, một reverse shell được mở, cho phép kẻ tấn công kết nối vào máy nạn nhân từ bên ngoài.
Kết quả: Kẻ tấn công có quyền truy cập vào máy Victim 1 trong mạng nội bộ, từ đó có thể tiến hành các bước tiếp theo.
```

Bước 1 — Brute Force vào Victim 1
```
Mô tả: Từ máy Kali đã có mặt trong mạng, kẻ tấn công sử dụng công cụ Brute Force (Hydra) để thực hiện nhiều lần đăng nhập sai liên tiếp vào dịch vụ RDP của Victim 1 (Windows 10) nhằm dò tìm mật khẩu của các tài khoản khác trong hệ thống.
Công cụ: Hydra, Medusa
Mục tiêu:
- Tạo nhiều sự kiện đăng nhập thất bại liên tiếp trong thời gian ngắn
- Kiểm tra khả năng phát hiện tấn công dựa trên tần suất (threshold-based detection)
```

Bước 2 — Đăng nhập thành công (Credential Compromise)
```
Mô tả: Sau nhiều lần thử, kẻ tấn công tìm được mật khẩu đúng và đăng nhập thành công vào Victim 1 qua RDP.
Mục tiêu:
- Tạo sự kiện đăng nhập thành công ngay sau chuỗi thất bại
- Kiểm tra khả năng tương quan: nhiều lần thất bại + 1 lần thành công = tấn công brute force thành công
```

Bước 3 — Sử dụng thông tin xác thực đã chiếm đoạt
```
Mô tả: Kẻ tấn công sử dụng tài khoản vừa chiếm được để thực hiện các hành động trong hệ thống: duyệt file, truy cập tài nguyên mạng, leo thang đặc quyền nếu có thể.
Mục tiêu:
- Theo dõi hành vi sử dụng credential hợp lệ sau khi bị lộ
- Phân tích rủi ro khi tài khoản người dùng thông thường bị xâm nhập
```

Bước 4 — Lateral Movement sang Victim 2
```
Mô tả: Từ Victim 1 đã bị kiểm soát, kẻ tấn công thực hiện di chuyển ngang sang Victim 2 (Ubuntu) thông qua SSH, sử dụng credential tìm được hoặc credential mặc định.
Công cụ: SSH client, RDP client tích hợp sẵn
Mục tiêu:
- Phát hiện đăng nhập từ xa bất thường giữa hai máy nội bộ
- Theo dõi Logon Type 3 (Network) hoặc Type 10 (RemoteInteractive)
- Phân tích sự kiện: IP nguồn là máy nội bộ thay vì IP ngoài
```

Bước 5 — Phát hiện và tự động hóa phản ứng
```
Mô tả: Wazuh Server liên tục thu thập log từ cả hai Victim. Khi phát hiện các hành vi nghi ngờ vượt ngưỡng cảnh báo, Wazuh tự động gửi webhook đến Shuffle để kích hoạt quy trình phản ứng sự cố.
Wazuh phát hiện tại các điểm:
- Nhiều lần đăng nhập thất bại trong thời gian ngắn (brute force threshold)
- Đăng nhập thành công ngay sau chuỗi thất bại
- Đăng nhập từ xa bất thường giữa các máy nội bộ
- Tài khoản có đặc quyền cao được dùng trong phiên remote
```