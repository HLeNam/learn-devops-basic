
# Triển khai Backend Java Spring Boot trên Server Linux

## I. Các bước triển khai dự án Backend

**Các bước đầu tiên tương tự với Frontend:** di chuyển file dự án từ máy cá nhân lên máy chủ server.

### 1. Chuẩn bị hệ thống và phân quyền

- Tạo thư mục riêng cho dự án.
- Tạo user riêng cho dự án.
- Phân quyền truy cập và quyền sở hữu cho thư mục dự án.

### 2. Cài đặt công cụ cần thiết

#### 2.1 Cài Java 17

```bash
sudo apt install openjdk-17-jdk openjdk-17-jre
java -version
```

> Ghi chú: Kiểm tra `java -version` để đảm bảo đã cài đúng Java 17.

#### 2.2 Cài Maven

```bash
sudo apt update
sudo apt install maven -y
mvn -version
```

> Ghi chú: Maven là công cụ build và quản lý dependency cho Java.

### 3. Thiết lập và cấu hình database

#### 3.1 Kiểm tra file cấu hình Spring

```bash
ls src/main/resources/
vi src/main/resources/application.properties
```

#### 3.2 Cài đặt MariaDB

```bash
sudo apt install mariadb-server
```

##### Kiểm tra kết nối:

```bash
netstat -ltpun
```

> Ghi chú: `127.0.0.1:3306` nghĩa là MariaDB chỉ chấp nhận kết nối từ localhost. Muốn truy cập từ máy khác, cần sửa cấu hình `bind-address` thành `0.0.0.0`.

##### Sửa cấu hình MariaDB:

```bash
sudo systemctl stop mariadb
vi /etc/mysql/mariadb.conf.d/50-server.cnf
# Sửa dòng bind-address = 0.0.0.0
sudo systemctl restart mariadb
```

##### Kiểm tra lại:

```bash
netstat -ltpun
```

#### 3.3 Tạo database và user riêng

```sql
mysql -u root -p
> create database shoeshop;
> create user 'shoeshop'@'%' identified by 'shoeshop';
> grant all privileges on shoeshop.* to 'shoeshop'@'%';
> flush privileges;
```

> Ghi chú:
> - `@'%'`: cho phép user `shoeshop` đăng nhập từ mọi IP.
> - `flush privileges`: nạp lại các quyền vừa cấp.

##### Kết nối từ xa:

```bash
mysql -h 192.168.0.110 -P 3306 -u shoeshop -p
```

> Ghi chú:
> - `-h`: IP máy chủ DB.
> - `-P`: cổng (3306 mặc định).
> - `-u`: user.
> - `-p`: yêu cầu nhập password.

##### Nhập dữ liệu vào database:

```sql
use shoeshop;
source /projects/shoeshop/shoe_shopdb.sql
```

> Ghi chú:
> - `source`: dùng để chạy file `.sql` và tạo bảng, dữ liệu.

### 4. Build dự án

```bash
mvn install -DskipTests=true
```

> Ghi chú:
> - `-DskipTests=true`: bỏ qua bước test khi build.
> - Sau khi build xong, file `.jar` nằm trong thư mục `target/`.

### 5. Run dự án

```bash
java -jar target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
```

#### Chạy dưới nền:

```bash
nohup java -jar target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar 2>&1 &
```

> Ghi chú:
> - `nohup`: giúp tiến trình không bị dừng khi thoát terminal.
> - `2>&1 &`: chuyển stderr về stdout, và chạy nền.
> - File log nằm trong `nohup.out`.

#### Kiểm tra tiến trình đang chạy:

```bash
ps -ef | grep shoe
```

> Ghi chú:
> - `ps -ef`: liệt kê toàn bộ tiến trình.
> - `grep shoe`: lọc các tiến trình có từ khóa `shoe`.

#### Dừng tiến trình:

```bash
kill -9 PID
```

> Ghi chú:
> - `PID`: ID của tiến trình lấy từ `ps -ef`.

### 6. Thay đổi user, build đúng cách

> Ghi chú: Nếu bạn build bằng user root thì file tạo ra sẽ thuộc root → user khác không truy cập được.

#### Xóa file build cũ:

```bash
rm -rf nohup.out target/
```

#### Chuyển sang user ứng dụng:

```bash
su shoeshop
mvn install -DskipTests=true
nohup java -jar target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar 2>&1 &
```

#### Theo dõi log:

```bash
tail -f nohup.out
```

> Ghi chú:
> - `tail -f`: theo dõi log theo thời gian thực (log sẽ tiếp tục hiển thị khi ứng dụng ghi thêm).

---

## II. Gợi ý mở rộng

- Cấu hình với Nginx reverse proxy.
- Thiết lập HTTPS với Let’s Encrypt.
- Cấu hình systemd service để quản lý ứng dụng tự động khởi động.
- Triển khai CI/CD với GitHub Actions hoặc GitLab CI.
