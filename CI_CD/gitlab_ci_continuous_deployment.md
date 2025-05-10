# Gitlab CI/CD

Những kiến thức cần biết để triển khai Gitlab CI/CD là gì?

Đúc kết thành đúng 2 bước sau đây:

Hãy hình dung rằng những công việc ta đang làm ở bài triển khai dự án linux trước đó như:
truy cập server, kéo code, build, run, check code,...
Bây giờ chúng ta tiến hành cài một công cụ nào đó đóng vai trò như một con robot và ta sẽ
viết kịch bản để nó thực hiện. Vậy các bước đó là:

1. Cài đặt con robot đó vào server, kết nó với dự án trên Gitlab
2. Viết file kịch bản (file cấu hình công việc).

Bản chất cũng chỉ có vậy.

Vậy con robot đó là gì?
Chính là Gitlab runner

Công cụ này ta sẽ cần cài đặt trên mỗi server mà ta cần triển khai dự án trên đó

Và tiếp theo kịch bản sẽ được viết ở đâu?
Kịch bản đó sẽ được viết trực tiếp trong dự án của bạn và format, tên file chính là:
".gitlab-ci.yml"

File yml có định dạng key-value

Khi ta đã liên kết con robot đó đến dự án và viết file kịch bản nó sẽ tự hiểu để thực hiện những công việc đó
một cách hoàn toàn tự động

# Cài đặt runner và kết nối runner đến dự án

