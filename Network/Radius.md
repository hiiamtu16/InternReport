# 1. Thông tin cần có
## 1.1. Thông tin Domain
- Domain FQDN: ad.hpadirect.vn
- Realm Kerberos: AD.HPADIRECT.VN
- NetBIOS: domain: AD
- Samba AD DC hostname: adcsamba.ad.hpadirect.vn
- Samba AD DC IP: 172.16.60.110

## 1.2. Thông tin server RADIUS
- hostname server: tunv-radius-lab
- FQDN: tunv-radius-lab.ad.hpadirect.vn
- IP RADIUS: 172.16.60.22
- OS: Ubuntu Server 24.04

## 1.3. Thông tin AP và WiFi
- Dải IP quản lý AP: 10.25.20.0/24
- SSID: TuNV STAFF
- VLAN user WiFi: 10.25.30.0/24
- RADIUS auth port: 1812
- RADIUS accounting port: UDP 1813
- clients.conf của FreeRADIUS khai báo dải IP của AP hoặc IP cố định của AP, không cần dải IP của user WiFi

# 2. Kiểm tra kết nối AD vs RADIUS
- Kiểm tra DNS trên RADIUS có trỏ vê Samba AD DC
```
resolvectl status
```
- Kết quả:
```
Current DNS Server: 172.16.60.110
DNS Servers: 172.16.60.110
```
- Kiểm tra bản ghi SRV của AD:
```
host -t SRV _ldap._tcp.ad.hpadirect.vn
host -t SRV _kerberos._udp.ad.hpadirect.vn
```

# 3. Chuẩn hoá hostname server RADIUS
- Tắt cloud init quản lý file hosts:
```
cat > /etc/cloud/cloud.cfg.d/99-disable-manage-etc-hosts.cfg <<'EOF'
manage_etc_hosts: false
EOF

cat /etc/cloud/cloud.cfg.d/99-disable-manage-etc-hosts.cfg
```
- Sửa file `/etc/hosts`:
```
cp /etc/hosts /etc/hosts.bak
cat > /etc/hosts <<'EOF'
127.0.0.1 localhost
172.16.60.22 tunv-radius-lab.ad.hpadirect.vn tunv-radius-lab
::1 localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
EOF
```
- Set hostname:
```
hostnamectl set-hostname tunv-radius-lab
```
- Kiểm tra:
```
hostname
hostname -f
```
- Kết quả đúng:
```
tunv-radius-lab
tunv-radius-lab.ad.hpadirect.vn
```

# 4. Cài đặt FreeRADIUS
## 4.1. Cài đặt các gói cần thiết
- Trên server RADIUS
```
apt update
apt install freeradius freeradius-utils samba winbind libnss-winbind libpam-winbind krb5-user smbclient dnsutils acl attr -y
```
- Khi được hỏi Kerberos realm, nhập:
```
AD.HPADIRECT.VN
```
## 4.2. Cấu hình Kerberos
- Backup file cũ:
```
cp /etc/krb5.conf /etc/krb5.conf.bak
nano /etc/krb5.conf
```
- Nội dung file:
```
[libdefaults]
  default_realm = AD.HPADIRECT.VN
  dns_lookup_realm = false
  dns_lookup_kdc = true
  ticket_lifetime = 24h
  renew_lifetime = 7d
  forwardable = true
[realms]
  AD.HPADIRECT.VN = {
    kdc = adcsamba.ad.hpadirect.vn
    admin_server = adcsamba.ad.hpadirect.vn
  }
[domain_realm]
  .ad.hpadirect.vn = AD.HPADIRECT.VN
  ad.hpadirect.vn = AD.HPADIRECT.VN
```
- Test Kerberos (có thể dùng tài khoản quyền admin thay vì dùng Administrator):
```
kinit Administrator
klist
```
## 4.3. Cấu hình Samba member server trên RADIUS
- Backup file cũ
```
cp /etc/samba/smb.conf /etc/samba/smb.conf.bak
nano /etc/samba/smb.conf
```
- Nội dung:
```
[global]
  workgroup = AD
  realm = AD.HPADIRECT.VN
  security = ADS
  server role = member server

  dedicated keytab file = /etc/krb5.keytab
  kerberos method = secrets and keytab

  winbind use default domain = yes
  winbind refresh tickets = yes
  winbind offline logon = no
  winbind enum users = yes
  winbind enum groups = yes

  idmap config * : backend = tdb
  idmap config * : range = 3000-7999
  idmap config AD : backend = rid
  idmap config AD : range = 10000-999999

  template shell = /bin/bash
  template homedir = /home/%D/%U
```
- Sửa NSS
```
nano /etc/nsswitch.conf
```
- Tìm 2 dòng `passwd` và `group` sửa thành:
```
passwd: files systemd winbind
group: files systemd winbind
```
- Tắt dịch vụ Samba AD DC trên server RADIUS nếu có:
```
systemctl disable --now samba-ad-dc 2>/dev/null
systemctl mask samba-ad-dc
systemctl unmask smbd nmbd winbind
```
- Restart dịch vụ:
```
systemctl restart smbd nmbd winbind
systemctl enable smbd nmbd winbind
```
- Kiểm tra config:
```
testparm -s
```
- Kết quả cần thấy:
```
Server role: ROLE_DOMAIN_MEMBER
```
## 4.4. Join server RADIUS vào Samba AD DC
- Chạy (dùng user administrator hoặc user khác):
```
net ads join -U tunv
```
- Kết quả thành công sẽ có dạng:
```
Joined 'TUNV-RADIUS-LAB' to dns domain 'ad.hpadirect.vn'
```
- Restart lại dịch vụ:
```
systemctl restart smbd nmbd winbind
```
- Kiểm tra trust domain:
```
wbinfo -t
```
- Kết quả đúng:
```
checking the trust secret for domain AD via RPC calls succeeded
```
- Kiểm tra lấy user và group từ AD:
```
wbinfo -u | head
wbinfo -g | head
getent passwd tunv
```
## 4.5. Cấu hình Samba AD DC hỗ trợ MSCHAPv2
- trên server AD:
```
testparm -s | grep -i "ntlm auth"
grep -n "ntlm auth" /etc/samba/smb.conf
```
- Nếu chưa có, thêm vào block `[global]`
```
ntlm auth = mschapv2-and-ntlmv2-only
```
- Hoặc dùng lệnh để thêm:
```
cp /etc/samba/smb.conf /etc/samba/smb.conf.bak.$(date +%F-%H%M)
sed -i '/^\[global\]/a\ ntlm auth = mschapv2-and-ntlmv2-only' /etc/samba/smb.conf
testparm -s | grep -i "ntlm auth"
```
- Restart Samba AD DC:
```
systemctl restart samba-ad-dc
systemctl status samba-ad-dc --no-pager -l
```
## 4.6. Test xác thực AD bằng ntlm_auth trên RADIUS server
- Trên server RADIUS, test bằng root:
```
ntlm_auth --request-nt-key --domain=AD --username=tunv
echo "EXIT_CODE=$?"
```
- Kết quả đúng:
```
EXIT_CODE=0
```
- Sau đó kiểm tra group winbind privileged:
```
ls -ld /var/lib/samba/winbindd_privileged
getent group winbindd_priv
id freerad
```
- Thêm user `freerad` vào group `winbindd_priv`:
```
usermod -aG winbindd_priv freerad
systemctl restart freeradius
id freerad
```
- Kết quả đúng cần thấy:
```
winbindd_priv
```
- Test lại `ntlm_auth` dưới quyền user `freerad`:
```
runuser -u freerad -- ntlm_auth --request-nt-key --domain=AD --username=tunv
echo "EXIT_CODE=$?"
```

