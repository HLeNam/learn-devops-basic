# Tổng hợp các lệnh quản trị server cơ bản trên Ubuntu

## 1. `free -m`

**Công dụng**: Kiểm tra trạng thái sử dụng RAM trên server (hiển thị theo đơn vị MB).

**Ví dụ**:

```bash
$ free -m
              total        used        free      shared  buff/cache   available
Mem:           3888         280        2805           1         802        3367
Swap:          3900           0        3900
```

---

## 2. `df -h /`

**Công dụng**: Kiểm tra dung lượng ổ đĩa hệ thống, còn trống bao nhiêu.

**Ví dụ**:

```bash
$ df -h /
Filesystem                         Size  Used Avail Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv   18G  6.5G   11G  39% /
```

---

## 3. `sudo hostnamectl set-hostname lab-server`

**Công dụng**: Đổi tên hostname của server (hostname chính là tên máy chủ trong mạng).

---

## 4. `sudo reboot`

**Công dụng**: Khởi động lại server.

---

## 5. `netstat -tlpun` (hoặc thay bằng `ss -tlpun`)

**Công dụng**: Kiểm tra các cổng đang mở và các dịch vụ đang lắng nghe trên server.

> Lưu ý: `netstat` thuộc gói `net-tools`, cần cài đặt bằng:

```bash
sudo apt install net-tools
```

**Khuyến nghị**: Dùng `ss -tlpun` thay thế vì hiện đại và có sẵn.

### Tùy chọn:

-   `-t`: Hiển thị kết nối TCP
-   `-u`: Hiển thị kết nối UDP
-   `-l`: Hiển thị các cổng đang lắng nghe
-   `-p`: Hiển thị chương trình và tiến trình liên quan
-   `-n`: Hiển thị địa chỉ IP và cổng ở dạng số
-   `-tlpun`: Kết hợp các tùy chọn trên

---

## 6. `ps -ef`

**Công dụng**: Xem danh sách các tiến trình (process) đang chạy trên hệ thống.

---

## 7. `ping 8.8.8.8`

**Công dụng**: Kiểm tra kết nối internet đến địa chỉ IP (ví dụ: 8.8.8.8 là DNS của Google).

---

## 8. `telnet [IP] [PORT]`

**Công dụng**: Kiểm tra khả năng kết nối đến 1 địa chỉ IP và cổng cụ thể.  
→ Dùng để kiểm tra xem cổng có mở và truy cập được không.

**Lưu ý**: Có thể cần cài đặt:

```bash
sudo apt install telnet
```

---

## 9. `traceroute -T -p [PORT] [IP]`

**Công dụng**: Kiểm tra đường đi của gói tin đến IP/PORT cụ thể.  
Dùng để chẩn đoán mạng, xem gói tin đi qua những router nào.

**Cài đặt**:

```bash
sudo apt install traceroute -y
```

---

## 10. `apt install [package]`

**Công dụng**: Cài đặt phần mềm hoặc gói trên Ubuntu.  
**Ví dụ**:

```bash
sudo apt install nginx
```

---

## 11. `apt remove [package]`

**Công dụng**: Gỡ bỏ phần mềm/gói khỏi hệ thống.  
**Ví dụ**:

```bash
sudo apt remove nginx
```

---

> Tài liệu này hữu ích cho người mới bắt đầu làm việc với server Ubuntu hoặc quản trị hệ thống cơ bản.
