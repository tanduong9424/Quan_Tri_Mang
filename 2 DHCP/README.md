# Cấu hình DHCP
- Khi cài dhcp xong thì copy file cấu hình mẫu sang **/etc/dhcpd.conf** để cấu hình

``cp /usr/share/doc/dhcp-4.2.5/dhcpd.conf.example /etc/dhcp/dhcpd.conf``

- Chỉnh ip tĩnh 192.168.1.1
sửa ```/etc/dhcp/dhcpd.conf```

Cấp từ 192.168.1.10 đến 192.168.1.100
```
    subnet 192.168.1.0 netmask 255.255.255.0 {
    range 192.168.1.10 192.168.1.100;
    option domain-name-servers server.sgu.edu.vn;
    option domain-name "sgu.edu.vn";
    option routers 192.168.1.1;
    option broadcast-address 192.168.1.255;
    option domain-name-servers 192.168.1.1;
    default-lease-time 600;
    max-lease-time 7200; #thời gian cho thuê tối đa
} 
```
rồi **restart** với **enable dhcpd**
