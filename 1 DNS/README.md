```
/etc/named.rfc1912.zones
nano /etc/named.conf
/var/named/sgu.edu.vn.zone
/var/named/HuynhTanDuong.edu.vn.zone
```
```
zone "sgu.edu.vn" IN {
  type master;
  file "forward.sgu.edu.vn";
  allow-update { none; };
};
```
```
zone 1.168.192.in-addr.arpa" IN {
  type master;
  file "reverse.192.168.1.0";
  allow-update { none; };
};
```
```
sudo systemctl start dnsmasq
sudo systemctl enable dnsmasq
```
```
/var/named/sgu.edu.vn.zone

/var/named/reverse.192.168.1.0
```
```
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
  2    IN  PTR   1youtube.com
  2    IN  PTR   1facebook.com
  2    IN  PTR   1tuilanumberone.com

  2    IN  PTR   ns1.1google.com.
  2    IN  PTR   ns1.1youtube.com
  2    IN  PTR   ns1.1facebook.com
  2    IN  PTR   ns1.1tuilanumberone.com
```

```
sudo named-checkzone sgu.edu.vn /var/named/HuynhTanDuong.edu.vn.zone
```

```
$TTL 86400
@    IN    SOA    server.HuynhTanDuong.edu.vn. root.HuynhTanDuong.edu.vn. (
        2018210901  ; Serial
        3600          ; Refresh
        1800          ; Retry
        604800          ; Expire
        86400          ; Minimum TTL
)
@    IN  NS    server.HuynhTanDuong.edu.vn.
@    IN  A     192.168.1.61
server  IN  A     192.168.1.61
```

```
$TTL 86400
@    IN    SOA    server.HuynhTanDuong.edu.vn. root.HuynhTanDuong.edu.vn. (
        2011071001 ; Serial
        3600         ; Refresh
        1800          ; Retry
        604800        ; Expire
        86400          ; Minimum TTL
)
@    IN  NS    server.HuynhTanDuong.edu.vn.
1    IN  PTR   server.HuynhTanDuong.edu.vn.
1    IN  PTR   HuynhTanDuong.edu.vn. 
```

**sudo lsof -i :53**

kiểm tra
```
/etc/named.rfc1912.zones
/var/named/forward.sgu.edu.vn
/var/named/forward.HuynhTanDuong.edu.vn
/var/named/reverse.192.168.1.0
```

#Forwarders

``nano /etc/named.conf``
 thêm forwarders {192.168.1.61;};
- Tắt **dnssec-enable** và **dnssec-validation** thành no, tắt selinux luôn trong nano /etc/sysconfig/selinux
- ``systemctl restart named``

# Backups
- Trong /etc/named.conf thêm trên  máy gốc thêm ``allow-transfer {192.168.1.61;}``
- Trong /var/named/forward.sgu.edu.vn  và /var/named/reverse.192.168.1.0 thêm zone secondary

- Trên máy backup trong /etc/named.conf them allow-transfer...
thêm 2 zone dưới vào  /etc/named.rfc1912.zones( chú ý nếu 1.168.192.in-addr.arpa đã tồn tại thì xóa đi r tạo cái khác)
```
zone "HuynhTanDuong.edu.vn" IN {
  type slave;                      
  file "slaves/forward.HuynhTanDuong.edu.vn";          
  master {192.168.1.61;};
};

zone "1.168.192.in-addr.arpa" IN {  
  type slave;                      
  file "slaves/reverse.192.168.1.0";    
  master {192.168.1.61;};
};
```
- Xong thì các file backup sẽ nằm ở /**var/named/slaves**

```
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
2    IN  PTR   1youtube.com
2    IN  PTR   1facebook.com
2    IN  PTR   1tuilanumberone.com

2    IN  PTR   ns1.1google.com.
2    IN  PTR   ns1.1youtube.com
2    IN  PTR   ns1.1facebook.com
2    IN  PTR   ns1.1tuilanumberone.com
```
