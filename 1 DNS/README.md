
# Hướng dẫn cấu hình DNS Server (BIND)

Tài liệu này tóm tắt các bước thiết lập DNS Forward, Reverse, Forwarders và mô hình Master/Slave.

## 1. Các tệp tin cấu hình chính
*   **Cấu hình hệ thống:** `/etc/named.conf`
*   **Khai báo Zone:** `/etc/named.rfc1912.zones`
*   **Tệp tin dữ liệu Zone (Forward):** 
    *   `/var/named/forward.sgu.edu.vn`
    *   `/var/named/forward.HuynhTanDuong.edu.vn`
*   **Tệp tin dữ liệu Zone (Reverse):** 
    *   `/var/named/reverse.192.168.1.0`

---

## 2. Cấu hình Global và Bảo mật (`/etc/named.conf`)

Mở tệp tin: `nano /etc/named.conf`

*   **Forwarders:** Chuyển tiếp yêu cầu DNS không giải quyết được sang server khác.
    ```bind
    forwarders { 192.168.1.61; };
    ```
*   **Tắt DNSSEC (để demo/thử nghiệm):**
    ```bind
    dnssec-enable no;
    dnssec-validation no;
    ```
*   **Tắt SELinux:** 
    *   Mở file: `nano /etc/sysconfig/selinux`
    *   Sửa thành: `SELINUX=disabled` (Cần khởi động lại máy).

---

## 3. Khai báo các Zone (`/etc/named.rfc1912.zones`)

### Forward Zone (Thuận)
```bind
zone "sgu.edu.vn" IN {
  type master;
  file "forward.sgu.edu.vn";
  allow-update { none; };
};

zone "HuynhTanDuong.edu.vn" IN {
  type master;
  file "forward.HuynhTanDuong.edu.vn";
  allow-update { none; };
};
```

### Reverse Zone (Nghịch)
```bind
zone "1.168.192.in-addr.arpa" IN {
  type master;
  file "reverse.192.168.1.0";
  allow-update { none; };
};
```

---

## 4. Chi tiết các tệp tin dữ liệu Zone (`/var/named/`)

### 4.1. Forward Zone: `forward.HuynhTanDuong.edu.vn`
```bind
$TTL 86400
@    IN    SOA    server.HuynhTanDuong.edu.vn. root.HuynhTanDuong.edu.vn. (
        2018210901  ; Serial
        3600        ; Refresh
        1800        ; Retry
        604800      ; Expire
        86400       ; Minimum TTL
)
@    IN  NS    server.HuynhTanDuong.edu.vn.
@    IN  A     192.168.1.61
server  IN  A     192.168.1.61
```

### 4.2. Reverse Zone: `reverse.192.168.1.0`
```bind
$TTL 86400
@    IN    SOA    server.HuynhTanDuong.edu.vn. root.HuynhTanDuong.edu.vn. (
        2011071001 ; Serial
        3600       ; Refresh
        1800       ; Retry
        604800     ; Expire
        86400      ; Minimum TTL
)
@    IN  NS    server.HuynhTanDuong.edu.vn.
1    IN  PTR   server.HuynhTanDuong.edu.vn.
1    IN  PTR   HuynhTanDuong.edu.vn. 
```

### 4.3. Ví dụ Zone PTR khác (Google/Youtube giả lập)
```bind
$TTL 1d
@    IN    SOA    ns1.1google.com. root.1google.com. (
          2024091801  ; Serial
          604800      ; Refresh
          7200        ; Retry
          2419200     ; Expire
          86400       ; Minimum TTL
);
@    IN  NS    ns1.1google.com.
2    IN  PTR   1google.com.
2    IN  PTR   1youtube.com.
2    IN  PTR   1facebook.com.
2    IN  PTR   1tuilanumberone.com.

2    IN  PTR   ns1.1google.com.
2    IN  PTR   ns1.1youtube.com.
```

---

## 5. Cấu hình Dự phòng (Master/Slave)

### Trên Máy Gốc (Master)
1.  Sửa `/etc/named.conf`: Thêm quyền cho phép máy Slave đồng bộ.
    ```bind
    allow-transfer { 192.168.1.61; };
    ```

### Trên Máy Backup (Slave)
1.  Sửa `/etc/named.conf`: Thêm `allow-transfer { any; };` (hoặc IP cụ thể).
2.  Thêm cấu hình vào `/etc/named.rfc1912.zones`:
    ```bind
    zone "HuynhTanDuong.edu.vn" IN {
      type slave;                      
      file "slaves/forward.HuynhTanDuong.edu.vn";          
      masters { 192.168.1.61; };
    };

    zone "1.168.192.in-addr.arpa" IN {  
      type slave;                      
      file "slaves/reverse.192.168.1.0";    
      masters { 192.168.1.61; };
    };
    ```
    *Lưu ý: Sau khi đồng bộ, tệp tin sẽ tự động xuất hiện tại `/var/named/slaves/`.*

---

## 6. Kiểm tra và Quản lý dịch vụ

### Quản lý dịch vụ
```bash
# Đối với BIND (Named)
sudo systemctl restart named
sudo systemctl enable named

# Nếu sử dụng dnsmasq (tùy chọn)
sudo systemctl start dnsmasq
sudo systemctl enable dnsmasq
```

### Kiểm tra lỗi cấu hình
```bash
# Kiểm tra cú pháp zone
sudo named-checkzone sgu.edu.vn /var/named/HuynhTanDuong.edu.vn.zone

# Kiểm tra port 53 (DNS) đang hoạt động
sudo lsof -i :53
```

### Kiểm tra file dữ liệu
Đảm bảo các file sau đã tồn tại và đúng nội dung:
- `/var/named/forward.sgu.edu.vn`
- `/var/named/forward.HuynhTanDuong.edu.vn`
- `/var/named/reverse.192.168.1.0`
