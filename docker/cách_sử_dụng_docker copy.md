# Cài đặt và sử dụng Docker

## Cài đặt

1. Tạo một thư mục để lưu trữ các cấu hình của công cụ.

    1. $ mkdir /tools
    2. $ cd /tools/
    3. $ mkdir docker
    4. $ cd docker/
    5. Tạo file: $ vi install-docker.sh

    ```bash
    ###Ubuntu###

    #!/bin/bash

    sudo apt update
    sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    echo "deb [signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt update
    sudo apt install -y docker-ce
    sudo systemctl start docker
    sudo systemctl enable docker
    sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    sudo chmod +x /usr/local/bin/docker-compose
    docker --version
    docker-compose --version
    ```

    6. Để chạy được file sh này ta phải cần cấp quyền thực thi cho nó
        ```bash
        chmod +x install-docker.sh
        ```
    7. Sẽ có một vài cách chạy:
        1. Cách 1: `./install-docker.sh`
        2. Cách 2: `sh install-docker.sh`
        3. Cách 3: `bash install-docker.sh`

## Giới thiệu Docker

Ở bài trước ta đã biết Docker là một môi trường độc lập, hiểu một cách đơn giản nó sẽ tương tự như bạn cài đặt server máy ảo lên trên chiếc ubuntu.

Vậy thì trước đó ta đã biết Docker chúng ta sẽ có 1 cục gọi là Docker Image và từ cục đó chúng ta sẽ tạo lên một cái Docker Container. Thì Docker Image thông thường chúng ta sẽ kéo từ Docker Hub về.

## Sử dụng Docker Image và Container

### Kéo và chạy Image Ubuntu

Ví dụ:
Giờ ta muốn cài đặt một Container là Ubuntu tương tự như việc chúng ta tạo một server ubuntu ấy thì ta sẽ tìm là ubuntu docker image.

Giờ muốn cài đặt ubuntu 22.04 ta sẽ tiến hành pull về:

```bash
docker pull ubuntu:22.04
```

Thì chính xác nó sẽ tiến hành kéo một cái Image tên là ubuntu sau dấu ":" là phiên bản của cái Image đó.

Dùng lệnh để kiểm tra image đã tải về:

```bash
docker images
```

Kết quả:

```
REPOSITORY    TAG     IMAGE ID      CREATED      SIZE
ubuntu        22.04   c42dedf797ba  12 days ago  77.9MB
```

Tiến hành chạy container từ image đã tải:

```bash
docker run --name ubuntu -it ubuntu:22.04
```

Với các tùy chọn:

1. `--name`: Đặt tên cho container
2. `-it`: Tương ứng với việc bạn tạo cái ubuntu lên rồi mình truy cập vào cái môi trường của nó tức là truy cập vào môi trường Container. Cũng giống như việc bạn tạo một server ubuntu và bạn tiến hành truy cập vào nó thôi.
3. `ubuntu:22.04`: Chỉ định image và phiên bản

--> `root@ubuntuserver` --> `root@7a85d1487fb8:/#` --> giờ ta đang ở trong môi trường Container của ubuntu 22.04, `7a85d1487fb8` là id của container.

```bash
root@7a85d1487fb8:/# pwd
/
root@7a85d1487fb8:/# ip a
bash: ip: command not found
root@7a85d1487fb8:/#
```

--> Thấy sự khác biệt là câu lệnh `ip a` không được sử dụng ở trong ubuntu này rồi.
Thì tại sao lại như vậy?

Chính xác các Image của Container sẽ được tối giản nhất mức có thể để khi mà ta triển khai dự án bằng Container, nó sẽ nhẹ nhàng hơn rất là nhiều so với việc mà chúng ta triển khai ở trên server.

```bash
root@7a85d1487fb8:/# ls
bin boot dev etc home lib lib32 lib64 libx32 media mnt opt proc root run sbin srv sys tmp usr var
root@7a85d1487fb8:/#
```

--> Tổ chức file rất giống ở server ubuntu của chúng ta

### Cài đặt công cụ trong Container

Giờ ta muốn cài công cụ nào đó, chẳng hạn:

```bash
root@7a85d1487fb8:/home# apt update
root@7a85d1487fb8:/home# apt install net-tools -y
root@7a85d1487fb8:/home# netstat -tlpun
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
```

### Quản lý Container

Thoát khỏi môi trường Container quay lại môi trường của server:

```bash
root@7a85d1487fb8:/home# exit
exit
```

