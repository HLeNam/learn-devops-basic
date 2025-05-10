# Tư duy viết Dockerfile

## Dockerfile để làn gì ?

File này sẽ tiến hành viết cấu hình để đưa source code của bạn và trong môi trường Container
Và cài đặt các công cụ cần thiết của dự án để sẵn sàn cho việc khởi chạy

## Dockerfile Commands

-   FROM [Tên Docker Image]
    Có thể hiểu như này: Để build dự án react chẳng hạn
    bạn sẽ cần cài đặt node trước đó mà chúng ta đã biết
    Thì cụm từ FROM + Tên của một Docker Image nào đó
    Image này chính xác chứa một server họ đã cài đặt nodejs lên trên đó
    Ví dụ:
    FROM node:20

-   WORKDIR
    Sau khi ta đã kéo một base ... nôm na như một server về rồi thì ta sẽ
    chỉ định thư mục làm việc cho nó
    Ví dụ:
    WORKDIR /app
    nếu thư mục app chưa có thì nó cũng sẽ tự tạo

-   COPY
    Đây chính xác là câu lệnh để copy source code của bạn vào trong Container
    Ví dụ:
    COPY ..
    Dấu "." đầu tiên có ý nghĩa là vị trị hiện tại của Dockerfile, tức là nếu như Dockerfile của bạn
    cùng cấp với các file dự án, sẽ tiến hành copy hết file dự án.
    Dấu "." thứ hai có ý nghĩa là vị trí hiện tại trong Container, chính là vị trí mà chúng ta chỉ định
    bằng WORKDIR ở đây là /app
    Vậy chính xác câu lệnh này sẽ copy toàn bộ source code của bạn từ trên server vào trong môi trường
    Container

-   RUN
    Đây là câu lệnh rất dễ hiểu
    Nó tương tự như việc bạn muốn chạy các câu lệnh trên linux vậy
    Ví dụ bạn muốn xem vị trí hiện tai:
    RUN pwd

-   ENV
    Lệnh này giúp ta khai báo các biến

-   EXPOSE
    Lệnh này là lệnh định nghĩa xem ứng dụng trong Container sẽ được chạy ở Port nào
    Bạn có nhớ bài trước chúng ta đã chạy câu lệnh -dp 8888:80 thì chính xác thì
    EXPOSE này sẽ là port bên phải
    EXPOSE 80
    Cũng tương tự như server thôi chúng ta muốn truy cập vào địa chỉ web thì trong Container
    cũng sẽ có port tương ứng
    Vậy là port bên trái là port của server ứng với port bên phải là port của Container
    hay nói đơn giản hơn là trong môi trường Container chạy web có port là 80 và từ các server
    khác chắc chắn là không thể truy cập được trực tiếp vào Container đó rồi nên là sẽ tiến hành
    Map port của Container ra ngoài Server Vậy thì các server khác sẽ truy cập vào port 8888 và sẽ
    xem được nội dung ở bên trong Container

-   CMD
-   ENTRYPOINT
    Cả 2 đều là các câu lệnh để chạy ứng dụng lên tuy nhiên:
    CMD thường được sử dụng để xác định lệnh và giá trị mặc định
    ENTRYPOINT thường được sử dụng khi bạn muốn giữ nguyên một lệnh cố định và cho phép lệnh được chỉ định
    khi chạy Container thêm vào cuối của nó.

## Tư duy viết Dockerfile tối ưu

1. Chạy ứng dụng bằng User khác: none root user
2. Chọn base Image phù hợp:
   Nếu đang chạy java 8 thì cũng nên cài maven java 8 để build và run dự án,
   điều này vừa giúp bạn tôi ưu dung lượng Docker Image build ra và giúp
   hạn chế những công cụ không cần thiết khiến Docker Image có thể bị nặng hơn

    Nên tìm kiếm những Docker Image nào được build sẵn dự trên alpine vì nó rất nhẹ
    và tối ưu, nó sẽ không cồng kềnh như ubuntu hoặc là CentOS

    Cùng với đó khi chọn base Docker Image ta nên chọn những Image uy tín như là
    official, verify hay spo???

3. Sử dụng Multipe stage giúp giảm kích thước Docker Image cuối cùng được build ra
