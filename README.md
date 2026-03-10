# MiniSOC Lab Detection

Dự án xây dựng một hệ thống Security Operations Center (SOC) thu nhỏ trong môi trường lab ảo hóa, mô phỏng toàn bộ vòng đời tấn công thực tế từ giai đoạn xâm nhập ban đầu đến khi bị phát hiện và xử lý tự động.

---

## Mục tiêu

Bài lab kiểm tra khả năng của hệ thống SIEM + SOAR trong việc:

- Thu thập và phân tích log từ nhiều máy trạm trong mạng nội bộ
- Phát hiện hành vi tấn công dựa trên rule có sẵn và rule tùy chỉnh
- Tương quan sự kiện theo timeline để tái hiện chuỗi tấn công
- Tự động hóa phản ứng sự cố thông qua SOAR mà không cần can thiệp thủ công

---

## Chuỗi tấn công mô phỏng

```
Phishing → Initial Access → Brute Force SSH → Credential Access
         → Privilege Escalation → Persistence → Detection → Automation
```

| Giai đoạn | Kỹ thuật MITRE ATT&CK | Công cụ |
|-----------|----------------------|---------|
| Initial Access | T1566 — Phishing | — (giả định) |
| Brute Force SSH | T1110.001 — Password Guessing | Hydra, rockyou.txt |
| Credential Access | T1078 — Valid Accounts | SSH |
| Privilege Escalation | T1548.001 — SUID Abuse | python3 SUID |
| Persistence | T1098.004 — SSH Authorized Keys | ssh-keygen |
| Persistence | T1053.003 — Cron | crontab |
| Persistence | T1136 — Create Account | useradd |
| Detection | — | Wazuh SIEM |
| Automation | — | Shuffle SOAR |

---

## Môi trường lab

| Máy | IP | OS | Vai trò |
|-----|----|----|---------|
| Kali Linux | 192.168.138.20 | Kali Rolling | Kẻ tấn công |
| Ubuntu Agent | 192.168.138.150 | Ubuntu 24.04 | Nạn nhân (Victim 2) |
| Wazuh Server | 192.168.138.10 | Ubuntu 22.04 | SIEM |
| Shuffle | 192.168.138.149 | — | SOAR |
| Windows Agent | 192.168.138.147 | Windows 10 | Máy trạm (monitoring) |

Tất cả các máy nằm trong subnet `192.168.138.0/24`, mô phỏng flat network nội bộ doanh nghiệp nhỏ chưa áp dụng network segmentation.

---

## Cấu trúc thư mục

```
MINISOC-LAB-DETECTION/
│
├── 01-environment-setup/          # Cấu hình môi trường lab
│   ├── diagram/                   # Sơ đồ topology mạng
│   ├── 01-network-topology.md     # Mô tả hạ tầng mạng
│   ├── 02-wazuh-setup.md          # Hướng dẫn cài đặt Wazuh
│   └── 03-shuffle-setup.md        # Hướng dẫn cài đặt Shuffle
│
├── 02-attack-scenarios/           # Kịch bản tấn công
│   ├── logs/                      # File log thô thu thập được
│   ├── screenshots/               # Ảnh chụp màn hình minh họa
│   ├── attack-chain-design.md     # Thiết kế tổng thể chuỗi tấn công
│   ├── attack-steps.md            # Chi tiết từng bước thực hiện
│   └── wazuh-rule.md              # Rule Wazuh phát hiện tấn công
│
├── 03-automation-response/        # Phản ứng sự cố tự động
│   └── shuffle-workflow.md        # Workflow Shuffle xử lý alert
│
├── report-pdf/                    # Báo cáo tổng hợp dạng PDF
└── README.md                      # File này
```

---

## Công nghệ sử dụng

**SIEM — Wazuh 4.x**
- Thu thập log từ agent qua port 1514
- Phân tích và tương quan sự kiện theo rule
- File Integrity Monitoring (FIM) phát hiện thay đổi file nhạy cảm
- Active Response tự động block IP kẻ tấn công
- Webhook gửi alert sang Shuffle khi vượt ngưỡng Level 10

**SOAR — Shuffle**
- Nhận webhook từ Wazuh
- Tự động hóa quy trình phản ứng sự cố
- Gửi email cảnh báo đến admin
- Tạo ticket sự cố
- Kích hoạt các hành động xử lý không cần can thiệp thủ công

**Framework tham chiếu**
- MITRE ATT&CK — phân loại kỹ thuật tấn công
- Cyber Kill Chain — mô hình vòng đời tấn công

---

## Hướng dẫn đọc báo cáo

Đọc theo thứ tự sau để hiểu đầy đủ từ hạ tầng đến kịch bản:

1. `01-environment-setup/01-network-topology.md` — Hiểu topology và vai trò từng máy
2. `01-environment-setup/02-wazuh-setup.md` — Cách Wazuh được cài đặt và cấu hình
3. `01-environment-setup/03-shuffle-setup.md` — Cách Shuffle được tích hợp
4. `02-attack-scenarios/attack-chain-design.md` — Tổng quan chuỗi tấn công
5. `02-attack-scenarios/attack-steps.md` — Chi tiết từng bước tấn công và phân tích log
6. `02-attack-scenarios/wazuh-rule.md` — Rule phát hiện và Active Response
7. `03-automation-response/shuffle-workflow.md` — Workflow phản ứng tự động

---

## Kết quả đạt được

- Wazuh phát hiện thành công toàn bộ chuỗi tấn công từ brute force đến persistence
- Active Response tự động block IP kẻ tấn công trong vòng chưa đầy 1 giây sau khi rule 40111 kích hoạt
- Shuffle nhận webhook và thực thi workflow phản ứng tự động không cần can thiệp thủ công
- Xác nhận True Positive — phân biệt hành vi tấn công với hoạt động bình thường dựa trên tương quan sự kiện theo timeline
