# Cấu hình AD

- Join domain bằng cái này  (chung card mạng)
**Control Panel\System and Security\System**
- change settings rồi sửa tên pc nếu cần, ấn chọn domain nhập tên miền đã cấu hình (tên miền phân giải đc bằng dns chưa có thì phải tạo)

- Bên server cha thì vd 192.168.1.1 /24 trỏ dns về chính nó
cấu hình DC bằng tools trong windows manager add new forest

# Cấu hình DC đồng hành

- join vào domain trước, sau đó dns chỉnh trỏ về DC 1
- đăng nhập bằng administrator
trên DC2 cấu hình AD domain service chọn add a domain controller to an existing domain, nhập domain DC1 vào next.
- xong r dô **Active Directory Site and Services ,Sites > Defaults > First-Site-Name > Servers**

- check xem cái  **NTDS Settings**, bật cái **Global Catalog** lên
rồi DC1 đổi alternate DNS thành ip DC2,preferred DNS thành 127.0.0.1 
DC2 đôi alternate DNS thành  ip DC1


# Cấu hình child domain
- Tạo máy mới nha chứ máy DC đồng hành dễ lỗi ấy

- IP alternate DNS cx trỏ về domain cha,
- Join domain , đăng nhập bằng admin vd domain cha là FIT thì đăng nhập FIT\Administrator , đổi tên pc nếu cần.
- Rồi dô cấu hình AD domain serverces.. chọn cái add a new domain to an existing forest ở dưới chọn child domain xong parent domain name chọn FIT\Administrator, đăng nhập các chỗ supply the credential ... change lại FIT\Adminstrator ... XOng
- Kiểm tra thì vào active directory domains and Trusts