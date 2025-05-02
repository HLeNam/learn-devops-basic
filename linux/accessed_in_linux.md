# Quyền truy cập trên Linux

## Tạo một user mới trên hệ thống:

sudo or root

1. useradd : tạo user nhưng chỉ tạo ra user thôi
2. adduser :

**ví dụ**:
$ useradd namhl1

$ adduser namhl2
Adding user `namhl2' ...
Adding new group `namhl2' (1002) ...
Adding new user `namhl2' (1002) with group `namhl2' ...
Creating home directory `/home/namhl2' ...
Copying files from `/etc/skel' ...
New password:
Retype new password:
passwd: password updated successfully
Changing the user information for namhl2
Enter the new value, or press ENTER for the default
Full Name []:
Room Number []:
Work Phone []:
Home Phone []:
Other []:
Is the information correct? [Y/n] y

## Chuyển qua một user nào đó

**Lệnh**: su username

## Xem thông tin của một user

Nằm trong /etc/passwd

**Lệnh**: vi /etc/passwd

## Xóa một user

**Lệnh**: deluser

ví dụ:
$ deluser namhl1
Removing user `namhl1' ...
Warning: group `namhl1' has no more members.
Done.

## Tạo một group

**Lệnh**: groupadd

ví dụ:
$ groupadd devops1

## Xóa một group

**Lệnh**: delgroup

ví dụ:
$delgroup devops1
Removing group `devops1' ...
Done.

## Thêm một user vào trong group

**Lệnh** usermod -aG [group name] [username]

ví dụ:
$ usermod -aG devops2 namhl2

Tùy chọn:

-   Thêm vào : usermod -a (append)
-   Liệt kê danh sách các group của namhl2 : usermod -G
-   Nếu không có -G thì lệnh sẽ thêm namhl2 vào devops2 nhưng sẽ xóa bỏ tất cả các group mà namhl2 đã có trước đó

## Kiểm tra groups của một user

**Lệnh**: groups [username]

ví dụ:
$ groups namhl2
namhl2 : namhl2 devops2

--> tại sao lại có group namhl2 ? --> vì trong linux khi tạo ra một user mới thì sẽ tự tạo một group tương ứng với user đó

## Xóa user khỏi group

**Lệnh**: deluser [username] [groupname]

ví dụ:
$ deluser namhl2 devops2
Removing user `namhl2' from group `devops2' ...
Done.

## Phân quyền sẽ gồm quyền truy cập và quyền sở hữu

## Quyền sở hữu

root@ubuntuserver:~# mkdir datas
root@ubuntuserver:~# touch datas/data1.txt
root@ubuntuserver:~# ls -l datas
total 0
-rw-r--r-- 1 root root 0 May 2 02:55 data1.txt

ta thấy cột có chữ root đầu tiên là chủ sở hữu và cột root thứ 2 là nhóm sở hữu

## Đổi quyền chủ sở hữu và nhóm sở hữu

**Lệnh**: chown [chủ sở hữu]:[nhóm sở hữu]

ví dụ:
$ chown root:devops2 datas/
$ ls -l
total 8
drwxr-xr-x 2 root devops2 4096 May 2 02:55 datas
drwx------ 3 root root 4096 May 1 15:33 snap

Tùy chọn:

-   Để thay đổi quyền của cả các cấp con: chown -R [chủ sở hữu]:[nhóm sở hữu]

## Quyền truy cập

Trên server linux sẽ có 3 quyền chính: rwx --> read write execute

$ ls -l
total 8
drwxr-xr-x 2 root devops2 4096 May 2 02:55 datas
drwx------ 3 root root 4096 May 1 15:33 snap

$ ls -l datas
total 0
-rw-r--r-- 1 root devops2 0 May 2 02:55 data1.txt

-   Là thư mục nếu chứa `d` ở đầu
-   Là file nếu chứa `-` ở đầu
-   3 ký tự tiếp theo sẽ tượng trưng cho quyền truy cập của chủ sở hữu
-   3 Ký tự tiếp theo nữa sẽ tượng trưng cho quyền truy cập của nhóm sở hữu
-   3 Ký tự tiếp theo nữa tượng trưng cho truy cập của khách

## Thay đổi quyền truy cập / quyền tác động

ugo --> [chủ sở hữu] [nhóm sở hữu] [khác]

**Lệnh**: chmod

ví dụ:
$ chmod g=rwx datas/

$ ls -l
total 8
drwxrwxr-x 2 root devops2 4096 May 2 02:55 datas
drwx------ 3 root root 4096 May 1 15:33 snap

$ chmod u=rwx,g=rx,o=- datas/
$ ls -l
total 8
drwxr-x--- 2 root devops2 4096 May 2 02:55 datas
drwx------ 3 root root 4096 May 1 15:33 snap

Còn có cách khác là phân bằng số: r=4, w=2, x=1 ==> full quyền sẽ = 4 + 2 + 1 = 7

