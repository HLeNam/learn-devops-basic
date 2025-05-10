# Dockerfile backend

Theo format mặc định của Docker thì Dockerfile sẽ được viết chính xác bằng Dockerfile

1. root@ubuntuserver:/data/shoeshop# vi Dockerfile
   Ta hoàn toàn có thể viết bằng tên file khác và sau đó ta cần phải thêm 1 tùy chọn để Docker hiểu
   được đó chính là định dạng của Dockerfile

Để triển khai bất kỳ dự án nào cũng sẽ cần những công cụ phù hợp và chỉ duy nhất 2 bước là
build và run

Ở đây với dự án java spring boot ta sẽ cần 2 công cụ là maven và java

Đầu tiên sẽ khai báo bước build:

Trong Dockerfile sử dụng dấu "#" để tương ứng với việc comment

```
## Build stage ##
```

Như bài trước đã nói ta sẽ cần một base Image,
Vậy thì base Image đó sẽ cần phải cài đặt maven để ta có thể build được dự án

1. Chọn image đúng version dự án --> tìm image maven được dựng trên java 8
2. Chọn image từ các nguồn official, verified, Sponsored --> để đảm bảo những Image đó là an toàn và tối ưu
3. Chọn base image là alpine --> giúp cho Image của ta nhẹ hơn

```
## Build stage ##
FROM maven:3.5.3-jdk-8-alpine
```

Chỉ định thư mục làm việc

```
## Build stage ##
FROM maven:3.5.3-jdk-8-alpine

WORKDIR /app
```

Copy source tại vị trí Dockerfile tương ứng đang đứng vào trong Container

```
## Build stage ##
FROM maven:3.5.3-jdk-8-alpine

WORKDIR /app
COPY . .
RUN mvn install  -DskipTests=true
```

Bước Run

Cũng sẽ tương tự ta cũng cần một base Image nào đó chỉ cài java thôi
vì khi ta chỉ chạy dự án java thì ta không cần cài maven làm gì
chính xác size của Docker Image cuối cùng sẽ chỉ dựa vào bước cuối cùng bạn chạy thôi
Có nghĩa là cái FROM cuối cùng của bạn và những câu lệnh ở trong đó, chứ còn những
bước build ở trên sẽ không ảnh hướng đến size Image của bạn. Nên dù ở trên có kéo những cái Image
rất nặng nhưng ở dưới ta tiến hành tách ra một layer khác thì size Docker Image của bạn sẽ chỉ
dựa trên layer dưới này thôi

nên dùng amazoncorretto nếu muốn cài base Image Java

```
## Build stage ##
FROM maven:3.5.3-jdk-8-alpine

WORKDIR /app
COPY . .
RUN mvn install  -DskipTests=true

## Run stage ##
FROM amazoncorretto:8u402-alpine-jre

```

Khi ta build được thư mục target ở bước build rồi thì làm sao để copy thư mục đó
xuống bước run để chúng ta khởi chạy

Thêm tùy chọn as build
Chính xác ta sẽ tiến hành gán output của bước build này vào trong build

```
## Build stage ##
FROM maven:3.5.3-jdk-8-alpine as build

WORKDIR /app
COPY . .
RUN mvn install  -DskipTests=true

## Run stage ##
FROM amazoncorretto:8u402-alpine-jre

WORKDIR /run
COPY --from=build /app/target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar /run/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
```

Sau khi tiến hành build dự án và copy thư mục target từ bước build vào trong thư mục run ở trong run stage rồi
thì sao để giải phóng nó ra
Tương tự như ở bên ngoài, ta cũng sẽ cần một Port để có thể truy cập vào, thì chính xác ở trong Dockerfile cũng như vậy

```
## Build stage ##
FROM maven:3.5.3-jdk-8-alpine as build

WORKDIR /app
COPY . .
RUN mvn install  -DskipTests=true

## Run stage ##
FROM amazoncorretto:8u402-alpine-jre

WORKDIR /run
COPY --from=build /app/target/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar /run/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar

EXPOSE 8080

ENTRYPOINT java -jar /run/shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
```

Dùng:
docker build -t [Tên của docker image đó]:[version] .

--> docker build -t shoeshop:v1 .

Chạy: docker run --name shoeshop -dp 8888:8080 shoeshop:v1

Bây giờ đơn giản bạn chỉ cần push cái image này lên trên một cái registry nào đó ví dụ docker hub đi chẳng hạn
thì ở trên các server khác sẽ chỉ cần kéo cái image này của bạn về cài đặc docker và chạy lên trên thành Container

Như bài trước đã nói nếu như bạn không tin một docker image nào ở một bên thứ 3 cung cấp thì bạn hoàn toàn có thể kéo
1 cái base image là alpine về cài java trên đó và chạy dự án thông thường

root@ubuntuserver:/data/shoeshop# cp Dockerfile Dockerfile-v2
root@ubuntuserver:/data/shoeshop# vi Dockerfile-v2

```
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

root@ubuntuserver:/data/shoeshop# docker build -t shoeshop:v2 -f Dockerfile-v2 .

root@ubuntuserver:/data/shoeshop# docker run --name shoeshop-v2 -dp 9999:8080 shoeshop:v2

Vậy tiếp theo để nâng cấp Dockerfile của ta hơn

Tiến hành tạo version mới

root@ubuntuserver:/data/shoeshop# cp Dockerfile-v2 Dockerfile-v3

root@ubuntuserver:/data/shoeshop# vi Dockerfile-v3

Chuyển đổi user:

```
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

root@ubuntuserver:/data/shoeshop# docker build -t shoeshop:v3 -f Dockerfile-v3 .

root@ubuntuserver:/data/shoeshop# docker logs -f shoeshop-v3

root@ubuntuserver:/data/shoeshop# docker exec -it shoeshop-v3 sh
/run $ ls -l
total 54820
-rw-r--r-- 1 shoeshop shoeshop 56133768 May 10 16:44 shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
/run $

root@ubuntuserver:/data/shoeshop# docker exec -it shoeshop-v2 sh
/run # ls -l
total 54820
-rw-r--r-- 1 root root 56133768 May 10 16:35 shoe-ShoppingCart-0.0.1-SNAPSHOT.jar
/run #
