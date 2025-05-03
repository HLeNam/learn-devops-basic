# Triển khai các dự án Backend

Các bước đầu tiên tương tự với Frontend là di chuyển file dự án từ máy cá nhân lên máy chủ server

Tiến hành:
Tạo thư mục riêng cho dự án
Tạo user riêng cho dự án
Phân quyền truy cập và quyền sở hữu cho thư mục dự án

Tiếp đến sẽ ví dụ triển khai một dự án Java Spring Boot:

1. Cài đặt công cụ cần thiết:
    1. Cài java
    2. Cài maven
2. Xem và sửa file cấu hình:
    1. file pom.xml
3. Cài đặt và thiết lập database:
    1. Ở dự án này dùng maria db
4. Build dự án
5. Run dự án
6. Kiểm tra hoạt động

Cài Java (17):
apt install openjdk-17-jdk openjdk-17-jre
java -version

Cài Maven:

1. Update the local package repository index:
   sudo apt update
2. Install Maven from the official Ubuntu repository:
   sudo apt install maven -y
3. Check the current Maven version to verify the installation:
   mvn -version

Thiết lập database:

1. Tìm file application.properties
   root@ubuntuserver:/projects/shoeshop# ls src/main/resources/
   application.properties static templates validation.properties

2. Nhìn thử file cấu hình:
   root@ubuntuserver:/projects/shoeshop# vi src/main/resources/application.properties

3. Cài đặt maria DB: (Chú ý: nếu có tài nguyên nên cài đặt db ở một server riêng và ở trên server đó thêm các loại db khác nữa để thuật tiện kết nối)
    1. apt install mariadb-server
    2. Kiểm tra kết nối:
        1. root@ubuntuserver:/projects/shoeshop# netstat -ltpun
           tcp 0 0 127.0.0.1:3306 0.0.0.0:\* LISTEN 7495/mysqld
        2. Nhận thấy rằng cổng 3306 chỉ đang chạy ở local của server
           --> không thể nào truy cập từ cách server khác
           Thông thường trong môi trường triển khai trong công ty nó đã là môi trường private rồi
           --> Hoàn toàn có thể chuyển 127.0.0.1:3306 --> 0.0.0.0:3306
    3. Sửa config: (các file cấu hình thường ở trong /etc)
        1. Trước khi sửa stop:
            1. root@ubuntuserver:/projects/shoeshop# systemctl stop mariadb
        2. Kiểm tra xem:
            ```
            root@ubuntuserver:/projects/shoeshop# ls /etc/mysql/mariadb.conf.d/
            50-client.cnf  50-mysql-clients.cnf  50-mysqld_safe.cnf  50-server.cnf
            ```
        3. Sửa:
            1. root@ubuntuserver:/projects/shoeshop# vi /etc/mysql/mariadb.conf.d/50-server.cnf
            2. Sửa dòng bind-address sang 0.0.0.0
            3. Khởi động lại
               root@ubuntuserver:/projects/shoeshop# systemctl restart mariadb
    4. Kiểm tra lại:
        1. root@ubuntuserver:/projects/shoeshop# netstat -ltpun
           tcp 0 0 0.0.0.0:3306 0.0.0.0:\* LISTEN 9058/mysqld
    5. Tạo database:
        1. root@ubuntuserver:/projects/shoeshop# mysql -u root: (chưa biết công dụng)
        2. MariaDB [(none)]> show datas (chưa biết công dụng)
        ```
         +--------------------+
         | Database           |
         +--------------------+
         | information_schema |
         | mysql              |
         | performance_schema |
         +--------------------+
         3 rows in set (0.001 sec)
        ```
        3. Tạo database:
            1. MariaDB [(none)]> create database shoeshop;
               Query OK, 1 row affected (0.001 sec)
            2. Khi tạo database tương ứng ta cũng phải tạo một user, chứ không được dùng root
               MariaDB [(none)]> create user 'shoeshop'@'%' identified by 'shoeshop'; (Chưa biết rõ các tham số và các câu lệnh của phần này và phần trước của đoạn khi vào database này)
               Query OK, 0 rows affected (0.000 sec)
            3. Gán quyền:
               MariaDB [(none)]> grant all privileges on shoeshop.\* to 'shoeshop'@'%'; (tương tự cũng không hiểu)
            4. Lưu lại các quyền mà ta thay đổi:
               MariaDB [(none)]> flush privileges;
        4. Mở database shoeshop?
           root@ubuntuserver:/projects/shoeshop# mysql -h 192.168.0.110 -P 3306 -u shoeshop -p (cũng chưa hiểu)
        5. Sử dụng database:
            1. MariaDB [(none)]> use shoeshop;
               Database changed
               MariaDB [shoeshop]>
            2. MariaDB [shoeshop]> show tables;
               Empty set (0.000 sec)
            3. Import file .sql
                1. MariaDB [shoeshop]> source /projects/shoeshop/shoe_shopdb.sql (cũng chưa hiểu)
            4. Kiểm tra lại:
                1. MariaDB [shoeshop]> show tables;
                ```
                +--------------------+
                | Tables_in_shoeshop |
                +--------------------+
                | Accounts           |
                | Order_details      |
                | Orders             |
                | Products           |
                +--------------------+
                4 rows in set (0.001 sec)
                ```