Để xem được các Container:

```bash
root@ubuntuserver:/tools/docker# docker ps
CONTAINER ID   IMAGE          COMMAND       CREATED          STATUS          PORTS     NAMES
```

--> Chính xác thì lệnh: `docker ps` chỉ xem được trạng thái của các Container đang chạy

```bash
root@ubuntuserver:/tools/docker# docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED          STATUS                      PORTS     NAMES
7a85d1487fb8   ubuntu:22.04   "/bin/bash"   8 minutes ago    Exited (130) 1 minute ago             ubuntu
```

--> Thêm `-a` để xem được tất cả những Container hiện có ở trên server

### Khởi động và truy cập Container

Để khởi động lại Container ở trên ta có thể dùng:

```bash
root@ubuntuserver:/tools/docker# docker start ubuntu
```

hoặc:

```bash
root@ubuntuserver:/tools/docker# docker start 7a8
```

(chỉ cần 3 ký tự đầu của Container id)

```bash
root@ubuntuserver:/tools/docker# docker ps
CONTAINER ID   IMAGE          COMMAND       CREATED          STATUS          PORTS     NAMES
7a85d1487fb8   ubuntu:22.04   "/bin/bash"   10 minutes ago   Up 20 seconds             ubuntu
```

--> Status sang `Up`

Để truy cập lại môi trường Container của Ubuntu thì ta sẽ không thể sử dụng lại câu lệnh `docker run -it` được, bởi vì câu lệnh `docker run` ta sẽ chỉ sử dụng duy nhất một lần để chạy Container ấy lên thôi. Thay vào đó ta sẽ phải sử dụng câu lệnh:

```bash
root@ubuntuserver:/tools/docker# docker exec -it [Container] [Câu lệnh để thực thi]
```

--> Ví dụ:

```bash
root@ubuntuserver:/tools/docker# docker exec -it ubuntu bash
```

## Chạy Web Server với Docker

### Chạy Nginx

Chạy một web server là nginx:
Để mà ta chạy được một Web server nginx thì các web server đó đầu ra cuối cùng sẽ là port.

```bash
root@ubuntuserver:/tools/docker# docker run --name nginx -dp [port]:[port] [Docker Image]
```

Tùy chọn:

-   `-d`: là để chạy dưới nền. Nếu không có tùy chọn `-d` thì những nội dung của Container sẽ được chạy trực tiếp ở trên màn hình
-   `-p`: có nghĩa là `--publish`

Sẽ chỉ định 2 port:

-   Bên trái: chính là port mà ta muốn chạy lên ở ngoài server
-   Bên phải: chính là port đang chạy ở trong Container

Ví dụ:

```bash
root@ubuntuserver:/tools/docker# docker run --name nginx -p 9999:80 nginx
```

Kiểm tra trạng thái:

```bash
root@ubuntuserver:/tools/docker# docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS                      PORTS     NAMES
91de1ac8f510   nginx          "/docker-entrypoint.…"   57 seconds ago   Exited (0) 6 seconds ago             nginx
7a85d1487fb8   ubuntu:22.04   "/bin/bash"              21 minutes ago   Up 10 minutes                        ubuntu
```

### Chạy Container từ Docker Hub

Tương tự bây giờ ta có thể chạy được những Container từ những Docker Image ở trên Docker Hub.
Ví dụ:

```bash
root@ubuntuserver:/tools/docker# docker run --name car-serv -dp 8888:80 elroydevops/car-serv
```

Thông thường những Image đã có trên Docker Hub thì ta sẽ không cần dùng `pull` mà ta có thể chạy trực tiếp như câu lệnh ở trên.

```bash
root@ubuntuserver:/tools/docker# docker ps
CONTAINER ID   IMAGE                  COMMAND                  CREATED          STATUS          PORTS                                   NAMES
85287e061243   elroydevops/car-serv   "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:8888->80/tcp, [::]:8888->80/tcp   car-serv
7a85d1487fb8   ubuntu:22.04           "/bin/bash"              25 minutes ago      Up 15 minutes                                           ubuntu
```

```bash
root@ubuntuserver:/tools/docker# docker ps -a
CONTAINER ID   IMAGE                  COMMAND                  CREATED          STATUS          PORTS                                   NAMES
85287e061243   elroydevops/car-serv   "/docker-entrypoint.…"   2 minutes ago    Up 2 minutes    0.0.0.0:8888->80/tcp, [::]:8888->80/tcp   car-serv
91de1ac8f510   nginx                  "/docker-entrypoint.…"   6 minutes ago    Exited (0) 5 minutes ago             nginx
7a85d1487fb8   ubuntu:22.04           "/bin/bash"              26 minutes ago   Up 15 minutes                                           ubuntu
```

