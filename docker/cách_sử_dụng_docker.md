# Cài đặt và sử dụng Docker

## Cài đặt

1. Tạo một thư mục để lưu trữ các cấu hình của công cụ.

    1. $ mkdir /tools
    2. $ cd /tools/
    3. $ mkdir docker
    4. $ cd docker/
    5. Tạo file: $ vi install-docker.sh

    ```
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
       chmod +x install-docker.sh
    7. Sẽ có một vài các chạy:
        1. Cách 1: ./install-docker.sh
        2. Cách 2: sh install-docker.sh
        3. Cách 3: bash install-docker.sh

Ở bài trước ta đã biết Docker là một môi trường độc lập, hiểu một cách đơn giản nó sẻ tương tự như bạn cài đặt server
máy ảo lên trên chiếc ubuntu.
Vậy thì trước đó ta đã biết Docker chúng ta sẽ có 1 cục gọi là Docker Image và từ cục đó chúng ta sẽ trên lên một cái
Docker Container. Thì Docker Image thông thường chúng ta sẽ kéo trên Docker Hub về
Ví dụ:
Giờ ta muốn cài đặt một Container là Ubuntu tương tự như việc chúng ta tạo một server ubuntu ấy thì ta sẽ tìm là
ubuntu docker image

giờ muốn cài đặt ubuntu 22.04 ta sẽ tiến hành pull về: docker pull ubuntu:22.04

Thì chính xác nó sẽ tiến hành kéo một cái Image tên là ubuntu sau dấu ":" là phiên bản của cái Image đó

Dùng lệnh: docker images
REPOSITORY TAG IMAGE ID CREATED SIZE
ubuntu 22.04 c42dedf797ba 12 days ago 77.9MB

Tiến hành chạy: docker run --name ubuntu -it ubuntu:22.04
Với các tùy chọn:

1. --name : Đặt tên cho nó
2. -it : Tương ứng với việc bạn tạo cái ubuntu lên rồi mình truy cập vào cái môi trường của nó tức là truy cập vào
   môi trường Container. Cũng giống như việc bạn tạo một server ubuntu và bạn tiến hành truy cập vào nó thôi.
3. image

--> root@ubuntuserver --> root@7a85d1487fb8:/# --> giờ ta đang ở trong môi trường Container của ubuntu 22.04
7a85d1487fb8 là id của container

root@7a85d1487fb8:/# pwd
/
root@7a85d1487fb8:/# ip a
bash: ip: command not found
root@7a85d1487fb8:/#

--> Thấy sự khác biệt là câu lệnh ip a không được sử dụng ở trong ubuntu này rồi
Thì tại lại như vậy?
Thì chính xác các cái Image của Container sẽ được tối giản nhất mức có thể để khi mà ta triển khai dư án bằng
Container nó sẽ nhẹ nhàng hơn rất là nhiều so với việc mà chúng ta triển khai ở trên server.

root@7a85d1487fb8:/# ls
bin boot dev etc home lib lib32 lib64 libx32 media mnt opt proc root run sbin srv sys tmp usr var
root@7a85d1487fb8:/#
--> Tổ chức file rất giống ở server ubuntu của chúng ta

Giờ ta muốn cài công cụ nào đó đi chẳng hạng:
root@7a85d1487fb8:/home# apt update
root@7a85d1487fb8:/home# apt install net-tools -y
root@7a85d1487fb8:/home# netstat -tlpun
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address Foreign Address State PID/Program name

Thoát khỏi môi trường Container quay lại môi trường của server:
root@7a85d1487fb8:/home# exit
exit

Để xem được các Container:
root@ubuntuserver:/tools/docker# docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
--> Chính xác thì lệnh: docker ps chỉ xem được trạng thái của các Container đang chạy

root@ubuntuserver:/tools/docker# docker ps -a
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
7a85d1487fb8 ubuntu:22.04 "/bin/bash" 8 minutes ago Exited (130) About a minute ago ubuntu
--> Thêm -a để xem được tất cả những Container hiện có ở trên server

Để khởi động lại Container ở trên ta có thể dùng:
root@ubuntuserver:/tools/docker# docker start ubuntu
hoặc:
root@ubuntuserver:/tools/docker# docker start 7a8 (chỉ cần 3 ký tự đầu của Container id)

root@ubuntuserver:/tools/docker# docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
7a85d1487fb8 ubuntu:22.04 "/bin/bash" 10 minutes ago Up 20 seconds ubuntu
--> Status sang Up

Để truy cập lại môi trường Container của Ubuntu thì ta sẽ không thể sử dụng lại câu lệnh docker run -it được
bởi vì câu lệnh docker run ta sẽ chỉ sử dụng duy nhất một lần để chạy Container ấy lên thôi mà ta sẽ phải sử
dụng câu lệnh là:
root@ubuntuserver:/tools/docker# docker exec -it [Container] [Câu lệnh để thực thi]
--> root@ubuntuserver:/tools/docker# docker exec -it ubuntu bash

Chạy một web server là nginx:
Vậy thì để mà ta chạy được một Web server nginx thì các web server đó đầu ra cuối cùng sẽ là port
root@ubuntuserver:/tools/docker# docker run --name nginx -dp [port]:[port] [Docker Image]

Tùy chọn:

-   -d : là để chạy dưới nền. Nếu không có tùy chọn -d thì những cái nội dung của Container sẽ được chạy trực tiếp ở trên
    màn hình
-   -p : có nghĩa là --publish

-   Sẽ chỉ định 2 port:
    -   Bên trái : chính là port mà ta muốn chạy lên ở ngoài server
    -   Bên phải : chính là port đang chạy ở trong Container

root@ubuntuserver:/tools/docker# docker run --name nginx -p 9999:80 nginx

root@ubuntuserver:/tools/docker# docker ps -a
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
91de1ac8f510 nginx "/docker-entrypoint.…" 57 seconds ago Exited (0) 6 seconds ago nginx
7a85d1487fb8 ubuntu:22.04 "/bin/bash" 21 minutes ago Up 10 minutes ubuntu

Tương tự bây giờ ta có thể chạy được những cái Container từ những Docker Image ở trên Docker Hub
ví dụ:
root@ubuntuserver:/tools/docker# docker run --name car-serv -dp 8888:80 elroydevops/car-serv

Thông thường những cái Image đã có trên Docker Hub thì ta sẽ không cần dùng pull mà ta có thể chạy như câu lệnh ở trên

root@ubuntuserver:/tools/docker# docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
85287e061243 elroydevops/car-serv "/docker-entrypoint.…" About a minute ago Up About a minute 0.0.0.0:8888->80/tcp, [::]:8888->80/tcp car-serv
7a85d1487fb8 ubuntu:22.04 "/bin/bash" 25 minutes ago Up 15 minutes

root@ubuntuserver:/tools/docker# docker ps -a
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
85287e061243 elroydevops/car-serv "/docker-entrypoint.…" 2 minutes ago Up 2 minutes 0.0.0.0:8888->80/tcp, [::]:8888->80/tcp car-serv
91de1ac8f510 nginx "/docker-entrypoint.…" 6 minutes ago Exited (0) 5 minutes ago nginx
7a85d1487fb8 ubuntu:22.04 "/bin/bash" 26 minutes ago Up 15 minutes

Vậy làm sao để dừng và xóa các Container cũng như là xóa các Image đi?

root@ubuntuserver:/tools/docker# docker stop [...]
--> Dừng các Container
ví dụ: root@ubuntuserver:/tools/docker# docker stop ubuntu

Để xóa dùng:
root@ubuntuserver:/tools/docker# docker rm -f car-serv

-   Tùy chọn -f để buộc xóa khi Container còn chạy

root@ubuntuserver:/tools/docker# docker images
REPOSITORY TAG IMAGE ID CREATED SIZE
ubuntu 22.04 c42dedf797ba 12 days ago 77.9MB
nginx latest a830707172e8 3 weeks ago 192MB
elroydevops/car-serv latest 2b7d4163107a 23 months ago 145MB

Để xóa image

1. Đầu tiên cái Container của cái Docker image đã phải được xóa

Dùng:
root@ubuntuserver:/tools/docker# docker rm -f $(docker ps -a)
--> Xóa toàn bộ Container hiện ở có trên server

root@ubuntuserver:/tools/docker# docker ps -a
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES

root@ubuntuserver:/tools/docker# docker images
REPOSITORY TAG IMAGE ID CREATED SIZE
ubuntu 22.04 c42dedf797ba 12 days ago 77.9MB
nginx latest a830707172e8 3 weeks ago 192MB
elroydevops/car-serv latest 2b7d4163107a 23 months ago 145MB

Xóa image:
docker rmi [Tên]:[Tag]
ví dụ: docker rmi ubuntu:22.04
