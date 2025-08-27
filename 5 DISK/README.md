```
fdisk -l
fdisk /dev/sdb (phân vùng ổ đĩa B)
n p(primary) enter enter +1G enter
p để xem | w để lưu
n e(extend) enter enter enter enter
mkfs.ext3 /dev/sdb1 format dạng ext3
```
**Tạo LVM (logical volume manager)
tạo thêm phân vùng extend  để gộp thành logical volume**
```
fdisk /dev/sdb 
ấn t
ấn 5 nhập hexcode là 8e rồi w để lưu.
Tương tự trên sdc5
```

**Tạo các physical volume :**

- pvcreate /dev/sdb5
- pvcreate /dev/sdc5

**Tạo Volume group để gom các physical volume lại:** 
``vgcreate VolumeA (tên VG) /dev/sdb5 /dev/sdc5``

**Tạo ra Logical volume bằng volumeGroup trên:**
``lvcreate -l 1.99G (kích thước) -n LV (tên logical volume) VolumeA (Volumegroup đc chọn)``

**Tiến hành định dạng cho logical Volume**
``mkfs.ext3 /dev/VolumeA/LV``

**Để thêm Tăng dung lựng logical volume** thì tạo physical volume mới rồi thêm vào bằng ``pvcreate /dev/sde5\\\vgextend VolumeA /dev/sde5``
rồi sau đó **tăng trong LV** ``lvextend -L +1.99G /dev/VolumeA/LV /dev/sde5``

# Cấu hình Quota
```
mkdir /home/Disk( chú ý không tạo trong folder 1 user cụ thể nào nha)
chmod 700 /home/Disk (toàn quyền người sở hữu)
chown user1 /home/Disk
```

```mount /dev/VolumeA/LV /home/Disk(file mount đến)
nano /etc/fstab
/dev/mapper/Volume-A-LV	/home/Disk		ext3	defaults,usrquota,grpquota	0 0
```
Sau đó mount lại

``mount -o remount /home/Disk``

Kiểm tra lỗi quota: 
``quotacheck -avug`` 
Nếu có 2 file aquota.group và aquota.user thì đã nhận quota

Cấu hình cho user1 chỉ đc dùng 1MB
500KB 1000KB

1MB=1024KB

**CẤU HÌNH QUOTA Ở ĐƠN VỊ MẶC ĐỊNH LÀ KB**
``edquota -u user1 rồi :i để insert /dev/mapper/VolumeA-LV ``

``0 500 1000 0 - -``


tiến hành bật quota : 
``quotaon -avug``

----------------------------------------------------------
Cấu hình samba
```
[Global]
	workgroup = WORKGROUP
	server string = Samba Server %v
	netbios name = centos
	map to guest = bad user
	dns proxy = no
	ntlm auth = yes 
[Anonymous]
	path = /home/htd/Desktop/Folder
# mount lần này khác với mount khi quota cho user nha chú ý
	browsable = yes
	writable = yes
	guest ok = yes
	read only = no
[Shared] 
	path = /home/Disk
# đây là mount cho user quota nè
	browsable = yes 
	writable = yes 
	guest ok = yes 
	read only = no 
```

Sau đó tiến hành dô orther locations /computer/home/htd chuyển quyền thư mục htd sang hết create and delete files. 

Làm tương tự với /htd/Desktop xong **systemctl restart smb nmb**

rồi client truy cập **\\192.168.1.1**


để test quota 
``dd if=/dev/zero of=testquota.txt bs=524288(512KB) count=1``

```fsutil file createnew testquota2.txt 104857600
fsutil file createnew C:\Users\Dell\Desktop\testDisk.txt 8388608 
(0.8MB)
fsutil file createnew C:\Users\Dell\Desktop\testDisk.txt 1258291
 (1,2MB)
fsutil file createnew C:\Users\Dell\Desktop\testDisk.txt 2201990```


