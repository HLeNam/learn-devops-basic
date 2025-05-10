# Tư duy viết Dockerfile

## Dockerfile để làm gì?

File này sẽ tiến hành viết cấu hình để đưa source code của bạn vào trong môi trường Container  
Và cài đặt các công cụ cần thiết của dự án để sẵn sàng cho việc khởi chạy

---

## Dockerfile Commands

-   **FROM [Tên Docker Image]**  
    Có thể hiểu như này: Để build dự án React chẳng hạn,  
    bạn sẽ cần cài đặt Node trước đó mà chúng ta đã biết  
    Thì cụm từ `FROM` + tên của một Docker Image nào đó  
    Image này chính xác chứa một server họ đã cài đặt Node.js lên trên đó  
    **Ví dụ:**

    ```dockerfile
    FROM node:20
    ```

-   **WORKDIR**  
    Sau khi ta đã kéo một base ... nôm na như một server về rồi thì ta sẽ  
    chỉ định thư mục làm việc cho nó  
    **Ví dụ:**

    ```dockerfile
    WORKDIR /app
    ```

    Nếu thư mục `app` chưa có thì nó cũng sẽ tự tạo

-   **COPY**  
    Đây chính xác là câu lệnh để copy source code của bạn vào trong Container  
    **Ví dụ:**

    ```dockerfile
    COPY . .
    ```

    Dấu `"."` đầu tiên có ý nghĩa là vị trí hiện tại của Dockerfile, tức là nếu như Dockerfile của bạn  
    cùng cấp với các file dự án, sẽ tiến hành copy hết file dự án.  
    Dấu `"."` thứ hai có ý nghĩa là vị trí hiện tại trong Container, chính là vị trí mà chúng ta chỉ định  
    bằng `WORKDIR`, ở đây là `/app`  
    → Câu lệnh này sẽ copy toàn bộ source code từ máy chủ vào môi trường Container

-   **RUN**  
    Đây là câu lệnh rất dễ hiểu  
    Nó tương tự như việc bạn muốn chạy các câu lệnh trên Linux vậy  
    **Ví dụ bạn muốn xem vị trí hiện tại:**

    ```dockerfile
    RUN pwd
    ```

-   **ENV**  
    Lệnh này giúp ta khai báo các biến môi trường  
    **Ví dụ:**

    ```dockerfile
    ENV NODE_ENV=production
    ```

-   **EXPOSE**  
    Lệnh này là lệnh định nghĩa xem ứng dụng trong Container sẽ được chạy ở Port nào  
    Bạn có nhớ bài trước chúng ta đã chạy câu lệnh `-dp 8888:80`  
    thì chính xác `EXPOSE` này sẽ là port bên phải

    ```dockerfile
    EXPOSE 80
    ```

    Tức là trong môi trường Container chạy web có port là 80  
    Và từ các server khác chắc chắn là không thể truy cập trực tiếp vào Container đó  
    → nên ta sẽ tiến hành **map port** của Container ra ngoài Server  
    → Các server khác sẽ truy cập vào port 8888 (trên host) để xem được nội dung bên trong Container

-   **CMD**
-   **ENTRYPOINT**  
    Cả 2 đều là các câu lệnh để chạy ứng dụng lên tuy nhiên:
    -   `CMD` thường được sử dụng để xác định lệnh và giá trị mặc định
    -   `ENTRYPOINT` thường được sử dụng khi bạn muốn giữ nguyên một lệnh cố định và cho phép thêm đối số khi chạy Container

---

## Tư duy viết Dockerfile tối ưu

1. **Chạy ứng dụng bằng User khác: none root user**  
   Điều này giúp tăng tính bảo mật cho Container của bạn

2. **Chọn base Image phù hợp**  
   Nếu đang chạy Java 8 thì cũng nên chọn base image có sẵn Java 8 để build và run dự án,  
   điều này giúp bạn tối ưu dung lượng Docker Image build ra và hạn chế những công cụ không cần thiết khiến Docker Image có thể bị nặng hơn  
   → Nên tìm kiếm những Docker Image nào được build sẵn dựa trên `alpine` vì nó rất nhẹ và tối ưu,  
   không cồng kềnh như `ubuntu` hoặc `CentOS`

    → Cùng với đó khi chọn base Docker Image ta nên chọn những Image **uy tín** như là:

    - `official`
    - `verified publisher`
    - `community-trusted`

3. **Sử dụng Multi-stage Build**  
   → Giúp giảm kích thước Docker Image cuối cùng được build ra  
   Ví dụ:

    ```dockerfile
    FROM maven:3.8.6-openjdk-17 AS builder
    WORKDIR /app
    COPY . .
    RUN mvn clean package -DskipTests

    FROM openjdk:17-alpine
    WORKDIR /app
    COPY --from=builder /app/target/myapp.jar app.jar
    ENTRYPOINT ["java", "-jar", "app.jar"]
    ```

---

## Ghi chú thêm:

-   Tránh để các file build như `node_modules`, `target`, `build` bên ngoài vào Docker Image  
    → Nên tạo `.dockerignore` giống như `.gitignore` để loại các file này ra khỏi quá trình `COPY`

-   Gộp các `RUN` thành một dòng duy nhất nếu có thể để giảm số layer được tạo ra  
    **Ví dụ:**

    ```dockerfile
    RUN apt-get update && apt-get install -y curl && apt-get clean
    ```

-   Sử dụng tag cụ thể cho image thay vì `latest` để tránh lỗi build không mong muốn khi image thay đổi nội dung
