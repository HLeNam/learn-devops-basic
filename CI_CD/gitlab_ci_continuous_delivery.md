# GitLab CI/CD - Continuous Delivery

## ✅ Ở phần trước

Ta đã viết file cấu hình `.gitlab-ci.yml` để tự động triển khai (Continuous Deployment - CD):

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

---

## ❓ Nhưng nếu ta muốn CD là **Continuous Delivery** thì sao?

> ✅ Tức là pipeline chạy build, nhưng `deploy` và `showlog` chỉ được thực hiện **thủ công** bởi người có quyền xác nhận.

---

## 🔑 Keyword quan trọng: `when: "manual"`

### 1. Chỉnh sửa stage `deploy`

Thêm thuộc tính `when: manual`:

```yaml
deploy:
    ...
    when: manual
    script:
        ...
    ...
```

### 2. Vậy còn stage `showlog` thì sao?

-   Nếu không thêm `manual`, GitLab sẽ bỏ qua `deploy` (vì manual) và **chạy luôn `showlog`**.
-   Điều đó là sai logic vì log có thể không chính xác (ứng dụng chưa được triển khai).
-   Do đó, ta cũng thêm `when: manual` cho `showlog`:

```yaml
showlog:
    ...
    when: manual
    script:
        ...
    ...
```

---

### 3. Mục đích việc dùng `manual` là gì?

-   Chỉ người có quyền **approve** (xác nhận) mới được phép thực thi `deploy`.
-   Dùng trong môi trường production hoặc staging sau khi:
    -   Quét bảo mật (security scan)
    -   Kiểm tra code chất lượng (code quality check)
    -   Xác minh test/pass của các bước trước

---

### 4. Kiểm soát quyền người được phép `deploy`

Thêm logic kiểm tra `GITLAB_USER_LOGIN` trong script:

```yaml
deploy:
    ...
    when: manual
    script:
        - >
            if [ "$GITLAB_USER_LOGIN" == "namhl" ]; then
                sudo cp target/$projectname-$version.jar $projectpath
                sudo chown -R $projectuser. $projectpath
                sudo su $projectuser -c "kill -9 $(ps -ef| grep $projectname-$version.jar | grep -v grep | awk '{print $2}')"
                sudo su $projectuser -c "cd $projectpath; nohup java -jar $projectname-$version.jar > nohup.out 2>&1 &"
            else
                echo "Permission denied"
                exit 1
            fi
    ...
```

📌 Điều này đảm bảo: **chỉ user `namhl` mới được triển khai**.

---

## 🔁 Full cấu hình `.gitlab-ci.yml` hỗ trợ Continuous Delivery

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
    when: manual
    script:
        - >
            if [ "$GITLAB_USER_LOGIN" == 'namhl' ]; then
                sudo cp target/$projectname-$version.jar $projectpath
                sudo chown -R $projectuser. $projectpath
                sudo su $projectuser -c "kill -9 $(ps -ef| grep $projectname-$version.jar | grep -v grep | awk '{print $2}')"
                sudo su $projectuser -c "cd $projectpath; nohup java -jar $projectname-$version.jar > nohup.out 2>&1 &"
            else
                echo "Permission denied"
                exit 1
            fi
    tags:
        - ubuntuserver
    only:
        - tags

showlog:
    stage: showlog
    variables:
        GIT_STRATEGY: none
    when: manual
    script:
        - sudo su $projectuser -c "cd $projectpath; tail -n 10000 nohup.out"
    tags:
        - ubuntuserver
    only:
        - tags
```

---

## ✅ Kết luận

-   Với `when: manual`, bạn đang thực hiện **Continuous Delivery**:  
    ✅ Build tự động → 🚦Deploy thủ công có kiểm soát

-   Hạn chế người không có quyền tự ý triển khai code lên môi trường quan trọng.

---
