
# 🗂️ Giới Thiệu Về Hệ Thống Tập Tin Linux (Linux File System)

Hệ thống tập tin (File System) trong Linux là cách tổ chức dữ liệu trên ổ cứng theo cấu trúc cây (tree hierarchy), bắt đầu từ thư mục gốc `/`.

---

## 🗺️ Cấu Trúc Cơ Bản Của Linux File System

```
/
├── bin/
├── boot/
├── dev/
├── etc/
├── home/
├── lib/
├── media/
├── mnt/
├── opt/
├── proc/
├── root/
├── run/
├── sbin/
├── srv/
├── sys/
├── tmp/
├── usr/
└── var/
```

---

## 📁 Ý Nghĩa Của Các Thư Mục Chính

### `/` – Thư mục gốc
- Là nơi bắt đầu của toàn bộ hệ thống tập tin.

### `/bin` – Binary
- Chứa các lệnh cơ bản dành cho người dùng (như `ls`, `cp`, `mv`, `rm`, v.v.).

### `/sbin` – System Binary
- Chứa các lệnh dành cho quản trị hệ thống, ví dụ: `iptables`, `reboot`.

### `/etc` – Configuration
- Lưu trữ các file cấu hình hệ thống.

### `/dev` – Device Files
- Đại diện cho các thiết bị vật lý như ổ cứng, USB (ví dụ: `/dev/sda1`).

### `/proc` – Process Information
- Hệ thống giả lưu trữ thông tin tiến trình và kernel (dạng text).

### `/var` – Variable
- Chứa các file thay đổi theo thời gian như log (`/var/log/`), mail...

### `/tmp` – Temporary
- Lưu các file tạm thời.

### `/usr` – User Programs
- Lưu trữ phần mềm và thư viện dành cho người dùng.

### `/lib`, `/lib64` – Libraries
- Chứa thư viện cần thiết cho các lệnh trong `/bin` và `/sbin`.

### `/boot` – Boot Loader
- Chứa kernel, initrd và GRUB để khởi động hệ thống.

### `/home` – Home Directories
- Thư mục cá nhân của mỗi người dùng (VD: `/home/hlnam`).

### `/root` – Home của superuser (root)
- Thư mục riêng của tài khoản quản trị `root`.

### `/media` – Mount point cho thiết bị di động (USB, CD-ROM)

### `/mnt` – Mount point tạm thời

### `/opt` – Optional
- Cài phần mềm bên ngoài, thường là phần mềm bên thứ 3.

---

## 📌 Một Số Ghi Chú Quan Trọng

- Tất cả mọi thứ trong Linux đều là **file** (kể cả thư mục, thiết bị, tiến trình...).
- Linux sử dụng **permissions** (quyền truy cập) để bảo vệ file/thư mục.
- Có thể dùng các lệnh như `df -h`, `du -sh`, `mount`, `lsblk` để quản lý hệ thống file.

---

## 🧪 Một Số Lệnh Quản Lý Hệ Thống Tập Tin

| Lệnh | Công dụng |
|------|-----------|
| `ls -l` | Xem chi tiết file/thư mục |
| `cd` | Di chuyển giữa các thư mục |
| `pwd` | In đường dẫn hiện tại |
| `mkdir` | Tạo thư mục |
| `touch` | Tạo file rỗng |
| `rm` | Xóa file/thư mục |
| `df -h` | Hiển thị dung lượng ổ đĩa |
| `du -sh [thư_mục]` | Xem dung lượng thư mục |
| `mount` / `umount` | Gắn / gỡ thiết bị lưu trữ |
| `lsblk` | Hiển thị ổ đĩa vật lý |

---

## 🔐 Quyền Truy Cập File (Permissions)

Cấu trúc: `-rwxr-xr--`

- `r`: read
- `w`: write
- `x`: execute
- 3 nhóm: owner | group | other

Sử dụng lệnh `chmod`, `chown`, `chgrp` để chỉnh quyền.

---

## 📚 Tài Liệu Tham Khảo

- `man hier` – Xem cấu trúc hệ thống thư mục
- [The Linux Documentation Project](https://tldp.org/LDP/Linux-Filesystem-Hierarchy/html/)
