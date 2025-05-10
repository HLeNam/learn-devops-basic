# Gitlab CI/CD - Cẩm nang triển khai

## Tổng quan kiến thức

Những kiến thức cần biết để triển khai Gitlab CI/CD bao gồm 2 bước chính:

1. **Cài đặt Gitlab Runner** vào server và kết nối nó với dự án trên Gitlab
2. **Viết file kịch bản** (file cấu hình công việc)

Các công việc thủ công trước đây như truy cập server, kéo code, build, run, check code... giờ sẽ được tự động hóa qua một "con robot" gọi là **Gitlab Runner** và các kịch bản được viết trong file `.gitlab-ci.yml`.

## Cài đặt Gitlab Runner và kết nối với dự án

1. **Cài đặt công cụ Gitlab Runner**:

    ```bash
    apt-get update
    curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | bash
    apt-get install gitlab-runner
    gitlab-runner -version
    ```

2. **Tìm hiểu về user của Gitlab Runner**:

    - Gitlab Runner có user riêng: `gitlab-runner`
    - Thư mục làm việc: `/home/gitlab-runner`
    - Kiểm tra: `vi /etc/passwd`

3. **Kết nối Gitlab Runner với dự án**:

    - Vào Gitlab: `Settings` → `CI/CD` → `Runners`
    - Trên server, chạy lệnh:
        ```bash
        gitlab-runner register
        ```
    - Nhập thông tin:
        - URL của Gitlab: `http://git.elroydevops.tech/`
        - Token đăng ký: `-vXyuochJNwHzpoPu3aF`
        - Mô tả cho runner: `ubuntuserver` (nên đặt trùng tên server)
        - Tags cho runner: `ubuntuserver` (nên đặt trùng tên server)
        - Chọn executor: `shell` (có thể chọn `shell`, `docker`, hoặc `kubernetes`)
            - `shell`: chạy trực tiếp câu lệnh trên server
            - `docker`: tạo môi trường độc lập và chạy các câu lệnh
            - `kubernetes`: chạy trên cụm kubernetes

4. **Cấu hình Runner**:

    - Chỉnh sửa file cấu hình: `vi /etc/gitlab-runner/config.toml`
    - Thay đổi `concurrent = 1` thành `concurrent = 4` (cho phép chạy đồng thời tối đa 4 dự án)

5. **Khởi động Runner**:

    ```bash
    nohup gitlab-runner run --working-directory /home/gitlab-runner --config /etc/gitlab-runner/config.toml --service gitlab-runner --user gitlab-runner 2>&1 &
    ```

    - Kiểm tra Runner đang chạy: `ps -ef | grep gitlab-runner`

6. **Kiểm tra kết nối**:
    - Vào Gitlab: `CI/CD` → `Available specific runners`
    - Sẽ thấy runner `ubuntuserver` đã xuất hiện
    - Các tùy chọn cần lưu ý:
        - **Active**: Nếu bỏ chọn, runner sẽ không online
        - **Protected**: Nếu chọn, chỉ những nhánh được bảo vệ mới chạy được
        - **Run untagged jobs**: Nếu chọn, runner sẽ chạy các job không có tag
        - **Lock to current projects**: Nếu chọn, runner chỉ chạy cho dự án hiện tại

## Viết file kịch bản CI/CD (.gitlab-ci.yml)

### Cấu trúc cơ bản của file kịch bản

File có định dạng YAML và được đặt tại gốc của dự án với tên chính xác là `.gitlab-ci.yml`.

### Ví dụ file kịch bản đơn giản

```yaml
stages:
    - build
    - deploy
    - checklog

build:
    stage: build
    script:
        - whoami
        - pwd
        - ls
    tags:
        - ubuntuserver
```

**Giải thích**:

-   `stages`: Định nghĩa các giai đoạn của pipeline (theo thứ tự)
-   `build`: Tên của job
-   `stage`: Job này thuộc giai đoạn nào
-   `script`: Các lệnh shell sẽ được thực thi
-   `tags`: Chỉ định runner nào sẽ thực thi job này

### Quy trình cơ bản khi chạy pipeline

Khi chạy pipeline, Gitlab Runner sẽ:

1. Tự động kéo code từ git repository về thư mục làm việc
2. Thực thi các lệnh trong phần `script`
3. Sau khi job hoàn thành, nó sẽ xóa thư mục làm việc

### Chiến lược Git

Để tránh việc xóa code giữa các stage, ta có thể sử dụng biến `GIT_STRATEGY`:

-   `GIT_STRATEGY: clone` - Tải hoàn toàn code mới (mặc định)
-   `GIT_STRATEGY: none` - Không thực hiện bất kỳ hành động Git nào

### Triển khai dự án Spring Boot

#### Bước 1: Chuẩn bị môi trường

```bash
# Tạo thư mục chứa ứng dụng
mkdir -p /datas/shoeshop
```

#### Bước 2: Cấp quyền cho gitlab-runner

