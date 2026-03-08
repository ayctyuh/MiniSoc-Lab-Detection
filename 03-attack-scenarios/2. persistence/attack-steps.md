# Lateral movement — Attack Steps
## Thông tin kịch bản
```
Kỹ thuật MITRE ATT&CK        T1021.001 — Remote Services: RDP
                             T1078 — Valid Accounts
                             T1003 — OS Credential Dumping
Nguồn tấn công               Ubuntu Agent (Victim 2) — 192.168.138.150
Mục tiêu                     Windows Agent (Victim 1) — 192.168.138.147
Dịch vụ                      RDP (port 3389)
Công cụ                      john the ripper, xfreerdp
```
## Các bước thực hiện
### Bước 1: Thu thập credential từ Victim 2
- Sau khi leo thang đặc quyền thành công lên root, kẻ tấn công bắt đầu giai đoạn thu thập thông tin nội bộ — mục tiêu là tìm hiểu có những tài khoản nào đang tồn tại trên hệ thống và tài khoản nào có thể khai thác tiếp.
```
cat /etc/passwd | grep -v nologin | grep -v false
```
![alt text](screenshots/check-user.png)
- Kết quả trả về cho thấy hệ thống có hai user đáng chú ý: huy (uid=1000) và ubuntu (uid=1001). User huy là tài khoản chính của máy với home directory tại /home/huy
![alt text](screenshots/check-user-huy.png)

### Bước 2: Khai thác history để tìm credential
- Một trong những sai lầm phổ biến nhất của người dùng Linux là vô tình để lộ password trong lịch sử lệnh. Khi chạy lệnh có chứa password dưới dạng tham số — ví dụ kết nối RDP, SSH, hoặc database — toàn bộ lệnh đó được lưu lại trong .bash_history. Với quyền root, kẻ tấn công có thể đọc file này của bất kỳ user nào:
```
cat /home/huy/.bash_history
```
![alt text](screenshots/bash-history.png)
- Ta có thể thấy ở dòng cuối cùng, đang hiển thị thông tin credential mà người dùng đã sử dụng để tiến hành kết nối đến RDP của một máy windows nào đó.
- 