# Triển khai các dự án Frontend

## **Di chuyển file dự án từ máy cá nhân lên server**

1. Tại thư mục chứa dự án mở Terminal lên.
2. Chạy lệnh:

    ```bash
    scp ./todolist.zip namhl@192.168.0.110:/home/namhl
    ```

3. Để giải nén, cài unzip:

    ```bash
    apt install unzip
    ```

4. Chuyển đến thư mục chứa file:

    ```bash
    cd /home/namhl
    ```

5. Giải nén dự án:

    ```bash
    unzip todolist.zip
    ```

6. Tạo thư mục chứa dự án:

    ```bash
    mkdir /projects
    ```

7. Di chuyển thư mục dự án vào thư mục mới:

    ```bash
    mv todolist /projects/
    ```

8. Kiểm tra quyền:

    ```bash
    ls -l /projects/
    ```

9. Tạo user riêng cho dự án:

    ```bash
    adduser todolist
    ```

10. Gán quyền sở hữu thư mục cho user mới tạo:

    ```bash
    chown -R todolist:todolist /projects/todolist/
    ```

11. Phân quyền thư mục cho nhóm:

    ```bash
    chmod -R 750 /projects/todolist/
    ```

12. Cập nhật hệ thống:

    ```bash
    apt update
    ```

13. Cài đặt Node.js:

    ```bash
    apt install nodejs
    ```

14. Chuyển sang user dự án:

    ```bash
    su todolist
    cd /projects/todolist/
    ```

15. Cài đặt npm:

    ```bash
    sudo apt install npm
    ```

16. Cài đặt thư viện:

    ```bash
    npm i
    ```

17. Cập nhật Node.js phiên bản 18:

    ```bash
    curl -s https://deb.nodesource.com/setup_18.x | sudo bash
    ```

18. Build dự án:

    ```bash
    npm run build
    ```

### **Các file cần lưu ý với dự án Node.js frontend**:

-   `package.json`
-   `*.config.js`

### **3 cách chạy một dự án frontend thông dụng**:

-   Sử dụng Web Server (Nginx)
-   Chạy bằng service (systemd)
-   Chạy bằng pm2

---

## **Chạy bằng Nginx Web Server**

1. Cài đặt nginx:

    ```bash
    apt install nginx
    ```

    - Mặc định nginx chạy ở port 80
    - Kiểm tra cổng:

        ```bash
        netstat -tlpun
        ```

2. Cấu hình nginx:

    ```bash
    cd /etc/nginx/
    vi sites-available/default
    ```

3. Kiểm tra config:

    ```bash
    nginx -t
    ```

4. Restart nginx:

    ```bash
    systemctl restart nginx
    ```

5. Tạo file cấu hình riêng:

    ```bash
    vi conf.d/todolist.conf
    ```

### Nội dung `conf.d/todolist.conf`

```nginx
server {
    listen 8081;
    root /projects/todolist/dist/;
    index index.html;
    try_files $uri $uri/ /index.html;
}
```

6. Kiểm tra và khởi động lại nginx:

    ```bash
    nginx -t
    systemctl restart nginx
    ```

### **Lỗi 500 khi truy cập**

Do quyền truy cập thư mục `/projects/todolist/dist/` không cho phép user nginx (`www-data`) đọc file. Kiểm tra user của nginx:

```bash
vi /etc/nginx/nginx.conf
# user là: www-data
```

Thêm user `www-data` vào group `todolist`:

```bash
usermod -aG todolist www-data
systemctl restart nginx
```

Nếu có nhiều dự án đang chạy:

```bash
nginx -s reload
```

---

## **Chạy bằng Service systemd**

Ví dụ triển khai frontend tên `vision`:

1. Tạo user:

    ```bash
    adduser vision
    ```

2. Gán quyền:

    ```bash
    chown -R vision:vision /projects/vision/
    chmod -R 750 /projects/vision/
    ```

3. Cài thư viện:

    ```bash
    cd /projects/vision/
    su vision
    npm i
    ```

4. Tạo file service:

    ```bash
    vi /lib/systemd/system/vision.service
    ```

### Nội dung `vision.service`

```ini
[Service]
Type=simple
User=vision
Restart=on-failure
WorkingDirectory=/projects/vision/
ExecStart=npm run start -- --port=3000
```

5. Khởi động service:

```bash
systemctl daemon-reload
systemctl start vision
systemctl status vision
netstat -ltpun
```

---

## **Chạy bằng PM2**

### Cài đặt PM2

```bash
npm install pm2 -g
```

### Khởi chạy ứng dụng bằng PM2

```bash
cd /projects/todolist/
pm run build
pm install serve -g
pm start
# hoặc:
pm2 start "npx serve -s dist" --name todolist
```

### Quản lý bằng PM2

```bash
pm2 list
pm2 stop todolist
pm2 restart todolist
pm2 delete todolist
pm2 logs todolist
```

### Lưu cấu hình tự động khởi động lại khi reboot:

```bash
pm2 startup
pm2 save
```

### Tài liệu tham khảo

-   [PM2 Documentation](https://pm2.keymetrics.io/docs/usage/quick-start/)
-   [Hướng dẫn PM2 bằng Claude](https://claude.ai/chat/9b5c1a86-7dbd-4ffc-b77e-ad9d92b3e68f)