Sửa file cấu hình của database:

1. root@ubuntuserver:/projects/shoeshop# vi src/main/resources/application.properties

Build dự án:

1. root@ubuntuserver:/projects/shoeshop# mvn install --help

2. Nên thêm tùy chọn:

    1. root@ubuntuserver:/projects/shoeshop# mvn install -DskipTests=true
    2. Thêm -DskipTests=true để bỏ qua quá trình test trực tiếp của tool maven (công đoạn test này chưa cần thiết)

3. root@ubuntuserver:/projects/shoeshop# ls
   pom.xml README.md shoe_shopdb.sql src target

4. root@ubuntuserver:/projects/shoeshop# ls target/
   classes maven-archiver shoe-ShoppingCart-0.0.1-SNAPSHOT.jar.original
   generated-sources maven-status test-classes
   generated-test-sources shoe-ShoppingCart-0.0.1-SNAPSHOT.jar

5. root@ubuntuserver:/projects/shoeshop# java -jar target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar

6. root@ubuntuserver:/projects/shoeshop# nohup java -jar target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar 2>&1 & (Chưa hiểu)
   [1] 9957
   root@ubuntuserver:/projects/shoeshop# nohup: ignoring input and appending output to 'nohup.out'

7. root@ubuntuserver:/projects/shoeshop# ls
   nohup.out pom.xml README.md shoe_shopdb.sql src target

8. root@ubuntuserver:/projects/shoeshop# ps -ef| grep shoe (chưa hiểu)
   root 9957 2544 10 18:08 pts/0 00:00:12 java -jar target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
   root 10038 2544 0 18:10 pts/0 00:00:00 grep --color=auto shoe

9. root@ubuntuserver:/projects/shoeshop# kill -9 9957 (chưa hiểu)

10. root@ubuntuserver:/projects/shoeshop# su shoeshop
    shoeshop@ubuntuserver:/projects/shoeshop$ ls -l
    total 2616
    -rw------- 1 root root 4589 May 3 18:08 nohup.out
    -rwxr-x--- 1 shoeshop shoeshop 3060 Feb 8 2024 pom.xml
    -rwxr-x--- 1 shoeshop shoeshop 616 Nov 15 2023 README.md
    -rwxr-x--- 1 shoeshop shoeshop 2650849 Nov 15 2023 shoe_shopdb.sql
    drwxr-x--- 4 shoeshop shoeshop 4096 Jan 9 2024 src
    drwxr-xr-x 8 root root 4096 May 3 18:02 target

    Vấn đề lúc nãy ta build dự án bằng user root --> điều này là chưa chính xác --> làm lại

11. Xóa

    1. root@ubuntuserver:/projects/shoeshop# rm -rf nohup.out target/ (chưa hiểu lắm các câu lệnh)

12. root@ubuntuserver:/projects/shoeshop# ls
    pom.xml README.md shoe_shopdb.sql src

13. root@ubuntuserver:/projects/shoeshop# su shoeshop
    shoeshop@ubuntuserver:/projects/shoeshop$

14. shoeshop@ubuntuserver:/projects/shoeshop$ mvn install -DskipTests=true

15. shoeshop@ubuntuserver:/projects/shoeshop$ nohup java -jar target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar 2>&1 &
    [1] 10285

16. shoeshop@ubuntuserver:/projects/shoeshop$ ls
    nohup.out pom.xml README.md shoe_shopdb.sql src target

17. shoeshop@ubuntuserver:/projects/shoeshop$ tail -f nohup.out (Chưa hiểu câu lệnh)
