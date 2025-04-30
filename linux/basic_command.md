# 📂 Các Lệnh Linux Cơ Bản Cho Người Mới Bắt Đầu

## 1. `pwd`

**Công dụng:** Hiển thị đường dẫn thư mục hiện tại (Print Working Directory).

```bash
$ pwd
/home/hlnam
```

---

## 2. `whoami`

**Công dụng:** Hiển thị tên user hiện đang đăng nhập.

```bash
$ whoami
hlnam
```

---

## 3. `cd [thư_mục]`

**Công dụng:** Di chuyển đến thư mục khác.

```bash
$ cd /
```

**Một số cách sử dụng khác:**

-   Di chuyển đến thư mục cha:

    ```bash
    $ cd ..
    ```

-   Trở về thư mục chính (home) của user:
    ```bash
    $ cd
    ```

---

## 4. `ls [thư_mục]`

**Công dụng:** Hiển thị danh sách file/thư mục trong một thư mục.

```bash
$ ls /
```

**Tùy chọn:**

-   Hiển thị chi tiết: `ls -l`
-   Hiển thị cả file/thư mục ẩn: `ls -a`
-   Sắp xếp theo thời gian mới nhất: `ls -t`
-   Kết hợp tất cả: `ls -lta`

---

## 5. `mkdir [tên_thư_mục]`

**Công dụng:** Tạo thư mục mới.

```bash
$ mkdir new
```

**Tùy chọn:**

-   Tạo thư mục lồng nhau:
    ```bash
    $ mkdir -p new/new1/new11
    ```

---

## 6. `touch [tên_file]`

**Công dụng:** Tạo một file rỗng mới.

```bash
$ touch new/data.txt
```

---

## 7. `rm [file|thư_mục]`

**Công dụng:** Xóa file hoặc thư mục.

```bash
$ rm new/data.txt
```

**Tùy chọn:**

-   Xóa đệ quy thư mục và nội dung: `rm -r`
-   Xóa không cần xác nhận: `rm -rf`

---

## 8. `cp [nguồn] [đích]`

**Công dụng:** Copy file hoặc thư mục.

```bash
$ cp -r new/ /tmp/
$ cp new/data.txt /tmp/
```

---

## 9. `mv [nguồn] [đích]`

**Công dụng:** Di chuyển hoặc đổi tên file/thư mục.

```bash
$ mv /tmp/data.txt data1.txt
$ mv /tmp/new/ new1
```

---

## 10. `echo [nội_dung]`

**Công dụng:** In nội dung ra màn hình hoặc ghi vào file.

```bash
$ echo "devops for fresher by me"
```

**Tùy chọn:**

-   Ghi đè nội dung vào file:

    ```bash
    $ echo "devops for fresher by me" > title.txt
    ```

-   Ghi thêm nội dung (không ghi đè):
    ```bash
    $ echo "devops for fresher by me 2" >> title.txt
    ```

---

## 11. `cat [file]`

**Công dụng:** Xem nội dung file.

```bash
$ cat title.txt
```

---

## 12. `history`

**Công dụng:** Hiển thị lịch sử các lệnh đã nhập.

```bash
$ history
```

---

## 13. `tail`

**Công dụng:** Hiển thị phần cuối của file.

```bash
$ tail -n 1 title.txt
```

**Ví dụ ghi log:**

```bash
$ tail -n 1 title.txt > log.txt
$ cat log.txt
```

**Tùy chọn:**

-   Xem `n` dòng cuối: `tail -n 5 file.txt`
-   Theo dõi file log realtime: `tail -f log.txt`

---

## 14. `sudo [lệnh]`

**Công dụng:** Thực thi lệnh với quyền `superuser` (quản trị).

```bash
$ sudo apt update
$ sudo rm -rf /important-dir
```

> ⚠️ **Cẩn thận khi dùng `sudo` kết hợp với lệnh xóa (`rm -rf`) vì có thể gây mất dữ liệu nghiêm trọng.**

---

## 15. Một số lệnh hữu ích khác

### `clear`

**Công dụng:** Xóa màn hình terminal.

```bash
$ clear
```

---

### `man [tên_lệnh]`

**Công dụng:** Xem tài liệu hướng dẫn sử dụng của một lệnh.

```bash
$ man ls
```

---

### `df -h`

**Công dụng:** Kiểm tra dung lượng ổ đĩa.

```bash
$ df -h
```

---

### `top` hoặc `htop`

**Công dụng:** Xem tiến trình đang chạy và tài nguyên hệ thống đang sử dụng.

```bash
$ top
```
