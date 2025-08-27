Cấu hình lên DC bằng samba
``sudo lsof -i :53``

``gedit /etc/samba/smb.conf``

```
------------------------------
# See smb.conf.example for a more detailed config file or
# read the smb.conf manpage.
# Run 'testparm' to verify the config is correct after
# you modified it.

[global]
	workgroup = sgu.edu.vn
	realm = sgu.edu.vn
	security = user
	domain master = yes
	domain logons = yes
	local master = yes
	preferred master = yes
	passdb backend = tdbsam
	idmap config * : range = 3000 - 7999
	idmap config * : backend = tdb
	logon path = \\%L\Profiles\%U 
	logon script = logon.bat
	add machine script = /usr/sbin/useradd -d /dev/null -g 200 -s /sbin/nologin -M %u
	lanman auth=yes
	ntlm auth=yes


[homes]
	comment = Home Directories
	browseable = yes
	writable = yes

[netlogon]
	comment = Network Logon Service
	path = /var/lib/samba/netlogon
	browseable = No
	writable = No

[Profiles]
	path = /var/lib/samba/profiles
	create mask = 0755
	directory mask = 0755
	writable = Yes
```
-----------
chú ý khi join domain k được thì cái workgroup trong smb.conf phải viết hoa vd SGU.EDU.VN