```bash
# Chỉnh sửa sudoers
visudo

# Thêm các dòng sau
gitlab-runner ALL=(ALL) NOPASSWD: /bin/cp\*
gitlab-runner ALL=(ALL) NOPASSWD: /bin/chown\*
gitlab-runner ALL=(ALL) NOPASSWD: /bin/su shoeshop\*
```

#### Bước 3: Viết file kịch bản CI/CD đầy đủ

```yaml
variables:
    projectname: shoe-ShoppingCart
    version: 0.0.1-SNAPSHOT
    projectuser: shoeshop
    projectpath: /datas/$projectuser/

stages:
    - build
    - deploy
    - showlog

build:
    stage: build
    variables:
        GIT_STRATEGY: clone
    script:
        - mvn install -DskipTests=true
    tags:
        - ubuntuserver
    only:
        - tags

deploy:
    stage: deploy
    variables:
        GIT_STRATEGY: none
    script:
        - sudo cp target/$projectname-$version.jar $projectpath
        - sudo chown -R $projectuser. $projectpath
        - sudo su $projectuser -c "kill -9 $(ps -ef| grep $projectname-$version.jar | grep -v grep | awk '{print $2}')"
        - sudo su $projectuser -c "cd $projectpath; nohup java -jar $projectname-$version.jar > nohup.out 2>&1 &"
    tags:
        - ubuntuserver
    only:
        - tags

showlog:
    stage: showlog
    variables:
        GIT_STRATEGY: none
    script:
        - sudo su $projectuser -c "cd $projectpath; tail -n 10000 nohup.out"
    tags:
        - ubuntuserver
    only:
        - tags
```

## Giải thích các lệnh quan trọng

### Giải thích lệnh tìm PID để kill

```bash
ps -ef | grep shoe-ShoppingCart-0.0.1-SNAPSHOT.jar | grep -v grep | awk '{print $2}'
```

-   `ps -ef`: Liệt kê tất cả các process đang chạy với định dạng đầy đủ
-   `grep shoe-ShoppingCart-0.0.1-SNAPSHOT.jar`: Lọc ra các process có chứa tên file jar
-   `grep -v grep`: Loại bỏ dòng chứa lệnh grep (tránh lọc ra lệnh grep chính nó)
-   `awk '{print $2}'`: In ra cột thứ 2 (PID) của kết quả

### Giải thích lệnh kill process

```bash
kill -9 $(ps -ef | grep shoe-ShoppingCart-0.0.1-SNAPSHOT.jar | grep -v grep | awk '{print $2}')
```

-   `kill -9`: Gửi tín hiệu SIGKILL để buộc process dừng ngay lập tức
-   `$(...)`: Thực thi lệnh trong ngoặc và sử dụng kết quả làm tham số

### Giải thích các lệnh trong script của stage deploy

```yaml
- sudo cp target/$projectname-$version.jar $projectpath
```

Sao chép file JAR từ thư mục build sang thư mục triển khai

```yaml
- sudo chown -R $projectuser. $projectpath
```

Thay đổi quyền sở hữu thư mục triển khai cho user của dự án (tham số `-R` đảm bảo áp dụng cho tất cả các file và thư mục con)

```yaml
- sudo su $projectuser -c "kill -9 $(ps -ef| grep $projectname-$version.jar | grep -v grep | awk '{print $2}')"
```

Dùng user của dự án để kill process cũ đang chạy (nếu có)

```yaml
- sudo su $projectuser -c "cd $projectpath; nohup java -jar $projectname-$version.jar > nohup.out 2>&1 &"
```

Chạy ứng dụng với user của dự án, ghi log vào file nohup.out, và chạy ở nền

## Lưu ý quan trọng

1. **Chỉ chạy pipeline khi tạo tag**:

    ```yaml
    only:
        - tags
    ```

    Thiết lập này đảm bảo pipeline chỉ chạy khi bạn tạo tag trên GitLab, không chạy mỗi khi commit.

2. **Tái sử dụng biến**:

    ```yaml
    variables:
        projectname: shoe-ShoppingCart
        version: 0.0.1-SNAPSHOT
        projectuser: shoeshop
        projectpath: /datas/$projectuser/
    ```

    Định nghĩa biến ở đầu file giúp dễ dàng tái sử dụng và bảo trì.

3. **Theo dõi log**:
    ```yaml
    showlog:
        stage: showlog
        script:
            - sudo su $projectuser -c "cd $projectpath; tail -n 10000 nohup.out"
    ```
    Stage này giúp hiển thị log của ứng dụng sau khi triển khai để kiểm tra nhanh.

## Kết luận

Gitlab CI/CD là công cụ mạnh mẽ giúp tự động hóa quy trình triển khai dự án. Thông qua Gitlab Runner và file kịch bản `.gitlab-ci.yml`, bạn có thể dễ dàng xây dựng một pipeline đáng tin cậy, từ việc build, test đến triển khai ứng dụng.