## Dừng và xóa Container/Image

### Dừng Container

Vậy làm sao để dừng và xóa các Container cũng như là xóa các Image đi?

```bash
root@ubuntuserver:/tools/docker# docker stop [tên_container/ID]
```

--> Dừng các Container
ví dụ:

```bash
root@ubuntuserver:/tools/docker# docker stop ubuntu
```

### Xóa Container

Để xóa container dùng:

```bash
root@ubuntuserver:/tools/docker# docker rm -f car-serv
```

-   Tùy chọn `-f` để buộc xóa khi Container còn chạy

### Xóa tất cả Container

```bash
root@ubuntuserver:/tools/docker# docker rm -f $(docker ps -a -q)
```

--> Xóa toàn bộ Container hiện có trên server

```bash
root@ubuntuserver:/tools/docker# docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

### Xóa Image

Kiểm tra image:

```bash
root@ubuntuserver:/tools/docker# docker images
REPOSITORY              TAG        IMAGE ID       CREATED         SIZE
ubuntu                  22.04      c42dedf797ba   12 days ago     77.9MB
nginx                   latest     a830707172e8   3 weeks ago     192MB
elroydevops/car-serv    latest     2b7d4163107a   23 months ago   145MB
```

Để xóa image:

1. Đầu tiên cái Container của cái Docker image đã phải được xóa

Xóa image:

```bash
docker rmi [Tên]:[Tag]
```

ví dụ:

```bash
docker rmi ubuntu:22.04
```

## Các lệnh Docker hữu ích khác

### Xem log của container

```bash
docker logs [tên_container/ID]
```

### Xem thông tin chi tiết về container

```bash
docker inspect [tên_container/ID]
```

### Copy file từ máy host vào container

```bash
docker cp [file_nguồn] [tên_container]:[đường_dẫn_đích]
```

### Copy file từ container ra máy host

```bash
docker cp [tên_container]:[đường_dẫn_nguồn] [đường_dẫn_đích]
```

### Tạo image từ container

```bash
docker commit [tên_container] [tên_image_mới]:[tag]
```

### Tạo volume để lưu trữ dữ liệu

```bash
docker volume create [tên_volume]
```

### Sử dụng volume khi chạy container

```bash
docker run -v [tên_volume]:[đường_dẫn_trong_container] [tên_image]
```

## Sử dụng Docker Compose

Docker Compose là công cụ giúp định nghĩa và chạy ứng dụng Docker đa container. Với Compose, bạn sử dụng file YAML để cấu hình các dịch vụ cho ứng dụng của mình, sau đó bạn tạo và khởi động tất cả các dịch vụ từ cấu hình đó với một lệnh duy nhất.

### Ví dụ về file docker-compose.yml cơ bản:

```yaml
version: "3"
services:
    web:
        image: nginx:latest
        ports:
            - "8080:80"
        volumes:
            - ./html:/usr/share/nginx/html

    db:
        image: mysql:5.7
        environment:
            MYSQL_ROOT_PASSWORD: example
            MYSQL_DATABASE: app_db
        volumes:
            - db_data:/var/lib/mysql

volumes:
    db_data:
```

### Các lệnh Docker Compose cơ bản:

Khởi động các container:

```bash
docker-compose up -d
```

Dừng các container:

```bash
docker-compose down
```

Xem trạng thái các container:

```bash
docker-compose ps
```

Xem logs:

```bash
docker-compose logs
```

## Tổng kết

Docker là công cụ mạnh mẽ giúp đóng gói và chạy ứng dụng trong môi trường cô lập gọi là container. Docker giúp đơn giản hóa việc triển khai ứng dụng, tiết kiệm tài nguyên so với máy ảo truyền thống và đảm bảo ứng dụng hoạt động nhất quán trên nhiều môi trường khác nhau.

Các thành phần chính trong Docker:

-   Docker Image: Bản mẫu chỉ đọc chứa một bộ hướng dẫn để tạo container
-   Docker Container: Phiên bản chạy của một image
-   Docker Hub: Kho lưu trữ trung tâm chứa các image
-   Docker Compose: Công cụ để định nghĩa và chạy ứng dụng Docker đa container
