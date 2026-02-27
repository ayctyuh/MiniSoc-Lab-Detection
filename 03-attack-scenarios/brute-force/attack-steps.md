# Brute Force SSH — Attack Steps
## Thông tin kịch bản
```
Kỹ thuật MITRE ATT&CKT       1110.001 — Brute Force: Password Guessing
Nguồn tấn công               Kali Linux — 192.168.138.20
Mục tiêu                     Ubuntu Agent (Victim 2) — 192.168.138.150
Dịch vụ                      SSH (port 22)
Công cụ                      HydraWordlistrockyou.txt
```

## Điều kiện tiên quyết

- Attacker đã có mặt trong mạng nội bộ (Initial Access đã thành công ở Bước 0)
- Victim 2 (Ubuntu) đã bật dịch vụ SSH và cho phép đăng nhập bằng mật khẩu (PasswordAuthentication yes)
- Wazuh Agent đã được cài đặt trên Victim 2 và kết nối về Wazuh Server