# 5. Cấu hình FreeRADIUS module mschap
- Backup file mschap ra thư mục ngoài `mods-enabled`
- Tạo thư mục backup:
```
mkdir -p /root/freeradius-backup
cp /etc/freeradius/3.0/mods-enabled/mschap /root/freeradius-backup/mschap.bak
```
- Mở file:
```
nano /etc/freeradius/3.0/mods-enabled/mschap
```
- Tìm dòng `ntlm_auth` và cấu hình thành:
```
ntlm_auth = "/usr/bin/ntlm_auth --request-nt-key --allow-mschapv2 --domain=AD --username=%{%{mschap:User-Name}:-%{User-Name}} --challenge=%{%{mschap:Challenge}:-00} --nt-response=%{%{mschap:NT-Response}:-00}"
```
- Kiểm tra:
```
grep -nE "^[[:space:]]*ntlm_auth|^[[:space:]]*#[[:space:]]*ntlm_auth" /etc/freeradius/3.0/mods-enabled/mschap
```
- Dòng đúng là dòng không bị comment bằng dấu #.

# 6. Cấu hình EAP dùng PEAP MSCHAPv2
- Backup file EAP:
```
cp /etc/freeradius/3.0/mods-enabled/eap /root/freeradius-backup/eap.bak
```
- Sửa default EAP type từ `md5` thành `peap`:
```
sed -i '0,/default_eap_type = md5/s/default_eap_type = md5/default_eap_type = peap/' /etc/freeradius/3.0/mods-enabled/eap
```
- Kiểm tra:
```
grep -nE "^[[:space:]]*default_eap_type|^[[:space:]]*peap|^[[:space:]]*mschapv2" /etc/freeradius/3.0/mods-enabled/eap | head -80
```
- Kết quả cần thấy:
```
default_eap_type = peap
peap {
  default_eap_type = mschapv2
}
```

# 7. Khai báo AP là RADIUS Client:
- Mở file:
```
nano /etc/freeradius/3.0/clients.conf
```
- Thêm block cho dải IP của AP: (ví dụ AP có IP thuộc dải 10.25.20.0 /24)
```
client test_site {
  ipaddr = 10.25.20.0/24
  secret = HpaRadius2026Test123
  require_message_authenticator = yes
}
```
- Kiểm tra syntax:
```
freeradius -XC | tail -30
```
- Kết quả đúng:
```
Configuration appears to be OK
```
- Restart:
```
systemctl restart freeradius
systemctl status freeradius --no-pager -l
```

# 8. Test RADIUS local bằng radtest
- Test cơ bản đường FreeRADIUS đến AD:
```
read -s -p "Nhap password domain cua tunv: " PASS; echo
radtest -t mschap tunv "$PASS" 127.0.0.1 0 testing123
unset PASS
```
- Kết quả đúng:
```
Received Access-Accept
```

# 9. Cấu hình RADIUS Profile trên Omada
- Tạo hồ sơ RADIUS
![Ảnh 1](?raw=1)
- Tạo SSID sử dụng RADIUS
![Ảnh 2](?raw=1)

  
