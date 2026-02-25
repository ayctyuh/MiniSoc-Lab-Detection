Objective:
- Mục tiêu của bài lab là mô phỏng một chuỗi tấn công hoàn chỉnh theo vòng đời tấn công thực tế trong môi trường doanh nghiệp, bao gồm các giai đoạn:
Brute Force → Credential Compromise → Lateral Movement → Detection → Automation

Thông qua kịch bản này, hệ thống sẽ được kiểm tra khả năng:
- Thu thập log từ nhiều máy trạm
- Phát hiện hành vi bất thường dựa trên rule
- Tương quan sự kiện theo timeline
- Tự động hóa xử lý cảnh báo thông qua SOAR

Môi trường triển khai bao gồm:
- Attacker: Kali Linux (thực hiện tấn công Brute Force và di chuyển ngang)
- Victim 1: Windows 10 (cài Wazuh Agent)
- Victim 2: Windows hoặc Linux (cài Wazuh Agent)
- SIEM: Wazuh Server (thu thập log và phát hiện tấn công)
- SOAR: Shuffle (tự động hóa phản ứng sự cố)
Mô hình triển khai theo kiến trúc mạng nội bộ, trong đó các máy Agent gửi log về Wazuh Server để phân tích và sinh cảnh báo.

Chuỗi tấn công được thiết kế theo các bước sau:
1. Brute Force vào victim 1
Kẻ tấn công sử dụng công cụ Brute Force từ Kali Linux để thực hiện nhiều lần đăng nhập sai thông qua dịch vụ RDP của victim 1.
Mục tiêu:
- Tạo nhiều sự kiện đăng nhập thất bại (Event ID 4625)
- Kiểm tra khả năng phát hiện tấn công dựa trên tần suất đăng nhập sai

2. Đăng nhập thành công
Sau nhiều lần thử mật khẩu, kẻ tấn công đăng nhập thành công vào Windows 1.
Mục tiêu:
- Tạo sự kiện đăng nhập thành công (Event ID 4624)
- Xác định khả năng phát hiện đăng nhập thành công sau nhiều lần thất bại

3. Sử dụng thông tin xác thực đã chiếm đoạt
Kẻ tấn công sử dụng tài khoản đã bị lộ để thực hiện truy cập hợp lệ vào hệ thống.
Mục tiêu:
- Theo dõi hành vi sử dụng credential hợp lệ
- Phân tích nguy cơ khi tài khoản bị xâm nhập

4. Lateral Movement sang máy khác
Từ victim 1, kẻ tấn công thực hiện di chuyển ngang sang Victim 2 thông qua RDP hoặc SSH.
Mục tiêu:
- Phát hiện đăng nhập từ xa bất thường
- Theo dõi Logon Type 3 hoặc 10
- Phân tích sự kiện đăng nhập từ máy nội bộ sang máy nội bộ

5. Kích hoạt quy trình tự động hóa
Khi Wazuh phát hiện các hành vi nghi ngờ (Brute Force hoặc Lateral Movement), hệ thống sẽ gửi cảnh báo sang Shuffle.
Shuffle sẽ thực hiện một hoặc nhiều hành động tự động như:
- Gửi email cảnh báo
- Gắn mức độ nghiêm trọng (Severity)
- Tạo ticket sự cố
- Ghi log sự cố

Trong suốt chuỗi tấn công, hệ thống dự kiến sẽ phát hiện tại các điểm sau:
- Nhiều lần đăng nhập thất bại trong thời gian ngắn
- Đăng nhập thành công sau nhiều lần thất bại
- Đăng nhập từ xa bất thường giữa các máy nội bộ
- Tài khoản có quyền cao được sử dụng trong phiên remote