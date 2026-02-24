1. Yêu cầu hệ thống
```
Thành phần      Giá trị
OS              Ubuntu Server 22.04
IP Address      192.168.1.10
RAM             4GB
CPU             2 cores
Vai trò         Wazuh Manager + Indexer + Dashboard
```

3. Cài đặt Wazuh
3.1 Cập nhật hệ thống
    sudo apt update && sudo apt upgrade -y
3.2 Cài đặt Wazuh All-in-One
    curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
    sudo bash wazuh-install.sh -a

4. Kiểm tra dịch vụ
    sudo systemctl status wazuh-manager
    ![alt text](diagram/Wazuh-manager.png)
    sudo systemctl status wazuh-indexer
    ![alt text](diagram/Wazuh-indexer.png)
    sudo systemctl status wazuh-dashboard
    ![alt text](diagram/wazuh-dashboard.png)

5. Cấu hình Opensearch trỏ đúng API
    ![alt text](diagram/Opensearch-dashboard.png)

6. Truy cập dashboard: https://192.168.138.10:443
    ![alt text](diagram/Wazuh.png)


7. Tạo và quản lý agent
Đối với agent linux, chọn phiên bản linux phù hợp
![alt text](diagram/Linux-agent.png) 
Điền thông tin:
- ĐỊa chỉ IP server
- Tên Agent
Sau đó chạy lệnh
![alt text](diagram/Linux-agent-command.png)
Kiểm tra hoạt động bằng cách: sudo systemctl status wazuh-agent
![alt text](02-environment-setup/diagram/linux-agent-status.png)

Đối với agent window làm tương tự so với linux