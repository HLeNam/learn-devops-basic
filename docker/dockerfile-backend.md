# Dockerfile Backend

Theo format mặc định của Docker thì Dockerfile sẽ được viết chính xác bằng `Dockerfile`

---

## Viết Dockerfile

```bash
root@ubuntuserver:/data/shoeshop# vi Dockerfile
```

Ta hoàn toàn có thể viết bằng tên file khác và sau đó ta cần phải thêm 1 tùy chọn để Docker hiểu được đó chính là định dạng của Dockerfile.

Để triển khai bất kỳ dự án nào cũng sẽ cần những công cụ phù hợp và chỉ duy nhất 2 bước là **build** và **run**.

Ở đây với dự án Java Spring Boot ta sẽ cần 2 công cụ là **Maven** và **Java**.

---

## Giai đoạn Build

Trong Dockerfile sử dụng dấu `#` để comment:

```Dockerfile
## Build stage ##
```

### Chọn base image:

1. Chọn image đúng version dự án --> tìm image Maven được dựng trên Java 8
2. Chọn image từ các nguồn official, verified, Sponsored --> đảm bảo an toàn và tối ưu
3. Chọn base image là Alpine --> giúp cho Image nhẹ hơn

```Dockerfile
## Build stage ##
FROM maven:3.5.3-jdk-8-alpine

WORKDIR /app
COPY . .
RUN mvn install  -DskipTests=true
```

---

## Giai đoạn Run

Dùng một base image chỉ chứa Java để giảm dung lượng:

```Dockerfile
## Run stage ##
FROM amazoncorretto:8u402-alpine-jre

WORKDIR /run
COPY --from=build /app/target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar /run/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar

EXPOSE 8080

ENTRYPOINT java -jar /run/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
```

Build image và chạy:

```bash
docker build -t shoeshop:v1 .
docker run --name shoeshop -dp 8888:8080 shoeshop:v1
```

Bạn có thể push image này lên registry (ví dụ Docker Hub) và dùng ở các server khác.

---

## Dockerfile-v2: Tự cài Java trên Alpine

```Dockerfile
## Build stage ##
FROM maven:3.5.3-jdk-8-alpine AS build

WORKDIR /app
COPY . .
RUN mvn install  -DskipTests=true

## Run stage ##
FROM alpine:3.19

RUN apk add openjdk8

WORKDIR /run
COPY --from=build /app/target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar /run/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar

EXPOSE 8080

ENTRYPOINT java -jar /run/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
```

```bash
docker build -t shoeshop:v2 -f Dockerfile-v2 .
docker run --name shoeshop-v2 -dp 9999:8080 shoeshop:v2
```

---

## Dockerfile-v3: Dùng non-root user

```Dockerfile
## Build stage ##
FROM maven:3.5.3-jdk-8-alpine AS build

WORKDIR /app
COPY . .
RUN mvn install  -DskipTests=true

## Run stage ##
FROM alpine:3.19

RUN adduser -D shoeshop
RUN apk add openjdk8

WORKDIR /run
COPY --from=build /app/target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar /run/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar

RUN chown -R shoeshop:shoeshop /run
USER shoeshop

EXPOSE 8080

ENTRYPOINT java -jar /run/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
```

```bash
docker build -t shoeshop:v3 -f Dockerfile-v3 .
docker logs -f shoeshop-v3
docker exec -it shoeshop-v3 sh
```

### Kiểm tra quyền file bên trong container

```bash
/run $ ls -l
-rw-r--r-- 1 shoeshop shoeshop 56133768 May 10 16:44 shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
```

```bash
# So sánh với v2 dùng root
/run # ls -l
-rw-r--r-- 1 root root 56133768 May 10 16:35 shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
```
