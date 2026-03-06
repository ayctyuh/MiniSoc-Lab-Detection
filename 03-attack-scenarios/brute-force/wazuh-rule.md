## 1. Tổng quan
File này mô tả các rule Wazuh được sử dụng để phát hiện tấn công Brute Force SSH, bao gồm các rule mặc định có sẵn, custom rule bổ sung để tăng độ nhạy phát hiện, cấu hình Active Response tự động block IP kẻ tấn công, và cách kiểm tra rule hoạt động đúng.
## 2. Các rule mặc định của Wazuh
- Các rule mặc định của Wazuh sẽ nằm tại: ```/var/ossec/ruleset/rules/0095-sshd_rules.xml```

### Rule 5503 — PAM Authentication Failure
```
<rule id="5503" level="5">
    <if_sid>5500</if_sid>
    <match>authentication failure; logname=</match>
    <description>PAM: User login failed.</description>
    <group>authentication_failed,pci_dss_10.2.4,pci_dss_10.2.5,gpg13_7.8,gdpr_IV_35.7.d,gdpr_IV_32.2,hipaa_164.312.b,nist_800_53_AU.14,nist_800_53_AC.7,tsc_CC6.1,tsc_CC6.8,tsc_CC7.2,tsc_CC7.3,</group>
  </rule>
```

### Rule 5760 — SSH Failed Password
```
<rule id="5760" level="5">
    <if_sid>5700,5716</if_sid>
    <match>Failed password|Failed keyboard|authentication error</match>
    <description>sshd: authentication failed.</description>
    <mitre>
      <id>T1110.001</id>
      <id>T1021.004</id>
    </mitre>
    <group>authentication_failed,gdpr_IV_35.7.d,gdpr_IV_32.2,gpg13_7.1,hipaa_164.312.b,nist_800_53_AU.14,nist_800_53_AC.7,pci_dss_10.2.4,pci_dss_10.2.5,tsc_CC6.1,tsc_CC6.8,tsc_CC7.2,tsc_CC7.3,</group>
  </rule>
```

### Rule 2501 — Failed login
```
<group name="syslog,access_control,">
  <rule id="2501" level="5">
    <match>FAILED LOGIN |authentication failure|</match>
    <match>Authentication failed for|invalid password for|</match>
    <match>LOGIN FAILURE|auth failure: |authentication error|</match>
    <match>authinternal failed|Failed to authorize|</match>
    <match>Wrong password given for|login failed|Auth: Login incorrect|</match>
    <match>Failed to authenticate user</match>
    <group>authentication_failed,pci_dss_10.2.4,pci_dss_10.2.5,gpg13_7.8,gdpr_IV_35.7.d,gdpr_IV_32.2,hipaa_164.312.b,nist_800_53_AU.14,nist_800_53_AC.7,tsc_CC6.1,tsc_CC6.8,tsc_CC7.2,tsc_CC7.3,</group>
    <description>syslog: User authentication failure.</description>
  </rule>
```
### Rule 40111 — Multiple Authentication Failures
```
<rule id="40111" level="10" frequency="12" timeframe="160">
    <if_matched_group>authentication_failed</if_matched_group>
    <same_source_ip />
    <description>Multiple authentication failures.</description>
    <mitre>
      <id>T1110</id>
    </mitre>
    <group>authentication_failures,pci_dss_10.2.4,pci_dss_10.2.5,gpg13_7.1,gpg13_7.8,gdpr_IV_35.7.d,gdpr_IV_32.2,hipaa_164.312.b,nist_800_53_AU.14,nist_800_53_AC.7,tsc_CC6.1,tsc_CC6.8,tsc_CC7.2,tsc_CC7.3,</group>
  </rule>
```

## 3. Custom rule
- Các rule được thêm sẽ nằm tại: ``` /var/ossec/etc/rules/local_rules.xml```