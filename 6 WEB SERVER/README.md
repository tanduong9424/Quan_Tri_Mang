# Cấu hình Apache Web Server với Virtual Host trên CentOS 7

## 1. Chuẩn bị thư mục website

Tạo thư mục chứa mã nguồn cho từng website:

```bash
mkdir -p /var/www/html/htd.edu.vn
mkdir -p /var/www/html/sgu.edu.vn
```

Cấp quyền cho Apache:

```bash
chown -R apache:apache /var/www/html/htd.edu.vn
chown -R apache:apache /var/www/html/sgu.edu.vn

chmod 755 /var/www
chmod 755 /var/www/html
```

---

## 2. Cấu hình Apache

Mở file cấu hình chính:

```bash
nano /etc/httpd/conf/httpd.conf
```

Thêm các dòng sau:

```apache
IncludeOptional conf.d/*.conf
NameVirtualHost *:80
```

---

## 3. Tạo Virtual Host cho htd.edu.vn

Tạo file:

```bash
nano /etc/httpd/conf.d/htd.edu.vn.conf
```

Nội dung:

```apache
<VirtualHost *:80>
    ServerAdmin webmaster@htd.edu.vn
    DocumentRoot /var/www/html/htd.edu.vn
    ServerName htd.edu.vn
    ServerAlias fit.htd.edu.vn
</VirtualHost>
```

---

## 4. Tạo Virtual Host cho sgu.edu.vn

Tạo file:

```bash
nano /etc/httpd/conf.d/sgu.edu.vn.conf
```

Nội dung:

```apache
<VirtualHost *:80>
    ServerAdmin webmaster@sgu.edu.vn
    DocumentRoot /var/www/html/sgu.edu.vn
    ServerName sgu.edu.vn
    ServerAlias fit.sgu.edu.vn
</VirtualHost>
```

---

## 5. Tạo trang web mặc định

### Website htd.edu.vn

```bash
nano /var/www/html/htd.edu.vn/index.html
```

Ví dụ:

```html
<h1>Welcome to htd.edu.vn</h1>
```

### Website sgu.edu.vn

```bash
nano /var/www/html/sgu.edu.vn/index.html
```

Ví dụ:

```html
<h1>Welcome to sgu.edu.vn</h1>
```

---

## 6. Cấp quyền cho file website

```bash
chmod 755 /var/www/html/htd.edu.vn/index.html
chmod 755 /var/www/html/sgu.edu.vn/index.html
```

---

## 7. Khởi động Apache

Cho phép Apache khởi động cùng hệ thống:

```bash
systemctl enable httpd
```

Khởi động dịch vụ:

```bash
systemctl start httpd
```

Khởi động lại sau khi thay đổi cấu hình:

```bash
systemctl restart httpd
```

---

# Cấu hình Website nằm ngoài thư mục mặc định

Ví dụ website được đặt tại:

```bash
/home/FTP/facebook.com
```

## 1. Tạo Virtual Host

```apache
<VirtualHost *:80>
    ServerAdmin dhuynh529@gmail.com
    DocumentRoot /home/FTP/facebook.com

    ServerName 1facebook.com
    ServerAlias ns1.1facebook.com

    <Directory /home/FTP/facebook.com>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

---

## 2. Cấp quyền cho thư mục

```bash
chown -R apache:apache /home/FTP/facebook.com
chmod -R 755 /home/FTP/facebook.com
```

---

## 3. Khởi động lại Apache

```bash
systemctl restart httpd
```

---

# Các file DNS Zone liên quan

```text
/var/named/htd.edu.vn.zone
/var/named/sgu.edu.vn.zone
```

Các bản ghi DNS cần trỏ về địa chỉ IP của Web Server để Virtual Host hoạt động chính xác.