()[https://elroydevops.tech/setup-gitlab-cicd-pipeline-private-registry/]

1. Cài đặt công cụ Gitlab runner

    1. $ apt-get update

    2. $ curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | bash

    3. $ apt-get install gitlab-runner

    4. $ gitlab-runner -version

2. Tương tự như nginx hay các công cụ khác, gitlab runner cũng có một user riêng:

    1. $ vi /etc/passwd
    2. --> gitlab-runner
    3. Thư mục làm việc /home/gitlab-runner

3. Kết nối gitlab runner với dự án:
    1. Vào gitlab vào Settings --> CI/CD --> Runners
    2. Vào server chạy lệnh:
        1. gitlab-runner register
        2. Nhập url của gitlab: --> http://git.elroydevops.tech/
        3. Enter the registration token: --> -vXyuochJNwHzpoPu3aF
        4. Enter a description for the runner:
           [ubuntuserver]: ubuntuserver (nên đặt cùng tên của server)
        5. Enter tags for the runner (comma-separated): (tên của con runner)
           ubuntuserver (nên đặt cùng tên của server)
        6. Enter an executor: shell, ssh, parallels, docker-windows, docker+machine, kubernetes, custom, virtualbox, docker, docker-autoscaler, instance:
            1. Có 3 executor cần chú ý: shell, docker, kubernetes
            2. Shell: sẽ chạy trực tiếp câu lênh trên server
            3. Docker: sẽ tạo ra một môi trường độc lập và chạy các câu lệnh hay kịch bản đó
            4. Hiện tại ta chọn shell
            5. --> Configuration (with the authentication token) was saved in "/etc/gitlab-runner/config.toml"
        7. $ vi /etc/gitlab-runner/config.toml
            1. Sửa dòng concurrent:
                1. Từ concurrent = 1 --> concurrent = 4
                2. Có ý nghĩa là: con runner này có thể chạy đồng thời bao nhiêu dự án, thế tức nghĩa là:
                   ví dụ: trên con server của ta không chỉ có 1 dự án thì ta có thể sử dụng con runner này để chạy
                   nhiều dự án khác nếu ta để 1 thì nếu có 1 dự án đã chạy rồi thì chỉ có thể chạy 1 dự án đấy thôi
                   các dự án khác phải chờ hoàn thành xong thì nó mới tiến hành chạy được.
        8. $ gitlab-runner run --> khởi động
            1. Còn các tùy chọn:
                1. --working-directory /home/gitlab-runner (chỉ định thư mục làm việc)
                2. --config /etc/gitlab-runner/config.toml
                3. --service gitlab-runner
                4. --user gitlab-runner (user để chạy)
                5. Lưu ý nếu chạy trực tiếp lệnh này thì nó sẽ chạy trên nền và ta không thể thao tác tiếp nữa
            2. --> chạy: nohup gitlab-runner run --working-directory /home/gitlab-runner --config /etc/gitlab-runner/config.toml --service gitlab-runner --user gitlab-runner 2>&1 &
            3. --> có thêm được file. nohup.out
        9. $ ps -ef| grep gitlab-runner:
            1. Kiểm tra
        10. Vào lại gitlab kiểm tra ở mục. CI/CD --> Available specific runners:
            1. Sẽ thấy có 1 con runner chạy đó là: ubuntuserver
            2. Bấm edit
            3. Các điều cần lưu ý:
                1. Active: tùy chọn này nếu ta bỏ chọn thì runner này sẽ không được online
                2. Protected: nếu ta chọn vào thì chỉ những nhánh nào được bảo vệ mới được chạy --> thường thì không chọn
                3. Run untagged jobs: Nếu chọn thì khi ta cấu hình kịch bản thì nó sẽ trực tiếp chạy trên những con runner
                   được kết nối đến dự án đó mà không cần chỉ định chính xác. (hơi khó hiểu)
                4. Lock to current projects: Khi tick chọn thì các dự án khác sẽ không thể sử dụng được runner này --> nên bỏ tick chọn.

# Viết kịch bản

1. Tại nhánh develop tạo file: ".gitlab-ci.yml"

```
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
```

2. commit
3. Vào CI/CD sẽ thấy có 1 pipeline (pipeline là gì?) đang hoạt động nhưng nó đang stuck vì:
   This job is stuck because the project doesn't have any runners online assigned to it.
4. Vào Settings --> CI/CD --> Runners --> Available specific runners --> Edit
   Hãy nhớ lại tùy chọn: Run untagged jobs
   Ta đã không tick chọn vào tùy chọn này, thế tức là nếu muốn chạy được các pipeline thì
   phải tick chọn vào tùy chọn này thì mới không cần khai báo chính xác một runner nào đó
   hay nói một cách đơn giản là khi bạn tick chọn tùy chọn này thì bạn không cần gọi tên runner là abcd
   Nhưng thực tế ta sẽ không tick chọn vào tùy chọn này mà ta sẽ tiến hành khai báo thêm một trường nữa
5. Tiến hành cancel pipeline này đi
6. Thêm một trường nữa trong ".gitlab-ci.yml"

```
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

7. Tiến hành commit lại
8. Quay lại phần CI/CD
9. Sẽ không thấy từ stuck nữa --> bấm vào --> xem chi tiết

```
Running with gitlab-runner 17.11.1 (96856197)
  on ubuntuserver Wz-opzwb, system ID: s_91477b4f9069 --> chạy trên server nào
Preparing the "shell" executor --> Kiểu shell executor
00:00
Using Shell (bash) executor... --> Sử dụng shell (bash)
Preparing environment
00:00
Running on ubuntuserver... --> Môi trường để chạy
Getting source from Git repository
00:01
Fetching changes with git depth set to 50...
Initialized empty Git repository in /home/gitlab-runner/builds/Wz-opzwb/0/shoeshop/shoeshop/.git/ --> gitlab-runner sẽ tiến hành kéo code về thư mục /home/gitlab-runner/builds/Wz-opzwb/0/shoeshop/shoeshop/ --> không cần bước copy code lên server một cách thủ công mà khi chỉ cần chạy pipeline là gitlab-runner sẽ tự kéo code về trên server --> Tức là kể cả ta không khai báo các câu lệnh trong script thì gitlab-runner vẫn mặc định kéo code về cho ta trên server
Created fresh repository.
Checking out 085728a7 as detached HEAD (ref is develop)...
Skipping Git submodules setup
Executing "step_script" stage of the job script
00:00 --> Các câu lệnh chạy trong pipeline
$ whoami
gitlab-runner --> chính xác user gitlab-runner đang kéo code về
$ pwd
/home/gitlab-runner/builds/Wz-opzwb/0/shoeshop/shoeshop --> vị trí làm việc chính là
$ ls
config.txt
login.txt
pom.xml
README.md
shoe_shopdb.sql
src
Cleaning up project directory and file based variables
00:00
Job succeeded
```

10. Chính xác khi ta chạy mỗi job nó sẽ xóa hoàn toàn code cũ trên server và thay thế bằng code ở trên git về thư mục làm việc vậy sẽ luôn đảm dù bạn có thay đổi bất cứ thứ gì đó trên server thì bạn chỉ cần chạy pipeline thì code mới sẽ được áp dụng

11. Sửa script:

```
stages:
    - build
    - deploy
    - checklog

build:
    stage: build
    script:
        - mvn install -DskipTests=true
    tags:
        - ubuntuserver

```

12. commit
13. Chờ build thành công
14. Kiểm tra:
    gitlab-runner@ubuntuserver:~/builds/Wz-opzwb/0/shoeshop/shoeshop$ ls
    config.txt login.txt pom.xml README.md shoe_shopdb.sql src target
    --> sẽ thấy đã tạo được thư mục target --> Tiến hành chạy target này
    Nhưng trong thực tế ta sẽ không chạy trực tiếp bằng user gitlab-runner mà
    ta sẽ chạy bằng user của dự án như đúng như các bước mà ta đã làm

15. Vì trước ta đã deploy thủ công nên không cần tạo user shoeshop nữa:

    1. Tạo thư mục /datas: root@ubuntuserver:~# mkdir /datas
    2. Tạo thư mục shoeshop: root@ubuntuserver:~# mkdir /datas/shoeshop
    3. Cho phép gitlab-runner được sử dụng sudo mà không cần password
        1. Vào: root@ubuntuserver:~# visudo
        2. Dưới dòng root thêm: (sẽ cho phép các câu lệnh nào gitlab-runner chó thể sử dụng thôi)
            1. gitlab-runner ALL=(ALL) NOPASSWD: /bin/cp\*
            2. gitlab-runner ALL=(ALL) NOPASSWD: /bin/chown\*
            3. gitlab-runner ALL=(ALL) NOPASSWD: /bin/su shoeshop\*

16. Tiếp tục thêm bước deploy:

```
stages:
    - build
    - deploy
    - checklog

build:
    stage: build
    script:
        - mvn install -DskipTests=true
    tags:
        - ubuntuserver

deploy:
    stage: deploy
    script:
        - java -jar target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
    tags:
        - ubuntuserver

```

--> Đoán xem pipeline của ta có chạy thành công dự án không ?
--> Sau khi commit vào lại CI/CD sẽ thấy build done nhưng deploy failed
Tại sao vậy?
Vì mỗi stage sẽ đều tiến hành xóa sạch code cũ trên server và tiến hành clone code mới về
mà code mới của ta hoàn toàn làm gì có thư mục target --> sao mà deploy được
Vậy chính xác nếu muốn có thư mục target ở deploy ta sẽ phải chạy script - mvn install -DskipTests=true ở deploy một lần nữa
Nhưng thực tế ta sẽ không làm như vậy ta sẽ làm như sau:

```
stages:
    - build
    - deploy
    - checklog

build:
    stage: build
    variables:
        GIT_STRATEGY: clone
    script:
        - mvn install -DskipTests=true
    tags:
        - ubuntuserver

deploy:
    stage: deploy
    variables:
        GIT_STRATEGY: none
    script:
        - java -jar target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
    tags:
        - ubuntuserver

```

Dùng GIT_STRATEGY ...

17. commit
18. Tiếp theo:
    1.  Thực tế ta sẽ không chạy bằng user gitlab-runner sẽ chạy bằng user chính xác của nó là shoeshop
    2.  Sửa tiếp file kịch bản:

```
stages:
    - build
    - deploy
    - checklog

build:
    stage: build
    variables:
        GIT_STRATEGY: clone
    script:
        - mvn install -DskipTests=true
    tags:
        - ubuntuserver

deploy:
    stage: deploy
    variables:
        GIT_STRATEGY: none
    script:
        - sudo cp target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar /datas/shoeshop
        - sudo chown shoeshop. /datas/shoeshop
        - sudo su shoeshop -c "cd /datas/shoeshop/; nohup java -jar shoe-ShoppingCart-0.0.1-SNAPSHOT.jar 2>&1 &"
    tags:
        - ubuntuserver
```

Cần giải thích ý nghĩa các lệnh trong script của stage deploy

19. commit

20. root@ubuntuserver:~# ls -l /datas
    total 4
    drwxr-xr-x 2 shoeshop shoeshop 4096 May 10 06:52 shoeshop
    --> sẽ thấy quyền đã được thay đổi cho shoeshop

    root@ubuntuserver:~# ls -l /datas/shoeshop/
    total 54820
    -rw-r--r-- 1 root root 56134101 May 10 06:52 shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
    --> Thiếu tùy chọn -R

    Và ngoài ra trong mục CI/CD thì thấy:

```
    ***************************
    APPLICATION FAILED TO START
    ***************************
    Description:
    Web server failed to start. Port 8080 was already in use.
```

    --> Vậy chính xác trước khi chạy dự án lên, ta cần kill cái process đã chạy trước đó
    --> Lấy ra pid của process:
    root@ubuntuserver:~# ps -ef| grep shoe-ShoppingCart-0.0.1-SNAPSHOT.jar | grep -v grep | awk '{print $2}'
    3960
    (Cần giải thích ý nghĩa và cú pháp của câu lệnh)
    --> Kill process
    kill -9 $(ps -ef| grep shoe-ShoppingCart-0.0.1-SNAPSHOT.jar | grep -v grep | awk '{print $2}')
    (Cần giải thích ý nghĩa và cú pháp của câu lệnh)

21. Sửa tiếp file kịch bản:

```
variables:
    projectname: shoe-ShoppingCart
    version: 0.0.1-SNAPSHOT
    projectuser: shoeshop
    projectpath: /datas/$projectuser/

stages:
    - build
    - deploy
    - checklog

build:
    stage: build
    variables:
        GIT_STRATEGY: clone
    script:
        - mvn install -DskipTests=true
    tags:
        - ubuntuserver

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
```

Cần giải thích ý nghĩa các lệnh đã sửa

22. commit
23. thử thêm thông tin config của database trên gitlab
24. commit lại
25. --> Thành công
26. Nhưng cũng không phải khi commit file nào ta cũng phải build lại dự án
27. Sửa tiếp file kịch bản:

```
variables:
    projectname: shoe-ShoppingCart
    version: 0.0.1-SNAPSHOT
    projectuser: shoeshop
    projectpath: /datas/$projectuser/

stages:
    - build
    - deploy
    - checklog

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
```

Vậy khi tạo tags thì pipeline mới chạy

28. Thêm showlog:

```
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

29. commit
30. Tạo Tags
31. Tạm ổn
