# Tư duy để triển khai dự án

**Mấu chốt của devops là triển khai càng nhiều loại dự án sẽ càng giá trị**

## Dự án được chia làm 2 loại chính:

-   `Frontend`
-   `Backend`

==> Để triển khai bất kỳ dự án nào thì cần phải có công cụ tương ứng:
ví dụ:

-   Muốn triển khai `nodejs` thì cần cài `nodejs`
-   Muốn triển khai `java` thì cần cài `java`
-   Muốn triển khai `php` thì cần cài `php`

==> Phiên bản của công cụ phải >= yêu cầu của dự án:
ví dụ:

-   Dự án yêu cầu `node` phiên bản 16 thì ta phải cài phiên bản >= 16

## File trong dự án

**Bất kỳ dự án nào cũng chia ra làm 3 loại file chính**:

-   File chức năng : Là file code thực hiện hiện các chức năng như là thêm giỏ hàng, tạo user,...
    ==> với devops chưa cần quan tâm
-   File cấu hình : Là file chứa các nội dung như kết nối database, dự án chạy ở port nào, những kết nối đến dự án khác
    ==> Đây chính xác là các file mà devops cần quan tâm
-   File khác : Như readme là file mô tả hệ thống
    ==> Cũng chưa cần quan tâm đến

## Bước triển khai

**Để triển khai mọi dự án đều sẽ có 2 bước**:

1. Build : chạy một câu lệnh nào đó để tạo ra được 1 thư mục hay 1 file nào đó
2. Run : từ file hoặc thư mục đó ta chạy nó lên

**Cần biết dự án đó cần công cụ gì?.**

**File cấu hình ở đâu? là file nào.**

**Làm sao để Build dự án?**

**Và Run nó lên?**

## Chú ý

1. Với mỗi dự án sẽ có một thư mục làm việc riêng.
2. Sẽ có người đùng dự án riêng.
    - Với mỗi dự án ta không thể nào dùng user root rồi chạy tất cả các dự án được.
    - Vì user root là user có quyền cao nhất trong hệ thống vì vậy nếu ta sử dụng user root để chạy dự án
    - thì khi dự án bị xâm nhập hoặc chiếm quyền sẽ rất nguy hiểm cho cả hệ thống đó
    - Bởi vì hacker sẽ có rất nhiều cách để khai thác hệ thống của ta
    - Vì họ đã chiếm được người dùng có quyền thao tác ở trên mọi hệ thống
    - Vậy đơn giản lúc này ta chỉ cần dùng một user khác chỉ có quyền trong thư mục làm việc
    - của dự án đó và chạy bằng chính user đó thôi
    - Khi dự án bị xâm nhập thì cũng chỉ có chính xác là thư mục đó bị ảnh hưởng
