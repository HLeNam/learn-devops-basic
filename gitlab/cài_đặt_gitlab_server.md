# Cài đặt GitLab Server

## 1. Tạo máy ảo Ubuntu

-   Tạo một máy ảo Ubuntu mới để thực hành cài đặt GitLab.

## 2. Tải GitLab EE Package

-   Nên cài các phiên bản GitLab Enterprise Edition (EE) **version 14 hoặc 15** để ổn định với các hướng dẫn phổ biến.
-   Link gợi ý: [GitLab EE trên Ubuntu focal](https://packages.gitlab.com/gitlab/gitlab-ee/packages/ubuntu/focal/gitlab-ee_14.4.1-ee.0_amd64.deb)

### Câu lệnh cài đặt nhanh:

```bash
# Thêm repository và key cho GitLab
curl -s https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash

# Cài đặt đúng version mong muốn (ví dụ: 14.4.1-ee.0)
sudo apt-get install gitlab-ee=14.4.1-ee.0
```

---

## 3. Thiết lập Domain Ảo (Fake Domain)

**Vấn đề**: Bạn muốn truy cập GitLab qua domain như `http://git.elroydevops.tech` nhưng **không có domain thật**.

✅ **Giải pháp**: Sử dụng phương pháp `add host` để ánh xạ domain ảo đến IP cục bộ.

### 3.1. Sửa file `/etc/hosts` trên **server GitLab**

```bash
vi /etc/hosts
```

➡️ Thêm dòng sau:

```
192.168.0.100 git.elroydevops.tech
```

> ✅ **Giải thích**: Khi bạn truy cập `http://git.elroydevops.tech`, hệ điều hành sẽ tra trong file `hosts` và thấy ánh xạ đến IP `192.168.0.100` nên sẽ truy cập đúng GitLab server.

### 3.2. Cấu hình GitLab sử dụng domain ảo này:

```bash
vi /etc/gitlab/gitlab.rb
```

➡️ Sửa dòng sau:

```
external_url 'http://git.elroydevops.tech'
```

➡️ Áp dụng cấu hình mới:

```bash
gitlab-ctl reconfigure
```

---

## 4. Thêm dòng `hosts` trên **máy Windows client** (nếu truy cập từ Windows)

-   Truy cập đường dẫn:

```
C:\Windows\System32\Drivers\etc
```

-   Backup file `hosts` gốc
-   Mở file `hosts` bằng Notepad (Run as Administrator)
-   Thêm dòng:

```
192.168.0.100 git.elroydevops.tech
```

-   Lưu file lại

> ✅ Bây giờ bạn có thể truy cập GitLab từ trình duyệt Windows qua `http://git.elroydevops.tech`.

---

## 5. Đăng nhập và Cấu hình GitLab lần đầu

### 5.1. Tài khoản mặc định:

-   Username: `root`
-   Lấy mật khẩu mặc định:

```bash
cat /etc/gitlab/initial_root_password
```

➡️ Sẽ thấy mật khẩu nằm trong output như:

```
Password: <mật-khẩu-gốc>
```

### 5.2. Đăng nhập GitLab qua trình duyệt

-   Truy cập: `http://git.elroydevops.tech`
-   Đăng nhập với tài khoản `root` và mật khẩu ở trên

---

## 6. Tùy chỉnh các thiết lập GitLab

-   Tắt tính năng **Sign up** để không ai tự tạo tài khoản.
-   Tắt tính năng **Default to Auto DevOps pipeline** cho tất cả dự án.
-   Đổi mật khẩu root nếu cần để tăng tính bảo mật.

---

## ⚠️ Lưu ý quan trọng:

-   Muốn các máy khác trong mạng nội bộ truy cập được GitLab qua domain ảo, bạn **phải chỉnh file `hosts` trên từng máy**.
-   Domain `git.elroydevops.tech` không phải domain thật. Nó **chỉ có hiệu lực trong mạng cục bộ** nhờ ánh xạ `hosts`.

---

📅 Tài liệu tạo ngày: **2025-05-04**
