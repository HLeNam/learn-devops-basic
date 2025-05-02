# 🛡️ Quyền Truy Cập Trên Linux

## 👤 Tạo User Mới Trên Hệ Thống

-   **useradd**: Tạo user nhưng không kèm thư mục home hay cấu hình.
-   **adduser**: Là script tiện ích cao hơn, có thêm cấu hình như home, shell, password...

**Ví dụ**:

```bash
$ sudo useradd namhl1            # Tạo user đơn giản
$ sudo adduser namhl2            # Tạo user kèm home và yêu cầu nhập password
```

> `adduser` thường dùng hơn vì tạo đầy đủ các thông tin và thư mục.

## 🔁 Chuyển Sang Một User Khác

```bash
$ su [username]
```

## 🔍 Xem Thông Tin User

Thông tin các user được lưu tại:

```bash
$ cat /etc/passwd
$ vi /etc/passwd       # Xem chi tiết bằng trình soạn thảo
```

## ❌ Xóa User

```bash
$ sudo deluser [username]

# Ví dụ:
$ sudo deluser namhl1
```

> Có thể dùng thêm `--remove-home` nếu muốn xoá cả thư mục home:

```bash
$ sudo deluser --remove-home namhl1
```

## 👥 Làm Việc Với Group

### ➕ Tạo Group Mới

```bash
$ sudo groupadd [groupname]

# Ví dụ:
$ sudo groupadd devops1
```

### ❌ Xóa Group

```bash
$ sudo delgroup [groupname]

# Ví dụ:
$ sudo delgroup devops1
```

### ➕ Thêm User Vào Group

```bash
$ sudo usermod -aG [groupname] [username]

# Ví dụ:
$ sudo usermod -aG devops2 namhl2
```

> ⚠️ Nếu không có `-a`, user sẽ bị loại khỏi các group khác!

## 📜 Kiểm Tra Group Của Một User

```bash
$ groups [username]

# Ví dụ:
$ groups namhl2
# Output: namhl2 : namhl2 devops2
```

> Khi tạo user mới, Linux tự tạo group trùng tên user (primary group).

## ❌ Xóa User Khỏi Group

```bash
$ sudo deluser [username] [groupname]

# Ví dụ:
$ sudo deluser namhl2 devops2
```

## 🗂️ Quyền Sở Hữu (Ownership)

```bash
$ mkdir datas
$ touch datas/data1.txt
$ ls -l datas
```

**Output**:

```
-rw-r--r-- 1 root root 0 May 2 02:55 data1.txt
```

-   Cột đầu tiên: quyền truy cập (permissions)
-   Chủ sở hữu: `root`
-   Nhóm sở hữu: `root`

## 🔧 Thay Đổi Quyền Sở Hữu

```bash
$ sudo chown [user]:[group] [file|folder]

# Ví dụ:
$ sudo chown root:devops2 datas/
$ ls -l
```

> Dùng `-R` nếu muốn áp dụng đệ quy cho tất cả file con:

```bash
$ sudo chown -R root:devops2 datas/
```

## 🔐 Quyền Truy Cập (Permissions)

-   `r` - read
-   `w` - write
-   `x` - execute

```bash
$ ls -l
```

Ví dụ:

```
drwxr-xr-x 2 root devops2 4096 May 2 02:55 datas
```

Ý nghĩa:

-   `d` → thư mục
-   `rwx` → quyền của user (chủ sở hữu)
-   `r-x` → quyền của group
-   `r-x` → quyền của others (người dùng khác)

## ⚙️ Thay Đổi Quyền Truy Cập

### Cách 1: Dùng ký hiệu `u`, `g`, `o` (user, group, others)

```bash
$ chmod g=rwx datas/
$ chmod u=rwx,g=rx,o=- datas/
```

### Cách 2: Dùng mã số

-   `r = 4`
-   `w = 2`
-   `x = 1`

```bash
$ chmod 750 datas/
```

-   `7` = `rwx` (user)
-   `5` = `r-x` (group)
-   `0` = `---` (others)

## ✅ Tóm Tắt Một Số Lệnh Quan Trọng

| Mục đích                | Lệnh ví dụ                    |
| ----------------------- | ----------------------------- |
| Tạo user                | `sudo adduser username`       |
| Xóa user                | `sudo deluser username`       |
| Tạo group               | `sudo groupadd groupname`     |
| Thêm user vào group     | `sudo usermod -aG group user` |
| Kiểm tra group của user | `groups username`             |
| Đổi chủ sở hữu file     | `sudo chown user:group file`  |
| Đổi quyền truy cập      | `chmod u=rwx,g=rx,o= file`    |
